# 2-Hour Demo Script

## Pre-Demo Setup (30 min before)

### Technical Checklist
- [ ] n8n instance running and accessible
- [ ] Workflow imported and **Active**
- [ ] Test webhook with 2-3 city pairs
- [ ] Browser tabs ready:
  - n8n workflow canvas
  - Test webhook URL in address bar
  - Backup city pairs in notes
- [ ] Screen sharing tested
- [ ] Backup: Screenshots of successful runs

### Talking Points Preparation
- [ ] Company/use case context
- [ ] Problem statement prepared
- [ ] ROI talking points ready
- [ ] Q&A anticipation list

---

## Act 1: The Hook (5 min)

### Goal
Grab attention immediately with a working demo

### Script

**Opening:**
> "Before I explain anything, let me show you something. I'm going to generate a complete trip summary—maps, weather, costs—in about 2 seconds."

**Action:**
1. Share screen showing browser with empty URL bar
2. Type slowly (let them see):
   ```
   https://your-n8n/webhook/trip-snapshot?origin=Lisbon&destination=Porto&start=2025-10-22&days=5
   ```
3. Hit Enter
4. **Pause** as page loads (let anticipation build)
5. **Gesture** as elements appear:
   - "There's the route"
   - "Distance and time"
   - "Weather forecast chart"
   - "Daily breakdown"

**Key Message:**
> "Everything you just saw was generated in real-time from a single URL. No dashboard to log into, no complex UI. Just a webhook."

**Transition:**
> "Let me show you how flexible this is..."

---

## Act 2: The Flexibility (10 min)

### Goal
Show that it's not just a demo—it works for any trip

### Script

**Test Different Routes:**

1. **International:** 
   ```
   ?origin=Paris&destination=Berlin&days=7
   ```
   > "Works internationally—notice how it handles the longer route, recalculates everything."

2. **Short Trip:**
   ```
   ?origin=Madrid&destination=Toledo&days=1
   ```
   > "Scales down to short trips. One day, still gives you weather."

3. **Edge Case:**
   ```
   ?origin=London&destination=Edinburgh&days=10
   ```
   > "Maximum 10 days—perfect for extended routes."

**Show the URL Pattern:**
> "The beauty is the simplicity. Every trip is just four parameters:
> - Origin city
> - Destination city  
> - Start date (optional)
> - Number of days (optional, default 5)"

**Mention Mobile:**
> "This same URL works on mobile—drivers can bookmark it with their routes."

**Transition:**
> "So how does this actually work? Let me show you the engine..."

---

## Act 3: Behind the Scenes (20 min)

### Goal
Build credibility by showing the workflow

### Script

**Open n8n Canvas:**
> "This entire system is one n8n workflow with 13 nodes. Let me walk you through it."

**Node-by-Node Walkthrough:**

1. **Webhook** (point at it)
   > "Starts here. GET request with those URL parameters."

2. **Validate & Defaults** (open it, show code briefly)
   > "First thing: validation. Missing params? Error message. Otherwise, set defaults."

3. **Geocoding** (two HTTP nodes)
   > "Two parallel calls to OpenStreetMap's Nominatim API. Converts 'Lisbon' to coordinates."
   > **Key point:** "No API key needed. It's free and public."

4. **Route Calculation** (OSRM node)
   > "This is OSRM—open-source routing. Gives us distance, time, and the exact path."
   > **Key point:** "Also free. Zero vendor lock-in."

5. **Weather** (Open-Meteo node)
   > "7-day forecast from Open-Meteo. Temperature highs, lows, precipitation."
   > **Key point:** "10,000 requests per day on their free tier."

6. **Chart Generation** (Code node - show briefly)
   > "This function does three things:"
   > - Calculates fuel cost (show the editable values)
   > - Builds the temperature chart URL
   > - Formats everything for display

7. **Map Preparation** (Code node)
   > "Converts coordinates from OSRM format to Leaflet format for the interactive map."

8. **HTML Response** (Code node - scroll through HTML template)
   > "Finally, we build the entire webpage. One HTML string with embedded JavaScript for the map."

**Live Edit Demonstration:**

> "Watch this—I'll change the fuel price from €1.85 to €2.00."

1. Open "Chart Data & Metrics" node
2. Find `const fuelPrice = 1.85;`
3. Change to `2.00`
4. Save workflow
5. Re-run the webhook URL
6. Show updated cost

**React to change:**
> "See? €65 instead of €58. That's the power of n8n—business logic you can edit on the fly."

**Transition:**
> "So we've got the core demo. Now let me show you how this scales to real use cases..."

---

## Act 4: Real-World Extensions (15 min)

### Goal
Make it concrete for their business

### Script

**Open Question:**
> "What would make this useful in your workflow? Notifications? Logging? Let me show you some options."

**Extension 1: Slack Notification**

1. **Add Slack node** (drag onto canvas)
2. Connect after "Set Payload"
3. Show message template:
   ```
   🗺️ New Trip: {{origin}} → {{destination}}
   📏 {{km}} km in {{hrs}} hours
   ⛽ Est. fuel: €{{fuelCost}}
   🌤️ Weather: {{chartUrl}}
   ```

> "Now every trip quote posts to your #logistics channel. Drivers see it, dispatchers track it."

**Extension 2: Google Sheets Logging**

> "For audit trails, add a Google Sheets node. Every trip becomes a row:"
> - Timestamp
> - Origin → Destination
> - Distance, Time, Cost
> - Weather outlook

> "End of month, you've got automatic reports."

**Extension 3: Customer Email**

> "Same HTML we're showing in the browser? Send it via email."
> - Add Gmail/SendGrid node
> - Subject: `Your Trip Summary: {{origin}} → {{destination}}`
> - Body: Use the HTML payload
> - Attach the chart image

**Integration Discussion:**
> "This connects to anything n8n supports:
> - Airtable (your booking system)
> - Your CRM (Salesforce, HubSpot)
> - Your calendar (auto-calculate travel time)
> - SMS via Twilio (driver reminders)"

**Transition:**
> "Let's talk about making this production-ready..."

---

## Act 5: Production Considerations (10 min)

### Goal
Address concerns about scaling and reliability

### Script

**The Free Tier Reality Check:**

> "Full transparency: We're using free public APIs. Here's when you'd upgrade:

| Service | Free Limit | Upgrade Point |
|---------|------------|---------------|
| Nominatim | 1 req/sec | >50 geocodes/min |
| OSRM | Fair use | Need SLAs |
| Open-Meteo | 10k/day | >300 trips/day |

> For most teams starting out, these limits are fine. As you scale, you have options:
> - Self-host OSRM (Docker image, takes 10 minutes)
> - Upgrade to Mapbox or Google (drop-in replacements)
> - Run your own Nominatim instance"

**Caching Strategy:**

> "First production optimization: cache geocoding results."
> - Store 'Lisbon' → coordinates in n8n database
> - Check cache before calling Nominatim
> - 90% reduction in API calls for repeat cities

**Authentication:**

> "Add API key auth in 5 lines:"
```javascript
const apiKey = q.apiKey;
if (apiKey !== 'your-secret-key') {
  throw new Error('Invalid API key');
}
```

**Error Handling:**

> "Production checklist:
> - Add 'If' nodes to catch API failures
> - Return friendly error messages
> - Log errors to Sentry or Datadog
> - Set up alerts for critical failures"

**Transition:**
> "Any questions so far? Or should I show you some advanced use cases?"

---

## Act 6: Q&A + Live Customization (30-45 min)

### Goal
Address their specific needs and show adaptability

### Prepared Responses

**Q: "Can this handle multi-stop routes?"**

**A:** "Yes! Two approaches:

1. **Simple:** Loop through waypoints
   - Split into segments (A→B, B→C, C→D)
   - Sum distances and times
   - Show cumulative costs

2. **Advanced:** Use OSRM's waypoint support
   - Single API call with multiple coordinates
   - Returns optimized route
   - Can reorder stops for efficiency

Want me to sketch that out?"

---

**Q: "What about toll costs?"**

**A:** "Great question. Two options:

1. **Flat rate:** Add per-km toll assumption
   ```javascript
   const tollPerKm = 0.15; // €0.15/km
   const tollCost = km * tollPerKm;
   ```

2. **Actual tolls:** Integrate TollGuru API
   - Send route coordinates
   - Get real toll prices by country
   - 500 free requests/month

Which fits your use case better?"

---

**Q: "Can we brand this?"**

**A:** "Absolutely. The HTML is fully customizable:
- Add your logo (image URL in template)
- Change colors (modify the CSS)
- Add company contact info
- Include terms & conditions
- White-label completely

Want me to show you where to edit?"

---

**Q: "What about train routes or public transit?"**

**A:** "Good news—same pattern, different API:
- Replace OSRM with Rome2rio API
- Or use Google Directions with transit mode
- Same workflow structure
- Just swap the HTTP request node

The beauty of n8n is you're not locked to any single provider."

---

**Q: "How do we trigger this from our booking system?"**

**A:** "Three approaches:

1. **Webhook trigger:** Your system calls our URL
2. **Polling:** n8n checks your system every X minutes
3. **Database trigger:** Watch for new bookings in your DB

Most common: Your system sends a POST with booking details, we return the trip summary, you store it."

---

**Q: "What's the execution time?"**

**A:** (Pull up n8n execution times)
> "Typically 2-3 seconds end-to-end:
> - 0.5s geocoding (parallel)
> - 0.8s routing
> - 0.6s weather
> - 0.3s chart + HTML generation
> - 0.2s response

All external API calls run in parallel where possible."

---

**Q: "Can we save these reports?"**

**A:** "Yes, several ways:

1. **PDF export:** Add Puppeteer node
   - Renders HTML to PDF
   - Attach to email or store in S3

2. **Screenshot:** QuickChart can render full HTML
   - Single image of entire page
   - Easy to share

3. **Permalink:** Store in database
   - Give each trip a unique ID
   - URL becomes: /trip/abc123
   - View anytime

Which fits your workflow?"

---

### Live Customization Demos

**Demo 1: Change Map to Satellite View**

1. Open "Set Presentation Payload" node
2. Find the Leaflet tile layer line
3. Replace with satellite tiles
4. Save and re-execute
5. Show updated map

**Demo 2: Add Company Logo**

1. In HTML template, add after `<h1>` tag:
   ```html
   <img src="https://your-company.com/logo.png" style="height:40px;margin-bottom:12px">
   ```
2. Save and execute
3. Show logo appears

**Demo 3: Change Temperature to Fahrenheit**

1. In "Chart Data & Metrics" node
2. Add conversion function:
   ```javascript
   const toF = (c) => Math.round((c * 9/5) + 32);
   const maxesF = maxes.map(toF);
   const minsF = mins.map(toF);
   ```
3. Update chart config to use `maxesF` and `minsF`
4. Change label to "°F"

---

## Closing (5 min)

### Goal
Clear next steps and leave resources

### Script

**Summary:**
> "So in the last 90 minutes, you've seen:
> - A working trip planning system
> - Zero API keys or vendor lock-in
> - Live customization of business logic
> - Real-world integration examples
> - Production-ready path

All from one n8n workflow."

**The Repository:**
> "Everything I showed you is on GitHub:
> - Complete workflow JSON
> - Setup guide
> - Customization examples
> - Integration snippets

[Show URL on screen: github.com/Kr8thor/trip-snapshot-n8n]

Fork it, modify it, deploy it. MIT license—do whatever you want with it."

**Next Steps:**
> "Three paths forward:

1. **DIY:** Import the workflow today, customize for your team
2. **Workshop:** 2-hour session where we build your specific use case
3. **Managed:** We deploy and maintain it for you

What makes sense for you?"

**Thank You:**
> "Questions? Concerns? Ideas? I'm here."

---

## Post-Demo

### Follow-Up Email Template

```
Subject: Trip Snapshot Demo - Resources & Next Steps

Hi [Name],

Thanks for joining the Trip Snapshot Generator demo today! As promised, here are the resources:

🔗 GitHub Repository:
https://github.com/Kr8thor/trip-snapshot-n8n

📄 Key Documents:
- SETUP.md: Step-by-step installation
- QUICK_REFERENCE.md: Cheat sheet for customizations
- EXAMPLES.md: Real-world use cases

🎯 What We Covered:
✅ Real-time trip summaries (maps, weather, costs)
✅ Zero API keys required
✅ Fully customizable workflow
✅ Production-ready patterns

💡 Quick Wins for Your Team:
- [Specific use case 1 discussed]
- [Specific use case 2 discussed]
- [Integration point mentioned]

📅 Next Steps:
[ ] Import the workflow (15 min)
[ ] Test with your sample data
[ ] Schedule follow-up call if needed

Let me know what questions come up!

Best,
[Your Name]
```

---

## Backup Plan (If Demo Breaks)

### Scenario 1: n8n Down
- Switch to **recorded demo video** (prepare in advance)
- Show **screenshots** of successful runs
- Walk through **workflow JSON** in text editor

### Scenario 2: Public APIs Rate Limited
- Use **cached example results** (save HTML pages)
- Show **localhost version** with mock data
- Emphasize "this is why you cache in production"

### Scenario 3: Internet Issues
- Have **offline slides** ready with screenshots
- Show **local n8n instance** on localhost
- Use **mobile hotspot** as backup internet

---

## Success Metrics

After the demo, you should have:
- [ ] Live working demo shown
- [ ] At least 3 customizations demonstrated
- [ ] Q&A addressed technical concerns
- [ ] Next steps agreed
- [ ] Follow-up scheduled (if interested)

---

**Remember:** The goal isn't to sell. It's to inspire them with what's possible with n8n automation. 🚀
