# d4-rescore
re-scoring a set of docked ligands with off-the-shelf algorithms to assess utility in virtual screening

In [1], Lyu et al dock Enamine REAL at the D4 receptor. They selected ~550 ligands at random from a range of high- and low-scoring buckets, and test these _in vitro_ at 10µM. This represents a perfect test-case for re-scoring algorithms:
- The actives all bind to the same binding site (with reasonably high confidence)
- The actives do not arise from congeneric series and are not based on prior knowledge, as is often the case in ChEMBL
- The inactive labels have experimental evidence
- The setting is a real use-case - i.e. re-ranking docked structures to identify hits
- The receptor is an 'easy' case: a small, enclosed, polar binding site

If re-scoring algorithms can accurately rank the actives before the inactives, they are unambiguously useful for re-scoring. If they do not, it indicates they are either not useful, or may suffer from high false-positive rate (like docking). In the high FPR case, they still may be useful but they just happened to fail on these ligands. 

## steps to reproduce:

1. read and embed ligands
See `1-read_and_embed_ligands.ipynb`. This step takes the SMILES codes given in the supplementary of Lyu et al and prepares them for docking in two ways: the first is directly embedding each ligand in 3D using RDKit's EKTDG method, the second is enumerating tautomers/charge states/enantiomers with the Durrant lab's Gypsum-DL, which also embeds into 3D.
2. prepare the protein for docking
This workflow uses the script available at https://github.com/ljmartin/pdb_to_pdbqt . The resulting PDB was converted to `.pdbqt` format with `obabel proteinH.pdb -xr -O proteinH.pdbqt`
3. dock!
Docking was run with Smina:
```
cd ./data/
smina -r proteinH.pdbqt -l ligands3d.sdf --autobox_ligand AQD_ligand.pdb -o ligands3d_docked.sdf
smina -r proteinH.pdbqt -l ligands3d_gypsum.sdf --autobox_ligand AQD_ligand.pdb -o ligands3d_gypsum_docked.sdf
```

## results
ROC:

![roc]('./figs/rocs.svg')

Early enrichment metrics:

![early]('./figs/early_enrichment.png')


(Ultra-large library docking for discovering new chemotypes)[https://www.nature.com/articles/s41586-019-0917-9]