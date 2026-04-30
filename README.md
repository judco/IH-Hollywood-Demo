# Idle Hacking — Hollywood Mode (Concept Demo)

A cinematic ambient visualization of idle progress for [Idle Hacking](https://www.idlehacking.com).

**Live demo:** https://judco.github.io/IH-Hollywood-Demo/

---

Inspired by Linux's `hollywood` package and adapted to Idle Hacking's idle-game philosophy: instead of staring at a static idle screen, players watch their rig **work** in cinematic detail — CPU bars crunching, networks scanning, exploits firing, loot flowing, and the operator console executing crafting commands on items as they drop.

This is a self-contained HTML file with no backend wiring. All numbers are fake, but everything uses the game's actual vocabulary, theme palette, and 5-second combat tick.

## Layout

```
┌──────────────────────────────────────────────────────────────────────┐
│ IDLE HACKING // OPERATOR trgKai // ZONE LOCAL NETWORK Lv1+ // ...    │
├─────────────────────┬────────────────────┬───────────────────────────┤
│ COMBAT FEED         │ NETWORK RECON      │ RESOURCE TELEMETRY        │
│                     │                    │ [Coding][Computing][...]  │
├─────────────────────┼────────────────────┼───────────────────────────┤
│ MEMORY DUMP         │ OPERATOR CONSOLE   │ LOOT STREAM               │
│                     │                    │                           │
├──────────────────────────────────────────────────────────────────────┤
│ chyron: [BREAKING] ... [MARKET] ... [EVENT] ... [LEADERBOARD] ...    │
└──────────────────────────────────────────────────────────────────────┘
```

## Panes

| Pane | What it does |
|---|---|
| **Combat Feed** | One fight at a time, ~5s progress bar matching the game's combat tick. Enemy level and rewards scale with the current zone. ~12% chance of chip drops. |
| **Network Recon** | Multi-step operations queue: nmap sweeps, Hydra brute force, exploit chains, packet capture, traceroute, DNS exfil, ARP MITM, TLS handshakes. |
| **Resource Telemetry** | Only one gathering skill is active at a time. Each tab (Coding / Computing / Mining / Sniffing) renders its own unique work feed. |
| **Memory Dump** | Hex/ASCII rolling dump with region markers (`.text` / `.heap` / `.stack` / `.payload`), embedded game-vocabulary strings, and occasional highlighted match lines. |
| **Operator Console** | Simulated terminal typing commands character-by-character. Operates on items the loot stream actually dropped — `enhance --augment`, `equip`, `decompile`, `market list`, etc. |
| **Loot Stream** | Equipment drops gated entirely by combat wins. Rarity bias and item level scale with the current zone. |

The header rotates through all 8 in-game zones every 30 seconds. The footer chyron carries fake breaking news, syndicate updates, market sales, and patch notes.

## Built Like Idle Hacking

Intentionally written in the same architectural style as the game itself, so any pane could be lifted into the production client with minimal refactoring:

- **Vanilla HTML / CSS / JS** — no framework, no bundler, no build step
- **CSS-variable theme system** — slots in alongside the existing 16 themes via the same `--bg-color`, `--text-color`, `--accent-color`, `--rarity-*` tokens
- **Same fonts** — IBM Plex Mono + VT323
- **CRT scanline overlay + toggle** — matches the game's existing styling
- **No external dependencies** beyond Google Fonts

## Controls

- **Skill tabs** in Resource Telemetry — switch which gathering skill is active
- **Theme picker** (top-right) — preview against 6 canonical themes: Phosphor Green, Amber, Dracula, Nord, Tokyo Night, Synthwave
- **CRT: ON/OFF** — toggle the scanline overlay; preference persists in `localStorage`
- **ESC** — disengage

## Run Locally

It's a single HTML file. No server required.

```
git clone https://github.com/judco/IH-Hollywood-Demo
cd IH-Hollywood-Demo
# open index.html in any browser
```

Theme and CRT state persist in `localStorage`.

## Integration Path

To wire Hollywood Mode to live player data, the proposal is to ship it as a `data-view` inside the existing game client — same WebSocket, same auth, same theme — rather than as an external site. That requires **no new auth surface** and **no new API endpoints**, just a small set of read-only hooks on the existing client state:

- `currentPlayer` snapshot (name, syndicate, zone, levels, currencies)
- `onCombatTick(handler)` — fires per 5s combat resolution
- `onLootDrop(handler)` — fires per equipment drop
- `onResourceGather(handler)` — fires per gathering tick
- `getActiveZone()` — current zone identifier + reward multipliers
- *(optional)* `onWsFrame(handler)` — tap on `/ws` messages, for the WS-flavored variant

Each pane is independent, so panes can be enabled incrementally as hooks become available. None of this requires the player to authenticate against any third-party service.

## Status

This repo is a **pitch artifact**, not an official feature. The goal is to make the case for Hollywood Mode visually rather than verbally. Feedback welcome.

## Credits

- **Idle Hacking** — game by **trgKai** ([idlehacking.com](https://www.idlehacking.com))
- Demo concept and code by **judco**
