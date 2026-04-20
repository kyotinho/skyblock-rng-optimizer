# SkyBlock RNG Meter Optimizer

A mathematically rigorous drop-rate calculator and RNG Meter strategy optimizer for Hypixel SkyBlock Slayers. Finds the exact **sweet spot (L\*)** — the number of runs to keep the meter ON before switching it off — to maximize drops per run over a full pity cycle.

> **Zero dependencies. Single HTML file. Open in any browser.**

---

## Features

- **Sweet spot solver** via O(m) Dynamic Programming — finds the exact L\* that maximizes `Eff(L) = expected drops / expected runs`
- **All 6 slayer types** covered: Zombie, Spider, Wolf, Enderman, Blaze, Vampire
- **9 bosses** with full loot tables (TOKEN, MAIN, EXTRA pools)
- **Magic Find** simulation — applies MF boost only to items with base chance < 5%, exactly as the game does
- **Aatrox XP buff** toggle (+25% XP per run)
- **Pity attribute** support (levels 0–10, each +1% XP per run)
- **Efficiency curve** chart showing Eff(L) across all possible L values
- **Strategic analysis** tab with plain-language recommendation per item
- **Progress bar** visualizing the sweet spot relative to pity

---

## Live tests and proof with visual PoC

Open `calculator.html` for tests or `visual-proof-of-concept.html` directly in your browser — no server, no install, no build step needed.

```
# Clone the repo
git clone https://github.com/your-username/skyblock-rng-optimizer.git
cd skyblock-rng-optimizer

# Open in browser (any OS)
open calculator.html          # macOS
xdg-open calculator.html      # Linux
start calculator.html         # Windows
# Open in browser (any OS)
open visual-proof-of-concept.html          # macOS
xdg-open visual-proof-of-concept.html      # Linux
start visual-proof-of-concept.html         # Windows
```

---

## How It Works

### The core problem

The RNG Meter boosts the **weight** of a selected item linearly up to 3× as it fills. Keeping the meter ON increases your per-run drop chance, but if the item drops while the meter is active, **the meter resets to zero** — you lose all accumulated XP. The question is: at what point does the reset risk outweigh the boost benefit?

### The model

For a cycle of `m` runs (the number needed to fill the meter to 100% pity), with the strategy of keeping the meter ON for `L` runs then OFF for the rest:

**Survival probability** (probability of reaching run L without dropping):

```
S(0) = 1
S(L+1) = S(L) × (1 − p_L)
```

**Boosted drop chance at run L** (from the official wiki formula):

```
multiplier = 1 + min(2 × StoredXP / RequiredXP, 2)
boostedWeight = baseWeight × multiplier
p_L = boostedWeight / (poolTotal − baseWeight + boostedWeight)
```

**Efficiency function** (drops per run over the full cycle):

```
Eff(L) = [1 + S(L) × (m − L) × p] / [sumS(L) + S(L) × (m − L + 1)]
```

where `p` is the base chance with Magic Find (meter OFF), and `sumS(L) = Σ S(i)` for `i = 0..L-1`.

**Sweet spot:** `L* = argmax Eff(L)` — found by a single linear scan in O(m).

For the full mathematical derivation, see [`MATH.md`](MATH.md).

### Magic Find

MF applies a weight multiplier of `(1 + MF/100)` — but only for items whose base drop chance is **below 5%** after RNG Meter modifications, exactly as specified by the wiki. Higher MF shifts L\* later because it raises the value of each meter-OFF run, reducing the relative cost of a reset.

### XP per run

```
XP_per_run = base_xp × (1 + aatrox × 0.25) × (1 + pityLevel × 0.01)
m = ceil(RequiredXP / XP_per_run)
RequiredXP = floor(50000 / baseChance%)
```

---

## Supported Bosses & Loot Tables

| Type | Boss | Base XP | Drops |
|------|------|---------|-------|
| Zombie | Revenant Horror T4 | 1000 | 12 |
| Zombie | Revenant Horror T5 | 1500 | 13 |
| Spider | Tarantula Broodfather T4 | 400 | 12 |
| Spider | Tarantula Broodfather T5 | 600 | 13 |
| Wolf | Sven Packmaster T4 | 500 | 9 |
| Enderman | Voidgloom Seraph T4 | 1000 | 11 |
| Blaze | Inferno Demonlord T4 | 1000 | 11 |
| Vampire | Riftstalker Bloodfiend T4 | 150 | 10 |

Drop weights sourced from the [Hypixel SkyBlock Wiki](https://wiki.hypixel.net/Slayer) and verified against community data. See [`MATH.md`](MATH.md#loot-table-sources) for per-item weight references.

---

## Repository Structure

```
skyblock-rng-optimizer/
├── calculadora.html      # The full application — single file, zero deps
├── README.md             # This file
├── MATH.md               # Full mathematical derivation and algorithm analysis
├── CONTRIBUTING.md       # How to add bosses, fix weights, open PRs
├── LICENSE               # MIT
└── .github/
    └── ISSUE_TEMPLATE/
        ├── wrong_weight.md
        └── new_boss.md
```

---

## Contributing

Found a wrong drop weight? Want to add a missing boss tier? See [`CONTRIBUTING.md`](CONTRIBUTING.md).

The fastest contribution is a weight correction — just open an issue with the item name, current weight in the file, correct weight from the wiki, and a link to the source.

---

## Limitations & Caveats

- **Loot table version:** Weights can change with game updates. If a patch changes drop rates, the `DATABASE` object in `calculadora.html` needs to be updated manually.
- **Slayer level gating:** The calculator does not model the fact that some drops require minimum Slayer levels to appear in the pool. Weights assume all drops are unlocked for your level.
- **Vampire Slayer XP:** The Riftstalker Bloodfiend uses Motes (Rift currency), not standard Slayer XP. The XP/run value in the calculator is an approximation based on community-reported averages.
- **T5 Tarantula / T5 Voidgloom / T4 Blaze weights:** Some of these are sourced from community spreadsheets, not directly from the official wiki, due to wiki access restrictions at scrape time. Treat them as best estimates.

---

## License

MIT — see [`LICENSE`](LICENSE).

Not affiliated with Hypixel or Mojang. All game data belongs to their respective owners.
