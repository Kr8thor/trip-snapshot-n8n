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

## 🚀 Quick Start

### Prerequisites

- n8n instance (local, cloud, or self-hosted)
- That's it. No API keys, no database, no external dependencies.

### 60-Second Setup

1. **Import the workflow**
   ```bash
   # Download
   wget https://raw.githubusercontent.com/Kr8thor/trip-snapshot-n8n/main/workflow.json
   
   # Import in n8n: Settings → Import from File → Choose workflow.json
   ```

2. **Activate & test**
   ```bash
   # Get your webhook URL and try:
   https://<your-n8n>/webhook/trip-snapshot?origin=Lisbon&destination=Porto&days=5
   ```

3. **You're done!** Open the URL in your browser.

📖 **Detailed setup guide**: [SETUP.md](SETUP.md)

---

## 📖 Documentation

| Document | Purpose | Read Time |
|----------|---------|-----------|
| **[SETUP.md](SETUP.md)** | Complete installation & configuration guide | 15 min |
| **[DEMO_SCRIPT.md](DEMO_SCRIPT.md)** | 2-hour presentation script with talking points | 10 min |
| **[QUICK_REFERENCE.md](QUICK_REFERENCE.md)** | Cheat sheet for live demos & customizations | 5 min |
| **[EXAMPLES.md](EXAMPLES.md)** | Real-world use cases & URL examples | 8 min |
| **[PREVIEW.md](PREVIEW.md)** | Visual mockup of output page | 5 min |
| **[CONTRIBUTING.md](CONTRIBUTING.md)** | How to contribute improvements | 5 min |

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
# Basic trip
?origin=Lisbon&destination=Porto

# With full params
?origin=Madrid&destination=Barcelona&start=2025-10-25&days=7

# International
?origin=Paris&destination=Berlin&start=2025-11-01&days=7
```

**More examples**: [EXAMPLES.md](EXAMPLES.md)

---

## 🏗️ Architecture

### 13-Node Workflow

```
Webhook → Validate → [Geocode Origin + Geocode Dest] → Merge → 
Pick Coords → [Get Route + Get Weather] → Merge → Chart/Metrics → 
Leaflet Prep → Set Payload → HTML Response
```

**Visual breakdown**: [PREVIEW.md](PREVIEW.md)

### Data Sources (All Free & Public)

| Service | Purpose | Free Tier | Upgrade Point |
|---------|---------|-----------|---------------|
| **Nominatim** | Geocoding | 1 req/sec | >50/min |
| **OSRM** | Routing | Fair use | Need SLAs |
| **Open-Meteo** | Weather | 10k/day | >300 trips/day |
| **QuickChart** | Charts | 100k/month | Heavy use |
| **Leaflet** | Maps | Unlimited CDN | N/A |

---

## 🛠️ Customization Examples

### Change Fuel Price/Consumption

**Node:** Chart Data & Metrics (#10)
```javascript
const litersPer100km = 10;  // Your vehicle rate
const fuelPrice = 1.85;     // Your region's price (€/L)
```

### Dark Map Style

**Node:** Set Presentation Payload (#12)
```javascript
// Replace tile layer URL:
L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}.png')
```

### Add Company Logo

**Node:** Set Presentation Payload (#12)
```html
<!-- Add after <h1> tag: -->
<img src="https://your-company.com/logo.png" style="height:40px">
```

**Full customization guide**: [QUICK_REFERENCE.md](QUICK_REFERENCE.md)

---

## 🎬 Demo Resources

### For Presenters

1. **[DEMO_SCRIPT.md](DEMO_SCRIPT.md)** - Complete 2-hour demo script
   - Pre-demo checklist
   - Act-by-act presentation flow
   - Q&A responses
   - Backup plans

2. **[QUICK_REFERENCE.md](QUICK_REFERENCE.md)** - Print for quick access
   - Node overview table
   - Common errors & fixes
   - Integration snippets

3. **[EXAMPLES.md](EXAMPLES.md)** - Test URLs ready to go
   - Domestic & international routes
   - Edge cases
   - Error scenarios

### Sample Demo Flow (5 min)

```bash
# 1. Show simple trip
?origin=Lisbon&destination=Porto&days=5

# 2. Change to international
?origin=Paris&destination=Berlin&days=7

# 3. Open n8n canvas, show nodes

# 4. Live edit fuel price in Chart node

# 5. Re-execute, show updated cost
```

---

## 📊 Use Cases

### Logistics & Fleet
- Daily driver briefings with route + weather
- Fuel budget validation before dispatch
- Route feasibility checks

### Travel & Tourism
- Instant trip quotes for customers
- Multi-day tour planning
- Seasonal weather insights

### Sales & Territory
- Route planning for field reps
- Territory coverage analysis
- Customer visit logistics

**More use cases**: [EXAMPLES.md](EXAMPLES.md)

---

## 🔌 Extensions

### Add Slack Notifications

1. Add Slack node after "Set Payload"
2. Message:
   ```
   🗺️ Trip: {{origin}} → {{destination}}
   📏 {{km}} km • ⏱️ {{hrs}} hrs • ⛽ €{{fuelCost}}
   🌤️ Chart: {{chartUrl}}
   ```

### Log to Google Sheets

1. Add Google Sheets node
2. Append: timestamp, origin, destination, km, hrs, fuel

### Email Customers

1. Add Gmail/SMTP node
2. Body: Use `{{$json.html}}` for full page
3. Subject: `Trip Summary: {{origin}} → {{destination}}`

**Integration guides**: [SETUP.md](SETUP.md) + [CONTRIBUTING.md](CONTRIBUTING.md)

---

## 🔒 Production Tips

### When to Upgrade APIs

| Metric | Free Tier OK | Upgrade Needed |
|--------|--------------|----------------|
| Trips/day | <300 | >300 |
| Geocoding rate | <1/sec | >50/min |
| Need SLAs | No | Yes |

### Quick Wins

1. **Cache geocoding** → Store "Lisbon" → coords in n8n DB
2. **Add error handling** → If nodes to catch API failures
3. **Rate limiting** → Use n8n's built-in limiter
4. **Authentication** → Add API key check in Validate node

**Production guide**: [SETUP.md](SETUP.md#production-deployment)

---

## 🤝 Contributing

We welcome contributions! See [CONTRIBUTING.md](CONTRIBUTING.md) for:

- Bug reports
- Feature requests
- Pull request guidelines
- Development setup

### Ideas Welcome

- [ ] Multi-stop routes (A → B → C)
- [ ] Public transit integration
- [ ] PDF export via Puppeteer
- [ ] Real-time traffic overlay
- [ ] Carbon footprint calculator
- [ ] Toll cost estimation

---

## 📄 License

MIT License - use freely in commercial projects.

---

## 🙏 Credits

Built with:
- **n8n** - Workflow automation platform
- **OpenStreetMap** - Map tiles & geocoding
- **OSRM** - Open-source routing
- **Open-Meteo** - Weather forecasts
- **QuickChart** - Chart generation
- **Leaflet** - Interactive maps

---

## 📞 Support

- **Issues**: [Report bugs](https://github.com/Kr8thor/trip-snapshot-n8n/issues)
- **Discussions**: [Ask questions](https://github.com/Kr8thor/trip-snapshot-n8n/discussions)
- **n8n Community**: [Get help](https://community.n8n.io)

---

## 🚀 Get Started

```bash
# 1. Clone or download
git clone https://github.com/Kr8thor/trip-snapshot-n8n.git

# 2. Import workflow.json into n8n

# 3. Activate workflow

# 4. Test with:
https://your-n8n/webhook/trip-snapshot?origin=Lisbon&destination=Porto&days=5

# 5. Wow your audience! 🎉
```

---

**Built with ❤️ for the automation community**

*Star ⭐ this repo if you found it useful!*

---

## 📚 Quick Links

- 📖 [Complete Setup Guide](SETUP.md)
- 🎬 [2-Hour Demo Script](DEMO_SCRIPT.md)
- 📋 [Quick Reference Card](QUICK_REFERENCE.md)
- 💡 [Use Case Examples](EXAMPLES.md)
- 🎨 [Visual Preview](PREVIEW.md)
- 🤝 [Contributing Guide](CONTRIBUTING.md)
