Lysozyme tutorial from http://www.mdtutorials.com/gmx/lysozyme/01_pdb2gmx.html

1. Download PDB file 1AKI

2. Strip water from 1AKI:
```
grep -h HOH 1aki.pdb > 1AKI_clean.pdb
```

3. pdb2gmx will generate topology from molecule.
```
gmx pdb2gmx -f 1AKI_clean.pdb -o 1AKI_processed.gro -water spce
```

## STEP TWO: EXAMINE topology

From topol.top file

```
#include "oplsaa.ff/forcefield.itp"
```

takes parameters from OPLS-AA force field.

[atoms]
has three columns:
nr: Atom number
type: Atom type
resnr: amino acid residue number
atom: atom name
cgnr: Charge group number
charge:
mass:
typeB, chargeB, massB: free energy perturbation (not discussed here)

## STEP THREE: DEFINE BOX AND SOLVATE
##### 1. define the box dimensions and fill it with solvent
Rhombic dodecahedron is recommended, volume is 71% of cube, saves number of water molecules

Define box with editconf:
```
gmx editconf -f 1AKI_processed.gro -o 1AKI_newbox.gro -c -d 1.0 -bt cubic
```

Where ` -c` centers protein in box, places it 1.0 nm from box edge `-d 1.0`. Box is defined as cubic `-bt cubic`.

To solvate:
```
gmx solvate -cp 1AKI_newbox.gro -cs spc216.gro -o 1AKI_solv.gro -p topol.top
```

where `-cp` is configuration from previous step

## STEP four: Add Ions

In topol.top, last line has `qtot 8`, meaning net charge of protein is $+8e-$
