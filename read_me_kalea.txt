Contribution
----------------------------------------------------------------------------------------------------------
Kalea Moore (UF CPET Student Science Training Program 2023)
Bhumika Singh (Alberto Perez Lab, Quantum theory Project, Department of Chemistry, University of Florida)
----------------------------------------------------------------------------------------------------------

==================================================================
HOW TO USE ALPHAFOLD (AF2) using Colabfold in HIPERGATOR (LINUX)
==================================================================

I) HOW TO LOG IN INTO HIPERGATOR
===============================

Using Terminal (Linux or Mac) or MobaXTerm (Windows)
-------------------------------------------------------

ssh -Y username@hpg2.rc.ufl.edu [here, username, e.g., kaleamoore]
	- Type in HiPerGator account password (you won't see the typing, but it is happening)
	- Choose a way to log in (1 - DuoMobile or  2 - via phone)
	- Once logged in, type "cd /blue/alberto.perezant/username/   Note: replace username with YOUR username.


Best practices

-- Always create a base directory to work on one problem. This helps avoide overwriting and makes your work systematic
For instance, all the work was carried in "Alphafold" directory


II) USEFUL LINUX COMMANDS
==============================

There are several sources on internet to help in learning commonly used commands, such as, https://www.geeksforgeeks.org/basic-linux-commands/

Here, only some of the basic commands are written to begin. 

NOTE: Hit enter after every command to finalize it.

1. pwd --> print the current directory path
2. mkdir --> create a new directory
3. cd --> change to another directory
	cd ../  --> go to previous directory
4. vi --> open a file to read and/or edit
	i --> to write or edit a file
	After you have finished writing or editing press esc and choose either of the options:
		:q! --> exit a file WITHOUT saving 
		:wq --> exit while SAVING the changes or additions to the file

5. ls --> list all the items present in the current directory
6. ls -rtlh --> list all the items from oldest(top) to latest(bottom) created item
7. rm --> to remove a particular file or directory
	rm -r filename/directoryname 	
		WARNING! rm is the most dangerous command if used carelessly you can end up deleting all you work. USE CAREFULLY, BE ALERT WHILE USING THIS COMMAND!
8. mv --> move a directory/file - mv [file] [new location] (e.g., mv S5 Alphafold/)
        > To move a file to a specific directory under another directory, use a / in between locations (eg.g., mv S5 Alphafold/mutations)

** Specific to Hipergator only **

9.  sbatch filename.sh [eg: submit_AF2_new.sh] --> to submit a job on Hipergator
10. squeue -u username [squeue -u kaleamoore] --> Check the status and time of the current run(s).


Note: All examples are simply from the current project and may or may not apply to your own.


III) RUNNING AF
====================
In this project, we worked on BRD4 ET domain-LANA peptide (RCSB PDB ID: 2ND0).
Everywhere, the native structure corresponds to this BRD$ ET-LANA complex

1. Firstly, we tried to run AF on the native sequence (No mutation)
----------------------------------------------------------------------

Required Files:
1) 2nd0.fasta as a template sequence
2) 2nd0.pdb --> This file must be kept with 'temp' directory. This pdb should be clean, meaning only one model should be used for NMR based structures.
3) complex.fasta --> This is the input fasta sequence for your complex
	eg of this file
---------------------------------------------------------------------------------------------------------
>seq
SEEEDKCKPMSYEEKRQLSLDINKLPGEKLGRVVHIIQSREPSLKNSNPDEIEIDFETLKPSTLRELERYVTSCLRKKRKPQA:NLQSSIVKFKKPLPLTQPG
---------------------------------------------------------------------------------------------------------

Note: the ':' separates the protein sequence from the peptide

4) submit_AF2_new.sh --> bash script to run AF using Colabfold in HPG

important command within this file is:

With Template
--------------------------------------------------------------------------------------------------------------------------------------------
colabfold_batch 2nd0.fasta results_temp --templates --custom-template-path ./temp --model-type 'alphafold2_ptm' --msa-mode 'mmseqs2_uniref'
--------------------------------------------------------------------------------------------------------------------------------------------

Without Template
--------------------------------------------------------------------------------------------------------------------------------------------
colabfold_batch sequence.fasta results  --model-type 'alphafold2_ptm' --msa-mode 'mmseqs2_uniref_env'
--------------------------------------------------------------------------------------------------------------------------------------------

Note: you don't need 2nd0.fasta, 2nd0.pdb when running the AF without template!


Running the job:

use the following command:
	sbatch submit_AF2_new.sh

check the status of job:
	squeue -u [username] (e.g., squeue -u kaleamoore)
> This is to check your runs and their status (e.g., time running). YOU CAN RUN MULTIPLE RUNS AT ONCE.


Results:

After successful completion of AF you should get the following files/directories

	results_temp/ (With template)
	results/      (Without Template)

Each of these directories contains:
> The Top 5 ranked PDBs
> Top 5 ranked structures pLDDT pngs
> seq.a3m file
> PAE pngs

Note: In case your job fails, then the directory will not contain thses important files mentioned above.
	In such situation look at AF2.err and Af2.out. They generally have information about the error occurred and is helpful to resolve the issue(s).

Analysis:
Transfer the files to your local system which have the necessary Visualizing software such as Nanome, Pymol, VMD or Chimera. You can either use command line (Mac/Linux users) or WinSCP (Windows users only) to transfer files
In your local terminal (i.e., the one where you have not logged in HPG)
Command: scp -r username@hpg.rc.ufl.edu:path/to/Alphafold/ ./Desktop 	Note: Please read or consult a trained person, before using this command

1. Visualize the pdbs in Nanome/Pymol/VMD/Chimera. You should analyze whether the structure predicted is matching with the already know structure or not.
2. Visualize the images generated to understand how good or bad the structures are predicted

WARNING: try to keep the filename in separate locations to avoid overwriting and easy to keep track of changes


2. Secondly, we tried to run AF on the mutated peptide sequence in complex with protein (We have just performed Single-point mutation)
---------------------------------------------------------------------------------------------------------------------------------------------------------

Make sure you're in the Alphafold directory

Create directories in the name of residue you mutated. Example S5 --> this denotes the One letter code for Aminoacid (here, serine) along with the number. 
command: mkdir S5

------------------------------------------------------------------------------------------------
------------------------------------------------------------------------------------------------
THE FOLLOWING STEPS APPLY TO EACH INDIVIDUAL DIRECTORY YOU MAKE FOR EACH RESIDUE BEING MUTATED.
------------------------------------------------------------------------------------------------
------------------------------------------------------------------------------------------------

A. Copy the complex.fasta from the base directory, as S5X.fasta.
B. In S5.fasta change the single letter of desired Aminoacid(here S5) with the new one(let's say X). Finally save the file with new changes.
C. Change the infile name and result directory in submit_AF2_new.sh corresponding to new mutation. For example:

With Template (make sure to have temp directory with necessary files in it for every residue directory)
--------------------------------------------------------------------------------------------------------------------------------------------
colabfold_batch S5C.fasta S5C_temp --templates --custom-template-path ./temp --model-type 'alphafold2_ptm' --msa-mode 'mmseqs2_uniref'
colabfold_batch S5D.fasta S5D_temp --templates --custom-template-path ./temp --model-type 'alphafold2_ptm' --msa-mode 'mmseqs2_uniref'
--------------------------------------------------------------------------------------------------------------------------------------------

* S5C and S5D are my mutations under the S5 directory. I'm substituting cysteine and then aspartic acid for serine 5.


Without Template
--------------------------------------------------------------------------------------------------------------------------------------------
colabfold_batch  S5C.fasta S5C  --model-type 'alphafold2_ptm' --msa-mode 'mmseqs2_uniref_env'
colabfold_batch  S5D.fasta S5D  --model-type 'alphafold2_ptm' --msa-mode 'mmseqs2_uniref_env'
--------------------------------------------------------------------------------------------------------------------------------------------

D. Similary create all the fasta files with all the possible mutations of S5. Keep on adding new lines in submit_AF2_new.sh for every fasta files created within S5.
This way you can submit multiple jobs using single bash file for one residue (here, S5)

E. Submit the job as you did with the native one.

F. Repeat this process and run all your mutations for all the remaining residues too. You don't have to wait for earlier job to finish, you can do it at the same time.



==================================================================
HOW TO USE ALPHAFOLD (AF2) using Colabfold Through Web Server
==================================================================

You can also use web version of Af provided by Colabfold (https://colab.research.google.com/github/sokrypton/ColabFold/blob/main/AlphaFold2.ipynb)


I) CoLab Fold Web
====================

A. For query_sequence, erase the default sequence. paste your own query sequence. 
	Taking the same example of BRD$ ET-LANA complex, you can paste the following sequence: SEEEDKCKPMSYEEKRQLSLDINKLPGEKLGRVVHIIQSREPSLKNSNPDEIEIDFETLKPSTLRELERYVTSCLRKKRKPQA:NLQSSIVKFKKPLPLTQPG

B. In case you are using a template, make sure to use the cleaned PDB as template.

C. Change template_mode to "custom," and click the play button to the left of the job. If given two pop up messages, click "Run anyway" and then "Ok."
	> When the ability to choose a file is given, click "Choose Files.'
	> Select the cleaned pdb file. Note: keep the name of cleaned PDB as 2nd0.pdb. Do not rename it as 2nd0_clean.pdb (for example) otherwise this will give an error later on in running AF. 
	  Instead you can create a backup of original pdb as 2nd0_original.pdb(for example)

D.After it's done running, click run on "Install dependencies" then after that's done running, click run on "MSA options."
E. When you get to "Advanced settings," click "save_all" or "save_to_google_drive" depending on which is better for you, then click run.
F. After that's done, click run on "Run Prediction."
G. After that's done, click "show_sidechains" and "show_mainchains," then run "Display3D structure."
H.  After that's done click run on "Plots."
I. After that's done, click run on "Package and download results" if you wish.

#########################################################################################################################################################################################

Good luck, and have fun learning!
Bhumika & Kalea


remaining:
Nanome instructions (Create a separate file)

