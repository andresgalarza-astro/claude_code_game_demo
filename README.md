# Neon Alley Brawl

A side-scrolling beat 'em up prototype that lives in a single HTML file. Fight through The Streets and The Warehouse, then take down The Overseer.

Everything is generated at runtime: the graphics are drawn on an HTML5 canvas and the sound effects and chiptune soundtrack are synthesised with the Web Audio API. There are no image or audio assets, no dependencies and no build step.

## How to play

Open `index.html` in a modern browser (Chrome, Edge, Firefox or Safari) and press **START GAME** or **Enter**. It plays with a keyboard on desktop and with on-screen controls on phones and tablets.

If you would rather serve it locally:

```bash
python -m http.server 8000
# then open http://localhost:8000
```

## Controls

| Key | Action |
| --- | --- |
| WASD / Arrow keys | Move (left/right and depth) |
| J | Light attack (tap three times for a combo) |
| K | Heavy attack (knockdown) |
| L | Block (hold, face the attacker) |
| Space | Jump (J or K in the air = flying kick) |
| E | Pick up weapon |
| P | Pause |
| Enter | Start / restart, confirm name entry |

### Phones and tablets

On a touch device the game switches to a touch layout automatically. To try it on a phone, serve the folder as shown above and open `http://<your-computer-ip>:8000` from a phone on the same network.

| Control | Action |
| --- | --- |
| Left thumb | Drag anywhere on the left side to move; the stick appears under your thumb |
| PUNCH | Light attack (tap three times for a combo) |
| KICK | Heavy attack (knockdown) |
| BLOCK | Hold, facing the attacker |
| JUMP | Jump (then PUNCH or KICK = flying kick) |
| GRAB | Pick up weapon; the button pulses when one is in reach |
| Pause button | Pause; the menus also have a fullscreen button where the browser supports it |

Both orientations work. In landscape the play field fills the screen and the controls float over the lower corners. In portrait the play field sits at the top and the controls use the space underneath. Landscape gives the bigger picture.

## Gameplay

The game has two levels of three waves each. Every wave locks the camera until all of its enemies are defeated, then you walk right to move on. The last wave of The Warehouse is the boss fight.

### Enemies

| Enemy | Health | Behaviour |
| --- | --- | --- |
| Grunt | 30 | Basic punches. |
| Brawler | 60 | Flashes red during a one-second wind-up, then throws an unblockable haymaker. Light hits do not interrupt the wind-up, so step out of his lane or knock him down. |
| Brute | 75 | Slow, with a heavy smash that knocks you down. |
| The Overseer | 260 | Boss. At 50% health he roars and enters Rage: faster, shorter cooldowns, and an unblockable ground slam if you stay close for too long. Jump over the slam or back away. |

Health values are base figures; they scale up with difficulty.

### Crates, food and weapons

Smash crates to get food and, sometimes, weapons. Food restores 30% of your maximum health when you walk over it. Weapons are picked up with **E** and replace your ground attacks with slower swings that have 1.5x reach and more damage and knockback.

| Weapon | Damage | Durability |
| --- | --- | --- |
| Iron pipe | x1.7 | 10 hits |
| Baseball bat | x1.45 | 12 hits |

A weapon loses one point of durability per connecting swing and breaks at zero. Getting knocked down makes you drop it. Items left on the floor disappear after a while.

### Difficulty

A "THREAT" multiplier shown in the HUD rises from x1.00 to x2.20 with the current level, your score and time spent playing. As it climbs, enemies gain health and speed, attack more often, and more of them are allowed on screen and attacking at once.

### Score and leaderboard

You earn points for defeating enemies (100 for a grunt up to 5000 for The Overseer) and for smashing crates. At the end of a run you enter a three-letter name and the top five scores are kept, along with best combo and time. Scores are saved in the browser's `localStorage`; if storage is unavailable (private browsing, for example) the board still works but resets when the page is closed.

## Code overview

All of the code is in `index.html`, organised into commented sections:

| Section | Responsibility |
| --- | --- |
| Constants & helpers | Resolution (800x450 logical), fixed 60 Hz physics step, utilities |
| `AudioEngine`, `Chiptune` | Synthesised sound effects and music |
| `Input` | Keyboard state and per-step "just pressed" edges |
| `TouchPad` | On-screen stick and buttons, translated into the same key codes as the keyboard |
| `ATK`, `WEAPONS`, `FEEL` | Frame data for every move, weapon stats, hit-stop and screen-shake tuning |
| `Entity`, `Player`, `Enemy`, `Overseer` | Shared physics and damage, state machines and AI |
| `DIFFICULTY` | The difficulty curve and everything that scales with it |
| `Destructible`, `Pickup` | Crates, food and weapons on the floor |
| Collision | Floor collision plus 3D hitbox-vs-hurtbox combat checks |
| `Renderer` | Procedural backgrounds, fighters and effects |
| `Leaderboard` | Top-five high scores |
| `LEVELS`, `Game` | Level and wave data, main loop, camera lock, HUD |

The world uses a belt-scroller pseudo-3D coordinate system: `x` is left/right, `y` is depth on the floor, and `z` is height above the floor.

Most tuning can be done by editing the data tables near the top of the script: `ATK` for moves, `ENEMY_TYPES` and `BOSS` for enemies, `WEAPONS` for weapons, `DIFFICULTY` for scaling and `LEVELS` for wave layouts.
