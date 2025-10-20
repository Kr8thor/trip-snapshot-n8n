# Detailed Setup Guide

This guide walks you through setting up the Trip Snapshot Generator workflow step-by-step.

## Prerequisites

### Required
- **n8n instance** (v1.0+ recommended)
  - Local: Docker, npm, or binary install
  - Cloud: n8n Cloud account
  - Self-hosted: VPS with n8n installed

### Optional (for extensions)
- Slack workspace + incoming webhook
- SMTP server or Gmail account
- Google Sheets API access

---

## Installation Methods

### Method 1: Import JSON (Recommended)

1. **Download the workflow**
   ```bash
   wget https://raw.githubusercontent.com/Kr8thor/trip-snapshot-n8n/main/workflow.json
   ```

2. **Import into n8n**
   - Open n8n UI
   - Click **Settings** (gear icon, top right)
   - Select **Import from File**
   - Choose `workflow.json`
   - Click **Import**

3. **Activate the workflow**
   - Click the **Active** toggle (top right)
   - Workflow should turn green

### Method 2: Manual Setup

If you prefer to build from scratch, follow the node-by-node guide below.

---

## Node-by-Node Configuration

### 1. Webhook Trigger

- **Node Type**: `Webhook`
- **HTTP Method**: `GET`
- **Path**: `trip-snapshot`
- **Response Mode**: `Last Node`

**Steps:**
1. Add a Webhook node to canvas
2. Set HTTP Method to GET
3. Set Path to `trip-snapshot`
4. Set Response Mode to "Using 'Respond to Webhook' Node"
5. Click **Test URL** to get test webhook
6. Switch to **Production** after testing

---

### 2. Validate & Defaults (Code Node)

**Code:**
```javascript
const q = $input.item.json.query || $input.item.json;
if (!q.origin || !q.destination) {
  throw new Error('Missing origin or destination. Example: ?origin=Lisbon&destination=Porto&start=2025-10-20&days=5');
}
const start = q.start || new Date().toISOString().slice(0,10);
const days = Math.max(1, Math.min(parseInt(q.days || '5', 10), 10));

const d = new Date(start);
d.setDate(d.getDate() + days - 1);
const end = d.toISOString().slice(0,10);

return { origin: q.origin, destination: q.destination, start, days, end };
```

---

### 3. Geocode Origin (HTTP Request)

- **Method**: `GET`
- **URL**: `https://nominatim.openstreetmap.org/search?format=json&q={{ $json.origin }}&limit=1&addressdetails=0`

---

### 4. Geocode Destination (HTTP Request)

- **Method**: `GET`
- **URL**: `https://nominatim.openstreetmap.org/search?format=json&q={{ $json.destination }}&limit=1&addressdetails=0`

---

### 5. Merge Geocodes (Merge Node)

- **Mode**: `Combine`
- **Combination Mode**: `Merge By Position`
- **Connect**: Origin → Input 1, Destination → Input 2

---

### 6. Pick Coords (Code Node)

**Code:**
```javascript
const items = $input.all();
const originRes = items[0].json;
const destRes = items[1].json;

if (!originRes[0] || !destRes[0]) {
  throw new Error('Could not geocode one of the locations.');
}

const origLat = parseFloat(originRes[0].lat);
const origLon = parseFloat(originRes[0].lon);
const destLat = parseFloat(destRes[0].lat);
const destLon = parseFloat(destRes[0].lon);

return { ...(items[0].json), origLat, origLon, destLat, destLon };
```

---

### 7. Get Route (HTTP Request)

- **Method**: `GET`
- **URL**: `https://router.project-osrm.org/route/v1/driving/{{ $json.origLon }},{{ $json.origLat }};{{ $json.destLon }},{{ $json.destLat }}?overview=full&geometries=geojson`

---

### 8. Get Weather (HTTP Request)

- **Method**: `GET`
- **URL**: `https://api.open-meteo.com/v1/forecast?latitude={{ $json.destLat }}&longitude={{ $json.destLon }}&hourly=temperature_2m&daily=temperature_2m_max,temperature_2m_min,precipitation_probability_max&timezone=auto&start_date={{ $json.start }}&end_date={{ $json.end }}`

---

### 9. Merge Route & Weather (Merge Node)

- **Mode**: `Combine`
- **Combination Mode**: `Merge By Position`
- **Connect**: Route → Input 1, Weather → Input 2

---

### 10. Chart Data & Metrics (Code Node)

See `workflow.json` for full code (includes fuel calculation and QuickChart URL generation).

---

### 11. Leaflet Polyline Prep (Code Node)

See `workflow.json` for coordinate transformation code.

---

### 12. Set Presentation Payload (Code Node)

See `workflow.json` for complete HTML template generation.

---

### 13. HTML Response (Respond to Webhook)

- **Respond With**: `Text`
- **Response Body**: `={{ $json.html }}`
- **Headers**: Add `Content-Type: text/html; charset=utf-8`

---

## Testing

### Test Workflow

1. **Get the webhook URL**
   - Click on Webhook node
   - Copy the Test URL or Production URL

2. **Test in browser**
   ```
   https://your-n8n-host/webhook-test/trip-snapshot?origin=Lisbon&destination=Porto&start=2025-10-22&days=5
   ```

3. **Verify output**
   - Map should load with route
   - KPIs should show distance, time, fuel
   - Weather chart should display
   - Forecast list should appear

### Test Different Scenarios

```bash
# Short domestic trip
?origin=Lisbon&destination=Porto&days=3

# Long international trip
?origin=Paris&destination=Berlin&start=2025-11-01&days=7

# Edge case: 1 day
?origin=Madrid&destination=Toledo&days=1

# Edge case: 10 days (max)
?origin=London&destination=Edinburgh&days=10
```

---

## Troubleshooting

### Common Issues

**1. "Could not geocode one of the locations"**
- Check spelling of city names
- Try adding country: `Lisbon, Portugal`
- Ensure Nominatim is accessible

**2. "No route found"**
- Verify coordinates are valid
- Check if origin and destination are routable by car
- Try a different routing service

**3. "Weather data unavailable"**
- Verify date format (YYYY-MM-DD)
- Check Open-Meteo API status
- Ensure coordinates are valid

**4. "Webhook returns 404"**
- Ensure workflow is **Active** (toggle is on)
- Use Production URL, not Test URL
- Check webhook path matches URL

**5. "Chart doesn't display"**
- Check browser console for errors
- Verify QuickChart URL is valid
- Test chart URL directly in browser

### Debug Mode

1. Open workflow
2. Click **Execute Workflow** (play button)
3. Enter test data manually
4. Inspect each node's output
5. Look for errors in node execution log

---

## Production Deployment

### 1. Use Production URL

- Switch webhook from Test to Production
- Note: Test URLs expire after a period of inactivity

### 2. Add Rate Limiting

- Use n8n's built-in rate limiter
- Or add a proxy like Nginx with rate limiting

### 3. Add Authentication

```javascript
// In Validate & Defaults node, add:
const apiKey = q.apiKey;
if (apiKey !== 'your-secret-key') {
  throw new Error('Invalid API key');
}
```

### 4. Set Up Monitoring

- Enable n8n workflow statistics
- Monitor execution times
- Track failure rates
- Set up alerts for critical failures

### 5. Consider Caching

- Cache geocoding results in n8n database
- Use Redis for frequently accessed routes
- Implement cache expiration (24-48 hours)

---

## Optional Enhancements

### Add Slack Notification

1. Add **Slack** node after "Set Presentation Payload"
2. Configure Slack credentials
3. Message template:
   ```
   🗺️ *New Trip Generated*
   📍 {{ $json.origin }} → {{ $json.destination }}
   📏 Distance: {{ $json.km }} km
   ⏱️ Time: {{ $json.hrs }} hours
   ⛽ Fuel: €{{ $json.fuelCost }}
   🌤️ Weather: {{ $json.chartUrl }}
   ```

### Add Email Notification

1. Add **Gmail** or **SMTP** node
2. Email body: Use `{{ $json.html }}` for full HTML
3. Subject: `Trip Summary: {{ $json.origin }} → {{ $json.destination }}`

### Log to Google Sheets

1. Add **Google Sheets** node
2. Append row with: date, origin, destination, km, hrs, fuel cost
3. Enable for trip audit trail

---

## Next Steps

- Customize fuel prices and consumption
- Add your company branding to HTML
- Integrate with your booking system
- Set up scheduled reports
- Add more data sources (traffic, tolls, etc.)

---

**Need help?** Open an issue or discussion on GitHub!