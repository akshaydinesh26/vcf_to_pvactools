# VCF to pvactools Nextflow Workflow

## Overview
**VCF to pvactools** is a modular Nextflow workflow for **neo peptide prediction** from tumor–normal sequencing data in the form of a vcf generated with mutect2 using sarek workflow. RNAseq data is added from the bam and gene and isoform count file into vcf before its processed for pvactools which is the neo peptide prediction tool. This workflow is coding the procedure recommened in the pvactools manual into a nextflow pipeline.



---

## Key Features
- Modular Nextflow (DSL2-ready structure)
- Fully Dockerized
- Supports DNA + RNA + Fusion integration
- Reproducible and scalable
- Per-patient output organization

---

## Prerequisites

### System Requirements
- Linux
- Nextflow >= 24.10.6
- Docker

### Required Input Data

#### DNA (nf-core/sarek)
- Tumor CRAM/BAM
- Filtered VCF (Mutect2 → filterMutectCalls)

#### RNA-seq (nf-core/rnaseq)
- Gene expression (RSEM)
- Isoform expression
- BAM file

#### Fusion (nf-core/rnafusion)
- Arriba fusion output

#### HLA Typing
- OptiType / HLA-HD / targeted typing

---

## Reference Data Requirements
- VEP Cache (local)
- Reference FASTA (same as VEP)
- GTF annotation (matching VEP version)
- IEDB cache (included in pVACtools container)

---

##  Installation

### 1. Install Docker
https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04

Enable Docker without sudo:
```bash
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
docker run hello-world
```

---

### 2. Install Nextflow
```bash
curl -s https://get.nextflow.io | bash
mv nextflow ~/bin/
```

Docs: https://www.nextflow.io/docs/latest/install.html

---

### 3. Setup VEP Cache
https://asia.ensembl.org/info/docs/tools/vep/script/vep_cache.html

---

## Docker Images

Pull required images:
```bash
docker pull quay.io/biocontainers/vt:2015.11.10--h5ef6573_4
docker pull ensemblorg/ensembl-vep:release_113.0
docker pull griffithlab/pvactools:5.4.1
docker pull quay.io/biocontainers/htslib:1.22.1--h566b1c6_0
docker pull staphb/samtools:latest
docker pull mgibio/bam_readcount_helper-cwl:1.2.1
docker pull griffithlab/vatools:5.2.0
```

---

## Custom Docker Images

moce into the dockerfile folder and create the following custom images. These images use the openly available docker images as base and adds the custom scripts to it.

```bash
docker build -f pvactools_5.4.1_plus_1 -t pcv/pvactools_plus:5.4.1 .
docker build -f regtools_plus_1 -t pcv/regtools_plus:1 .
```

---

## Setup Workflow

```bash
unzip phantom_mini.zip
cd phantom_mini
```

### Configuration
- Update `nextflow.config`:
  - FASTA path - the fasta can be downloaded from igenome.
  - GTF path - from igenome
  - CPU & memory - based on system availability

- Add VEP cache path in:
```
conf/profile.config
```

---

## Input Format

Prepare a TSV sample sheet using the provided template.

Each row = one patient/sample.

---

## Run Workflow

```bash
nextflow run phantom.nf \
  --sample_sheet input.tsv \
  --cpus 8 \
  --max_memory 32.GB \
  --outdir results/
```

---

## Output

- `results/` → final outputs
  - `PATIENT_ID/` → per-patient results
- `work/` → intermediate files
- `nextflow.log` → execution log

If `--outdir` is not specified:
```
./results
```

---

## Workflow Summary

```
VCF + RNA + Fusion + HLA
        ↓
   Annotation (VEP)
        ↓
   pVACtools Modules
        ↓
 Neoantigen Candidates
```

---

## Use Cases
- Cancer immunotherapy research
- Neoantigen vaccine design
- Multi-omics analysis pipelines

---

## Contributing
Pull requests are welcome.

---

## Author
Akshay Dinesh - github.com/akshaydinesh26
