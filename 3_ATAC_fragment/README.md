## Note: In the latest scHiCAR protocol, read 1 corresponds to the ATAC-seq fragment.
### 1. Download all the files to your folder
```
ATAC_fragment_folder
├── Snakefile
├── cluster.json
├── config.yaml #modify this file based on your file paths
├── sample2json.py
├── fq  # create symbolic links to the *_cutME_L001_R1_001.fastq.gz files from 2_DNA_preprocess/05_cutME_fq in this folder
│   ├── DNA_example_cutME_L001_R1_001.fastq.gz
└── script
    ├── extract_barcode_based_on_knee.R
    ├── scHiCAR_R2_parse.py
```

### 2.Create samples.json file

`python3 sample2json.py --fastq_dir fq`

### 3. Run snakemake pipeline (customize -p as needed based on your HPC environment)
Before running Snakemake, please make sure all required R/Python packages used in the `.R`/`.py` file under the `script` folder are installed.

`snakemake --latency-wait 60 -p -j 99 --cluster-config cluster.json --cluster "sbatch -p common -J {cluster.job} --mem={cluster.mem} -N 1 -n {threads} -o {cluster.out} -e {cluster.err} " &> log &`

The output `02_fragment/*.log` files summarize alignment statistics and serve as QC metrics.  The `03_filtered/*.filtered.tsv.gz` files can be used in standard scATAC-seq downstream analysis (such as with [ArchR](https://www.archrproject.com/articles/Articles/tutorial.html)). Note: The fragment coordinates are not Tn5-offset corrected. Apply strand-specific offsets (start + 4 bp for the positive strand and end - 5 bp for the negative strand) if Tn5 insertion coordinates are needed.
