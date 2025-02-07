# Methods, code and data for admixture simulations
## Run simulations
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
...or for multigenerational output:
```bash
for f in {1..10} ;do python Downloads/AdmixSim2.py -i AdmixSim2fungalAdmixOnly/AdmixSim2bottleneck -p Admix1,Admix1,Admix1,Admix1,Admix1,Admix1,Admix1,Admix1,Admix1 -g 2,3,4,5,6,7,8,9,10 -n 50,50,50,50,50,50,50,50,50 --rec-rate 0.0000005 --mut-rate 0.0000005 -o AdmixSim2testExpt${f}.out; done
```
3. Use AdmixSim2structure.sh script to generate fasta files and structure input files. Structure data is down-sampled 20-fold to minimize linked markers:
```bash
for f in {1..10}; do bash AdmixSim2structure.sh AdmixSim2fungaladmixonlyG${f}.out; done
```
...or for multigenerational outputs:
```bash
for f in {2..10}; do awk -v expt=$f 'BEGIN{n=1; group=1} {filename="AdmixSim2Expt" expt "_G" group ".fasta"; print ">seq"n"\n"$0 > filename; n++; if (n > 100) {n=1; group++}}' AdmixSim2testExpt.out.hap; done
```
## Build trees
1. Use RAxML to build a maximum likelihood tree:
```bash
/Applications/standard-RAxML-master/raxmlHPC-SSE3 -m GTRGAMMA -n AdmixSim2fungaladmixonly -s AdmixSim2fungaladmixonly.out.fasta -p 1234 -f a -x 4321 -# 100
```
2. Use GetPairwiseDistances.R script to generate a pairwise distance table (taxa_pairwise_dist.txt)
3. Sort distance table from highest to lowest distance:
```bash
sort -k4nr taxa_pairwise_dist.txt | awk '{print $2, $3, $4}' | sed 's/"//g' > AdmixSim2fungaladmixonly_distances.txt
```
## Build splitstrees on subsampled data
1. Use MaxPairwiseDistances.pl script to subsample 31 individuals with highest pairwise distances:
```bash
perl MaxPairwiseDistances.pl AdmixSim2fungaladmixonly_distances.txt 31 > seqs2keep
```
2. Drop tips from tree that are not in this list:
```bash
perl KeepFastas.pl seqs2keep AdmixSe2fungaladmixonly31.fasta
```
## Run BIC analyses to perform clustering
1. Use structure file generated above to perform BIC analyses using the BIC.R script.
## Run structure to estimate admixture proportions
Use this [mainparams](scripts/mainparams) file and this [extraparams](scripts/extraparams) file to run structure with the following command:
```bash
for k in {2..10}; do f in {2..10}; do structure -k $k -in
```
## Calculate actual admixture proportions and plot actual admixture inheritance patterns
Use AdmixSim2Plots.R code
