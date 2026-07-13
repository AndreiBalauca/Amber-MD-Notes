# AMBER Command Reference

## Purpose

This file is a compact command reference for the AMBER molecular dynamics workflow. It is intended as a quick reminder of commonly used commands, files, and flags during system preparation, minimisation, heating, equilibration, production MD, and trajectory analysis.

---

## 1. Activate AmberTools

```bash
conda activate AmberTools26
```

Activates the AmberTools environment.

Useful checks:

```bash
pwd
ls -lh
```

| Command | Purpose |
|---|---|
| `pwd` | Shows the current working directory |
| `ls -lh` | Lists files with readable file sizes |

---

## 2. Protein Preparation

Clean a PDB file:

```bash
pdb4amber -i protein.pdb -o protein_clean.pdb
```

| Flag | Purpose |
|---|---|
| `-i protein.pdb` | Input PDB file |
| `-o protein_clean.pdb` | Cleaned output PDB file |

---

## 3. Start `tleap`

```bash
tleap
```

Load the protein force field:

```text
source leaprc.protein.ff19SB
```

Load the cleaned protein:

```text
protein = loadPDB protein_clean.pdb
```

Check the system:

```text
check protein
```

---

## 4. Solvation and Ion Addition

Load the water model:

```text
source leaprc.water.tip3p
```

Solvate the system:

```text
solvateBox protein TIP3PBOX 15.0
```

Check total charge:

```text
charge protein
```

Add ions to neutralise the system:

```text
addIons protein Na+ 0
```

or:

```text
addIons protein Cl- 0
```

Save AMBER topology and coordinates:

```text
saveAmberParm protein protein_solvated.prmtop protein_solvated.inpcrd
quit
```

| Command | Purpose |
|---|---|
| `source leaprc.water.tip3p` | Loads TIP3P water model |
| `solvateBox protein TIP3PBOX 15.0` | Adds a 15 Å water box |
| `charge protein` | Checks system charge |
| `addIons protein Na+ 0` | Adds enough Na⁺ ions to neutralise a negatively charged system |
| `addIons protein Cl- 0` | Adds enough Cl⁻ ions to neutralise a positively charged system |
| `saveAmberParm` | Saves `.prmtop` and `.inpcrd` files |

---

## 5. Create Input Files with `nano`

Minimisation:

```bash
nano min.in
```

Heating:

```bash
nano heat.in
```

Equilibration:

```bash
nano eq.in
```

Production:

```bash
nano prod.in
```

In `nano`:

| Shortcut | Purpose |
|---|---|
| `Ctrl + O` | Save file |
| `Enter` | Confirm filename |
| `Ctrl + X` | Exit |

---

## 6. Run Minimisation

```bash
sander -O -i min.in -p protein_solvated.prmtop -c protein_solvated.inpcrd -r min.rst -o min.out -inf min.mdinfo
```

| Flag | Purpose |
|---|---|
| `-O` | Overwrite existing output files |
| `-i min.in` | Input file |
| `-p protein_solvated.prmtop` | Topology file |
| `-c protein_solvated.inpcrd` | Starting coordinates |
| `-r min.rst` | Output restart file |
| `-o min.out` | Output log |
| `-inf min.mdinfo` | Short progress file |

---

## 7. Run Heating

```bash
sander -O -i heat.in -p protein_solvated.prmtop -c min.rst -o heat.out -r heat.rst -x heat.nc -inf heat.mdinfo
```

| Flag | Purpose |
|---|---|
| `-i heat.in` | Heating input file |
| `-c min.rst` | Restart file from minimisation |
| `-o heat.out` | Heating output log |
| `-r heat.rst` | Restart file after heating |
| `-x heat.nc` | Heating trajectory |
| `-inf heat.mdinfo` | Short progress file |

---

## 8. Run Equilibration

```bash
sander -O -i eq.in -p protein_solvated.prmtop -c heat.rst -o eq.out -r eq.rst -x eq.nc -inf eq.mdinfo
```

| Flag | Purpose |
|---|---|
| `-i eq.in` | Equilibration input file |
| `-c heat.rst` | Restart file from heating |
| `-o eq.out` | Equilibration output log |
| `-r eq.rst` | Restart file after equilibration |
| `-x eq.nc` | Equilibration trajectory |
| `-inf eq.mdinfo` | Short progress file |

---

## 9. Run Production MD

```bash
sander -O -i prod.in -p protein_solvated.prmtop -c eq.rst -o prod.out -r prod.rst -x prod.nc -inf prod.mdinfo
```

| Flag | Purpose |
|---|---|
| `-i prod.in` | Production input file |
| `-c eq.rst` | Restart file from equilibration |
| `-o prod.out` | Production output log |
| `-r prod.rst` | Restart file after production |
| `-x prod.nc` | Production trajectory |
| `-inf prod.mdinfo` | Short progress file |

---

## 10. Check Output Files

After each run:

```bash
ls -lh
cat min.mdinfo
tail min.out
```

For other stages:

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

| Command | Purpose |
|---|---|
| `ls -lh` | Check output files were created |
| `cat *.mdinfo` | View short progress summary |
| `tail *.out` | Check the end of the output log |

---

## 11. Start `cpptraj`

```bash
cpptraj protein_solvated.prmtop
```

Load trajectory:

```text
trajin prod.nc
```

Image trajectory:

```text
autoimage
```

Write processed trajectory:

```text
trajout prod_imaged.nc
run
quit
```

---

## 12. Common `cpptraj` Examples

RMSD:

```text
trajin prod.nc
autoimage
rms first :1-999@CA out rmsd.dat
run
quit
```

RMSF:

```text
trajin prod.nc
autoimage
rms first :1-999@CA
atomicfluct :1-999@CA byres out rmsf.dat
run
quit
```

Distance:

```text
trajin prod.nc
autoimage
distance d1 :100@CA :200@CA out distance.dat
run
quit
```

Hydrogen bonds:

```text
trajin prod.nc
autoimage
hbond HB out hbonds.dat
run
quit
```

Note: residue ranges such as `:1-999@CA` are placeholders and should be changed for the system being analysed.

---

## 13. Important File Types

| File | Meaning |
|---|---|
| `.pdb` | Protein structure file |
| `.prmtop` | AMBER topology file |
| `.inpcrd` | AMBER coordinate file |
| `.in` | AMBER input control file |
| `.rst` | Restart file |
| `.out` | Output log |
| `.mdinfo` | Short progress file |
| `.nc` | NetCDF trajectory file |
| `.dat` | Analysis output data |

---

## 14. Key Parameter Reminders

| Parameter | Meaning |
|---|---|
| `imin=1` | Run minimisation |
| `imin=0` | Run molecular dynamics |
| `irest=0` | Start new MD run |
| `irest=1` | Restart/continue MD run |
| `ntx=1` | Read coordinates only |
| `ntx=5` | Read coordinates and velocities |
| `ntb=1` | Constant volume periodic boundary conditions |
| `ntb=2` | Constant pressure periodic boundary conditions |
| `ntp=1` | Isotropic pressure scaling |
| `ntt=3` | Langevin thermostat |
| `dt=0.002` | 2 fs timestep |
| `ntr=1` | Turn on restraints |
| `ntr=0` | No restraints |
| `ntpr` | Energy print frequency |
| `ntwx` | Trajectory write frequency |
| `ntwr` | Restart write frequency |

---

## Key Takeaway

This command reference is a quick reminder of the AMBER workflow. It should be used together with the detailed notes for each stage, where the purpose of the commands and parameters is explained.
