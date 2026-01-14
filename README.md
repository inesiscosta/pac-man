# Pac-Man Modified

A take on Pac-Man written in PEPE-16 assembly, the 16-bit teaching processor used at Instituto Superior Técnico. Eat all four pieces of candy before the ghosts catch you.

Built with [@mc8mac](https://github.com/mc8mac) and [@S3nutna](https://github.com/S3nutna) for the Computer Architecture course (2024/2025).

## Demo

https://github.com/user-attachments/assets/a4633438-94b2-4e2f-a2e1-454167eb1a92

## How to Play

- Pac-Man starts in the box in the middle of the screen. A candy sits in each corner.
- Up to four ghosts appear at random over time and chase you. If one catches you, Pac-Man explodes and it's game over.
- Eat all four candies to win.
- The 7-segment displays count up as you play.

### Controls

Movement uses the simulator's 4×4 keypad as a compass. Keys `0`, `1` and `2` move you up-left, up and up-right, and so on around the grid:

```
 0   1   2         ↖  ↑  ↗
 4   ·   6    →    ←  ·  →
 8   9   A         ↙  ↓  ↘
```

| Key | Action |
|---|---|
| `C` | Start |
| `D` | Pause / resume |
| `E` | End the game |

## How It Works

The whole game is about 1,900 lines of assembly in [`src/pacman.asm`](src/pacman.asm), driven by the simulator's clocks through interrupts:

| Interrupt | Job |
|---|---|
| 0 | Moves the ghosts |
| 1 | Advances the counter on the 7-segment displays |
| 2 | Times the explosion when a ghost catches Pac-Man, clearing it after 1.5 seconds |

- **Interrupts only raise flags.** Each interrupt routine just sets a flag. The main loop checks the flags and does the actual work, so the interrupt routines stay short and never draw anything themselves.
- **Sprites are data tables.** Each sprite is a table of height, width and pixel colours: Pac-Man with his mouth open left or right, the ghosts, the candy, the explosion. One `draw_object` routine draws any of them on the 64×32 pixel screen.
- **Collisions use pixel colours.** Before moving, the game reads the colour of the pixels it would move onto. A ghost's colour means game over, a candy's colour means it's eaten, and the blue walls block the move.
- **The ghosts chase you.** Each tick, a ghost compares its row and column with Pac-Man's and steps one pixel closer on both axes, so it can move diagonally.
- **A game-state machine.** The game moves between `INITIAL`, `PLAYING`, `PAUSED`, `WON` and `GAME_OVER`. Each state has its own background image and sound, played through the simulator's MediaCenter.
- **A decimal counter in hex.** The displays read hex digits, so the counter adds 6 to carry into the tens digit and 96 to carry into the hundreds, keeping the display in decimal.

## Running

You need Java with JavaFX.

1. Open [`src/simulator.jar`](src/simulator.jar), the course's PEPE-16 simulator.
2. Load [`src/circuit.cir`](src/circuit.cir). It wires up the processor, screen, keypad, displays and clocks.
3. Load `pacman.asm` into the processor, start the simulation, and press `C`.

## License

[MIT](LICENSE) © 2024 Inês Costa, Marcos Machado and Nuno Antunes
