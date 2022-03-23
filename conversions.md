### Basic conversion script when split by chromosome using sbatch job

```
#!/bin/bash

#SBATCH --job-name=vcf2plink
#SBATCH --time=01:00:00
#SBATCH --array=1-22
#SBATCH --cpus-per-task=1
#SBATCH --mem-per-cpu=2G
#SBATCH --output=./logs/chr%a.out
#SBATCH --error=./logs/chr%a.err

ml load plink/1.9

chr=${SLURM_ARRAY_TASK_ID}

[INSERT CONVERSION COMMAND] 
```

### Basic conversion script when split by chromosome using interactive job

```
for chr in {1..22}; 
do [INSERT CONVERSION COMMAND]; 
done
```

### Convert plink files to vcf files (and compress as .bgz files)

```
plink --bfile input_prefix --keep-allele-order  --recode vcf-iid bgz --out output_prefix
```

### Convert vcf files to plink files
* may need to add --double-id if your vcf files do not have FID in the sample IDs

```
plink --vcf chr${chr}.vcf.gz  --make-bed --out $chr 
```
