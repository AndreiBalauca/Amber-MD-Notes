# 05 — Equilibration

## Purpose

Equilibration is performed after heating and before production MD. The aim is to let the system settle under the intended simulation conditions before collecting data for analysis.

After heating, the system may have reached the target temperature, but pressure, density, solvent organisation, and restraints may still need to stabilise.

---

## Why Equilibration Matters

Equilibration helps:

- stabilise temperature and pressure
- adjust box size and density
- allow solvent and ions to relax
- reduce artefacts from setup
- prepare the system for production MD

Production MD should not usually begin immediately after heating.

---

## Creating the Input File

I usually create the equilibration input file using `nano`:

```bash
nano eq.in
```

In `nano`:

- `Ctrl + O` saves
- `Enter` confirms
- `Ctrl + X` exits

---

## Basic Equilibration Input File

```text
Equilibration

&cntrl
imin=0
irest=1
ntx=5
ntb=2
ntp=1
pres0=1.0
taup=2.0
ntt=3
gamma_ln=1.0
temp0=300.0
dt=0.002
nstlim=50000
ntpr=100
ntwx=500
ntr=1
restraint_wt=5.0
restraintmask='!:WAT,Na+,Cl-'
/
```

This continues from the heating restart file and runs MD under constant pressure conditions.

---

## Key Parameters

| Parameter | Meaning |
|---|---|
| `imin=0` | Run molecular dynamics |
| `irest=1` | Continue from a previous restart |
| `ntx=5` | Read coordinates and velocities |
| `ntb=2` | Constant pressure periodic boundary conditions |
| `ntp=1` | Isotropic pressure scaling |
| `pres0=1.0` | Target pressure of 1 atm |
| `taup=2.0` | Pressure relaxation time |
| `ntt=3` | Langevin thermostat |
| `gamma_ln=1.0` | Langevin collision frequency |
| `temp0=300.0` | Target temperature |
| `dt=0.002` | 2 fs timestep |
| `nstlim=50000` | Number of MD steps |
| `ntpr=100` | Print energy every 100 steps |
| `ntwx=500` | Write trajectory every 500 steps |
| `ntr=1` | Use positional restraints |
| `restraint_wt=5.0` | Restraint strength |
| `restraintmask='!:WAT,Na+,Cl-'` | Restrain everything except water and ions |

---

## Restart Settings

Equilibration usually continues from heating:

```text
irest=1
ntx=5
```

This means AMBER reads both coordinates and velocities from `heat.rst`.

This is different from heating, which usually starts with:

```text
irest=0
ntx=1
```

---

## Constant Pressure

Heating often uses constant volume:

```text
ntb=1
```

Equilibration commonly introduces constant pressure:

```text
ntb=2
ntp=1
pres0=1.0
```

This allows the box size and density to adjust before production MD.

---

## Restraints

Restraints may still be used during equilibration, but they are often reduced compared with heating.

For example:

```text
restraint_wt=5.0
```

is weaker than:

```text
restraint_wt=10.0
```

Some workflows gradually reduce restraints over several equilibration stages.

---

## Running Equilibration

```bash
sander -O -i eq.in -p protein_solvated.prmtop -c heat.rst -o eq.out -r eq.rst -x eq.nc -inf eq.mdinfo
```

| Flag | Purpose |
|---|---|
| `-O` | Overwrite existing output files |
| `-i eq.in` | Equilibration input file |
| `-p protein_solvated.prmtop` | Topology file |
| `-c heat.rst` | Restart file from heating |
| `-o eq.out` | Output log |
| `-r eq.rst` | Restart file after equilibration |
| `-x eq.nc` | Trajectory file |
| `-inf eq.mdinfo` | Short progress file |

---

## Checking the Output

```bash
ls -lh
cat eq.mdinfo
tail eq.out
```

Check that the run completed successfully and that temperature, pressure, and density behaved reasonably.

---

## Important Output Files

| File | Purpose |
|---|---|
| `eq.rst` | Starting point for production MD |
| `eq.out` | Full output log |
| `eq.nc` | Equilibration trajectory |
| `eq.mdinfo` | Short run summary |

---

## Common Mistakes

- Starting production MD too early
- Confusing `irest=1, ntx=5` with heating settings
- Forgetting pressure equilibration
- Removing restraints too quickly
- Assuming equilibration worked without checking output files

---

## Key Takeaway

Equilibration lets the heated system stabilise under production-like conditions before data collection begins.

---

## Research Data Note

This file uses generic example filenames and educational commands only. Research-specific systems, unpublished coordinates, trajectories, output files, and lab-owned scripts should not be uploaded without permission.
