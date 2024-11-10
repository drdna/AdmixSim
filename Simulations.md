# Methods, code and data for admixture simulations
1. Use AdmixSim2 to run simulations for fungal admixture scenarios
Input files:
AdmixSim2fungal.ind
AdmixSim2fungal.hap
AdmixSim2fungal.snv
AdmixSim2fungal.mod
2. Run admixture simulations using the following code to sample 50 individuals (100 haploid chromosomes) at the end of each generation:
```bash
for f in {1..10}; do python Downloads/AdmixSim2.py -i AdmixSim2fungal -p Admix1 -g $f -n 50 --rec-rate 0.0000005 --mut-rate 0.0000005 -o AdmixSim2fungaladmixonlyG${f}.out; done
```
3. Use AdmixSim2structure.sh script to generate fasta files and structure input files:
```bash
for f in {1..10}; do bash AdmixSim2structure.sh AdmixSim2fungaladmixonlyG${f}.out; done
```
4. Use RAxML to build a maximum liklehood tree:
```bash
/Applications/standard-RAxML-master/raxmlHPC-SSE3 -m GTRGAMMA -n AdmixSim2fungaladmixonly -s AdmixSim2fungaladmixonly.out.fasta -p 1234 -f a -x 4321 -# 100
```
5. Use GetPairwiseDistances.R script to generate a pairwise distance table (taxa_pairwise_dist.txt)
6. Sort distance table from highest to lowest distance:
```bash
sort -k4nr taxa_pairwise_dist.txt | awk '{print $2, $3, $4}' | sed 's/"//g' > AdmixSim2fungaladmixonly_distances.txt
```
7. Use MaxPairwiseDistances.pl script to subsample 31 individuals with highest pairwise distances:
```bash
perl MaxPairwiseDistances.pl AdmixSim2fungaladmixonly_distances.txt 31 > seqs2keep
```
8. Drop tips from tree that are not in this list:
```bash
perl KeepFastas.pl seqs2keep AdmixSe2fungaladmixonly31.fasta
```
