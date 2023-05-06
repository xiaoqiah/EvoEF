# Introduction to EvoEF
EvoEF is an abbreviated name of the **Evo**Design physcics-base **E**nergy **F**unction. EvoDesign is a de novo protein design method developed by the Yang Zhang Lab at the University of Michigan. The evolutionary profile- and physics-based potential is used for protein design scoring. In the earlier version of EvoDesign, FoldX is used to compute the physical energy. Since physical energy is significant in modeling atomic-level interactions, it plays important role in protein-protein interaction design. To improve the computational accuracy and speed of EvoDesign, we developed EvoEF to replace FoldX.


# What EvoEF can do?
The following useful functions are supported by EvoEF:

**ComputeStability**: compute the stability (or total energy) of a protein or protein complex.

**ComputeBinding**: compute the binding interaction energy of a protein-protein complex.

**RepairStructure**: repair incomplete side chains of a protein. The side chains will be optimized to reduce steric clashes at the best. The hydroxyl hydrogens of Ser, Thr, and Tyr are optimized. Side-chain groups of His, Asn, and Gln may be flipped for optimizing hydrogen-bonding networks.

**BuildMutant**: build mutation model.
  
**OptimizeHydrogen**: optimize hydroxyl hydrogens for Ser, Thr, and Tyr.

**Note that EvoEF works with amino acids/proteins only. It cannot handle nucleotides, DNA, RNA, water, and/or other molecules.**

# Usage suggestions
EvoEF is most widely used to calculate the thermodynamic change (ΔΔ<i>G</i><sub>bind</sub> and ΔΔ<i>G</i><sub>stability</sub>) caused by amino acid mutations. ΔΔ<i>G</i><sub>stability</sub> is an indicator of measures protein stability change upon mutation(s), and ΔΔ<i>G</i><sub>bind</sub> is an indicator of the binding affinity change of protein-protein interaction upon mutation(s), where a negative Δ<i>G</i> value indicates favorable energy change.

The following steps are suggested for calculating ΔΔ<i>G</i>:

**1. Repair your structure**

  <i>$path/EvoEF --command=RepairStructure --pdb=your.pdb</i>
  
  Running this command successfully will generate a model named your_Repair.pdb

**2. Build mutant**

  <i>$path/EvoEF --command=BuildMutant --pdb=your_Repair.pdb --mutant_file=individual_list.txt</i>
  
  Running this command will generate a panel of models named as your_Repair_Model_dddd.pdb, where dddd is a four digit number from 0001 to 9999, depending on the number of mutants to be generated that are listed in the mutant file.

**3. Calculate ΔΔ<i>G</i>**
  For ΔΔ<i>G</i><sub>stability</sub>, first, calculate the stability score for the reference protein:
  
  $path/EvoEF --command=ComputeStability --pdb=your_Repair.pdb. The total energy is referred to as Δ<i>G</i><sub>stability,ref</sub>.
  
  Then, calculate the stability score for the mutant protein, e.g.:
  
  <path>/EvoEF --command=ComputeStability --pdb=your_Repair_Model_0001.pdb. The total energy is referred to as ΔΔ<i>G</i><sub>stability,mut</sub>.
  
  Finally, ΔΔ<i>G</i><sub>stability</sub><sup>ref->mut</sup> = Δ<i>G</i><sub>stability</sub><sup>mut</sup> - Δ<i>G</i><sub>stability</sub><sup>ref</sup>

(3.2) ddG(binding)
first calculate dG(binding) for the wildtype complex:
./EvoEF --command=ComputeBinding --pdb=your_Repair.pdb
The total score is dG(binding, wildtype)

then calcualte dG(binding) for a mutant:
./EvoEF --command=ComputeBinding --pdb=your_Repair_Model_0001.pdb
the total score is dG(binding, Model_0001)

ddG(binding, wildtype->Model_0001) = dG(binding,Model_0001) - dG(binding, wildtype)

If your complex has >2 chains, you can use the 'split' option to divide your
protein into two parts for binding assessment (see below for details).


# Installation
change directory into EvoEF/, and run:

g++ -O3 --fast-math -o EvoEF src/*.cpp

or run (if you use a UNIX/Linux environment):

./build.sh


# Usage
(1) To compute protein stability, you can run:

./EvoEF --command=ComputeStability  --pdb=protein.pdb


(2) To compute protein-protein binding affinity, you can run:

./EvoEF --command=ComputeBinding --pdb=complex.pdb
  
or:
  
./EvoEF --command=ComputeBinding --split=A,BC --pdb=complex.pdb

user should specify how to split the chains into two parts for computing the binding affinity. Otherwise EvoEF will output the interaction energy between any two chain pair.

(3) To repair the structure model and do energy minimization:

./EvoEF --command=RepairStructure --pdb=protein.pdb [--num_of_runs=3]

A new structure model named "model_Repair.pdb" will be generated in the directory where you run the command. Running the command successfully should generate a new structure file named “mod-el_Repair_Model_1.pdb”. In the mutant model, the optimized polar hydrogen coordinates are also shown.

(4) To build mutation model, you can run:

./EvoEF --command=BuildMutant --pdb=model.pdb --mutant_file=individual_list.txt  [--num_of_runs=10]

Here, the "individual_list.txt" file shows the mutants that you want to build. It has the following format:

CA171A,DB180E;

Each mutation is written in one line ending with “;”, and multiple mutants are divided by “,”. Note that there’s no gap/space between single mutations. For each single mutation, the first alphabet is the wild-type amino acid, the second is the identifier of the chain that the amino acid is attached to, the number is the position of the amino acid in the chain, and the last alphabet is the amino acid after mutation. Running the command successfully should generate a new structure file named “model_Repair_Model_1.pdb”. In the mutant model, the optimized polar hydrogen coordinates are also shown.

(5) To optimize the hydroxyl hydrogens, run:
./EvoEF --command=OptimizeHydrogen --pdb=model.pdb [--num_of_runs=3]
A new model named "model_OptH.pdb" will be generated.


(6) For a detailed description of the supported commands and options, run:
./EvoEF --help


# Disclaimer and copyright
Copyright (c) 2019 Xiaoqiang Huang. EvoEF is free to academic users.


# Contact
For suggestions or bug reporting, please contact xiaoqiah@outlook or xiaoqiah@umich.edu


# Reference
Pearce R, Huang X, Setiawan D, Zhang Y. EvoDesign: Designing Protein–Protein Binding Interactions Using Evolutionary Interface Profiles in Conjunction with an Optimized Physical Energy Function. Journal of Molecular Biology (2019) 431: 2467-2476.
