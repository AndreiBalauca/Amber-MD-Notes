# 01 — Protein Preparation

## Purpose

Protein preparation is the first stage of my AMBER molecular dynamics workflow. The aim is to take a raw protein structure and convert it into a cleaned structure that can be loaded into `tleap` and prepared for simulation.

This step matters because molecular dynamics depends heavily on the quality of the starting structure. If the input structure contains problems, those problems can carry through into minimisation, heating, equilibration, and production MD.

---

## Why Protein Preparation Matters

A raw PDB file is not always ready for molecular dynamics. It may contain issues such as:

* missing hydrogen atoms
* crystallographic waters
* alternative atom positions
* missing residues or atoms
* non-standard residue names
* ligands or cofactors that need separate treatment
* incorrect protonation states
* chain breaks

Before starting a simulation, the structure should be inspected and cleaned rather than treated as automatically correct.

---

## Basic Workflow

First, I activate the AmberTools environment and move into my project directory:

```bash
conda activate AmberTools26
pwd
cd ~/path/to/project
```

I then clean the original PDB file using `pdb4amber`:

```bash
pdb4amber -i protein.pdb -o protein_clean.pdb
```

In this command:

* `-i protein.pdb` is the input structure
* `-o protein_clean.pdb` is the cleaned output structure

After this, I start `tleap`:

```bash
tleap
```

Inside `tleap`, I load the protein force field:

```text
source leaprc.protein.ff19SB
```

Then I load the cleaned PDB file with loadPDB:

```text
protein = loadPDB protein_clean.pdb
```

I always check the system before saving files:

```text
check protein
```

If the structure is acceptable, I save the AMBER topology and coordinate files:

```text
saveAmberParm protein protein.prmtop protein.inpcrd
quit
```

---

## Important Files

The main files produced at this stage are:

```text
protein.prmtop
protein.inpcrd
```

The `.prmtop` file contains the topology and force field information for the system.

The `.inpcrd` file contains the starting coordinates.

These files are needed before running AMBER simulation programs such as `sander` or `pmemd`.

---

## Key Commands

| Command                        | Purpose                                    |
| ------------------------------ | ------------------------------------------ |
| `conda activate AmberTools26`  | Activates the AmberTools environment       |
| `pdb4amber`                    | Cleans a PDB file for AMBER                |
| `tleap`                        | Builds AMBER topology and coordinate files |
| `source leaprc.protein.ff19SB` | Loads the AMBER protein force field        |
| `loadPDB`                      | Loads the cleaned protein structure        |
| `check protein`                | Checks the system for problems             |
| `saveAmberParm`                | Saves topology and coordinate files        |
| `quit`                         | Exits `tleap`                              |

---

## Common Mistakes I Want to Avoid

### Skipping structure inspection

A PDB file should not be assumed to be simulation-ready. Cleaning and checking the structure is part of the scientific workflow, not just a technical step.

### Ignoring `check protein`

The `check` command is important because it can reveal problems before simulation files are saved. Errors at this stage should be understood rather than ignored.

### Confusing file types

The cleaned `.pdb` file is not the final AMBER simulation input. AMBER simulations need `.prmtop` and `.inpcrd` files.

### Memorising commands without understanding them

It is not enough to remember the commands. I also need to understand what each command does and why it is used.

---

## Key Takeaway

Protein preparation converts a raw structural file into a chemically sensible system that AMBER can understand. A careful preparation step reduces the chance of errors later during minimisation, heating, equilibration, and production molecular dynamics.

---

## Research Data Note

This file uses generic example filenames only. Research-specific structures, unpublished systems, trajectories, analysis outputs, and lab-owned files should not be uploaded without permission.
