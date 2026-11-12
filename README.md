# Arcane Quest

A Las Vegas style slot machine PWA with a mythical quest theme: dragon eggs, crystal orbs, moon blades, elixirs, spellbooks, rune stones, a wizard wild and a treasure scatter. Play money only, fully offline, no external dependencies, no paid APIs.

## Files

| File | Purpose |
|------|---------|
| `index.html` | The whole game: markup, CSS, JavaScript |
| `manifest.json` | PWA manifest (name, icons, standalone display) |
| `sw.js` | Service worker that caches everything for offline play |
| `icon-192.png`, `icon-512.png` | App icons |

## Run locally

Service workers need HTTP(S), so don't open `index.html` by double-clicking. From this folder run:

```
python -m http.server 8080
```

Then open `http://localhost:8080`.

## Deploy (GitHub Pages)

1. Put all five files in the root of a repo (or a `/docs` folder).
2. Enable Pages in the repo settings.
3. Open the Pages URL on your phone and choose **Add to Home Screen** / **Install app**.

If you change any file after deploying, bump `C` in `sw.js` (for example `gs7-v2`) so installed copies pick up the update.

## How it plays

- 5 reels by 3 rows with up to 20 paylines. Wins pay left to right from reel 1 (3, 4 or 5 of a kind) on every active line.
- Choose how many lines to play (1, 3, 5, 10, 15, 20) and the bet per line. Total bet = lines x bet per line. Tap Paylines to see the active line shapes.
- Wizard wild appears on reels 2-4 and substitutes for everything except the treasure scatter.
- 3 or more treasure scatters anywhere pay a bonus on the total bet and award 10 free spins with all wins doubled.
- 5 dragon eggs on an active payline pays the Dragon's Hoard jackpot.
- Tap SPIN while the reels are moving to slam-stop them. Space bar also spins.
- Auto, Turbo, Max bet (20 lines at the top bet), sound toggle, and a daily bonus with a streak.
- Balance, bet, lines and jackpot are stored in `localStorage` on the device.

## Tuning

Everything is near the top of the `<script>` in `index.html`:

- `WT` - symbol weights (rune, book, potion, sword, orb, drag, wild, scat) on the reels (lower numbers make a symbol rarer)
- `PAY` - paytable, as multiples of the line bet for 3 / 4 / 5 of a kind
- `LN` - the 20 payline shapes (row index per reel)
- `BETS` - selectable bet per line, `LINES` - selectable line counts
- `JP0` - jackpot reset value

The payout rate has not been simulated. After changing `WT` or `PAY`, run a few thousand automated spins to check the return before you rely on the feel.

## Notes

- Symbols are inline SVG drawn in the page, so they look the same on every device.
- Sound is synthesised with the Web Audio API; browsers only start audio after the first tap.
- The footer shows session time and net result as a reminder that this is a game with play money.
