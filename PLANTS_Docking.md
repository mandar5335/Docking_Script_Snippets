A. Error-free approach:
1. Combine multple mol2 to single mol2 file (This method is mostly used)

   ```
   output_mol2_file = "top50_PLANT_docked_poses.mol2"

    with open(output_mol2_file, "w") as outfile:
    for mol2_file in ordered_mol_list:
        with open(mol2_file, "r") as infile:
            outfile.write(infile.read())
   ```
B. Using RDkit:

2. Using RDKit assign property to Mol2 and save
   (Issue: RDKit has issue with atom types or aromaticity, so it skips molecule. So, sanitization is avoided)

  Step 1:
  
   ```
mols = []

for idx, mol in enumerate(mol_list, start=1):
    
    ### Everything is set to 'False' otherwise rdkit was omitting molecules 
    ### Error appears to be arising from uncommon MOL2 atom types
    
    suppl = Chem.MolFromMol2File(f'{PLANT_RESULTS_FOLDER}/{mol}.mol2', removeHs=False, sanitize=False, cleanupSubstructures=False)
    suppl.SetProp('_Rank',f'{idx}')
    
    if suppl is not None:
        mols.append(suppl)

print(f"Loaded {len(mols)} molecules")
```

  Step 2: 

  ```
f = open('molecules_missed_in_combined_SDF.dat', 'w')

with SDWriter("combined.sdf") as writer:
    for mol in mols:
        try:
            writer.write(mol)
        except:
            f.write(mol.GetProp('_Rank')+'\t'+mol.GetProp('_Name')+'\n')
            pass
f.close()
```
   
