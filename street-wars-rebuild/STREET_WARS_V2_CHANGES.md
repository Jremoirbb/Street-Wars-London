# STREET WARS LONDON — Rebuild Design Document
### Delta from Grey Market codebase
### Date: 8 April 2026
### Author: sneaky_no_face

---

## HOW TO USE THIS DOCUMENT

This document describes how to transform the Grey Market codebase (grey-market.html) into the new Street Wars London. Grey Market is the starting point — its layout, systems, and architecture are preserved. This doc covers what to change, replace, add, and remove.

Reference files:
- `grey-market.html` — the starting codebase
- `street_wars_scenarios.md` — original Street Wars flavour, events, and mechanics to port across

Execute changes via sequenced prompts to Claude Code. Verify after each step.

---

## 1. IDENTITY & BRANDING

### Replace Throughout

| Grey Market | Street Wars London |
|---|---|
| GREY MARKET | STREET WARS LONDON |
| INTERNATIONAL ARMS TRADE | — (remove subtitle or replace) |
| "30 days. 6 regions. 36 countries." | "30 days. 6 boroughs. 36 ends." |
| Enter alias... | Enter street name... |
| "STEP IN" / "LET'S GO" | "LET'S GO" |
| $ (dollars) | £ (pounds) |
| sneaky_no_face credit | sneaky_no_face credit (keep) |

### Meta Tags

- Title: "Street Wars London"
- Theme colour: #1a1a2e (dark) — or carry over Grey Market's #0D0D12
- OG image: update URL when deployed
- Favicon: update

### Colour Palette

Switch from Grey Market's purple accent to Street Wars' orange accent:

| Role | Grey Market | Street Wars |
|---|---|---|
| Background | #0D0D12 | #1a1a2e or keep #0D0D12 |
| Surface | #1A1A24 | Keep or match |
| Accent / CTA | #7C3AED (purple) | #F97316 (orange) |
| Accent hover | #8B5CF6 | #FB923C |
| Green | #22C55E | #22C55E (keep) |
| Red | #EF4444 | #EF4444 (keep) |
| Amber | #F59E0B | #F59E0B (keep) |

All buttons, highlights, and accent elements switch from purple to orange.

### Fonts

Grey Market uses JetBrains Mono + Inter. Street Wars uses JetBrains Mono + DM Sans.

Replace Inter with DM Sans throughout.

---

## 2. TONE & VOICE

### The Feel

London street culture. Slang, humour, personality. The player is a dealer on the estates, not an international broker. Every message should feel like a text from your mate, not an intelligence report.

### Voice Rules

- London street slang throughout. "Mandem", "ends", "feds", "bare", "peak", "wagwan"
- Light humour is fine — this isn't Grey Market's clinical tone
- Drugs are "food", "bits", "packs" in flavour text but listed by name in the UI
- Boroughs are "ends"
- Money is "P's", "bread", "paper" in flavour text, £ in the UI
- Events read as street gossip, not intelligence reports
- Creditor messages have distinct personalities — Greezy talks different to Val

### What to Preserve from Original Street Wars

Refer to `street_wars_scenarios.md` for existing slang, event messages, and flavour. Port across any messages that still fit. The tone is already established — don't lose it in the rebuild.

---

## 3. MAP — BOROUGHS & LOCATIONS

### Replace Grey Market Regions with London Boroughs

| # | Borough | Area | Locations |
|---|--------|------|-----------|
| 0 | Lewisham | South East | Catford, Deptford, New Cross, Brockley, Lee, Hither Green |
| 1 | Southwark | South | Peckham, Bermondsey, Elephant & Castle, Camberwell, Borough, Walworth |
| 2 | Lambeth | South | Brixton, Stockwell, Streatham, Clapham, Vauxhall, Kennington |
| 3 | Camden | North | Camden Town, Kentish Town, Chalk Farm, King's Cross, Holloway, Hampstead |
| 4 | Hackney | East | Dalston, Shoreditch, Stoke Newington, Clapton, Homerton, Hackney Central |
| 5 | Brent | North West | Harlesden, Stonebridge, Kilburn, Wembley, Willesden, Neasden |

### Risk Bias per Borough

| Borough | Risk Bias | Reasoning |
|---|---|---|
| Lewisham | 0.08 | Moderate — residential, some heat |
| Southwark | 0.12 | High — Peckham, Bermondsey, active |
| Lambeth | 0.14 | Highest — Brixton, Stockwell, intense |
| Camden | 0.06 | Lower — tourist areas mixed in |
| Hackney | 0.10 | Moderate-high — Dalston, Clapton |
| Brent | 0.12 | High — Harlesden, Stonebridge estate |

### Travel Rules

Keep Grey Market's system:
- Within borough: 1 day, free
- Between boroughs: 2 days, free
- Day 29: inter-borough travel blocked
- Day 30: all travel blocked, Cash Out only

Replace all "region" references with "borough" and all "country" references with "location" or "ends" throughout the codebase.

---

## 4. ITEMS — DRUGS

### Replace Grey Market Items

| # | Drug | Base Price | Volatility | Heat Impact |
|---|------|-----------|------------|-------------|
| 0 | Acid | £1,200 | 2.2 | None |
| 1 | Cocaine | £18,000 | 1.8 | Low |
| 2 | Crack | £1,500 | 2.0 | None |
| 3 | Hashish | £800 | 1.8 | None |
| 4 | Heroin | £8,000 | 2.0 | Low |
| 5 | Ecstasy | £25 | 2.5 | None |
| 6 | Ketamine | £600 | 2.5 | None |
| 7 | Mushrooms | £500 | 2.0 | None |
| 8 | Speed | £150 | 2.2 | None |
| 9 | Weed | £400 | 1.8 | None |

### Heat Impact

Unlike Grey Market where high-tier arms raised Interpol alert significantly, drugs have a lighter heat footprint. Only the most serious drugs draw police attention:

| Tier | Drugs | Heat Formula |
|---|---|---|
| LOW | Cocaine (1), Heroin (4) | `min(3, 1 + Math.floor(amount / 5))` |
| NONE | All others | 0 |

Cocaine and heroin are Class A and attract attention in volume. Everything else flies under the radar. This is a much gentler heat system than Grey Market's arms — the main heat sources are events (snitch, undercover, staying too long) not trading.

### Item Availability / Scarcity

Keep Grey Market's system:
- 60% all 10 available
- 25% one missing
- 10% two missing
- 5% three missing

Unavailable items do not appear in the BUY panel (same as current Grey Market behaviour).

### No Embargoes

Remove the embargo system entirely. There's no UN inspecting London drug dealers. Remove:
- `activeEmbargoes` state
- Embargo wire messages
- UN Inspector encounters
- Embargo tick-down logic in travel/go dark
- Restricted/amber item display in market
- All embargo-related code

### Mega Price Display

Keep the 🔥 indicator on items priced above `basePrice × 6`.

---

## 5. ECONOMY

### Starting Conditions

| Parameter | Value |
|---|---|
| Starting cash (own) | £2,000 |
| Starting cash (total) | £2,000 + creditor loan |
| Game length | 30 days |
| Starting capacity | 100 units |
| Starting location | Random borough, random location |
| Starting heat | 0% |
| Starting health | 100% |

### Creditor Loans

| Creditor | Debt | Interest | Starting Cash (total) |
|---|---|---|---|
| Greezy | £4,000 | 10%/day | £4,000 |
| Ren | £4,500 | 9%/day | £4,250 |
| Auntie | £5,000 | 8%/day | £4,500 |
| Val | £6,000 | 6%/day | £5,000 |

Formula: `startingCash = 2000 + Math.floor(creditor.debt * 0.5)`

### Net Worth

```
netWorth = cash + sum(inventory[i] * prices[i]) - debt
```

### Capacity Upgrades (Shop)

Replace Grey Market's transport tiers:

| Item | Price | Capacity | Description |
|---|---|---|---|
| Starting | Free | 100 | "Backpack and pockets" |
| Moncler Jacket | £20,000 | +25 (125) | "Looking fresh, extra pockets" |
| Canada Goose | £40,000 | +40 (165) | "Big coat energy" |

Keep from original Street Wars. These also increase mugging risk:
- Moncler: +5% mugging chance
- Canada Goose: +8% mugging chance

### No Transport Tiers

Grey Market has Duffel Bags → Cargo Truck → Cargo Plane → Container Network. Remove this entire system. Street Wars uses Moncler / Canada Goose instead which are bought from the Shop.

---

## 6. CREDITOR SYSTEM

### Overview

Four creditors replace Grey Market's four. Same architecture — selection screen, escalation stages, debt mechanics, creditor protection. Different characters, different London flavour.

### Creditor Selection Screen

Same layout as Grey Market — 2×2 grid on desktop, swipeable carousel on mobile. Each card shows:
- Pixel art portrait (base64-encoded — to be created)
- Name (large, bold)
- Title (accent colour)
- Description
- Strategic hint (amber)
- Debt amount, interest rate, starting cash

### Header Text

Replace Grey Market's header: "You need capital. Banks aren't an option. Not for what you do..." with:

"You need P's to get started. Banks? Nah. Not for this. Four people are willing to front you the money. All of them are dangerous. Choose who you owe."

### The Four Creditors

#### GREEZY — The OG
- **Title:** Debt collector from the estate.
- **Description:** Everyone knows Greezy. He's always around, always watching. Owes people all over south London, and people owe him. Small time but persistent. He'll bleed you dry £50 at a time.
- **Strategic hint:** "Comes around often. Takes a little each time. Death by a thousand cuts."
- **Debt:** £4,000 · **Interest:** 10%/day · **Starting cash:** £4,000
- **Mechanic:** Fastest escalation, frequent small hits. Cash + small health damage.
- **Escalation stages:**
  - Stage 0 (Warning): minDay 4, gap 4 days
  - Stage 1 (First visit): minDay 9, gap 4 days
  - Stage 2 (Heavy): minDay 15, gap 3 days
- **Escalation effects:**
  - Warning: No damage. "Greezy's been asking about you."
  - First visit: Cash 18%, health -10%.
  - Heavy: Cash 28%, health -25%.
  - Repeat: Cash 28%, health -20%, gap 3 days.
- **Intro message:** "Greezy knows you're back on the roads. You owe him £4,000. He ain't forgetting."
- **Grey Market equivalent:** Osei

#### REN — The Inmate
- **Title:** Locked up in Belmarsh. Still running things.
- **Description:** Serving 12 years but you wouldn't know it. Smuggled phone, loyal soldiers outside. His boys are young and hungry — they're not taking your money, they're taking your stock. That's how they eat while he's inside.
- **Strategic hint:** "Doesn't touch your cash. Takes your goods. His boys need product to move."
- **Debt:** £4,500 · **Interest:** 9%/day · **Starting cash:** £4,250
- **Mechanic:** Takes inventory, not cash. Punishes players who hold stock.
- **Escalation stages:**
  - Stage 0 (Warning): minDay 5, gap 5 days
  - Stage 1 (First visit): minDay 12, gap 5 days
  - Stage 2 (Heavy): minDay 20, gap 4 days
- **Escalation effects:**
  - Warning: No damage. Message from a burner phone.
  - First visit: 25-30% of all inventory taken. If carrying nothing, health -15%.
  - Heavy: 50-60% of all inventory taken + health -10%. If carrying nothing, health -25%.
  - Repeat: 50-60% inventory, health -10%, gap 4 days.
- **Intro message:** "Ren sent a message from Belmarsh. You owe him £4,500. His boys outside are waiting."
- **Grey Market equivalent:** Unique (no direct parallel — cargo-focused like partial Volkov)

#### AUNTIE — The Ghanaian Matriarch
- **Title:** Runs the money transfer shop in Peckham.
- **Description:** Everyone in the community knows Auntie. She's warm, she's polite, she'll ask about your mum. Then she'll send her nephews to take everything you own. You can't hide — someone's auntie will spot you at the bus stop.
- **Strategic hint:** "Slow to move. But when she collects, she takes everything — cash, goods, and your whole day."
- **Debt:** £5,000 · **Interest:** 8%/day · **Starting cash:** £4,500
- **Mechanic:** Slowest escalation, heaviest consequences. Takes cash, goods, and time.
- **Escalation stages:**
  - Stage 0 (Warning): minDay 6, gap 7 days
  - Stage 1 (First visit): minDay 14, gap 6 days
  - Stage 2 (Heavy): minDay 22, gap 5 days
- **Escalation effects:**
  - Warning: No damage. Word through the community.
  - First visit: Cash 30%, inventory 30% (all items), health -15%.
  - Heavy: Cash 50%, inventory 60% (all items), health -40%, lose a day.
  - Repeat: Cash 50%, inventory 60%, health -30%, lose a day, gap 5 days.
- **Intro message:** "Auntie knows you're out here. You owe her £5,000. She doesn't forget. Nobody in Peckham does."
- **Grey Market equivalent:** Volkov

#### VAL — The Matriarch
- **Title:** Old school south London. The mum behind the family.
- **Description:** Big earrings, leopard print, corner of the pub. Everyone comes to Val. Her sons do the collecting but she gives the orders. Believes in manners, respect, and breaking fingers when respect isn't shown. One warning. One.
- **Strategic hint:** "One warning. Then her boys come round. There is no second chance."
- **Debt:** £6,000 · **Interest:** 6%/day · **Starting cash:** £5,000
- **Mechanic:** Highest debt, lowest interest, but only ONE warning before catastrophe.
- **Escalation stages:**
  - Stage 0 (Warning): minDay 8, gap 10 days
  - Stage 1 (Heavy): minDay 19, gap 0 (immediate repeat potential)
- **Escalation effects:**
  - Warning: No damage. Word through her eldest.
  - Heavy: Health reduced to max(5, min(15, health - 60 - random(0-19))). Cash 60%. Inventory 75% (all items). Lose a day. Devastating.
  - Repeat: Health -40%, Cash 40%, inventory 40%, lose a day, gap 8 days.
- **Intro message:** "Val's eldest came by. Said his mum wants a word about that £6,000. Be smart about this."
- **Grey Market equivalent:** Marchetti

### Creditor Protection

Same system as Grey Market. Available during mugging and rival encounters when `debt > 0`.

Button: "Call [Creditor name]" with cost shown.

Cost: threat fee × 1.15, added to debt not cash.

**Creditor-specific messages:**

- **Greezy:** "One phone call. Greezy's boys turn up. The problem disappears. £X added to what you owe Greezy."
- **Ren:** "A message to Belmarsh. Twenty minutes later, Ren's soldiers arrive. £X added to your debt with Ren."
- **Auntie:** "Auntie makes one call. Her nephews show up. Nobody argues with Auntie's family. £X added to what you owe Auntie."
- **Val:** "Val sends word through her eldest. The situation is handled. Permanently. £X added to what you owe Val."

### Creditor Warning Messages (3 per creditor per stage)

These need to be written in each creditor's voice. Examples:

**Greezy warning:**
- "Greezy's been asking where you're at. Says you owe him. This is him being polite."
- "Someone told you Greezy was in your ends yesterday. He wasn't buying groceries."
- "Your phone buzzes. Unknown number. 'Yo, Greezy wants his P's. Don't make him come find you.'"

**Ren warning:**
- "Burner text: 'Ren says pay up. Don't make us come looking.' The number's already disconnected."
- "One of Ren's boys was outside your spot this morning. Didn't say anything. Didn't need to."
- "Message from inside: 'Tell man I ain't forgotten. The interest is running.'"

**Auntie warning:**
- "You bumped into one of Auntie's nieces at the shop. She smiled and said 'Auntie says hello.' That wasn't a greeting."
- "Word came through the grapevine — Auntie's been mentioning your name at church. That's never good."
- "Your mum got a call from Auntie asking how you're doing. She wasn't asking about your health."

**Val warning:**
- "Val's eldest found you at the pub. 'Mum wants her money. This is me asking nicely. There won't be a second time.'"
- "You got a voicemail. Val's voice, calm as anything: 'You know what you owe, love. Sort it out.'"
- "Someone left an envelope at your door. Inside: a note in Val's handwriting. Just the amount. No threats needed."

*First visit and heavy messages to be written in the same style — escalating in severity, staying in character.*

---

## 7. PROTECTION SYSTEM

### Keep from Original Street Wars

The Shifty/Jamal mutual exclusivity system ports across directly.

#### 🐷 PC Shifty — Anti-Police

- Tier 1: max(£5k, 5% NW) → 2 uses
- Tier 2: max(£15k, 10% NW) → 2 uses
- Tier 3: max(£30k, 15% NW) → 2 uses then gone
- 3-4 travel cooldown after each use
- Permanent +8% mugging risk
- Locks out Jamal permanently: "Jamal don't roll with snitches"

#### 💪 Jamal — Anti-Mugging

- Cost: max(£5k, 15% cash)
- Blocks 2 muggings then ghost 5-8 travels
- Re-hireable
- +8% police risk while active
- Block count hidden (intentional uncertainty)
- Locks out Shifty permanently: "Shifty ain't working with Jamal"

### Shop UI

Moncler, Canada Goose, Shifty, and Jamal all available from the Shop popup. Same as original Street Wars. In Grey Market this was the Transport popup — rename to "Shop".

---

## 8. HEAT SYSTEM

### Replaces Grey Market's Interpol Alert System

Same bar, same mechanics, different name and flavour.

### Visual Design

Bar in the sticky header. Labelled "HEAT" not "INTERPOL".

| Range | Colour | Status Text (desktop only) |
|-------|--------|---|
| 0-15% | Green | "Ghost. Feds don't know you exist." |
| 15-35% | Green/Amber | "They've heard your name." |
| 35-50% | Amber | "Feds are asking questions." |
| 50-65% | Amber/Orange | "CID have opened a file on you." |
| 65-85% | Orange/Red | "Armed response on standby." |
| 85-99% | Red | "One more move and it's over." |
| 100% | — | NICKED. Game over. No escape. |

### What Raises Heat

| Source | Increase |
|---|---|
| Buy/sell transaction | +1-2% |
| Large trade (10+ units) | +3-5% |
| Selling to undercover (clean sale) | +2% (hidden — building a case) |
| Undercover bust | +15-20% |
| Snitch event | +5-15% |
| Police raid | +10-15% |
| Refusing police (run/caught) | +5-10% |
| Staying in same borough 3+ turns | +3% per turn |
| Buying Moncler/Goose | +3% (looking flash draws attention) |

### What Lowers Heat

| Source | Decrease |
|---|---|
| Natural decay per turn | -2% |
| Travelling to new borough | -3-5% |
| New SIM service | -20% (one use only) |
| Disappear | -12% (3 uses per game, 3-day cooldown) |
| Mentioning Shifty (after police) | -5% (feds back off) |

### 100% Heat = Nicked

Instant game over. No popup, no bribe, no escape. Straight to NICKED screen.

---

## 9. HEALTH SYSTEM

### Port from Grey Market

Same system, different flavour.

- Range: 0-100%, starts at 100%
- Bar only displayed when health < 100%
- Same colour coding: green (71-100%), amber (41-70%), red (0-40%)

### Health 0% = Hospitalised

Phase transitions to `"hospitalised"`. Random message:

- "[Name] was admitted to A&E in {location}. Broken ribs, fractured jaw. The streets won."
- "[Name] collapsed outside a chicken shop in {location}. Someone eventually called an ambulance."
- "[Name] limped into {location} hospital at 3am. Couldn't even give a fake name properly."
- "[Name] woke up in a hospital bed in {location} with no phone, no cash, and a drip in their arm."

### Sources of Health Damage

| Source | Damage |
|---|---|
| Mugging refusal | -8 to -15% |
| Rival dealer refusal | -15 to -30% |
| Police run (failed) | -10% |
| Police surrender | -5% |
| Undercover bust | -5% |
| Raid (rivals) | -10% |
| Greezy first visit | -10% |
| Greezy heavy/repeat | -25% / -20% |
| Ren first visit (no cargo) | -15% |
| Ren heavy (no cargo) | -25% |
| Ren heavy (with cargo) | -10% |
| Auntie first visit | -15% |
| Auntie heavy/repeat | -40% / -30% |
| Val heavy | To 5-15% health (devastating) |
| Val repeat | -40% |

### Recovery — The Nurse (Ellie)

| Service | Cost | Effect | Cooldown | Appears |
|---|---|---|---|---|
| The Nurse | 15% of cash, min £500 | Health +30% | 3 days | When health < 100% |

Button label: "The Nurse"
Story messages use her name "Ellie":
- "Ellie meets you round the back of the ward. Twenty minutes, no paperwork. You'll live."
- "Ellie says this is the third time this month. The price just went up."
- "Ellie patches you up between shifts. Says if you come back looking like this again she's done."

---

## 10. EVENT SYSTEM

### Single Roll Pool

Replace Grey Market's event system with Street Wars events. Same architecture — one roll per turn after travel, normalised probability table.

```javascript
function rollEvent(gs) {
  const heatScale = gs.heat / 100;  // renamed from alertLevel
  const cargoValue = gs.inventory.reduce((sum, qty, i) => sum + qty * gs.prices[i], 0);
  const totalInventory = gs.inventory.reduce((a, b) => a + b, 0);
  const boroughRisk = BOROUGHS[gs.borough].riskBias;

  const nothing = 0.15;
  const police = gs.heat > 10 ? 0.05 + heatScale * 0.25 : 0;
  const mugging = 0.06 + boroughRisk * 0.2;
  const rivals = totalInventory > 0 ? 0.06 + (gs.turnsInBorough > 3 ? 0.06 : 0) : 0;
  const undercover = totalInventory > 0 ? 0.05 : 0;
  const snitch = 0.05;
  const raid = gs.turnsInBorough >= 5 ? 0.08 : 0;  // only triggers at 5+ turns
  const surgeTip = 0.10;
  const crashTip = 0.08;
  const headsUp = 0.04;
  const opportunity = 0.08;
  const insider = 0.07;
  const streetDeal = 0.05;
  const luckyDay = 0.04;
  const flavour = 0.04;
  const badDay = 0.03;

  // Normalise and roll...
}
```

### Event Details

#### Nothing (15%)
Quiet turn. No popup, no event message. Or a brief atmospheric message that fades.

#### Police (5% + scaling)
Port from original Street Wars with health additions.

**If clean (no drugs):** Auto-resolve. "Feds stopped you. Nothing on you. They let you go."

**If carrying, options:**

| Option | Requirement | Effect |
|---|---|---|
| Mention Shifty | Shifty available | Walk free, use -1, cooldown starts, heat -5% |
| Run | Always | 35% escape. Fail: lose 70-80% biggest item + 20% cash + lose a day + health -10% + heat +5-10% |
| Dump | Always | Lose ALL inventory. Keep cash, day, health |
| Surrender | Always | Lose 60-80% biggest item + lose a day + health -5% |

#### Mugging (6% + scaling)
Simplified from original. Binary choice.

| Option | Effect |
|---|---|
| Pay up | Lose 15-30% cash. No goods, no health. |
| Refuse | Health -8 to -15%. Keep cash and goods. Heat +3-5%. |
| Jamal blocks | Nothing happens. Uses a block. |

Mugging chance modifiers: Shifty bought +8%, Goose +8%, Moncler +5%.

#### Rival Dealers (6% + scaling)
Only fires if carrying cargo. Turf war.

| Option | Effect |
|---|---|
| Pay | Lose 30% of all cargo |
| Call [Creditor] | Fee × 1.15 added to debt |
| Refuse | Health -15 to -30%. Keep goods and cash. Heat +8-12%. |

#### Undercover (5%)
Only fires if carrying inventory. A vehicle pulls up wanting to buy.

**Setup:**
- Random vehicle from pool: Audi A3, Silver BMW 1 Series, Black Golf GTI, Grey Ford Focus ST, White Vauxhall Astra, Dark blue Skoda Octavia
- Random item the player is holding
- Quantity: 2-5 units
- Price: top dollar — `basePrice × (2.5 + Math.random() * 1.5)` (2.5x to 4x)

**Options:**
- **Sell** — take the risk
- **Walk away** — nothing happens

**If sell:**
- Bust chance scales with heat:

| Heat | Bust chance |
|---|---|
| 0-15% | 25% |
| 15-35% | 35% |
| 35-50% | 45% |
| 50-65% | 55% |
| 65%+ | 65% |

- **Clean sale:** Cash gained. Heat +2% (hidden case building). Good messages with occasional doubt: "Quick sale. No drama. Although... that car's been round the block twice now."
- **Bust:** All drugs seized. Health -5%. Heat +15-20%. Lose a day. "Blue lights. That was unmarked feds. You spend the night in a cell in {location}."

**Messages (random vehicle, random item):**
- "Someone in a {vehicle} pulls up. Asks if you're holding any {item}. Wants {qty}. Offering £{price}."
- "{vehicle} slows down. Window drops. 'You got any {item}? I'll take {qty} off you right now. £{price}.'"
- "Bloke in a {vehicle} pulls over. Looks around. 'Heard you might have {item}. Need {qty}. I'll pay £{price}.'"

#### Snitch (5%)
Unavoidable. No options. Heat +5-15%.

Messages:
- "Someone's been talking. Your name came up. Heat's rising."
- "Word got back to you — someone grassed to the feds. Watch your back."
- "You've been mentioned. Don't know who, don't know when. But the feds heard something."
- "Loose lips. Someone on the ends has been chatting. Your heat just went up."

#### Raid (8% when 5+ turns in same borough)
Triggered by staying too long. Type depends on protection:

- **Shifty active:** Rival gang raid (Shifty can't stop rivals)
- **Jamal active:** Police raid (Jamal can't stop a warrant)
- **Neither:** 50/50 coin flip

**Effect:** Lose ALL inventory. No options, no negotiation.

**Additional effects:**
- Police raid: Heat +10-15%
- Rival raid: Health -10%

**Rival raid messages:**
- "Word got back to the wrong people. You've been on their patch too long. They hit your spot in {location}. Took everything."
- "Local crew kicked your door in. You've been too visible in {borough} for too long. Stock's gone. All of it."
- "You got comfortable. They noticed. Rival dealers cleared out your stash in {location}."

**Police raid messages:**
- "Feds got a warrant. Too long in {borough}, too many eyes. Armed police hit your spot in {location}. Everything seized."
- "CID have been watching you for days. You stayed in {borough} too long. They took the lot."
- "Blue lights at 5am. Your stash in {location} is evidence now."

#### Word on the Street: Surge (10%)
Price spike at a named location. Sets `pendingNews` with `type: "spike"`.

Messages use street language:
- "Bare man in {location} paying mad money for {item} right now. Get there quick."
- "Your boy says {item} is going for stupid prices in {location}. Might be worth the trip."
- "{item} drought in {location}. Anyone holding is naming their price."

#### Word on the Street: Crash (8%)
Price collapse at a named location. Sets `pendingNews` with `type: "crash"`.

- "Market's dead for {item} in {location}. Everyone's sitting on it."
- "{item} everywhere in {location}. Price has bottomed out."
- "Too much {item} floating around {location}. Can't give it away."

#### Heads Up / Threat Warning (4%)
Informational only. No mechanical effect.

- "Feds are all over {borough} this week. Bare stop and searches."
- "Someone got nicked in {location} yesterday. Keep your head down round there."
- "CID set up in {borough}. Undercovers everywhere apparently."

#### Tip / Opportunity (8%)
Sets `pendingNews` with `type: "spike"` for a named location.

- "Your man in {location} says {item} is selling for top dollar. Might wanna swing by."
- "Heard {item} is moving fast in {location}. Premium prices if you've got any."

#### Your Boy Called / Insider Tip (7%)
Sets `activeTip` for a specific location.

- "Your boy in {location} hit you up. Says {item} is {expensive/cheap} right now."
- "Got a text from your connect in {location}. {item} going for {expensive/cheap} prices."

#### Street Deal (5%)
Cheap bulk buying opportunity. Same mechanics as Grey Market's black market deal.

- Quantity: 2-9 units at steep discount
- "Man on the corner's got {qty} {item} going cheap. £{price} for the lot. No questions."
- "Someone's offloading {qty} {item} quick. £{price} takes everything."

#### Lucky Day (4%)
Free cash: £500 - £5,000.

- "Found a wallet in {location}. £{amount} cash. No ID. Your lucky day."
- "Old mate owed you one. Brown envelope, £{amount}. Didn't even ask what it's for."
- "Scratched a lottery ticket at the newsagent. £{amount}. Small wins."

#### Flavour / Atmospheric (4%)
No mechanical effect. Fades after 5 seconds.

Port the best atmospheric messages from original `street_wars_scenarios.md` and write new ones for the new boroughs (Lambeth, Brent).

#### Bad Day (3%)
Small cash loss: £100 - £1,000.

- "Phone got nicked on the bus. That's £{amount} gone on a new one."
- "Dropped a bag in {location}. £{amount} in the wind."
- "Got fined £{amount} on the train. No oyster. Peak."

---

## 11. SERVICES

### Two Services Only

| Service | Cost | Effect | Uses | Appears |
|---|---|---|---|---|
| New SIM | £3,000 | Heat -20% | Once per game | From turn 1 via progressive disclosure (when heat > 15%) |
| The Nurse | 15% of cash, min £500 | Health +30% | Unlimited, 3-day cooldown | When health < 100% |

### New SIM

When purchased, show a brief message: "New number. New you. For now."

The button disappears permanently after use. Cannot be re-bought.

### No Other Services

Remove Grey Market's Clean Passport alias change mechanic, Port Official, and Counter-Surveillance. Street Wars has New SIM (one use) and Disappear (3 uses) as the only heat reduction tools.

---

## 12. DISAPPEAR

### Keep from Grey Market Overhaul

Renamed from "Go Dark." 3 uses per game, 3-day cooldown.

- Alert (heat) drops 12%
- Day advances, debt compounds, prices regenerate
- No events fire (except creditor check)

### London Flavour Text

Replace Grey Market's region-specific messages with borough-specific London messages (3 per borough, 18 total):

**Lewisham:**
- "You spend the day in a mate's flat in {location}. Curtains drawn, phone off. Nobody knows you're here."
- "Back room of a barbershop in {location}. Your man says you can stay till closing. No questions."
- "Nan's house in {location}. She doesn't ask why you look like that. Makes you a cup of tea."

**Southwark:**
- "You duck into a chicken shop in {location} and sit in the back for hours. Nobody looks twice."
- "Your cousin's place in {location}. He's at work. You've got the keys. You wait."
- "A launderette in {location}. Warm, loud, anonymous. You sit and watch the machines spin."

**Lambeth:**
- "You disappear into the estate in {location}. Everyone minds their business round here."
- "A church hall in {location}. The pastor doesn't ask questions. You sit in the back pew."
- "Your girl's mum's place in {location}. She thinks you're here for dinner. You let her think that."

**Camden:**
- "You blend into the market crowd in {location}. Just another face. Phone off, hood up."
- "A pub basement in {location}. The landlord owes you one. You sit in the dark and wait."
- "Hostel in {location}. Cash, no name. The receptionist doesn't even look up."

**Hackney:**
- "You lie low in a studio flat in {location}. Mattress on the floor, no WiFi. Perfect."
- "Community centre in {location}. You pretend you're there for the yoga class. Nobody checks."
- "Your boy's warehouse in {location}. Cold, empty, safe. You wait it out."

**Brent:**
- "A flat above a bookies in {location}. The noise from downstairs covers everything."
- "You stay with your uncle in {location}. He watches TV. You watch the window. Neither of you talks."
- "Back of a minicab office in {location}. The controller says you were never here."

---

## 13. POLICE ENCOUNTERS

### Scaled with Heat (same tiers as Grey Market's Interpol)

Replace Interpol tier names with London police:

| Heat Range | Encounter | Description |
|---|---|---|
| Below 35% | Stop and search | Routine. If clean, walk. If carrying, options. |
| 35-65% | CID investigation | Serious. Bribe or lose goods. |
| 65-85% | Armed response | Very serious. Big bribe or big loss. |
| 85-99% | Full operation | Devastating. Pay or get nicked. |
| 100% | Nicked | Instant. Game over. |

### Bribe = Backhander

Replace "bribe" language with "backhander" throughout. "Slip him a few quid" not "pay a bribe."

### Police Messages

Write in London voice. Examples:

**Stop and search (low heat):**
- "Two feds stop you on the high street in {location}. 'Just a routine check, mate.' They pat you down."
- "Unmarked car pulls up in {location}. 'Where you going? What's in the bag?' Casual but they're watching."

**CID (medium heat):**
- "CID pull you into {location} station. They've been building a file. This isn't a chat anymore."
- "Plain clothes grab you in {location}. They know your name. They want to see what you're carrying."

**Armed response (high heat):**
- "Armed officers. {location}. Hands on the car. They're not messing about."
- "Three armed units. They've been waiting for you in {location}. This is serious."

---

## 14. END SCREENS

### Three Fail States

#### NICKED (heat 100%)
- Header: "NICKED" in red
- Subheader: "It's over."
- Messages:
  - "[Name] was arrested in {location}. The feds had been watching for weeks."
  - "[Name] got pulled in {location}. They found everything. Remanded to Belmarsh."
  - "[Name] got nicked outside a chicken shop in {location}. Three squad cars. The whole ends saw it."
  - "[Name] thought they were smart. Met Police thought otherwise. Custody in {location}."

#### HOSPITALISED (health 0%)
- Header: "HOSPITALISED" in red
- Subheader: "It's over."
- Messages as defined in Section 9.

#### BROKE
- No inventory, no cash, can't do anything.
- "You're done. Nothing to sell, nothing to buy. The streets chewed you up."

### Success Screens

#### FINAL BALANCE (Day 30)
Keep Grey Market's structure but with London narrative tiers.

**£100,000+:**
- "You made more P's in 30 days than most people see in a lifetime. New phone, new ends, new everything."
- "£{networth}. Enough to disappear. New name, new postcode. Nobody on the estate will see you again."

**£50,000-£99,999:**
- "Solid run. You came out ahead and you're still walking. Not everyone can say that."
- "£{networth}. Enough to sort yourself out. Maybe even go legit. Maybe."

**£20,000-£49,999:**
- "You survived. Made a bit of bread. Nothing to write home about but you're free."
- "30 days of stress for £{networth}. Was it worth it? Only you know."

**£0-£19,999:**
- "You barely made it out. Pocket change and bad memories."
- "30 days on the roads for almost nothing. At least you're not in hospital. Or jail."

**Below £0:**
- "You owe more than you made. The streets won."
- "Negative. You managed to lose money selling drugs. That takes a special kind of talent."

#### WALKED AWAY (early cash out)
Same structure, London flavour:
- "You knew when to stop. Most don't. That's why most end up nicked or hospitalised."
- "You walked away with £{networth}. The smart ones always do."

---

## 15. ACHIEVEMENTS

### Keep All 12 from Original Street Wars

| Emoji | Key | Trigger |
|---|---|---|
| £ | nw50k | Net worth ≥ £50,000 |
| 💷 | nw100k | Net worth ≥ £100,000 |
| 💰 | nw500k | Net worth ≥ £500,000 |
| 🤑 | nw1m | Net worth ≥ £1,000,000 |
| 🆓 | debtfree | Debt fully repaid |
| 🏦 | bank50k | Cash ≥ £50,000 |
| 🧥 | moncler | Bought Moncler |
| 🧥 | goose | Bought Canada Goose |
| 🏃 | escaped | Escaped from police |
| 🐷 | shifty | Hired PC Shifty |
| 🎯 | tip | Profited from a tip |
| 🔥 | megaspike | Sold during a mega price spike |

Storage key: `sw_achievements` (same as current — backwards compatible).

Achievement bar displays above the event bar. Milestone toasts with sound.

---

## 16. UI / LAYOUT

### Port Grey Market Layout As-Is

The entire Grey Market layout carries across:

- Sticky single-line header: Day, Cash, Debt, Mute, Heat bar, Health bar (when < 100%)
- Location panel with borough grid and "Travel to another borough" button
- Event bar
- Side-by-side BUY/SELL panels with column headers (ITEM/PRICE and ITEM/QTY/AVG)
- Bottom-pinned button bar with progressive disclosure

### Progressive Disclosure

| Element | Appears when |
|---|---|
| Creditor debt button | Always from turn 1 |
| Disappear | Always from turn 1 |
| Shop | Turn 3+ |
| Price History | After first travel |
| Services (New SIM) | Heat > 15% |
| The Nurse | Health < 100% |
| Cash Out | Debt cleared AND day < 30, OR day >= 30 |

### Achievement Bar

Add the achievement bar from original Street Wars. Position: between the event bar and the market panels, or above the event bar. Shows unlocked achievement emojis in a horizontal row.

### Button Labels

| Button | Label |
|---|---|
| Creditor | [Creditor name] in red |
| Shop | Shop |
| Services | Services |
| Prices | Prices |
| Disappear | Disappear (X) |
| Cash Out | Cash Out |

### Colour Scheme

All accent/CTA elements use orange (#F97316) instead of Grey Market's purple.

---

## 17. AUDIO

### Keep or Replace

Grey Market uses base64-encoded real audio files. Street Wars uses Tone.js synthesized sounds.

**Decision needed:** Use real audio files (like Grey Market) or keep Tone.js? Real audio is higher quality but increases file size. Tone.js is lighter.

**Recommendation:** Keep Tone.js for Street Wars — it's already working and the synth sounds fit the vibe. Port the Tone.js sound setup from the current Street Wars index.html.

| Sound | Trigger |
|---|---|
| Buy/Sell | Transaction |
| Travel | Moving locations |
| Police | Police encounter |
| Trouble | Mugging, rivals, raid |
| Lucky | Windfall, good events |
| Broke | Can't afford something |
| Milestone | Achievement unlock |

---

## 18. LEADERBOARD

### Supabase — Existing Setup

The existing Street Wars Supabase leaderboard can be reused:
- URL: nizekjtkeyzslnxhalji.supabase.co
- Table: `leaderboard` (or create `sw2_leaderboard` if you want separation)
- Same upsert logic — best score per player
- Same RLS policies
- Score capped ±£10M

**Decision needed:** Reuse existing `leaderboard` table (scores mix with v1) or create a new table?

---

## 19. STORAGE KEYS

| Key | Storage | Content |
|---|---|---|
| `sw_career_stats` | Local (store wrapper) | Career stats JSON |
| `sw_achievements` | Local (store wrapper) | Achievement unlock flags |
| `leaderboard` or `sw2_leaderboard` | Supabase | Global top 10 |

Same keys as current Street Wars where possible for backwards compatibility.

---

## 20. REMOVED FROM GREY MARKET

The following Grey Market systems are NOT carried across:

- Embargo system (no UN inspectors, no restricted items, no embargo wire messages)
- CIA/MI6 encounters
- Security crew system (replaced by Shifty/Jamal)
- Transport upgrade tiers (replaced by Moncler/Canada Goose)
- Clean Passport alias change (replaced by New SIM with no name change)
- Port Official and Counter-Surveillance services
- All arms dealing terminology, item names, region names
- Inter/DM Sans font pairing (use JetBrains Mono/DM Sans)
- Purple accent colour (use orange)

---

## 21. BUILD SEQUENCE

Recommended order for Claude Code prompts:

1. **Rebrand & theme** — Replace all Grey Market text, colours, fonts, meta tags. Dollar to pound. Purple to orange. Inter to DM Sans.
2. **Map swap** — Replace regions/countries with boroughs/locations. Update all travel references.
3. **Item swap** — Replace arms with drugs. Update prices, volatility, heat impact formulas. Remove embargo system.
4. **Creditor swap** — Replace 4 creditors with Greezy, Ren, Auntie, Val. New portraits needed. New escalation mechanics for Ren (goods-focused).
5. **Protection system** — Add Shifty and Jamal to Shop. Port mechanics from original Street Wars.
6. **Heat system** — Rename alert to heat. New status messages. Add trade-based heat generation. Adjust scaling.
7. **Event system** — Replace event pool with Street Wars events. Add undercover, snitch, raid. Remove CIA/MI6, UN.
8. **Police encounters** — Rewrite to London voice. Add run/dump/surrender/Shifty options.
9. **Services** — Strip to New SIM + The Nurse. Remove others.
10. **End screens** — Nicked, Hospitalised, final balance tiers. London narratives.
11. **Achievements** — Port achievement bar and 12 achievements from original Street Wars.
12. **Audio** — Swap to Tone.js from original Street Wars or keep real audio (decision needed).
13. **Flavour pass** — Write all missing message variants. Port best messages from original Street Wars.
14. **Testing & balance** — Playtest, tune probabilities, adjust economy.

---

*Street Wars London rebuild document — 8 April 2026. Based on Grey Market codebase with Street Wars content and new systems.*
