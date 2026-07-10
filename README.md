# Pandemic

An agent-based pandemic-spread simulator written in C — inspired by *Plague Inc: Evolved*.

## About

Pandemic is a command-line program that simulates how a virus spreads through a
population of agents moving across a world. The world is modelled as a complete
graph whose vertices are locations; each agent follows its own cyclic route
between locations. In every simulation step agents move, the disease progresses
(each infected agent may die, recover, or stay ill), and infection spreads among
agents sharing a location. The run ends when every agent is dead, the virus is
eradicated, or a configured step limit is reached, after which the program prints
cumulative statistics (total infections, deaths, survivors, most infectious
location, and step count).

The virus, agents, and world are all described through the command line and two
CSV input files. A large part of the work was writing robust CSV parsers that
validate every row and free all resources on error. Disease dynamics (death,
recovery, and transmission) are driven by probability rolls against configurable
lethality, infectivity, duration, immunity, and exposure parameters, with a
fixable random seed so runs can be reproduced and compared against a reference
implementation.

## Tech stack

- **Language:** C (gnu99)
- **Build:** CMake (>= 3.0)
- **Testing:** CUT unit-testing framework (bundled under `tests/`)
- **Compiler flags:** `-Wall -Wextra -pedantic`

## Build & run

```bash
# Configure and build
cmake -S . -B build
cmake --build build

# Run a simulation: ./pandemic [OPTIONS] agents.csv world.csv
./build/pandemic agents/sample.csv worlds/village.csv

# Example with options
./build/pandemic --infectivity 0.7 --lethality 0.3 --random-seed 1337 --verbose \
    agents/sample.csv worlds/village.csv
```

### Options

| Option | Meaning | Default |
| --- | --- | --- |
| `--lethality <float>` | Chance an infected agent dies, `[0,1]` | `0.5` |
| `--infectivity <float>` | Transmission strength, `[0,1]` | `0.5` |
| `--duration <float>` | Affects the chance of recovery, `[0,1]` | `0.5` |
| `--vaccine-modifier <float>` | Effect of vaccination on rolls | `1.2` |
| `--max-steps <int>` | Cap on simulation steps | run until stable |
| `--random-seed <int>` | Seed for reproducible runs | `time(NULL)` |
| `--verbose` | Print per-step infection/recovery/death events | off |

Sample input files live in `agents/` and `worlds/`. The world CSV uses
`id;name;exposure` rows; the agents CSV uses `id;route;is_vaccinated;immunity;is_infected`.

### Tests

```bash
cmake --build build --target pandemic-tests
./build/pandemic-tests
```

## Notes

University assignment for **MUNI PB071** ("HW06: Pandémia s.r.o."). The full
original specification (in Slovak) is preserved in [`assignment.ad`](assignment.ad).
No AI tools were used during development.
