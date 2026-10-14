# MOF DFT Analysis

DFT-based analysis of metal-organic frameworks (MOFs) using VASP, covering hydrogen addition and Bader charge analysis of water adsorption.

## Repository structure

```
MOF-DFT-analysis/
├── README.md
├── notebooks/
│   ├── mof_hydrogen_addition.ipynb
│   └── bader_charge_analysis.ipynb
├── data/
│   ├── cuta/
│   │   ├── POSCAR_hydr
│   │   ├── POSCAR_dehy
│   │   ├── ACF_hydr_CuTA.dat
│   │   └── ACF_dehy_CuTA.dat
│   └── mgta/
│       ├── POSCAR_hydr
│       ├── POSCAR_dehy
│       ├── ACF_hydr_MgTA.dat
│       └── ACF_dehy_MgTA.dat
└── results/
    ├── <system>_charges_comparison.csv
    ├── <system>_per_atom_charges.csv
    └── <system>_water_charges.csv
```

`<system>` is `cuta` or `mgta`. Result files are written when the notebook is run.

## Requirements

- Python 3.8+
- `numpy`, `pandas`
- Jupyter
- VASP output: `POSCAR` and Bader `ACF.dat` (from the [Henkelman group Bader code](https://theory.cm.utexas.edu/henkelman/code/bader/))

```
pip install numpy pandas jupyter
```

---

## 1. MOF Hydrogen Addition

`notebooks/mof_hydrogen_addition.ipynb`

*Documentation to be added.*

---

## 2. Bader Charge Analysis

`notebooks/bader_charge_analysis.ipynb`

Bader charge analysis of the metal-triazolate MOFs Cu(TA)2 (CuTA) and Mg(TA)2 (MgTA). Hydrated and dehydrated structures are compared to quantify charge redistribution on water adsorption.

### Method

1. Atom order and element counts are read from each `POSCAR`.
2. Per-atom Bader charges are read from each `ACF.dat`.
3. Net atomic charge is computed as `reference electrons - Bader charge`. A positive value is a cationic atom, a negative value is anionic.
4. Atoms are paired between the two states by element and position within that element. Atoms present in both structures are the framework. Atoms present only in the hydrated structure are water.
5. Framework charges are compared between states, and the total charge held by water is reported.

### Outputs

- Per-element mean net charge for framework atoms (dehydrated, hydrated, and the difference). Positive difference means the element becomes more cationic after water adsorption.
- Charges of the water atoms.
- Charge conservation check: charge on water plus framework change should be approximately zero.
- A ranked list of the atoms with the largest charge change.
- CSVs in `results/` (summary, per-atom, water).

### Generating the inputs

For all-electron Bader charges, run VASP with `LAECHG = .TRUE.`, then:

```
chgsum.pl AECCAR0 AECCAR2
bader CHGCAR -ref CHGCAR_sum
```

This produces `ACF.dat`. Rename it to `ACF_hydr_<name>.dat` or `ACF_dehy_<name>.dat` and place it in the matching `data/` folder with its `POSCAR`.

### To run

1. Place the four input files for each system in `data/cuta/` or `data/mgta/` (`POSCAR_hydr`, `POSCAR_dehy`, `ACF_hydr_<name>.dat`, `ACF_dehy_<name>.dat`).
2. Open the notebook and run all cells from the top.

### Notes

- **Reference electrons.** The `REFERENCE` dictionary sets the electron count subtracted from each Bader charge. Use atomic number Z if Bader was run with `-ref CHGCAR_sum` (as above). Use `ZVAL` from `OUTCAR` if Bader was run on valence-only `CHGCAR`. The notebook prints the total net charge of each cell and flags a mismatch, which should be near zero for a neutral cell.
- **Atom ordering.** The framework atom order must be identical in the hydrated and dehydrated POSCARs, with water atoms appended last.
- **POSCAR format.** VASP5 format is required (element symbols on line 6).
