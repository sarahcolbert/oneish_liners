### Convert plink files to vcf files (and compress as .bgz files)

```
plink --bfile input_prefix --keep-allele-order  --recode vcf-iid bgz --out output_prefix
```

### Convert vcf files to plink files (when split by chr)

```
for chr in {1..22}; 
do plink --vcf chr${chr}.vcf.gz  --make-bed --out $chr; 
done
```

### Convert 
