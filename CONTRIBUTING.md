# Contributing

Contributions are welcome — especially weight corrections and new boss tiers. Here's how to do each.

---

## Reporting a wrong drop weight

Open an issue using the **Wrong weight** template (`.github/ISSUE_TEMPLATE/wrong_weight.md`) and provide:

- Item name
- Boss name and tier
- Current weight in the file
- Correct weight
- Source link (wiki page or community spreadsheet)

You can also fix it directly with a pull request — see below.

---

## Fixing a weight in a pull request

All loot table data lives in the `DATABASE` object near the top of `calculadora.html`. Each boss looks like this:

```js
"Sven Packmaster T4": {
  name: "Sven Packmaster T4",
  base_xp: 500,
  drops: [
    { name: "Wolf Tooth",          weight: 10000, pool: "TOKEN" },
    { name: "Hamster Wheel",       weight: 2000,  pool: "MAIN"  },
    { name: "Overflux Capacitor",  weight: 5,     pool: "MAIN"  },
    { name: "Spirit Rune I",       weight: 833,   pool: "EXTRA" },
    // ...
  ]
}
```

To fix a weight, change the `weight` value and open a PR. Include the wiki source in the PR description.

---

## Adding a new boss or tier

1. Find the boss's drop table on the [Hypixel SkyBlock Wiki](https://wiki.hypixel.net/Slayer) or its individual boss page.
2. Note each drop's **name**, **weight**, and **pool type** (TOKEN / MAIN / EXTRA).
3. Note the boss's **base XP per kill**.
4. Add an entry to the `DATABASE` object in `calculadora.html` following the existing format.
5. If adding a new slayer type (not Zombie/Spider/Wolf/Enderman/Blaze/Vampire), add it as a new top-level key.

### Pool types

| Pool | Meaning |
|------|---------|
| `TOKEN` | The guaranteed token drop (Revenant Flesh, Wolf Tooth, etc.) — always weight 10000 |
| `MAIN` | Main loot table — rolled after the token drop |
| `EXTRA` | Extra loot table — rolled separately; rune drops typically live here |

### Base XP values

| Boss | Base XP |
|------|---------|
| Revenant Horror T1 | 5 |
| Revenant Horror T2 | 15 |
| Revenant Horror T3 | 200 |
| Revenant Horror T4 | 1000 |
| Revenant Horror T5 | 1500 |
| Tarantula Broodfather T1 | 5 |
| Tarantula Broodfather T2 | 25 |
| Tarantula Broodfather T3 | 100 |
| Tarantula Broodfather T4 | 400 |
| Tarantula Broodfather T5 | 600 |
| Sven Packmaster T1 | 5 |
| Sven Packmaster T2 | 25 |
| Sven Packmaster T3 | 100 |
| Sven Packmaster T4 | 500 |
| Voidgloom Seraph T1 | 50 |
| Voidgloom Seraph T2 | 100 |
| Voidgloom Seraph T3 | 400 |
| Voidgloom Seraph T4 | 1000 |
| Inferno Demonlord T1 | 30 |
| Inferno Demonlord T2 | 100 |
| Inferno Demonlord T3 | 400 |
| Inferno Demonlord T4 | 1000 |
| Riftstalker Bloodfiend T1 | 30 |
| Riftstalker Bloodfiend T2 | 60 |
| Riftstalker Bloodfiend T3 | 100 |
| Riftstalker Bloodfiend T4 | 150 |

---

## Code style

- No external build tools. The file must remain a single `.html` that opens directly in a browser.
- No new runtime dependencies. Chart.js is loaded from cdnjs and that's the only external script.
- JavaScript inside `<script>` — no TypeScript, no modules.
- Keep the existing `DATABASE` structure. Don't add properties that aren't `name`, `weight`, or `pool` to drop objects without also updating `computeWeights()` and `solveOptimal()`.

---

## Pull request checklist

- [ ] Weight source linked in the PR description
- [ ] Tested by opening `calculadora.html` locally and verifying the affected boss renders correctly
- [ ] No changes outside `calculadora.html` unless fixing docs

---

## Questions

Open a Discussion or an issue. Tag it with `question`.
