# 04 — Heating

## Purpose

Heating is the stage where the minimised molecular system is gradually brought from a low starting temperature to the target simulation temperature.

After energy minimisation, the system has improved geometry but it does not yet represent a realistic molecular dynamics trajectory. Heating introduces atomic motion in a controlled way before equilibration and production molecular dynamics.

The aim is to avoid shocking the system by immediately starting full-temperature dynamics.

---

## Why Heating Matters

A newly minimised system may still contain local strain or imperfect solvent organisation. If the simulation is started too aggressively, the system can become unstable.

Heating helps to:

* gradually introduce atomic velocities
* bring the system toward the target temperature
* reduce the risk of early simulation instability
* allow solvent and ions to begin relaxing around the restrained solute
* prepare the system for pressure equilibration

Heating is therefore a transition between static minimisation and dynamic equilibration.

---

## Basic Heating Input File

A basic AMBER heating input file is:

```text
Heating

&cntrl
imin=0
irest=0
ntx=1
ntb=1
ntt=3
gamma_ln=1.0
tempi=0.0
temp0=300.0
dt=0.002
nstlim=50000
ntpr=100
ntwx=500
ntr=1
restraint_wt=10.0
restraintmask='!:WAT,Na+,Cl-'
/
```

This example heats the system from 0 K to 300 K over 50,000 steps using a 2 fs timestep.

---

## Meaning of Key Parameters

| Parameter                       | Meaning                                                             |
| ------------------------------- | ------------------------------------------------------------------- |
| `imin=0`                        | Runs molecular dynamics rather than minimisation                    |
| `irest=0`                       | Starts a new MD run rather than restarting a previous one           |
| `ntx=1`                         | Reads coordinates but not velocities from the input coordinate file |
| `ntb=1`                         | Uses constant volume periodic boundary conditions                   |
| `ntt=3`                         | Uses Langevin thermostat temperature control                        |
| `gamma_ln=1.0`                  | Collision frequency for Langevin dynamics                           |
| `tempi=0.0`                     | Initial temperature                                                 |
| `temp0=300.0`                   | Target temperature                                                  |
| `dt=0.002`                      | Timestep of 0.002 ps, equal to 2 fs                                 |
| `nstlim=50000`                  | Number of MD steps                                                  |
| `ntpr=100`                      | Prints energy information every 100 steps                           |
| `ntwx=500`                      | Writes coordinates to the trajectory every 500 steps                |
| `ntr=1`                         | Turns on positional restraints                                      |
| `restraint_wt=10.0`             | Sets the restraint strength                                         |
| `restraintmask='!:WAT,Na+,Cl-'` | Applies restraints to everything except water and ions              |

---

## Simulation Length

The length of the heating run is determined by:

```text
simulation time = dt × nstlim
```

For this example:

```text
0.002 ps × 50000 = 100 ps
```

So this heating stage runs for 100 ps.

---

## Why `irest=0` and `ntx=1` Are Used

For the first molecular dynamics stage after minimisation, the simulation is started as a new MD run.

```text
irest=0
ntx=1
```

This means AMBER reads the coordinates from the minimised structure but does not continue from old velocities.

New velocities are assigned according to the starting temperature and heating setup.

---

## Temperature Control

The target temperature is set using:

```text
temp0=300.0
```

The initial temperature is set using:

```text
tempi=0.0
```

This means the system is heated from 0 K toward 300 K.

The thermostat is controlled using:

```text
ntt=3
gamma_ln=1.0
```

Here, `ntt=3` selects Langevin temperature control, and `gamma_ln` sets the collision frequency.

---

## Why Restraints Are Used During Heating

During heating, restraints are often applied to the protein or non-solvent atoms. This helps prevent the protein structure from moving too much while the solvent and ions begin to relax.

In this example:

```text
restraintmask='!:WAT,Na+,Cl-'
```

the restrained atoms are everything except water and ions.

This means water and ions can move more freely, while the protein remains restrained during early heating.

---

## Running Heating with `sander`

The heating stage can be run using:

```bash
sander -O -i heat.in -p protein_solvated.prmtop -c min.rst -o heat.out -r heat.rst -x heat.nc -inf heat.mdinfo
```

Here:

| Flag                         | Purpose                                         |
| ---------------------------- | ----------------------------------------------- |
| `-O`                         | Allows AMBER to overwrite existing output files |
| `-i heat.in`                 | Heating input control file                      |
| `-p protein_solvated.prmtop` | AMBER topology file                             |
| `-c min.rst`                 | Starting coordinates from minimisation          |
| `-o heat.out`                | Full heating output file                        |
| `-r heat.rst`                | Restart file after heating                      |
| `-x heat.nc`                 | Trajectory file                                 |
| `-inf heat.mdinfo`           | Short progress information file                 |

---

## Checking the Output

Useful commands after heating include:

```bash
ls -lh
cat heat.mdinfo
tail heat.out
```

The output should be checked to confirm that the heating stage completed successfully and that the temperature increased as expected.

---

## Important Output Files

After heating, important files include:

```text
heat.rst
heat.out
heat.nc
heat.mdinfo
```

The `heat.rst` file contains the coordinates and velocities after heating. This is usually used as the starting point for equilibration.

The `heat.out` file contains detailed output information.

The `heat.nc` file contains the saved trajectory frames.

The `heat.mdinfo` file gives a shorter summary of the run.

---

## Common Mistakes I Want to Avoid

### Confusing minimisation with molecular dynamics

In minimisation, `imin=1`.

In heating, `imin=0`, because actual molecular dynamics is being performed.

### Using restart settings incorrectly

For the first MD stage after minimisation, `irest=0` and `ntx=1` are used because the simulation is starting fresh from coordinates rather than continuing from previous velocities.

Later stages often use:

```text
irest=1
ntx=5
```

because they continue from a previous restart file containing coordinates and velocities.

### Forgetting the timestep calculation

With:

```text
dt=0.002
nstlim=50000
```

the simulation length is 100 ps, because `dt` is measured in picoseconds.

### Forgetting restraints during early heating

Heating an unrestrained system too early may allow large structural changes before the solvent and ions have relaxed properly.

### Confusing `ntpr` and `ntwx`

`ntpr` controls how often energy information is printed.

`ntwx` controls how often coordinates are written to the trajectory.

---

## Key Takeaway

Heating gradually brings the minimised system to the target simulation temperature under controlled conditions. It introduces dynamics while reducing the risk of instability before equilibration and production molecular dynamics.

---

## Research Data Note

This file uses generic example filenames and educational commands only. Research-specific systems, unpublished coordinates, trajectories, output files, and lab-owned scripts should not be uploaded without permission.
