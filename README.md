# delta_cs
Software to compare experimental NMR chemical shifts with those predicted by PPM and CH3shift on molecular ensembles
delta_CS.py performs a comparison of predicted chemical shifts (from either PPM or ch3shift) with experimental values available on the BMRB database. The comparison is reported as two different metrics: RMSD (only PPM) and a reduced chi-squared. The latter is defined as the ratio of the squared difference of chemical shift prediction and experimental value to the error of the predictor for the specific atom type (these errors are provided by PPM and CH3Shift and invoked during the pipeline). 
The script considers possible differences in the nomenclature used in the PPM/CH3shift output, particularly challenging for protons, the nomenclature of the reference PDB and the nomenclature in the BMRB database. This can be useful, for instance, in cases where the full-length variant is not used. The only requirement is that reference PDB must not contain non-canonical residues, such as capping groups, that are not defined in the forcefield CHARMM27. 

**REQUIREMENTS**
delta_CS.py must be run with using python 3.7 and has the following package dependencies: 
- NumPy
- sys
- re
- gromacs
- pandas
- math
- Bio (biopandas)
- argparse
- pynmrstar

delta_CS.py relies on two auxiliary files contained in the folder *aux_files*, which must be located in the same folder as delta_CS.py:
- error_protons_ppm.txt (error for the chemical shift predictions of the different proton types by ppm).
- nomenclature.txt (nomenclature equivalencies across PDB, CH3Shift and PPM)



**USE**

An example call of delta_CS.py would look the following:
>>  python delta_CS.py -exp BMRBENTRY -bp PATH_TO_BMDB.DAT_FROM_PPM  -ref PATH_TO_YOUR_PDB_REFERENCE -pdb_mapping yes -histograms yes

- -exp (mandatory): entry of the BMRB id for comparison. 
- -pb (optional): path to the BRMB.DAT file generated by PPM.
- -ch3 (optional): path to the .txt file generated by CH3shift.
- -ref (mandatory): path to your reference PDB file.
- -pdb_mapping: yes/no (default: yes). Controls the ouput of the obtained reduced chi-squared as B-factor to visualize on Pymol (per atom type).
- -histograms: yes/no (default: yes). Controls the ouput of histograms of obtained reduced chi-squared for backbone and CB atoms (per atom type). If set to yes, the -pb flag must contain a path to the PPM output file. Otherwise, an exception will be raised.  



**OUTPUT** 

*CH3shift: path to -ch3 flag must be provided*

The following output folders are created:
1. csv_summary: contains the files 'summary_CH3shift.csv' and 'output_chi_squared_CH3.csv'. The former lists the reduced chi-squared metric for all those atoms for which the computation was possible. The latter provides a per-atom-type average of the obtained chi-squared metrics. 
2. CH3_pdbs (-pdb_mapping must be set to yes): contains pdbs where the reduced chi-squared values of a specific atom (methyl C) are mapped as a B-factor of the CA of that residue. For each atom type, there is one PDB file collecting all reduced chi-squared values of that atom type. 

*PPM: path to -pb must be provided*

1. csv_summary: with files
    I.  'output_RMSD_BB.csv': contains the per-atom-type average RMSD of the predictions for backbone and CB atoms.
    II. 'output_chi_quared_BB.csv': contains the per-atom-type average reduced chi-squared of the predictions for backbone and CB atoms.
    III. 'output_chi_squared_H_ppm.csv': contains the per-atom-type average reduced chi-squared of the predictions for protons.
    IV. 'summary_PPM.csv': summary for all atoms.
2. CH3_pdbs (-pdb_mapping must be set to yes): contains pdbs where the reduced chi-squared values of a specific atom (methyl C) are mapped as a B-factor of the CA of that residue. For each atom type, there is one .pdb file collecting all reduced chi-squared values of that atom type.
3. Histogram (-histograms must be set to yes): plots with the distribution of the chi-square vales per atom type. It reports what percentage of the compute values are below 5.
4. BB_pdbs: (-pdb_mapping must be set to yes): contains pdbs where the reduced chi-squared values of a specific atom (backbone and CB) are mapped as a B-factor of the CA of that residue. For each atom type, there is one PDB file collecting all reduced chi-squared values of that atom type. 
