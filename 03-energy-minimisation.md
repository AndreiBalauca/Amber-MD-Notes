# 03 — Energy Minimisation

## Purpose

Energy minimisation is usually performed before molecular dynamics begins. The aim is to relax bad contacts and reduce severe steric clashes in the prepared system before heating and equilibration.

After protein preparation, solvation, and ion addition, the system may contain unfavourable atomic contacts. These can occur because water molecules and ions are placed around the protein computationally rather than through a physical relaxation process.

Energy minimisation helps create a more stable starting point for molecular dynamics.

---

## Why Energy Minimisation Matters

A freshly prepared system is not automatically ready for dynamics. If severe clashes remain, the simulation may become unstable during heating or early equilibration.

Energy minimisation can help reduce:

* steric clashes
* unrealistic bond or angle strain
* poor solvent placement
* bad contacts between protein, water, and ions
* high initial potential energy

This step does not generate a realistic trajectory. It is a preparatory step used to improve the starting geometry.

---

## Basic Minimisation Input File

A basic AMBER minimisation input file is:

```text
Minimisation

&cntrl
imin=1
maxcyc=5000
ncyc=2500
ntpr=100
ntr=1
restraint_wt=10.0
restraintmask='!:WAT,Na+,Cl-'
/
```

This file tells AMBER to perform energy minimisation with positional restraints on the non-solvent part of the system.

---

## Meaning of Key Parameters

| Parameter                       | Meaning                                                |
| ------------------------------- | ------------------------------------------------------ |
| `imin=1`                        | Runs energy minimisation instead of molecular dynamics |
| `maxcyc=5000`                   | Total number of minimisation cycles                    |
| `ncyc=2500`                     | Switches minimisation method after 2500 cycles         |
| `ntpr=100`                      | Prints energy information every 100 cycles             |
| `ntr=1`                         | Turns on positional restraints                         |
| `restraint_wt=10.0`             | Sets the restraint strength                            |
| `restraintmask='!:WAT,Na+,Cl-'` | Applies restraints to everything except water and ions |

---

## Steepest Descent and Conjugate Gradient

In AMBER minimisation, the early cycles often use steepest descent minimisation. This is useful for removing severe clashes because it is robust when the starting structure is far from ideal.

After `ncyc`, AMBER switches to conjugate gradient minimisation. This is more efficient once the worst clashes have been reduced.

For example:

```text
maxcyc=5000
ncyc=2500
```

This means:

* 5000 total minimisation cycles
* first 2500 cycles use steepest descent
* remaining 2500 cycles use conjugate gradient

---

## Why Restraints Are Used

During early minimisation, restraints are often applied to the protein or other non-solvent atoms. This allows the solvent and ions to relax around the structure without allowing the protein to move too much too early.

In this example:

```text
restraintmask='!:WAT,Na+,Cl-'
```

The `!` means “not”.

So the mask means:

```text
restrain everything that is not water, sodium ions, or chloride ions
```

This allows water and ions to move while keeping the protein restrained.

---

## Running Minimisation with `sander`

The minimisation can be run using:

```bash
sander -O -i min.in -p protein_solvated.prmtop -c protein_solvated.inpcrd -r min.rst -inf min.mdinfo -o min.out
The optional `-O` flag can be added to overwrite existing output files from a previous run.
```

Here:

| Flag                         | Purpose                                           |
| ---------------------------- | ------------------------------------------------- |
| `-i min.in`                  | Input control file                                |
| `-p protein_solvated.prmtop` | AMBER topology file                               |
| `-c protein_solvated.inpcrd` | Starting coordinate file                          |
| `-r min.rst`                 | Restart/output coordinate file after minimisation |
| `-inf min.mdinfo`            | Short progress information file                   |
| `-o min.out`                 | Full output file                                  |
| `-O` | Allows AMBER to overwrite existing output files |

---

## Checking the Output

Useful commands after minimisation include:

```bash
ls -lh
cat min.mdinfo
tail min.out
```

These commands help check whether output files were created and whether the minimisation completed successfully.

---

## Important Output Files

After minimisation, important files include:

```text
min.rst
min.out
min.mdinfo
```

The `min.rst` file contains the minimised coordinates and is usually used as the starting structure for heating.

The `min.out` file contains detailed minimisation output.

The `min.mdinfo` file gives a shorter summary of the run.

---

## Common Mistakes I Want to Avoid

### Forgetting that minimisation is not MD

Energy minimisation does not simulate time. It relaxes the structure to reduce unfavourable contacts before molecular dynamics begins.

### Forgetting restraints

For early minimisation, restraints can help prevent the protein from moving too much while solvent and ions relax.

### Writing the restraint mask incorrectly

The restraint mask must be written carefully. Extra spaces or incorrect punctuation can cause errors or change what atoms are restrained.

For example:

```text
restraintmask='!:WAT,Na+,Cl-'
```

is cleaner than:

```text
restraintmask='!: WAT, Na+,Cl-'
```

### Confusing input and output coordinate files

The `-c` flag gives the starting coordinates.

The `-r` flag writes the restart file after minimisation.

---

## Key Takeaway

Energy minimisation relaxes the prepared solvated system before molecular dynamics begins. It reduces severe clashes and creates a more stable starting point for heating, equilibration, and production MD.

---

## Research Data Note

This file uses generic example filenames and educational commands only. Research-specific systems, unpublished coordinates, trajectories, output files, and lab-owned scripts should not be uploaded without permission.
