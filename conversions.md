### Convert plink files to vcf files (and compress as .bgz files)

```
plink --bfile input_prefix --keep-allele-order  --recode vcf-iid bgz --out output_prefix
```

### Convert vcf files to plink files

```
plink --vcf my.vcf.gz  --make-bed --out output_prefix
```

### Convert 
