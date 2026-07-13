# 07 — Trajectory Analysis

## Purpose

Trajectory analysis is performed after production molecular dynamics. The aim is to examine the saved trajectory and extract meaningful structural information from the simulation.

Production MD generates many frames over time. These frames need to be analysed carefully before drawing conclusions about protein stability, flexibility, ligand behaviour, or conformational change.

---

## Why Trajectory Analysis Matters

A trajectory is not useful by itself. It must be checked and analysed.

Trajectory analysis can help assess:

- whether the simulation was stable
- how much the protein moved over time
- which regions were flexible
- whether a ligand, lipid, or residue interaction was maintained
- whether the system behaved reasonably
- whether more simulation time or repeats are needed

Numerical analysis should usually be combined with visual inspection.

---

## Common Analysis Tools

| Tool | Use |
|---|---|
| `cpptraj` | AMBER trajectory analysis |
| VMD | Visualising trajectories |
| Python / MDAnalysis | Custom analysis and plotting |
| PyMOL / ChimeraX | Structure inspection and figures |

---

## Important Input Files

Common files used for analysis include:

| File | Purpose |
|---|---|
| `protein_solvated.prmtop` | Topology file |
| `prod.nc` | Production trajectory |
| `prod.rst` | Final restart structure |
| `prod.out` | Production output log |

The topology file is needed so that the trajectory coordinates can be interpreted correctly.

---

## Starting `cpptraj`

A basic `cpptraj` session can be started with:

```bash
cpptraj protein_solvated.prmtop
```

Inside `cpptraj`, the trajectory can be loaded:

```text
trajin prod.nc
```

---

## Imaging the Trajectory

Periodic boundary conditions can cause molecules to appear broken or outside the box. Imaging is often used to make the trajectory easier to analyse and visualise.

Example:

```text
autoimage
```

The processed trajectory can be written using:

```text
trajout prod_imaged.nc
run
quit
```

---

## RMSD Analysis

RMSD measures how much a structure changes compared with a reference structure.

Example:

```text
trajin prod.nc
autoimage
rms first :1-999@CA out rmsd.dat
run
quit
```

Here:

| Command | Meaning |
|---|---|
| `rms first` | Calculates RMSD relative to the first frame |
| `:1-999@CA` | Selects alpha carbons for residues 1–999 |
| `out rmsd.dat` | Writes RMSD values to a file |

The residue range should be changed to match the system being analysed.

---

## RMSF Analysis

RMSF measures how flexible each residue is during the trajectory.

Example:

```text
trajin prod.nc
autoimage
rms first :1-999@CA
atomicfluct :1-999@CA byres out rmsf.dat
run
quit
```

RMSF is useful for identifying flexible loops, termini, or regions that move more during the simulation.

---

## Distance Analysis

Distance analysis can track whether two residues, atoms, or groups stay close during the simulation.

Example:

```text
trajin prod.nc
autoimage
distance d1 :100@CA :200@CA out distance.dat
run
quit
```

This measures the distance between the alpha carbon of residue 100 and the alpha carbon of residue 200.

---

## Hydrogen Bond Analysis

Hydrogen bond analysis can be used to examine interactions between residues, ligands, water molecules, or other groups.

Example:

```text
trajin prod.nc
autoimage
hbond HB out hbonds.dat
run
quit
```

Hydrogen bond results should be interpreted carefully and checked visually where possible.

---

## Visual Inspection in VMD

A trajectory can also be inspected visually in VMD.

Typical checks include:

- whether the protein remains stable
- whether the system looks broken or poorly imaged
- whether a ligand remains in the binding site
- whether water, ions, or lipids behave strangely
- whether there are obvious artefacts

Visual inspection should not replace quantitative analysis, but it is an important quality-control step.

---

## Common Mistakes

- Analysing a trajectory without checking whether the run completed
- Forgetting to image the trajectory
- Using the wrong topology file
- Selecting the wrong residue range
- Interpreting RMSD alone as proof of stability
- Ignoring visual inspection
- Overinterpreting one short trajectory
- Uploading real research trajectories publicly

---

## Key Takeaway

Trajectory analysis converts production MD output into interpretable results. Good analysis combines quantitative metrics, visual inspection, correct atom selections, and cautious interpretation.

---

## Research Data Note

This file uses generic example commands and educational explanations only. Research-specific trajectories, structures, plots, analysis outputs, and lab-owned scripts should not be uploaded without permission.
