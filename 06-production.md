# 06 — Production Molecular Dynamics

## Purpose

Production molecular dynamics is the main simulation stage used to collect trajectory data for analysis.

After minimisation, heating, and equilibration, the system should be stable enough to run under the intended simulation conditions.

The production trajectory can then be used to study structural stability, conformational changes, interactions, and system behaviour over time.

---

## Why Production MD Matters

Production MD is where data is collected.

It can be used to analyse:

- protein stability
- residue flexibility
- ligand or lipid interactions
- hydrogen bonds
- distances between residues
- conformational changes
- trajectory clustering
- representative structures

Production MD should only be interpreted after checking that the system was properly prepared and equilibrated.

---

## Creating the Input File

I usually create the production input file using `nano`:

```bash
nano prod.in
```

In `nano`:

- `Ctrl + O` saves
- `Enter` confirms
- `Ctrl + X` exits

---

## Basic Production Input File

```text
Production MD

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
nstlim=500000
dt=0.002
ntpr=1000
ntwx=1000
ntwr=1000
ntr=0
/
```

This example continues from the equilibration restart file and runs production MD without positional restraints.

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
| `nstlim=500000` | Number of MD steps |
| `ntpr=1000` | Print energy every 1000 steps |
| `ntwx=1000` | Write trajectory every 1000 steps |
| `ntwr=1000` | Write restart file every 1000 steps |
| `ntr=0` | No positional restraints |

---

## Simulation Length

The simulation length is calculated as:

```text
simulation time = dt × nstlim
```

For this example:

```text
0.002 ps × 500000 = 1000 ps = 1 ns
```

Longer simulations can be run by increasing `nstlim`.

For example:

```text
0.002 ps × 6000000 = 12000 ps = 12 ns
```

---

## Running Production MD

```bash
sander -O -i prod.in -p protein_solvated.prmtop -c eq.rst -o prod.out -r prod.rst -x prod.nc -inf prod.mdinfo
```

| Flag | Purpose |
|---|---|
| `-O` | Overwrite existing output files |
| `-i prod.in` | Production input file |
| `-p protein_solvated.prmtop` | Topology file |
| `-c eq.rst` | Restart file from equilibration |
| `-o prod.out` | Output log |
| `-r prod.rst` | Restart file after production |
| `-x prod.nc` | Production trajectory |
| `-inf prod.mdinfo` | Short progress file |

---

## Checking the Output

```bash
ls -lh
cat prod.mdinfo
tail prod.out
```

Check that the run completed successfully and that temperature, pressure, and energy behaved reasonably.

---

## Important Output Files

| File | Purpose |
|---|---|
| `prod.rst` | Final restart file |
| `prod.out` | Full production output log |
| `prod.nc` | Production trajectory |
| `prod.mdinfo` | Short run summary |

---

## Common Mistakes

- Starting production before proper equilibration
- Forgetting that production is the data collection stage
- Interpreting one short trajectory too strongly
- Not checking temperature, pressure, or energy stability
- Saving trajectory frames too frequently or too rarely
- Confusing restart files with trajectory files
- Uploading real research trajectories publicly

---

## Key Takeaway

Production MD is the main data collection stage of the simulation workflow. The trajectory generated here should only be interpreted after confirming that the system was properly prepared, heated, equilibrated, and checked.

---

## Research Data Note

This file uses generic example filenames and educational commands only. Research-specific systems, unpublished coordinates, trajectories, production outputs, analysis results, and lab-owned scripts should not be uploaded without permission.
