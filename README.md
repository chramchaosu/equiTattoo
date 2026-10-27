# equiTattoo

A phone-first app for a tattoo artist: a log of tattoos and a stock list of needle cartridges.
The whole app is one file, `index.html`. It needs no server, no build step and no account.

## What it does

**Tattoos**
- Each tattoo has a date and the client's name (both required), plus an optional description and notes.
- Optional client questions: age group (15–24, 25–34, 35–44, 45–54, 55–64, 65–99+) and gender.
- Photos in three groups: reference photos from the client, the finished tattoo, and the healed tattoo.
  Every photo has an editable time stamp; healed photos also show how many days after the session they were taken.
- Cartridges used, added either
  1. **by hand**: brand, needle thickness, needle count, type. The most used combinations, brands and types are offered first, or
  2. **by scanning the label**: a photo of the box is read with [Tesseract.js](https://github.com/naptha/tesseract.js) and the details are filled in for checking. Understands codes such as `1203RL` (#12 / 0.35 mm, 3 needles, Round Liner), `0.35mm 7RS`, `#10 9 Curved Magnum`.

**Equipment**
- Stock list of cartridges with quantity and a "running low" threshold.
- Cartridges used on a tattoo that match a stock item are taken off the stock automatically (and put back if removed from the tattoo).

**Backup**
- All data stays on the device (IndexedDB). The Backup tab saves everything, photos included, into one JSON file and can restore it.

## Code layout

`index.html` contains, in order: page styles (colour tokens at the top, with a dark theme), the page structure,
and one script split into numbered sections (fixed lists, helpers, database, photos, label reading,
statistics, screens, tattoo list, tattoo editor, cartridge pop-up, equipment, backup, start-up).

Data model (IndexedDB database `equiTattoo`, version 1):

| Store        | Record |
|--------------|--------|
| `tattoos`    | `{ id, date, clientName, description, ageGroup, gender, notes, cartridges[], coverThumb, createdAt, updatedAt }` |
| `photos`     | `{ id, tattooId, kind: 'reference' \| 'finished' \| 'healed', takenAt, image, thumb, addedAt }` |
| `cartridges` | `{ id, brand, gauge, needleCount, config, quantity, lowStockAt, createdAt, updatedAt }` |

Photos are shrunk to at most 1600 px (plus a 320 px thumbnail) before saving.

## Notes

- Label scanning downloads the text-recognition engine (about 5 MB) the first time, so it needs internet once.
  It cannot run inside the Claude artifact preview, which blocks those downloads; everything else works there.
- Plain HTML, CSS and modern JavaScript; no frameworks.
