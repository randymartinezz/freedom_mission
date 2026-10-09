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

## Accent colour

One token, `--accent: #d92d20`, plus `--accent-border` and `--accent-soft`
derived from it, and `--recovery: #2e7d5b` for rest days. Everything that highlights — completed check-ins, completed
days on the map, progress fills, active pills, the DONE button — reads from
these. Change the three values at the top of the stylesheet and the whole app
follows; nothing is hardcoded.

Today's ring on the mission map stays off-white on purpose, so it never
collapses into the red ring that marks a month turning over.

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

Each card carries its own progress bar. `POSITION` sets the target and the
rule for each:

| card | rule | reads |
| --- | --- | --- |
| Net Worth | `up` | 15,000 / 100,000 = 15% |
| Monthly Income | `up` | 4,000 / 10,000 = 40% |
| Liquid Net Worth | `floor0` | 0% while negative; normal toward 25,000 after |
| Debt | `down` | cleared from `debtBaseline` toward 0 |

`floor0` exists because liquid net worth starts below zero: reaching $0 is the
first checkpoint, and nothing under it shows as negative progress.

`debtBaseline` is where the debt started. The bar measures how much of *that*
has been cleared — 6,000 → 0%, 4,500 → 25%, 3,000 → 50%, 0 → 100% — so leave
it alone as you pay down, and move it only if you take on new debt.

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

Books carries an optional `reading` block — the one book open right now,
shown under the counts. Set `reading: null` to hide it:

```js
reading: { title: 'Think and Grow Rich', status: 'In progress' }
```

Body counts down. It needs `from` — the weight you started at — or there is no
honest percentage, and the bar stays hidden rather than showing a misleading
zero:

```js
{ id: 'body', from: 85, current: 78, target: 70 }   // bar at 47%
```

### Daily standard (on Home)

The nightly check-in on Home, under Current mission. Five items, tapped once each:
MARTIN CAPITAL (future wealth — any of the three businesses), LEGENDARY
(current income), TRAIN (physical capacity), READ (knowledge), MINDSET
(mental clarity). Under twenty seconds, by design.

MARTIN CAPITAL, READ and MINDSET toggle between empty and done. LEGENDARY and TRAIN
add a third state, N/A, for days you were never scheduled — **N/A leaves the
denominator, so it reads `2 / 4`, not a miss.**

What each item means is written as a comment above `STANDARD` in the source,
deliberately not on screen, so the card stays compact.

Press **DONE** to file the day. The circles stop responding and the button
reads DAY COMPLETED, or DAY SAVED · INCOMPLETE if something eligible was left
undone — an unfinished day is still recorded, it just does not count. Pressing
the button again reopens today, so one stray tap never costs a day.

### Year-end mission (on Home)

A fixed calendar window: **9 October to 31 December 2026, 84 days**. It never
restarts. A missed day costs the streak and nothing else — the dates keep
advancing to the 31st either way.

Three month blocks, laid out as calendars (Monday first), one circle per date:

| circle | meaning |
| --- | --- |
| solid red | executed — every eligible item done |
| solid green | recovery Sunday |
| outlined | missed — filed incomplete, or never filed at all |
| dim | still in the future |
| thin off-white ring | today, on top of whichever state applies |
| thin red ring | the 1st of November and December |

**Sundays are recovery days, derived from the date alone.** There are 12 of
them in the window, so the real denominator is 72, not 84. A recovery Sunday:

- needs no check-in and is never marked missed
- is excluded from COMPLETED's denominator
- does not add to the streak, and does not break it either

So Friday executed → Sunday recovery → Monday executed reads as a streak of 3.
Nothing about recovery is stored; it is recomputed from the calendar, so it
stays correct after any refresh.

Below: COMPLETED x / 84, MISSED, CURRENT STREAK. The circles are a record, not
controls — they are not tappable.

### Where the data lives

One key, `freedomMissionData`, holding one JSON object:

```js
{
  version: 1,
  challenge: { start: "2026-10-09", end: "2026-12-31" },
  days: {
    "2026-10-09": { items: { build: 1, work: 2, read: 1 }, saved: true }
    //              0 = not done, 1 = done, 2 = n/a
  }
}
```

It is written the instant anything changes — marking an item, pressing DONE —
and it holds to four rules:

1. **Saved data is never overwritten by defaults.** On load the saved object is
   merged *onto* the defaults, so a field added in a later version appears
   without disturbing anything already recorded.
2. **Nothing derived is stored.** Completed, missed and streak are recomputed
   from `days` on every render. A stored counter can fall out of step with the
   days it counts, and then it lies.
3. **Nothing is cleared automatically.** A new calendar day adds a blank entry
   and leaves every earlier one alone.
4. **Reads and writes are wrapped.** If a browser blocks storage the app still
   runs; it just forgets when you close it.

Data written under the earlier `fm.standard` / `fm.challenge` keys is migrated
on first load. Those keys are left in place rather than deleted.

The keys inside `items` (`build`, `work`, `reflect`) are storage keys, not
labels — renaming an item on screen never touches them, so BUILD becoming
DEEP WORK and then MARTIN CAPITAL left every filed day intact.

### What is *not* stored

Net worth, goal targets, business revenue, the twelve-month goals — everything
edited in the source rather than in the app — is deliberately left out.

If those were cached in `localStorage`, the cache would outrank the file, and
editing `BALANCE` would appear to do nothing. The file stays the source of
truth for them. When any of it becomes editable in the UI, it moves into the
object above.

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
HOME       Where I am -> what I do today -> the record -> what's next

           Current position  four cards, each with its own progress bar
                             Business equity ~$10,000 (estimated, excluded)
           Daily standard    Martin Capital · Legendary · Train · Read ·
                             Mindset, then DONE files the day
           Year-end mission  Oct 09 -> Dec 31, 84 days, one continuous map
                             completed · missed · streak
           Next unlock       first goal still locked in WEALTH

WEALTH     Net worth roadmap: $100K -> $250K -> $500K -> $1M
           Dad's First Car   $6,000   LOCKED  (Family mission)
           Rolex Datejust   $15,000   LOCKED
           Corvette C8     $100,000   LOCKED
           Each with its unlock requirements

BUSINESS   MARTIN GROW    First 10 Clients
           MY MISSION     First 10 Paid Subscribers
           VERANO DESERT  4 Profitable Events / 12 Months
           One question each: is it getting customers?

LIFE       12 month vision — four measurable outcomes:
           Monthly Income $10,000/mo · Books 24 · Body 70 KG
           Family Experience (status)
           Protect  the five monthly commitments, $9,300/mo total
```
