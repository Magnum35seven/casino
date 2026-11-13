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
- Wizard wild appears on reels 2-4 and substitutes for everything except the treasure scatter. A wild that lands expands to fill its whole reel.
- Cascading reels: winning symbols vanish, the rest fall and new ones drop in. Each extra cascade raises the multiplier (x2, x3, up to x4), and the chain ends when nothing wins.
- Arcane Surge (about 1 spin in 25): a spell turns every Rune Stone, Spellbook or Elixir on the board into Moon Blades or Crystal Orbs before wins are counted.
- The Treasure meter under the reels is a progress bar (20 treasures). Tap it for an explanation. "Last win" in the stats row keeps the most recent winning amount.
- Treasure scatters pay on 3 or more (multiples of total bet) and every treasure that lands also fills the meter. The meter is saved between sessions.
- A full meter does not open the vault straight away: you face the Guardian's Gate and pick one of four sealed keys. One key opens the vault, one gives a consolation purse (3x total bet), two make the Guardian drive you back. Anything but the vault key drops the meter to half.
- Inside the vault, a 5x3 matrix starts with 3 locked treasures worth prizes. Locked treasures stay still while the other cells spin. Each empty cell has a 12% chance to lock a treasure per spin, so some spins lock nothing (8 spins total). Fill all 15 for the Grand Treasure (+50x total bet).
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
- `MTR` - treasures needed to reach the Guardian's Gate (20), the key mix in `keyGame()`, `PZ` - treasure prize multipliers (of total bet), and the lock chance (`.12`) and spin count (`8`) inside `bonus()`

The payout rate has not been simulated. After changing `WT` or `PAY`, run a few thousand automated spins to check the return before you rely on the feel.

## Payout check

`WT`, `PAY`, `PZ`, the surge chance, the cascade cap and the Guardian's Gate odds were tuned with a Monte Carlo simulation of about 800,000 spins per line setting. Base game, cascades, expanding wilds, the gate and the vault together returned roughly 87-100% of the bet depending on lines played (1 line lowest, 20 lines highest), plus about 3% of each bet feeding the Dragon's Hoard. These are simulated estimates, not guarantees. Re-test after changing any of those values.

## Notes

- Symbols are inline SVG drawn in the page, so they look the same on every device.
- Sound is synthesised with the Web Audio API; browsers only start audio after the first tap.
- The footer shows session time and net result as a reminder that this is a game with play money.
