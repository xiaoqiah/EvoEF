# Introduction
EvoEF is an abbreviated name of the **Evo**Design physcics-base **E**nergy **F**unction. EvoDesign is a de novo protein design method developed by the Yang Zhang Lab at the University of Michigan. The evolutionary profile- and physics-based potential is used for protein design scoring. In the earlier version of EvoDesign, FoldX is used to compute the physical energy. Since physical energy is significant in modeling atomic-level interactions, it plays important role in protein-protein interaction design. To improve the computational accuracy and speed of EvoDesign, we developed EvoEF to replace FoldX.


# What EvoEF can do?
EvoEF can do the following calculations given a protein structure in the PDB format:

**ComputeStability**: compute the stability (or total energy) of a protein or protein complex.

**ComputeBinding**: compute the binding interaction energy of a protein-protein complex.

**RepairStructure**: repair incomplete side chains of a protein. The side chains will be optimized to reduce steric clashes at the best. The hydroxyl hydrogens of Ser, Thr, and Tyr are optimized. Side-chain groups of His, Asn, and Gln may be flipped for optimizing hydrogen-bonding networks.

**BuildMutant**: build mutation model.
  
**OptimizeHydrogen**: optimize hydroxyl hydrogens for Ser, Thr, and Tyr.

**<i>Note that EvoEF works with amino acids/proteins only. It cannot deal with nucleotides, DNA, RNA, water, and/or other molecules.</i>**

# Usage
EvoEF is most widely used to calculate the thermodynamic change (ΔΔ<i>G</i><sub>bind</sub> and ΔΔ<i>G</i><sub>stability</sub>) caused by amino acid mutations. ΔΔ<i>G</i><sub>stability</sub> is an indicator of measures protein stability change upon mutation(s), and ΔΔ<i>G</i><sub>bind</sub> is an indicator of the binding affinity change of protein-protein interaction upon mutation(s), where a negative Δ<i>G</i> value indicates favorable energy change.

The following steps are suggested for calculating ΔΔ<i>G</i>:

## 1. Repair your structure

$path/EvoEF --command=RepairStructure --pdb=your.pdb
  
Running this command successfully will generate a model named "your_Repair.pdb".

## 2. Build mutant

$path/EvoEF --command=BuildMutant --pdb=your_Repair.pdb --mutant_file=individual_list.txt
  
Running this command will generate a panel of models named as your_Repair_Model_dddd.pdb, where dddd is a four digit number from 0001 to 9999, depending on the number of mutants to be generated that are listed in the mutant file.

## 3.1 Calculate ΔΔ<i>G</i><sub>stability</sub>

First, calculate the stability score for the reference protein (Δ<i>G</i><sub>stability,ref</sub>):
  
$path/EvoEF --command=ComputeStability --pdb=your_Repair.pdb
  
Then, calculate the stability score for the mutant protein (Δ<i>G</i><sub>stability,mut</sub>), e.g.:
  
$path/EvoEF --command=ComputeStability --pdb=your_Repair_Model_0001.pdb
  
ΔΔ<i>G</i><sub>stability</sub> = Δ<i>G</i><sub>stability,mut</sub> - Δ<i>G</i><sub>stability,ref</sub>

## 3.2 Calculate ΔΔ<i>G</i><sub>bind</sub>
  
First, calculate the binding interaction score for the reference protein-protein complex (Δ<i>G</i><sub>bind,ref</sub>):

$path/EvoEF --command=ComputeBinding --pdb=your_Repair.pdb

Then, calculate the binding interaction score for the mutant complex (Δ<i>G</i><sub>bind,mut</sub>), e.g.:

$path/EvoEF --command=ComputeBinding --pdb=your_Repair_Model_0001.pdb

ΔΔ<i>G</i><sub>bind</sub> = Δ<i>G</i><sub>bind,mut</sub> - Δ<i>G</i><sub>bind,ref</sub>

If the protein complex has more than chains, you can use the "split" option to divide it into two parts before calculating the binding energy (see below).


# Installation
change into the $path/EvoEF/ directory, and run: <i> g++ -O3 --fast-math -o EvoEF src/*.cpp </i> or <i> ./build.sh </i> if you are working in a UNIX/Linux environent.


# Usage

## ComputeStability

$path/EvoEF --command=ComputeStability  --pdb=protein.pdb

## ComputeBinding

$path/EvoEF --command=ComputeBinding --pdb=complex.pdb

$path/EvoEF --command=ComputeBinding --split=A,BC --pdb=complex.pdb

User should specify how to split chains into two parts for "ComputeBinding". Otherwise, EvoEF will compute the interaction energy between any pair of chains.

## RepairStructure

$path/EvoEF --command=RepairStructure --pdb=your.pdb --num_of_runs=3

Running this command will create a new structure model named "your_Repair.pdb" in the current directory. The option "--number_of_runs" specify the number of repeated times of repairing/optimizing the structure sequentially (default: 3).

## BuildMutant

$path/EvoEF --command=BuildMutant --pdb=your.pdb --mutant_file=individual_list.txt  --num_of_runs=10 

Here, the "individual_list.txt" file shows the mutants that you want to build. It has the following format:

CA171A,DB180E;

Each mutant is written in one line ending with “;”, and multiple mutations in a mutant are divided by “,”. Note that there is no gap or space character between single mutations. For each single mutation, the first letter is the reference amino acid, the second letter is the chain identifier of the amino acid followed by its position in the chain, and the last letter is the amino acid after mutation. Running the command successfully should generate a new structure file named “your_Model_1.pdb”. The option "--number_of_runs" specify the number of repeated times of optimizing the rotamers of the mutated and surrounding residues sequentially (default: 10). 

## OptimizeHydrogen

$path/EvoEF --command=OptimizeHydrogen --pdb=your.pdb --num_of_runs=3

Running this command successfully will create a file named "your_OptH.pdb" in the current directory. The option "--number_of_runs" specify the number of repeated times of optimizing the locations of Ser/Thr/Tyr hydroxyl hydrogens (default: 3).

## To show EvoEF usage:

$path/EvoEF --help

# Copyright & Contact
Copyright (c) 2019 Xiaoqiang Huang. EvoEF is free to academic users. For suggestions or bug reporting, please contact xiaoqiah@outlook.com or xiaoqiah@umich.edu

# Reference
Pearce R, Huang X, Setiawan D, Zhang Y. EvoDesign: Designing Protein–Protein Binding Interactions Using Evolutionary Interface Profiles in Conjunction with an Optimized Physical Energy Function. Journal of Molecular Biology (2019) 431: 2467-2476.
