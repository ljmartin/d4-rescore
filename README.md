# d4-rescore
re-scoring a set of docked ligands with off-the-shelf algorithms to assess utility in virtual screening

In [1], Lyu et al dock Enamine REAL at the D4 receptor. They selected ~550 ligands at random from a range of high- and low-scoring buckets, and test this _in vitro_ at 10µM. This represents a perfect test-case for re-scoring algorithms:
- The actives all bind to the same binding site (with reasonably high confidence)
- The actives do not arise from congeneric series and are not based on prior knowledge, as is often the case in ChEMBL
- The inactive labels have experimental evidence
- The setting is a real use-case - i.e. re-ranking docked structures to identify hits
- The receptor is an 'easy' case: a small, enclosed, polar binding site

If re-scoring algorithms can accurately rank the actives before the inactives, they are unambiguously useful for re-scoring. If they do not, it indicates they are either not useful, or may suffer from high false-positive rate (like docking). In the high FPR case, they still may be useful but they just happened to fail on these ligands. 

## docking:

PDB 5WIU was downloaded from RCSB. Missing atoms and hydrogens were added with (PDBFixer)[https://github.com/openmm/pdbfixer] in server mode. Ligand PBD was separated with VMD. Prepared protein was converted to `.pdbqt` file with `obabel protein_5WIU.pdb -xr -O protein_5WIU.pdbqt`

Docking was run with Smina:
```
smina - protein_5WIU.pdbqt -l mols.sdf --autobox_ligand ligand_5WIU.pdb -o docked.sdf
```

(Ultra-large library docking for discovering new chemotypes)[https://www.nature.com/articles/s41586-019-0917-9]