# Photos & logos

Drop your own files in THIS folder and the app picks them up with no code
change. This folder must sit next to index.html.

## Photos

Wealth:

- `rolex.jpg`     — Rolex Datejust (Home "Next unlock" + Wealth card)
- `corvette.jpg`  — Corvette C8

Life (12 month vision):

- `income.jpeg`   — Monthly Income
- `books.jpeg`    — Books
- `gym.jpeg`      — Body
- `trip.jpeg`     — Family Experience

Life photos get the same desaturated, dimmed treatment as the locked Wealth
goals, so bright subjects never fight the overlay text.

If one is missing, a neutral stock photo is used instead.

The cards are landscape and use `object-fit: cover`, so a tall portrait image
gets cropped hard through its middle. A wide crop around 3:2 is safest; a
square works if the subject sits in the middle.

Locked goals render desaturated and dimmed on purpose — that is the "not yet
earned" state, not a problem with your photo.

The extension is not what decides the format: browsers sniff the file itself.
`rolex.jpg` is currently a WebP and loads fine.

## Logos

- `freedommission.png` — THE FREEDOM MISSION brand mark (lock screen + Home)
- `martingrow.png`    — MARTIN GROW wordmark (Business card)
- `mymission.png`     — MY MISSION wordmark (Business card)
- `veranodesert.png`  — VERANO DESERT lockup (Business card)

Transparent PNGs, white ink. A one-line wordmark renders at 20px tall with the
width automatic, so any ratio works. A taller lockup sets its own size with
`logoHeight` in the business entry — VERANO DESERT uses 64. If a file is
missing the business name shows as type instead, so the card never breaks.

All were normalised to pure white, alpha channel untouched so the antialiasing
survives, and trimmed to their content so the declared size is the size you see:

- MY MISSION arrived at RGB 198 — washed out beside MARTIN GROW at 255.
- VERANO DESERT arrived at RGB 56,51,43, a near-black brown that was all but
  invisible on this background.
- FREEDOM MISSION arrived at 2048x768 and 952 KB; trimmed and scaled to 700px
  wide (3x its largest on-screen size) it is 21 KB.

## If a change does not show up

Browsers cache images hard. After replacing a file, reload with cache bypass
(Cmd+Shift+R) or the old version keeps rendering — the file on disk will look
correct while the screen does not.

## originals/

Untouched copies of everything as supplied, in case you want to redo a crop
or restore a logo's original tone:

- `rolex.png`, `corvette.png` — the first pair of photos, before cropping
- `martingrow.png`, `mymission.png` — logos before the white normalisation

To re-crop a photo from an original:

```bash
sips -c <height> <width> --cropOffset <top> <left> originals/corvette.png --out /tmp/c.png
sips -z 600 900 /tmp/c.png --out /tmp/c2.png
sips -s format jpeg -s formatOptions 88 /tmp/c2.png --out corvette.jpg
```
