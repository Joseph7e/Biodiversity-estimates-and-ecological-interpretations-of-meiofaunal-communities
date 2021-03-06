# Primer-Extract

Results and Methods for in silico extraction of amplicon fragment given a set of primers and distance comparison of two primers


# Straight to the Results

![image](https://user-images.githubusercontent.com/18738632/39929009-ec4b4a6c-5504-11e8-88b1-e4717472b280.png)

![image](https://user-images.githubusercontent.com/18738632/39932062-2125a48c-550d-11e8-902f-9c8a361fd022.png)

# Methods:

## Starting Database: https://www.arb-silva.de/download/archive/qiime/
SILVA_18S_99_OTUS.fasta

SILVA_18S_majority_taxonomy.tsv

## 18S Primers: 5' -> 3'
### V1-V2 primers:
 Forward: GCTTGTCTCAAAGATTAAGCC
 
 Reverse:CCTGCTGCCTTCCTTRGA
### V9 primers:
 Forward:GTACACACCGCCCGTC
 
 Reverse:TGATCCTTCTGCAGGTTCACCTAC


## Step 1: Narrow reference to Metazoa
python3 extract_metazoa.py

## Step 2: Extract amplicon region (run twice, once for each primer set)

### Method 1: primer-blast (be sure to rev comp reverse primers)
blastn -task blastn-short -query $concatenated_primers -subject ../metazoa.fasta -outfmt '6 qseqid qlen sseqid slen length pident qstart qend sstart send evalue bitscore' -out metazoa_v9.blast -word_size 7 -evalue 1000

### Method 2: Cutadapt
cutadapt --discard-untrimmed -g $forward_primer 99_otus_18S.fasta 2> /dev/null | cutadapt --discard-untrimmed -a $reverse_primer - 2> /dev/null > extracted_region.fasta


### Method 3: CLC in silco PCR extraction
CLC has a tool that uses BLAST, similiar to above. Results are consitent across the three methods.


## Step 3: Parse BLAST (if necessary) and divide extracted sequence into a per phylum fasta file.
python3 primerRegionExtractor.py


## Step 4: Align and construct distance matrix with clustalo, once per phylum
clustalo -i $extracted_region_per_phylum -o Annelida_regions_V9.align --distmat-out=Annelida_distance_V9.txt --full


### Run on all fastas
./auto_run_alignment.sh <fasta1> <fasta2> ...


## Step 5: Parse all distance matrixes and construct output figures (Original uses jupyter notebook)
#### On one file:
python3 calculate_average_distance.py
#### on all files:
python3 Distance_Matrix_phylum_based.py

### Extra: Convert jupyter notebook to regular python script
jupyter nbconvert --to script Distance_Matrix_phylum_based.ipynb
