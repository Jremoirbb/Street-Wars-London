# Drug Wars London — Full Game Reference
### by sneaky_no_face

---

## Available Variables (use in any message)

| Variable      | What it becomes                                |
|--------------|------------------------------------------------|
| `{item}`      | Random drug name (e.g. Cocaine, Weed)         |
| `{location}`  | Random location (e.g. Peckham, Dalston)        |
| `{area}`      | Random borough (e.g. Lewisham, Camden)         |
| `{qty}`       | Random quantity (5–19 units)                    |
| `{lowprice}`  | Cheap deal price (10–30% of base, total)       |
| `{smallcash}` | Small cash (£100–£400)                         |
| `{bigcash}`   | Large cash (£500–£2,000)                       |
| `{lostcash}`  | Mugging loss (£300–£1,100)                     |
| `{coatprice}` | Jacket upgrade price (£300–£700)               |

---

## 1. 📰 Evening Standard (News) — 15% chance
**Effect:** Randomly crashes OR spikes the price of {item} (50/50 chance of each)

Messages:
1. "BREAKING: Major FED operation in {location}! Street prices for {item} expected to surge."
2. "Contaminated batch of {item} across {area}. Dealers scrambling for clean supply."
3. "Tube strike means less Police on the streets tonight. Busy evening expected."
4. "New mayor vows crackdown on dealing. Extra patrols in {location}."
5. "Customs bust at Dover halts massive {item} shipment. Expect shortages."
6. "City boys on a bender! Demand for {item} through the roof."

---

## 2. 👵 Strange Encounter (Character) — 12% chance
**Effect:** Varies per message

Messages:
1. "An old woman on the bus whispers: \"My grandson left some {item} at mine. £{lowprice} for {qty} units.\"" → **Cheap deal offer**
2. "Granny with a shopping trolley: \"Found {item} in my late husband's shed. Want it for £{lowprice}?\"" → **Cheap deal offer**
3. "Elderly lady at the chippy: \"Don't trust anyone in {location}, dear. Filth are everywhere.\"" → **Warning only**
4. "Sweet old nan hands you £{smallcash}: \"You remind me of my grandson.\"" → **Free cash** (button: "Thanks nan!")
5. "You caught your ops slipping! You grabbed yourself £{smallcash}." → **Free cash** (button: "Say less")

**Detection logic:**
- Contains "hands you" or "ops slipping" → free cash
- Contains "Filth" or "filth" → warning only
- Everything else → cheap deal offer

---

## 3. 🚨 Police! — 12% chance
**Effect:** If carrying gear and NO PC Fox protection → choose Run / Dump / Surrender. If clean → walk free. If PC Fox paid → auto-resolve.

Messages:
1. "STOP! Metropolitan Police! You're surrounded!"
2. "Undercover officers from an unmarked car! \"Don't move!\""
3. "Police helicopter over {location}. Officers flood the area."
4. "Sniffer dogs at the Tube station! Officer pulls you aside."

**Outcomes when carrying gear (no Fox protection):**

| Choice | What happens |
|--------|-------------|
| 🏃 Run (35% escape) | **Escaped:** Walk free, +3 reputation |
| 🏃 Run (65% caught) | Lose 70–80% of your biggest item + 20% cash fine |
| 🗑️ Dump everything | Lose ALL inventory — but walk clean |
| 🤲 Surrender | Lose 60–80% of your biggest item |

**With PC Fox protection:** Auto-resolve. "You mention PC Fox's name. The officers exchange a look, nod, and wave you through."

---

## 4. 🐺 PC Fox — 7% chance
**Effect:** Corrupt officer offers protection for 10% of your current cash

Messages:
1. "A plain-clothes officer approaches. \"Name's Fox. I know what you do. 10% of your cash and I'll make sure the boys leave you alone.\""
2. "PC Fox leans against the wall. \"We can do this the easy way. 10% and you've got a friend on the inside.\""

**If you pay:** foxProtection = true. All future police encounters auto-resolve. 🐺 icon shows in your stats.
**If you don't pay:** Nothing happens, but next police stop you're on your own.
**If already paid:** "PC Fox spots you. \"Relax, we're still good. Stay smart out there.\""

---

## 5. 🤝 Street Deal — 10% chance
**Effect:** Offers {qty} units of {item} for £{lowprice} (well below market)

Messages:
1. "Shifty bloke: \"Bruv, {qty} {item} going cheap. £{lowprice} for the lot.\""
2. "Unknown number texts: \"Premium {item}, just arrived. {qty} units, £{lowprice}.\""
3. "Man in a Range Rover: \"Need to shift {qty} {item} fast. £{lowprice}.\""

---

## 6. 🗣️ Word on the Street (Tips) — 14% chance
**Effect:** No gameplay effect — flavour/hints only

Messages:
1. "One ting from Barking texts: \"Big party in {location}. {item} gonna fly. Stock up.\""
2. "Overheard on road: \"{item} has dried up. Anyone with stock makes a killing.\""
3. "Cuzzy came through and said \"Mind out. Loan shark Delroy is asking about you.\""
4. "Barber shop: \"Fake {item} in {location}. Real stuff's worth double.\""

---

## 7. 🎰 Lucky Day — 7% chance
**Effect:** Free cash (£{bigcash})

Messages:
1. "Found an envelope behind a bin — £{bigcash} inside!"
2. "Someone dropped their wallet. £{bigcash} in cash. Finders keepers."
3. "Scratch card winner — £{bigcash}!"

---

## 8. 🔪 Trouble! (Mugging) — 10% chance
**Effect:** Lose £{lostcash} cash. If message contains "some" and you have that item, also lose 40% of it.

Messages:
1. "Two lads corner you. \"Cash, now.\" You hand over £{lostcash}." (button: "...")
2. "Bumped on the high street. £{lostcash} gone. Classic move." (button: "...")
3. "Yutes on E bikes. They take £{lostcash} and some {item}." → **Also loses inventory** (button: "...")
4. "You got caught off the ends! You got dipped and lost £{lostcash}." (button: "Lock in bro!")

**Detection:** "off the ends" → custom button "Lock in bro!", "some" → also loses inventory

---

## 9. 🧥 The Jacket Man — 5% chance
**Effect:** +25 inventory capacity for £{coatprice}

Messages:
1. "Dodgy bloke: \"Deep pockets, hidden compartments. +25 jacket space. £{coatprice}.\""
2. "Market geezer: \"Custom jacket, secret pockets. +25 jacket space. £{coatprice}.\""

---

## 10. 🔥 MEGA PRICE SURGE — 7% chance
**Effect:** One drug goes to 8–20x its base price. Shows with 🔥 icon and orange highlight in the drug list.

Messages:
1. "EMERGENCY: {item} supply has completely collapsed! Prices are going MENTAL!"
2. "Every dealer in London wants {item} right now. If you're holding, today's your day."
3. "Underground rave in {location} — {item} demand has gone through the roof!"

---

## Game Mechanics

### Starting Conditions
| Setting           | Value        |
|------------------|--------------|
| Starting Cash     | £2,000       |
| Starting Debt     | £5,500       |
| Debt Interest     | 10% per day  |
| Max Borrowing     | £10,000 total|
| Starting Capacity | 100 units    |
| Game Length        | 30 days      |
| Boroughs          | 6            |
| Locations         | 36 (6 per borough) |
| Drug types        | 10           |

### Drug Base Prices & Volatility

| Drug       | Base Price | Volatility | Typical Range         |
|-----------|------------|------------|----------------------|
| Acid       | £1,200     | 4.0        | £240 – £4,800       |
| Cocaine    | £18,000    | 3.0        | £3,600 – £54,000    |
| Crack      | £1,500     | 3.5        | £300 – £5,250       |
| Hashish    | £800       | 3.0        | £160 – £2,400       |
| Heroin     | £8,000     | 3.5        | £1,600 – £28,000    |
| Ecstasy    | £25        | 5.0        | £5 – £125           |
| Mushrooms  | £600       | 5.0        | £120 – £3,000       |
| Opium      | £500       | 3.5        | £100 – £1,750       |
| Speed      | £150       | 4.0        | £30 – £600          |
| Weed       | £400       | 3.0        | £80 – £1,200        |

### Boroughs & Locations

| Borough      | Locations |
|-------------|-----------|
| Lewisham     | Catford, Deptford, New Cross, Brockley, Lee, Hither Green |
| Southwark    | Peckham, Bermondsey, Elephant & Castle, Camberwell, Borough, Walworth |
| Camden       | Camden Town, Kentish Town, Chalk Farm, King's Cross, Holloway, Hampstead |
| Wandsworth   | Battersea, Tooting, Clapham, Putney, Balham, Wandsworth Town |
| Westminster  | Soho, Mayfair, Pimlico, Marylebone, Paddington, Victoria |
| Hackney      | Dalston, Shoreditch, Stoke Newington, Clapton, Homerton, Hackney Central |

### Key Mechanics

**Banking:** Cash in the bank is safe from muggings AND police fines. You must withdraw to buy drugs.

**Loan Shark (Delroy):** Borrow up to £10,000 total. Debt grows 10% per day compound. Paying it off is critical.

**PC Fox Protection:** Pay 10% of your cash once. All future police encounters auto-resolve. Shown as 🐺 in stats.

**Dump:** Emergency button to clear all inventory. Useful before police encounters or when you need to go clean.

**Price History:** Tracks last 6 locations visited with all prices. Use it to plan return visits.

**Reputation:** Increases by 1 per trade. Higher reputation = slightly higher police encounter chance.

**Broke Check:** If your cash can't buy the cheapest drug, and you have no stock and nothing in the bank, game over.

**Day Countdown Messages:**
- Day 25: "5 days left. Time's running out."
- Day 27: "3 days left. Make it count."
- Day 29: "Tomorrow's your last day. Sell everything or go broke."
- Day 30: "LAST DAY. Cash out or lose everything."

**Mega Spike Detection:** Any drug priced above 6x its base price shows with 🔥 icon and orange row highlight.

### Sound Effects
| Event    | Sound |
|----------|-------|
| Buy      | Cash register ping |
| Sell     | Coin drop tinks |
| Travel   | Whoosh sweep |
| Police   | Wee-woo siren |
| Mugging  | Impact thud |
| Lucky    | Ka-ching bells |
| Broke    | Descending sad tone |
