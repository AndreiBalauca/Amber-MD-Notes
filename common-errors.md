# Common Errors and Troubleshooting

## Purpose

This file collects common AMBER workflow errors and practical fixes. The aim is to make troubleshooting easier during protein preparation, system setup, minimisation, heating, equilibration, production MD, and trajectory analysis.

---

## 1. Wrong File Type Used

### Problem

Using the wrong file with the wrong AMBER flag.

Example mistake:

```bash
sander -i min.in -p protein.pdb -c protein.inpcrd
```

### Why It Happens

The `-p` flag requires an AMBER topology file, not a PDB file.

### Fix

Use:

```bash
-p protein.prmtop
-c protein.inpcrd
```

Key rule:

| Flag | Expected file |
|---|---|
| `-p` | `.prmtop` topology file |
| `-c` | input coordinate/restart file |
| `-r` | output restart file |
| `-o` | output log file |
| `-x` | trajectory file |

---

## 2. Forgetting `-O`

### Problem

AMBER refuses to overwrite old output files.

### Fix

Add:

```bash
-O
```

Example:

```bash
sander -O -i min.in -p protein.prmtop -c protein.inpcrd -r min.rst -o min.out -inf min.mdinfo
```

`-O` allows AMBER to overwrite existing output files.

---

## 3. Incorrect Restraint Mask

### Problem

The restraint mask is written incorrectly.

Incorrect:

```text
restraintmask='!: WAT, Na+,Cl-'
```

Better:

```text
restraintmask='!:WAT,Na+,Cl-'
```

### Why It Matters

AMBER masks are sensitive to syntax. Extra spaces or incorrect punctuation can cause errors or select the wrong atoms.

---

## 4. Overinterpreting Short Simulations

### Problem

Making strong conclusions from one short production run.

### Why It Matters

Short trajectories can be useful for testing setup conditions, but they are not usually enough for strong mechanistic claims.

### Better Approach

Use short simulations for:

- checking stability
- testing setup conditions
- identifying obvious problems
- practising analysis workflows

Use longer or repeated simulations before making stronger conclusions.

---

## 5. Not Checking Output Files

### Problem

Assuming a job worked just because output files were created.

### Fix

After each stage, check:

```bash
ls -lh
cat mdinfo_file
tail output_file
```

Examples:

```bash
cat min.mdinfo
tail min.out
```

```bash
cat heat.mdinfo
tail heat.out
```

```bash
cat eq.mdinfo
tail eq.out
```

```bash
cat prod.mdinfo
tail prod.out
```

---

## 6. Uploading Research Data Publicly

### Problem

Accidentally uploading unpublished or confidential research files.

### Do Not Upload

- unpublished structures
- lab-owned input files
- trajectories
- topology/coordinate files from research systems
- production outputs
- analysis plots from unpublished projects
- HPC scripts from the lab unless approved

### Safe to Upload

- generic educational notes
- public example workflows
- general command explanations
- placeholder filenames
- references to official documentation

---

## Key Takeaway

Most AMBER errors come from file confusion, syntax mistakes, incorrect masks, overinterpreting short runs, or not checking outputs carefully.

Troubleshooting should be systematic: check the input files, check the command, check the output, and confirm that each stage uses the correct files from the previous stage.
