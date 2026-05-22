# BDI-Evacuation-Development
This project extends the existing “StationNightclub” evacuation model developed in GAMA Platform by introducing advanced crowd congestion mechanisms, temporary immobilization, dynamic bottlenecks, and panic-driven movement disruptions, significantly improving the realism of emergency evacuation and crowd behavior simulation.
# README – StationNightclub Model

## Overview

`StationNightclub.gaml` is an Agent-Based Model (ABM) created in the GAMA Platform environment. The simulation reproduces the evacuation of people from a nightclub during a fire emergency.

The model combines:

* fire and smoke propagation,
* crowd dynamics,
* congestion effects,
* emotional contagion,
* BDI architecture (Belief–Desire–Intention),
* social relationships,
* visibility reduction caused by smoke,
* helping behaviour,
* evacuation decision-making.

This document focuses especially on the modifications introduced into the movement and congestion system.

---

# Model Architecture

## Main Species

### `victim`

Represents people inside the nightclub.

Agents:

* move through the environment,
* perceive smoke, fire, exits, and other agents,
* react emotionally,
* make evacuation decisions,
* can help friends,
* can become injured or die.

The species uses the `simple_bdi` architecture.

---

### `cell`

Grid cells discretizing the environment.

Each cell stores:

* smoke level,
* fire state,
* walls,
* exits,
* number of dead bodies.

---

### `fire`

Represents the fire source.

Responsible for:

* smoke generation,
* fire propagation,
* increasing environmental danger.

---

### `dead_body`

Represents dead victims.

Bodies remain in the environment and influence movement congestion.

---

# Environment

The environment is created from GIS shapefiles:

| File       | Purpose                       |
| ---------- | ----------------------------- |
| `Wall.shp` | walls                         |
| `Exit.shp` | exits                         |
| `Bar.shp`  | internal obstacles / bathroom |

---

# Key Behavioural Systems

## Smoke Propagation

Smoke spreads through neighbouring cells.

Effects:

* reduced visibility,
* increased stress,
* health degradation,
* altered evacuation decisions.

---

## Emotional Contagion

Agents can transmit fear to nearby agents.

This mechanism simulates:

* panic waves,
* social stress propagation,
* crowd emotional synchronization.

---

## Social Relations

Some agents are paired as friends.

Friends:

* search for each other,
* help each other,
* share evacuation information.

---

# IMPORTANT CHANGES INTRODUCED IN THE MODEL

The latest modifications focus mainly on:

1. realistic congestion,
2. temporary immobilization,
3. dynamic crowd blocking,
4. bottleneck generation,
5. panic-driven movement disruptions.

These changes significantly improve the realism of evacuation dynamics.

---

# 1. Random Stop / Freeze Behaviour

## Added Code

```gaml
if has_desire(fleeing) and !(empty(victim at_distance 0.3)) and flip(0.05) {
    write "Poczekam i odpocznę sobie: " + self;
    slow_timer <- 3.0;
}
```

## Explanation

Agents:

* must be evacuating,
* must be physically close to another victim,
* have a 5% probability of stopping.

The stop lasts for 3 seconds.

---

## Scientific Meaning

This mechanism simulates:

* stumbling,
* temporary panic freeze,
* hesitation,
* crowd pressure,
* exhaustion,
* local instability in evacuation flow.

This is important for realistic crowd evacuation modelling.

---

# 2. Complete Immobilization Instead of Simple Slowdown

## Previous Version

```gaml
speed <- base_speed * slow_factor;
```

The victim was only slowed down.

---

## New Version

```gaml
base_speed <- 0.0;
```

The victim completely stops.

---

## Consequences

This change allows:

* full blockage formation,
* local jams,
* compression zones,
* realistic crowd congestion.

The crowd is no longer continuously fluid.

---

# 3. Dynamic Shape Change During Immobilization

## Added Code

```gaml
shape <- rectangle(1.0,1.0) rotated_by heading;
```

## Previous Shape

```gaml
shape <- rectangle(0.35,0.6) rotated_by heading;
```

---

## Explanation

During immobilization, the victim occupies more space.

This simulates:

* fallen people,
* blocking individuals,
* reduced passability,
* physical obstruction.

---

## Importance

This modification is critical because it introduces:

* dynamic bottlenecks,
* crowd compression,
* realistic obstruction effects.

Agents now physically interfere with the movement of others.

---

# 4. Slow Timer Mechanism

## Added Code

```gaml
slow_timer <- slow_timer - step;
```

## Explanation

The stop duration decreases over time.

Simulation step:

```gaml
step <- 0.5#s;
```

Therefore:

* each cycle reduces the timer by 0.5 seconds,
* agents recover movement automatically.

---

# 5. Recovery After Immobilization

## Added Code

```gaml
} else {
    base_speed <- gauss(0.8,0.3);
    shape <- rectangle(0.35,0.6) rotated_by heading;
}
```

## Explanation

After stopping:

* agents recover movement,
* their normal shape is restored,
* movement resumes.

---

# 6. Congestion System Modification

## Previous Version

```gaml
speed <- max([0.1,base_speed * (1 - victims_around / coeff_congestion)]);
```

Minimum speed was always `0.1`.

Agents could never fully stop.

---

## New Version

```gaml
speed <- max([0.0,base_speed * (1 - victims_around / coeff_congestion)]);
```

Agents can now fully stop.

---

## Consequences

This enables:

* complete traffic jams,
* exit blockage,
* crushing zones,
* local movement collapse,
* domino effects.

This is a major improvement for emergency crowd simulation realism.

---

# 7. New Emergent Phenomena

The modifications allow the model to generate new emergent behaviours:

| Behaviour                | Previously Possible |
| ------------------------ | ------------------- |
| full crowd blockage      | partially           |
| exit clogging            | weak                |
| chain stop effects       | no                  |
| domino congestion        | no                  |
| crowd collapse           | no                  |
| dynamic bottlenecks      | limited             |
| sudden casualty increase | limited             |

---

# 8. Research Importance

These changes improve the model in several scientific areas:

* evacuation modelling,
* crowd dynamics,
* panic simulation,
* emergency architecture,
* behavioural modelling,
* neuroinclusive architecture research.

The new congestion logic makes the simulation significantly closer to real-world crowd disasters.

---

# 9. Potential Limitation

Current code:

```gaml
base_speed <- gauss(0.8,0.3);
```

is recalculated repeatedly.

This may create unstable movement oscillations.

A more stable solution could be:

```gaml
speed <- base_speed;
```

or limiting minimum values.

---

# 10. Summary

The introduced modifications significantly increase the behavioural realism of the simulation.

The model now reproduces:

* localized congestion,
* temporary immobilization,
* dynamic bottlenecks,
* crowd compression,
* stop-wave propagation,
* evacuation instability.

These mechanisms are highly relevant for:

* fire evacuation studies,
* crowd safety research,
* architectural simulations,
* emergency planning,
* neuroinclusive spatial analysis.
