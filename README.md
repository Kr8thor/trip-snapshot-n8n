# 🗺️ Trip Snapshot Generator

> **Live demo-ready n8n workflow** that generates beautiful, interactive trip summaries with maps, weather forecasts, and cost estimates—**zero API keys required**.

![Demo Preview](https://img.shields.io/badge/n8n-workflow-FF6D5A?style=for-the-badge&logo=n8n)
![No API Keys](https://img.shields.io/badge/API%20Keys-ZERO-success?style=for-the-badge)
![Demo Ready](https://img.shields.io/badge/Demo-Ready%20in%2060min-blue?style=for-the-badge)

---

## 🎯 What It Does

Hit a single webhook with origin, destination, start date, and trip duration. Get back:

✅ **Interactive map** with driving route polyline (Leaflet + OSM)  
✅ **Distance & drive time** (OSRM public routing API)  
✅ **7-day weather forecast** with temperature chart (Open-Meteo + QuickChart)  
✅ **Fuel cost estimate** (configurable assumptions)  
✅ **Optional Slack/Email notifications** with summary  

### Perfect For

- **Live Demos**: Wow audiences in 2 minutes with zero setup friction
- **Logistics Prototypes**: Trip briefings, driver notifications, route audits
- **Learning n8n**: Clean example of chaining HTTP requests, functions, and visualizations

---

## 🚀 Quick Start (60 min setup)

### Prerequisites

- n8n instance (local, cloud, or self-hosted)
- That's it. No API keys, no database, no external dependencies.

### Setup Steps

1. **Import the workflow**
   - Copy the contents of `workflow.json`
   - In n8n: Settings → Import from File/URL
   - Paste the JSON or upload the file

2. **Activate the webhook**
   - Open the "Webhook" node
   - Click "Test URL" to get the webhook endpoint
   - Switch to "Production" mode and save
   - Copy the Production URL

3. **Test it**
   ```bash
   # Replace <your-n8n-host> with your actual n8n URL
   https://<your-n8n-host>/webhook/trip-snapshot?origin=Lisbon&destination=Porto&start=2025-10-22&days=5
   ```

4. **Open in browser**
   - You should see a beautiful trip summary page!

---

## 📋 API Parameters

| Parameter | Required | Default | Description |
|-----------|----------|---------|-------------|
| `origin` | ✅ Yes | - | Starting city (e.g., "Lisbon", "Madrid") |
| `destination` | ✅ Yes | - | Destination city (e.g., "Porto", "Barcelona") |
| `start` | No | Today | Trip start date (YYYY-MM-DD) |
| `days` | No | 5 | Trip duration (1-10 days) |

### Example URLs

```bash
# Lisbon to Porto, 5 days starting tomorrow
/webhook/trip-snapshot?origin=Lisbon&destination=Porto&start=2025-10-21&days=5

# Madrid to Barcelona, 3 days
/webhook/trip-snapshot?origin=Madrid&destination=Barcelona&days=3

# International trip
/webhook/trip-snapshot?origin=Paris&destination=Berlin&start=2025-11-01&days=7
```

---

## 🎨 What You'll See

The generated page includes:

1. **Interactive Map**
   - Full route polyline from origin to destination
   - Start and end markers
   - Pan and zoom (Leaflet.js)

2. **Key Metrics Cards**
   - Total distance (km)
   - Estimated drive time (hours)
   - Fuel cost estimate (€)

3. **Weather Forecast**
   - Daily min/max temperature chart
   - Precipitation probability
   - 5-7 day outlook

4. **Forecast Details**
   - Daily breakdown with temps and rain chance
   - Editable fuel assumptions

---

## 🏗️ Architecture

### Data Sources (All Free & Public)

| Service | Purpose | Rate Limits | Docs |
|---------|---------|-------------|------|
| **Nominatim** | Geocoding | 1 req/sec | [Link](https://nominatim.org/release-docs/latest/api/Search/) |
| **OSRM** | Routing | Fair use | [Link](http://project-osrm.org/docs/v5.24.0/api/) |
| **Open-Meteo** | Weather | 10k req/day | [Link](https://open-meteo.com/en/docs) |
| **QuickChart** | Chart images | 100k charts/mo | [Link](https://quickchart.io/documentation/) |
| **Leaflet** | Interactive maps | CDN | [Link](https://leafletjs.com/) |

---

## 🛠️ Customization

### Adjust Fuel Costs

Edit the "Chart Data & Metrics" Function node:

```javascript
const litersPer100km = 10;  // Change to your vehicle's consumption
const fuelPrice = 1.85;      // Change to your region's price (€/L)
```

### Add Slack Notifications

1. Add a Slack node after "Set Payload"
2. Configure your Slack credentials
3. Send message with:
   ```
   🗺️ New Trip: {{$json.origin}} → {{$json.destination}}
   📏 {{$json.km}} km in {{$json.hrs}} hours
   ⛽ Est. fuel: €{{$json.fuelCost}}
   🌤️ Chart: {{$json.chartUrl}}
   ```

### Change Map Style

In the HTML Response node, replace the tile layer:

```javascript
// Default OpenStreetMap
L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png')

// Dark mode (CartoDB Dark Matter)
L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}.png')

// Satellite (Esri World Imagery)
L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}')
```

---

## 🎬 Demo Script (2-hour presentation)

### Act 1: The Hook (5 min)

"Imagine you need to brief 50 drivers every morning with personalized route data, weather forecasts, and cost estimates. Here's how we do it in one webhook call..."

1. Open browser with clean URL bar
2. Type: `<webhook-url>?origin=Lisbon&destination=Seville&start=2025-10-25&days=4`
3. Hit Enter → page loads with full trip summary
4. **Pause for effect** as audience sees map + chart appear

### Act 2: The Flexibility (10 min)

1. Change origin/destination live: Porto → Santiago, Madrid → Valencia
2. Adjust days: 3, 7, 10
3. Show responsive design on mobile (if available)

### Act 3: Behind the Scenes (20 min)

1. Open n8n workflow canvas
2. Walk through each node:
   - "No API keys stored anywhere"
   - "Public endpoints = zero vendor lock-in"
   - "Each function is 10-15 lines of readable JavaScript"
3. Edit fuel price live in the Function node
4. Re-execute → show updated cost

### Act 4: Real-World Extensions (15 min)

1. Add Slack node → show notification in #logistics
2. Mention CSV logging: "Every trip is auditable"
3. Discuss integration points:
   - Connect to booking system (Airtable, Google Sheets, CRM)
   - Trigger from new order webhook
   - Email customer 24hrs before departure

### Act 5: Q&A + Live Customization (20-30 min)

- Take audience requests: "Can we add toll estimates?" (yes, with TollGuru API)
- "What about train routes?" (swap OSRM for a rail API)
- "Multi-stop trips?" (loop through waypoints)

---

## 📊 Use Cases

### Logistics & Fleet Management
- Daily driver briefings
- Route feasibility checks
- Fuel budget validation
- Weather-aware dispatch

### Travel & Tourism
- Instant trip proposals for customers
- Multi-destination tour planning
- Seasonal weather insights

### Sales & Marketing
- Interactive quote generation
- Territory planning for reps
- Event logistics

### Internal Ops
- Office relocation planning
- Equipment transfer logistics
- Emergency response routing

---

## 🔒 Production Considerations

### When to Upgrade from Public APIs

| Service | Free Tier Limit | Upgrade When... | Alternative |
|---------|----------------|-----------------|-------------|
| Nominatim | 1 req/sec | >50 geocodes/min | Mapbox, Google Geocoding |
| OSRM | Fair use | Need SLAs or routing profiles | ORS self-hosted, Mapbox Directions |
| Open-Meteo | 10k req/day | >300 trips/day | Weatherbit, OpenWeather |

### Recommended Enhancements

- **Caching**: Store geocoded cities in n8n database or Redis
- **Error handling**: Add "If" nodes to catch 404s or rate limits
- **Authentication**: Add API key to webhook for internal use
- **Rate limiting**: Use n8n's built-in rate limiter per user/IP

---

## 🤝 Contributing

Ideas for extensions:

- [ ] Multi-leg routes (A → B → C)
- [ ] Public transit option (via Transitland)
- [ ] Historical weather comparison
- [ ] PDF export (via Puppeteer or Gotenberg)
- [ ] Real-time traffic overlay
- [ ] Carbon footprint calculator

PRs welcome! See `CONTRIBUTING.md` for guidelines.

---

## 📄 License

MIT License - feel free to use in commercial projects.

---

## 🙏 Credits

- **OpenStreetMap** for map tiles and Nominatim geocoding
- **OSRM** for fast, open-source routing
- **Open-Meteo** for weather forecasts
- **QuickChart** for chart generation
- **n8n** for the automation platform
- **Leaflet** for interactive maps

---

## 📞 Support

- **Issues**: [GitHub Issues](https://github.com/Kr8thor/trip-snapshot-n8n/issues)
- **Discussions**: [GitHub Discussions](https://github.com/Kr8thor/trip-snapshot-n8n/discussions)
- **n8n Community**: [community.n8n.io](https://community.n8n.io)

---

**Built with ❤️ for the automation community**

*Star ⭐ this repo if you found it useful!*
