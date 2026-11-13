# zappy

Zappy is the end-of-year project of the second year at Epitech, built by a team
of five. Teams of AI players share a world shaped like a torus. They have to
collect food and stones and level up through rituals, and the first team to get
six players to the maximum level wins.

The project has three programs that only talk to each other over TCP:

| Program | Language | Role |
|---------|----------|------|
| `zappy_server` | C | Owns the game state, runs the rules and the clock, serves every client with one `poll()` loop |
| `zappy_ai` | Python | One process per player: it sees its surroundings, moves, talks to its team and joins rituals |
| `zappy_gui` | C++17, raylib | 3D spectator view of the world, live |

```
  zappy_ai ──┐
  zappy_ai ──┼──── TCP (text protocol) ────▶ zappy_server ◀──── TCP ──── zappy_gui
  zappy_ai ──┘
```

## Highlights

- **Server**: event-driven with no threads. Each player has a command queue, and
  each command takes a duration that depends on the frequency `-f`. Includes
  unit tests (Criterion) for the parser, map, network, world and utilities.
- **Protocol**: one protocol for players and one for the spectator, described
  in [docs/protocol.md](docs/protocol.md).
- **AI**: a state machine (survive, gather, rally, level up). A leader is
  elected, calls the team through broadcasts and starts the rituals. New
  players are created automatically with `Fork`.
- **GUI**: a layered design (network, world, rendering, UI), animated 3D models,
  views of each tile and player, and a broadcast history.

## Build

Requires `make`, `cmake`, a C and C++17 compiler, [raylib](https://www.raylib.com/)
and Python 3.

```sh
make            # zappy_server, zappy_gui, zappy_ai
```

## Run

```sh
./zappy_server -p 4242 -x 20 -y 20 -n red blue -c 4 -f 100
./zappy_gui -p 4242 -h localhost
./zappy_ai -p 4242 -n red -h localhost      # one per player
```

| Server option | Meaning |
|---------------|---------|
| `-p` | Port |
| `-x`, `-y` | Map size |
| `-n` | Team names |
| `-c` | Players allowed per team at the start |
| `-f` | Time unit: higher means faster |

Server tests: `make -C NET/tests tests_run`.

## Documentation

- [docs/architecture.md](docs/architecture.md): the three components and how the server works inside
- [docs/protocol.md](docs/protocol.md): every command and message
- [docs/ai.md](docs/ai.md): AI strategy
- [docs/gui.md](docs/gui.md): GUI internals

## Team

| Part | Members |
|------|---------|
| Server (C) and coordination | Léandre Fouret, [David Caron](https://github.com/sojournerfr) |
| GUI (C++) | [Arthur Vignes](https://github.com/4rthurV), [Eliott Duchêne](https://github.com/Yoyott29) |
| AI (Python) | [Arthur Piron](https://github.com/Arthur-piron) |

## License

[MIT](LICENSE)
