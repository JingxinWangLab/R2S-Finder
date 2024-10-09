RNA Secondary Structure Finder (R2S-Finder) is a pipeline to identify stable RNA structures using genome-wide chemical probing data. The code provided here contains all the code needed for extracting RNA structures in the human genome. You can explore the data using HTML links without downloading the data:

Human: https://jingxinwanglab.github.io/R2S-Finder/human_output.html

E. coli: https://jingxinwanglab.github.io/R2S-Finder/ecoli_output.html

Viruses: https://jingxinwanglab.github.io/R2S-Finder/virus_output.html

In step 1, I downloaded SHAPE scores associated with genome annotations from the RASP database (http://rasp.zhanglab.net/) for human, E. coli, SARS-CoV-2, and Zika virus. I extracted genes with valid SHAPE scores (including zero reactivity) of at least 11 nucleotides (nts) in length (denoted as SHAPE(+) regions). If the SHAPE(+) regions had 5% missing SHAPE signals within the region, I discarded the genes (or transcripts) without further analysis. For DMS data, the tolerability of missing data was increased from 5% to 65%, because G and U are considered DMS(–). I reformatted the filtered data into SHAPE map files compatible with the Superfold software package (version 1.0) (https://github.com/Weeks-UNC/Superfold). The virus genomes were considered as single entries without separating the genes. 

Source code (Jupyter Notebook): step1_shape_extract.ipynb

Tested system: Linux, Python 2.7

Install dependencies using the yml file: 
conda create --name r2sfinder1 --file r2sfinder_1.yml

Install dependencies manually: 
python==2.7
pandas==0.24.2
pyBigWig==0.3.18
pyfaidx==0.7.0
numpy==1.16.6
biopython==1.76
jupyter

Human genome sequence (hg38.fa) and annotation file (hg38.gff3) should be downloaded and save in ./genomes and ./genomes/seq. 

In step 2, RNA folding prediction was performed with all SHAPE map files using Superfold, which set the SHAPE pseudoenergy restraints to match the observed SHAPE signals. Superfold calls the Fold program in the RNAstructure package to calculate the Shannon entropy. Superfold/Fold also provides the probability of all base pairs (dp file) and the most probable RNA secondary structures (ct file). 

Install Superfold: 
https://github.com/Weeks-UNC/Superfold

Install RNAstructure package:
https://rna.urmc.rochester.edu/RNAstructure.html

Tested system: Linux, Python 2.7 (same conda environment as above can be used)

Source code (Jupyter Notebook): step2_superfold.ipynb

To identify stable RNA regions in step 3, I used three criteria to remove ambiguous or unstable structures: (1) The most probable base pairing of any nucleotide must be contained within the region. (2) All nucleotides involved in base-pairing must have a probability > 10% (−log(Probability) < 1) at all points and an average probability > 50% (−log(Probability) < 0.301) across the entire region. (3) If such a stable region is fully included within a larger region that also fulfills the above two criteria, the larger stable region is used. 

As the 4th step in the pipeline, I defined stem-loops as the unpaired sequences of 3−10 nts with one stem having a minimum of 3 base pairs (bp). Similarly, internal loops and bulges were defined as unpaired sequences (0−10 nts on either side) with two stems having a minimum of 3 base pairs each. The internal loops and bulges are denoted as m × n and m × 0 (or 0 × n), respectively, where m is the unpaired nucleotides at the 5’ end and n at the 3’ end. It is worth noting that m × n bulges were considered equivalent to n × m bulges (e.g., AG × G and G × AG bulges) in my analyses. I mapped the sequences with SHAPE scores, along with the identified stable sequences, stem-loops, internal loops, and bulges, to reference transcript sequences of the specific organisms. 

Source code (Jupyter Notebook): step3_4_stable_region_id.ipynb

Tested system: Mac, Python 2.7

Install dependencies using the yml file: 
conda create --name r2sfinder3 --file r2sfinder_3.yml

Install dependencies manually: 
pandas==0.24.2
numpy==1.16.5
jupyter
tqdm

