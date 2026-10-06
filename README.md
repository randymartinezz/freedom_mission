# FREEDOM MISSION

Build the life. A private, mobile-first personal command center.

**Everything is in `index.html`.** One file — markup, styles, logic and every
photo and logo, embedded. It depends on nothing: no server, no npm, no build
step, no `images/` folder, no internet.

## Run

Double-click `index.html`. **PIN: `7777`**

The PIN is a local privacy screen, not authentication. There is no server and
no account; the data lives in the file.

### On your phone

AirDrop `index.html` to the iPhone and open it in Safari, or drop it in iCloud
Drive / Dropbox. Nothing else needs to travel with it.

## Editing

Open `index.html` in any text editor. Skip the first `<script>` — that is the
embedded image data, ten very long lines, nothing to read there. The second
`<script>` is the app:

```
1. CONFIG    PIN
2. DATA      everything the app shows   <- edit this, nothing else
3. HELPERS
4. PIECES    reusable HTML builders
5. SCREENS   Home / Wealth / Business / Life
6. LOCK      PIN screen
7. APP       state, render, events
```

### Money

`BALANCE` lists what you own and owe, line by line. **You never type a total** —
net worth, liquid net worth, debt and the estimated total are all derived, so
the parts can never disagree with the sum.

```js
var BALANCE = {
  assets: [
    { label: 'Camaro', value: 18000 },
    { label: 'Cash savings', value: 3000, liquid: true }
  ],
  debts: [
    { label: 'Credit card', value: 6000, shortTerm: true }
  ],
  estimatedEquity: [
    { label: 'Verano Desert', value: 10000 }
  ],
  monthlyIncome: 4000
};
```

- `liquid: true` — cash you could spend this week. Feeds Liquid Net Worth.
- `shortTerm: true` — debt due inside a year. Subtracted from Liquid Net Worth.

```
NET WORTH        assets - debts            18,000 + 3,000 - 6,000 = 15,000
LIQUID NET WORTH liquid assets - short debt  3,000 - 6,000        = -3,000
MONTHLY INCOME   monthlyIncome                                      4,000
DEBT             sum of debts                                       6,000
```

`estimatedEquity` is deliberately **excluded** from net worth — a business
valuation is a guess, confirmed wealth is not. It sits below the grid at lower
weight, with the combined ESTIMATED TOTAL NET WORTH.

`monthlyIncome` feeds both the Home tile and the Life income goal, so they can
never disagree. Set it to `null` and both show a dash instead of a made-up
number.

### Business

`BUSINESSES` holds one number that moves per venture: `value`. The big count,
the fill and which milestones turn gold all follow from it.

A milestone with `at` completes itself; one with `done` is a gate you flip by
hand, for things no counter can know:

```js
{ name: 'V1 Live', done: false }   // flip to true the day it ships
{ name: '5 Clients', at: 5 }       // completes itself
```

Optional per-business fields: `countLabel`, `nextLabel` (defaults to "Next
action"), `showCurrentMilestone: false`, `logoHeight`, and `money` — a list, so
one venture shows a single monthly figure and another shows two totals.

### Life

`VISION` holds the twelve-month outcomes. Set `current` and the card derives
the status pill and the bar. `0` or empty reads NOT STARTED, anything between
reads IN PROGRESS, hitting the target reads COMPLETED. Family Experience has no
number, so there you set `status` by hand.

Body counts down. It needs `from` — the weight you started at — or there is no
honest percentage, and the bar stays hidden rather than showing a misleading
zero:

```js
{ id: 'body', from: 85, current: 78, target: 70 }   // bar at 47%
```

### Photos and logos

All embedded in `IMG` at the top of the file as `data:` URIs. To swap one,
replace its URI — or point the field at a normal path like `'images/foo.jpg'`
and keep that file beside `index.html`.

The `images/` folder next to this README is **not used by the app**. It holds
the source files the embedded versions were made from, plus `images/originals/`
with everything exactly as supplied. Keep it as your archive, or delete it —
`index.html` does not care.

## Screens

```
HOME       Brand mark, centred
           Net worth $15,000 · Liquid -$3,000 · Income $4,000 · Debt $6,000
           Business equity ~$10,000 (estimated, kept out of net worth)
           Current mission -> $100,000 net worth, 15%
           Liquidity track -> $0 / $10K / $25K
           Next unlock -> Rolex Datejust

WEALTH     Net worth roadmap: $100K -> $250K -> $500K -> $1M
           Rolex Datejust  $15,000   LOCKED + requirements
           Corvette C8    $100,000   LOCKED + requirements

BUSINESS   MARTIN GROW    First 10 Clients
           MY MISSION     First 10 Paid Subscribers
           VERANO DESERT  4 Profitable Events / 12 Months
           One question each: is it getting customers?

LIFE       12 month vision — four measurable outcomes:
           Monthly Income $10,000/mo · Books 10 · Body 70 KG
           Family Experience (status)
           Protect  the five monthly commitments, $9,300/mo total
```
