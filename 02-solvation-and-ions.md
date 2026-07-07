# 02 — Solvation and Ion Addition

## Purpose

After protein preparation, the next stage is to place the protein in a solvent environment and add ions where needed. In most biomolecular molecular dynamics simulations, the protein is simulated in water rather than in vacuum.

The aim of this step is to create a realistic solvated system that can later be minimised, heated, equilibrated, and used for production molecular dynamics.

---

## Why Solvation Matters

Proteins behave differently in water compared with vacuum. Water affects:

* protein stability
* hydrogen bonding
* electrostatic interactions
* conformational flexibility
* ion interactions
* solvent-exposed residues

For most biological systems, including soluble proteins and membrane proteins, the environment around the protein is essential for realistic molecular dynamics.

---

## Loading the Water Model

Inside `tleap`, the protein force field is loaded first:

```text
source leaprc.protein.ff19SB
```

The cleaned protein structure is then loaded:

```text
protein = loadPDB protein_clean.pdb
```

To solvate the system, a water model must also be loaded. For many AMBER workflows, TIP3P water is commonly used:

```text
source leaprc.water.tip3p
```

---

## Solvating the Protein

A simple solvated box can be created using:

```text
solvateBox protein TIP3PBOX 15.0
```

In this command:

* `protein` is the system being solvated
* `TIP3PBOX` specifies the water box type
* `15.0` gives the distance between the solute and the edge of the water box in Ångström

The water box provides a solvent environment around the protein.

---

## Why Box Size Matters

The water box should be large enough that the protein does not interact unrealistically with its own periodic image during simulation.

A small water box can cause artefacts because molecular dynamics simulations usually use periodic boundary conditions.

A larger buffer gives the protein more realistic solvent surroundings, but also increases the number of atoms and computational cost.

---

## Checking the System Charge

Before adding ions, the total charge of the system should be checked:

```text
charge protein
```

This helps determine whether counterions are needed to neutralise the system.

---

## Adding Counterions

If the system has a positive total charge, chloride ions can be added:

```text
addIons protein Cl- 0
```

If the system has a negative total charge, sodium ions can be added:

```text
addIons protein Na+ 0
```

The `0` means that enough ions are added to neutralise the system.
Important: values such as `1` or `2` do **not** mean “neutralise until the system has a net charge of +1 or +2”. They specify the exact number of ions to add.

For example:

- `addIons protein Na+ 0` adds enough sodium ions to neutralise a negatively charged system.
- `addIons protein Na+ 2` adds exactly two sodium ions.

---

## Checking the Final System

After solvation and ion addition, the system should be checked again:

```text
check protein
```

This helps identify possible problems before saving the AMBER topology and coordinate files.

The final system can then be saved:

```text
saveAmberParm protein protein_solvated.prmtop protein_solvated.inpcrd
quit
```

---

## Important Output Files

After solvation and ion addition, the main output files are:

```text
protein_solvated.prmtop
protein_solvated.inpcrd
```

The `.prmtop` file stores the topology and force field parameters for the full solvated system.

The `.inpcrd` file stores the coordinates of the protein, water molecules, and ions.

These files are used as input for energy minimisation.

---

## Key Commands

| Command                            | Purpose                                                             |
| ---------------------------------- | ------------------------------------------------------------------- |
| `source leaprc.water.tip3p`        | Loads the TIP3P water model                                         |
| `solvateBox protein TIP3PBOX 15.0` | Places the protein in a water box                                   |
| `charge protein`                   | Checks the total system charge                                      |
| `addIons protein Na+ 0`            | Adds sodium counterions to neutralise a negatively charged system   |
| `addIons protein Cl- 0`            | Adds chloride counterions to neutralise a positively charged system |
| `check protein`                    | Checks the solvated system                                          |
| `saveAmberParm`                    | Saves the solvated topology and coordinate files                    |

---

## Common Mistakes I Want to Avoid

### Forgetting to load the water model

If the water model is not loaded, `tleap` may not recognise the solvent box correctly.

### Adding the wrong ion type

The ion added depends on the system charge. A positively charged system needs negative counterions, while a negatively charged system needs positive counterions.

### Ignoring system size

A larger water box is more realistic but computationally more expensive. The choice of box size affects the number of atoms and therefore the simulation cost.

### Forgetting to check the system after ion addition

The system should be checked again after solvation and ion addition, not only after loading the protein.

---

## Key Takeaway

Solvation and ion addition convert the prepared protein into a more physically realistic simulation system. This step ensures that the protein is surrounded by water and that the system charge is handled before energy minimisation.

---

## Research Data Note

This file uses generic example filenames and educational commands only. Research-specific systems, unpublished structures, membrane setups, trajectories, and lab-owned files should not be uploaded without permission.
