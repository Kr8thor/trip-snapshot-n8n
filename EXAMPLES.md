# Trip Snapshot Examples

Real-world example queries and their expected outputs.

## Basic Examples

### Example 1: Lisbon to Porto

**URL:**
```
/webhook/trip-snapshot?origin=Lisbon&destination=Porto&start=2025-10-22&days=5
```

**Expected Output:**
- Distance: ~315 km
- Drive Time: ~3.0 hours
- Fuel Cost: ~€58 (at default settings)
- Weather: 5-day forecast for Porto
- Map: Route via A1 highway

---

### Example 2: Madrid to Barcelona

**URL:**
```
/webhook/trip-snapshot?origin=Madrid&destination=Barcelona&days=7
```

**Expected Output:**
- Distance: ~620 km
- Drive Time: ~6.0 hours
- Fuel Cost: ~€115
- Weather: 7-day forecast for Barcelona
- Map: Route via A-2 highway

---

### Example 3: Paris to Berlin

**URL:**
```
/webhook/trip-snapshot?origin=Paris&destination=Berlin&start=2025-11-01&days=7
```

**Expected Output:**
- Distance: ~1,050 km
- Drive Time: ~10.5 hours
- Fuel Cost: ~€195
- Weather: 7-day forecast for Berlin
- Map: International route through France, Belgium, Netherlands, Germany

---

## Edge Cases

### Short Trip (1 day)

**URL:**
```
/webhook/trip-snapshot?origin=Madrid&destination=Toledo&days=1
```

**Expected Output:**
- Distance: ~85 km
- Drive Time: ~1.0 hour
- Fuel Cost: ~€16
- Weather: Same-day forecast only

---

### Long Trip (10 days max)

**URL:**
```
/webhook/trip-snapshot?origin=Lisbon&destination=Barcelona&days=10
```

**Expected Output:**
- Distance: ~1,000 km
- Drive Time: ~10.0 hours
- Fuel Cost: ~€185
- Weather: 10-day extended forecast

---

## International Examples

### London to Edinburgh

**URL:**
```
/webhook/trip-snapshot?origin=London&destination=Edinburgh&days=4
```

**Expected Output:**
- Distance: ~665 km
- Drive Time: ~7.0 hours
- Map: Route through England and Scotland

---

### Amsterdam to Copenhagen

**URL:**
```
/webhook/trip-snapshot?origin=Amsterdam&destination=Copenhagen&days=6
```

**Expected Output:**
- Distance: ~850 km
- Drive Time: ~9.5 hours
- Map: Route through Netherlands, Germany, Denmark

---

## Demo Scenarios

### Fleet Dispatch Demo

**Scenario:** Daily driver briefing for logistics company

**URLs to rotate:**
```bash
# Driver 1: Warehouse to Barcelona
?origin=Madrid&destination=Barcelona&start=2025-10-23&days=2

# Driver 2: Lisbon to Seville
?origin=Lisbon&destination=Seville&start=2025-10-23&days=3

# Driver 3: Porto to Santiago
?origin=Porto&destination=Santiago de Compostela&start=2025-10-23&days=2
```

---

### Tourism Agency Demo

**Scenario:** Instant trip quotes for customers

**URLs to showcase:**
```bash
# Romantic getaway (Portugal)
?origin=Lisbon&destination=Sintra&days=2

# Coastal road trip (Spain)
?origin=Barcelona&destination=Valencia&days=5

# Cultural tour (Italy)
?origin=Rome&destination=Florence&days=4
```

---

### Sales Territory Demo

**Scenario:** Route planning for field sales reps

**URLs:**
```bash
# Northern region visit
?origin=Manchester&destination=Glasgow&days=3

# Eastern territory
?origin=Berlin&destination=Warsaw&days=5

# Southern coverage
?origin=Milan&destination=Naples&days=4
```

---

## Weather Variations

### Summer Trip (High Temps)

**URL:**
```
/webhook/trip-snapshot?origin=Seville&destination=Granada&start=2025-07-15&days=3
```

**Expected:** Hot temperatures (35-40°C), low precipitation

---

### Winter Trip (Cold/Rain)

**URL:**
```
/webhook/trip-snapshot?origin=Stockholm&destination=Oslo&start=2025-12-20&days=5
```

**Expected:** Cold temperatures (-5 to 5°C), high precipitation probability

---

## Error Scenarios

### Missing Parameters

**URL:**
```
/webhook/trip-snapshot?origin=Lisbon
```

**Expected Error:**
```
Missing origin or destination. Example: ?origin=Lisbon&destination=Porto&start=2025-10-20&days=5
```

---

### Invalid City

**URL:**
```
/webhook/trip-snapshot?origin=InvalidCity123&destination=Porto
```

**Expected Error:**
```
Could not geocode one of the locations.
```

---

### Invalid Date Format

**URL:**
```
/webhook/trip-snapshot?origin=Lisbon&destination=Porto&start=2025-13-45
```

**Expected:** Falls back to today's date (validation in code)

---

## Customization Examples

### Modified Fuel Consumption

**Change in "Chart Data & Metrics" node:**
```javascript
// Default
const litersPer100km = 10;

// For van/truck
const litersPer100km = 15;

// For electric (€/kWh calculation)
const kwhPer100km = 20;
const electricityPrice = 0.30;
```

---

### Different Fuel Prices by Region

```javascript
// Auto-detect region and adjust
let fuelPrice = 1.85; // Default EU

if ($json.destination.includes('UK')) {
  fuelPrice = 1.65; // UK average
} else if ($json.destination.includes('Norway')) {
  fuelPrice = 2.10; // Norway
}
```

---

## Advanced Use Cases

### Multi-Day Route with Stops

**Workflow Extension:**
1. Split long trips (>500km) into daily segments
2. Generate separate forecasts per stop
3. Calculate cumulative fuel costs

**Example Output:**
```
Day 1: Lisbon → Coimbra (200km, €37)
Day 2: Coimbra → Porto (115km, €21)
Total: 315km, €58
```

---

### Return Trip Calculation

**Add to Code Node:**
```javascript
const roundTrip = q.roundTrip === 'true';
if (roundTrip) {
  km = km * 2;
  hrs = (parseFloat(hrs) * 2).toFixed(1);
  fuelCost = fuelCost * 2;
}
```

**URL:**
```
?origin=Lisbon&destination=Porto&roundTrip=true&days=5
```

---

## Integration Examples

### Slack Bot Command

**Slack Slash Command:**
```
/trip-quote Lisbon to Porto, 5 days
```

**Bot Response:**
```
🗺️ Trip: Lisbon → Porto
📏 Distance: 315 km
⏱️ Time: 3.0 hours
⛽ Fuel: €58
🌤️ Weather forecast: [Link to chart]
📋 Full details: [Link to webhook page]
```

---

### CRM Integration

**Trigger:** New booking in Airtable/Salesforce

**Action:**
1. Get origin/destination from CRM
2. Call trip-snapshot webhook
3. Email customer with trip details
4. Log estimate back to CRM

---

### Calendar Integration

**Trigger:** Calendar event created with location

**Action:**
1. Parse event location
2. Calculate travel from office to location
3. Send notification with trip prep
4. Add buffer time to calendar

---

**Want to add your own example?** Submit a PR or open a Discussion!