# Quick Reference Card

## 🚀 Essential Commands

### Import Workflow
```bash
# Download
wget https://raw.githubusercontent.com/Kr8thor/trip-snapshot-n8n/main/workflow.json

# Or curl
curl -O https://raw.githubusercontent.com/Kr8thor/trip-snapshot-n8n/main/workflow.json
```

### Test URL Template
```
https://<your-n8n>/webhook/trip-snapshot?origin=<city>&destination=<city>&start=<YYYY-MM-DD>&days=<1-10>
```

### Example Queries
```bash
# Basic
?origin=Lisbon&destination=Porto

# With dates
?origin=Madrid&destination=Barcelona&start=2025-10-25&days=7

# Minimal (1 day)
?origin=Paris&destination=Versailles&days=1
```

---

## 📊 Node Overview

| # | Node Name | Type | Purpose |
|---|-----------|------|---------|
| 1 | Webhook | Trigger | Receive GET request |
| 2 | Validate & Defaults | Code | Parse & validate params |
| 3 | Geocode Origin | HTTP | Get origin coordinates |
| 4 | Geocode Destination | HTTP | Get destination coordinates |
| 5 | Merge Geocodes | Merge | Combine geocode results |
| 6 | Pick Coords | Code | Extract lat/lon values |
| 7 | Get Route (OSRM) | HTTP | Calculate driving route |
| 8 | Get Weather | HTTP | Fetch 7-day forecast |
| 9 | Merge Route & Weather | Merge | Combine route + weather |
| 10 | Chart Data & Metrics | Code | Calculate km, hrs, fuel, chart |
| 11 | Leaflet Prep | Code | Format coordinates for map |
| 12 | Set Payload | Code | Build HTML page |
| 13 | HTML Response | Respond | Return webpage |

---

## 🔧 Quick Customizations

### Change Fuel Price
**Node:** Chart Data & Metrics (step 10)
```javascript
const fuelPrice = 1.85;  // Change this value
```

### Change Consumption Rate
**Node:** Chart Data & Metrics (step 10)
```javascript
const litersPer100km = 10;  // Change this value
```

### Change Map Style
**Node:** Set Payload (step 12)
```javascript
// Find this line:
L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png')

// Replace with dark mode:
L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}.png')
```

### Modify Temperature Chart Colors
**Node:** Chart Data & Metrics (step 10)
```javascript
// Find datasets array, change borderColor:
{ label: 'Max °C', borderColor: 'rgb(255, 99, 132)' },  // Red
{ label: 'Min °C', borderColor: 'rgb(54, 162, 235)' }   // Blue
```

---

## 🐛 Common Errors & Fixes

| Error | Cause | Fix |
|-------|-------|-----|
| "Missing origin or destination" | Incomplete URL | Add both params: `?origin=X&destination=Y` |
| "Could not geocode" | Invalid city name | Check spelling, add country: `Paris, France` |
| "No route found" | Impossible route | Verify cities are accessible by car |
| 404 on webhook | Workflow not active | Toggle workflow to Active |
| Map doesn't load | JavaScript error | Check browser console |

---

## 📊 API Rate Limits

| Service | Free Limit | Reset Period | Notes |
|---------|------------|--------------|-------|
| Nominatim | 1 req/sec | N/A | Usage policy applies |
| OSRM | Fair use | N/A | Public instance |
| Open-Meteo | 10k/day | Daily | Per IP |
| QuickChart | 100k/month | Monthly | Per account |

**For production:** Consider self-hosting or paid alternatives.

---

## 🎨 HTML Customization Zones

### Colors
```javascript
// In Set Payload node, modify <style> section:
background:#0b1020;  // Main background
color:#e9eef7;       // Main text
background:#0f1730;  // Card background
border:1px solid #1b2650;  // Card borders
```

### Layout
```javascript
// KPI cards (3 columns default)
.kpis{grid-template-columns:repeat(3,1fr)}

// Change to 4 columns:
.kpis{grid-template-columns:repeat(4,1fr)}
```

### Map Height
```javascript
#map{height:360px}  // Change to 480px for larger map
```

---

## 🔌 Integration Snippets

### Slack Message
```javascript
{
  "text": "🗺️ *Trip Quote*",
  "blocks": [
    {
      "type": "section",
      "text": {
        "type": "mrkdwn",
        "text": `📍 *${origin}* → *${destination}*\n📏 ${km} km • ⏱️ ${hrs} hrs • ⛽ €${fuelCost}`
      }
    },
    {
      "type": "image",
      "image_url": chartUrl,
      "alt_text": "Weather forecast"
    }
  ]
}
```

### Email HTML Template
```html
<h2>Trip Summary: {{origin}} → {{destination}}</h2>
<p><strong>Distance:</strong> {{km}} km</p>
<p><strong>Drive Time:</strong> {{hrs}} hours</p>
<p><strong>Est. Fuel:</strong> €{{fuelCost}}</p>
<img src="{{chartUrl}}" alt="Weather" style="max-width:600px;">
<a href="{{webhookUrl}}" style="display:inline-block;padding:10px 20px;background:#4A90E2;color:white;text-decoration:none;border-radius:5px;">View Full Details</a>
```

### CSV Log Format
```csv
Timestamp,Origin,Destination,Distance (km),Time (hrs),Fuel Cost (€),Start Date,Days
2025-10-20 14:30,Lisbon,Porto,315,3.0,58,2025-10-22,5
```

---

## 🎯 Demo Checklist

### Before Demo
- [ ] Workflow is Active
- [ ] Test with 3 different city pairs
- [ ] Verify map loads correctly
- [ ] Check chart displays properly
- [ ] Prepare backup URLs in case of API issues

### During Demo
- [ ] Start with familiar cities (Lisbon → Porto)
- [ ] Show live customization (edit fuel price)
- [ ] Demonstrate error handling (missing param)
- [ ] Switch to international route (Paris → Berlin)
- [ ] Open n8n canvas to show workflow

### Demo URLs to Bookmark
```bash
# Short & visual
/webhook/trip-snapshot?origin=Lisbon&destination=Porto&days=3

# International
/webhook/trip-snapshot?origin=Paris&destination=Amsterdam&days=5

# Long distance
/webhook/trip-snapshot?origin=Madrid&destination=Barcelona&days=7
```

---

## 📦 Export for Sharing

### Export Workflow
1. Open workflow in n8n
2. Click **...** (more menu)
3. Select **Download**
4. Share `workflow.json`

### Share Results
```javascript
// Add to Set Payload node for shareable link:
const shareUrl = `https://your-n8n/webhook/trip-snapshot?origin=${origin}&destination=${destination}&start=${start}&days=${days}`;

// Add to HTML:
<a href="${shareUrl}">Share this trip</a>
```

---

## 🔐 Security Tips

### Add API Key Auth
```javascript
// In Validate & Defaults node:
const apiKey = q.apiKey || q.key;
const validKeys = ['demo-key-123', 'prod-key-456'];

if (!validKeys.includes(apiKey)) {
  throw new Error('Invalid or missing API key');
}
```

### Rate Limiting
```javascript
// Simple rate limit (store in n8n DB or Redis):
const requestKey = `trip:${$json.origin}:${$json.destination}`;
// Check if key exists, throw error if too recent
// Store timestamp for this request
```

### CORS Headers (if needed)
```javascript
// In HTML Response node, add headers:
{
  "Access-Control-Allow-Origin": "*",
  "Access-Control-Allow-Methods": "GET",
  "Access-Control-Allow-Headers": "Content-Type"
}
```

---

## 🚀 Performance Tips

1. **Cache geocoding results** (store in n8n database)
2. **Add retry logic** for failed API calls
3. **Use CDN** for Leaflet and chart images
4. **Minimize HTML size** (remove comments, minify CSS)
5. **Add loading state** (show spinner while fetching)

---

## 📞 Quick Help

- **GitHub Issues**: [Report bugs](https://github.com/Kr8thor/trip-snapshot-n8n/issues)
- **Discussions**: [Ask questions](https://github.com/Kr8thor/trip-snapshot-n8n/discussions)
- **n8n Community**: [Get support](https://community.n8n.io)

---

**Print this page for quick reference during demos! 🖨️**
