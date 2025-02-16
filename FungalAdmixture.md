## Simulate the evolution of an entire fungal population in a hybrid swarm
1. Use AdmixSim2 to evolve a population for 10 generations following the mating of two distinct haplotypes. Sample 50 individuals (100 chromosomes) from each of generations 2 through 10. Evolutionary model file looks like this:
```bash
*1-10	Anc1,Anc2,Admix1f 
0,0,50	0.5,0.5,0
0,0,500	0,0,1
0,0,5000	0,0,1
0,0,50000	0,0,1
0,0,50000	0,0,1
0,0,50000	0,0,1
0,0,50000	0,0,1
0,0,50000	0,0,1
0,0,50000	0,0,1
0,0,50000	0,0,1
```
and code used was as follows (recombination rate was 2e-7 and mutation rate was 0):
```bash
for f in {2..10}; do python Downloads/AdmixSim2.py -i AdmixSim2fungalAdmixOnly/AdmixSim2bottleneck -p Admix1,Admix1,Admix1,Admix1,Admix1,Admix1,Admix1,Admix1,Admix1 -g 2,3,4,5,6,7,8,9,10 -n 50,50,50,50,50,50,50,50,50 --rec-rate 0.0000002 --mut-rate 0 -o AdmixSim2testG${f}.out; done
```
## Determine structure of the evolved populations:
1. Build fasta and structure files using the evolved haplotypes in the .hap files and site information in the .snv files:
```bash
for f in `ls AdmixSim2testExpt*.out.hap`; do perl FastaStructreFromAdmixSim2.pl $f; done
```
(note: the structure input files are downsampled - only every 20th variant site included)

2. Run STRUCTURE using k values of 1 through 10 with 10 iterations of each experiment (10 evolved populations x 9 generations x 10 k values x 10 replicates):
```bash
for rep in {1..10}; do for k in {1..10}; do for f in `ls *Expt*Gen*Structure.txt`; do sbatch $script/Structure.sh $f $k extraparamsLinkage $rep; done; done; done
```
3. Group results for each ExperimentxGeneration into its own directory:
```bash
for f in AdmixSim2testExpt1.out_Gen*txt; do for g in {2..10}; do dir="${f/\.out*/_Gen${g}}"; mkdir -p "$dir"; cp "${f/Gen*/Gen${g}}.Structure"* "$dir"; done; done
```
4. Use StructureHarvester to identify best K value for each run:
```bash
for f in `ls AdmixSim2testExpt1_Gen*; do StructureHarvester.py --dir $f --out ${f}_shout --evanno --clumpp; done
```
5. Gather best K values as determined by StructureHarvester:
```bash
for f in `ls AdmixSim2testExpt1_Gen*out/evanno.txt`; do perl BestK.pl $f; done
```
6. Determine consensus population affiliation for all sampled individuals using CLUMPP:
```bash
code
```
7. Build metadata table for annotating the Nneighbor networks:
```bash
code
```
## Generate annotated neighbor networks for all populations
1. Use the custom script NeighborNetworks.R to generate networks using fasta files as inputs.

## Extract admixture proportions for each individual from CLUMPP outputs

## Determine actual admixture proportions using information from .recseg files

## Generate plot to show correlation between inferred admixture proportions and actual admixture proportions


