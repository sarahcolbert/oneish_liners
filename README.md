# Helpful One-ish Liners in the Shell


## ONE LINERS FOR WORKING WITH SUMMARY STATISTICS

### How to add an N, or sample size column to summary statistics

In this example, we want to create a column with the same sample size (80000) for each SNP and name that column N.

```
awk '{print $0,80000}' file.txt | sed '1s/80000/N/' > file_with_N.txt
```

### How to swap allele columns that are in the wrong order (useful for SAIGE, PRS-CS)

Say for example you have a file which looks like:
```
SNP CHROM POS A1  A2
rs10875231  1 100000012 T G
rs186077422 1 10000006  A G
rs114947036 1 100000135 T A
rs6678176 1 100000827 T C
```

It might be the case, that depending on the GWAS software, A2 = effect/reference allele and A1 = non-reference allele. This is the case for summary statistics produced by SAIGE. For many programs, like PRS-CS, it may ask that A1 = reference allele and A2 = non-reference allele. You can switch the two columns using the code below. Be sure to swap column indices for the correct ones in your file if they differ. 

```
head -1 input_file.txt > output_file.txt; awk 'FNR > 1 { t = $4; $4 = $5; $5 = t; print; }' input_file.txt >> output_file.txt
```

### How to calculate betas from odds ratios

Say you have a summary statistics file with the following columns: SNP, A1, A2, OR, se, pval.
Since beta = log(OR) we can use the odds ratio to calculate the beta/effect size using awk. 

```
sed '1d' input_file.txt | awk '{print $1,$2,$3,log($4),$5,$6}' | sed '1i\SNP A1 A2 beta se pval' > output_file.txt

```


### How to add a header to a file

If you want to add a header to an existing file without creating a new file, you can use the '-i' flag with sed, like so:

```
sed -i '1i\SNP A1 A2 BETA P' input_file.txt
```

If you would like to create a new file instead of replacing, you can remove the '-i' flag and then pipe the output to a new file, like so

```
sed '1i\SNP A1 A2 BETA P' input_file.txt > output_file.txt
```



### How to remove NAs or rows with empty fields from munged summary statistics

This code is useful for input files for GNOVA and SUPERGNOVA, since they are not compatible with files with empty fields.

```
zcat munged.sumstats.gz | awk 'NF==5' > complete.sumstats.txt
```

## ONE LINERS FOR WORKING WITH PLINK FILES

### How to merge plink files that are separated by chromosome

In this example, we have plink files for a sample that are split up by chromosome (e.g., plink_chr1.{fam,bed,bim}, plink_chr2.{fam,bed,bim}, etc.). We want to merge these files to have one set of plink files for the entire genome.

```
for i in {1..22}
do
echo plink_chr$i >> mergelist.txt
done

plink --merge-list mergelist.txt --make-bed --out merged_plink
```


### Convert plink files to vcf files (and compress as .bgz files)

```
plink --bfile input_prefix --keep-allele-order  --recode vcf-iid bgz --out output_prefix
```

## NON-SPECIFIC ONE LINERS

### How to delete all files in a directory that contain a string

```
### use awk to print "rm filename" to remove.sh so that you can first check you are only removing the files you want
find . | xargs grep -l STRING | awk '{print "rm "$1}' > remove.sh
bash remove.sh
```


### Check outerr directory for jobs that may have been cancelled due to time limit and get a list of those jobs

You could also search for strings like "oom-kill" if jobs are being killed because not enough memory was requested

```
grep -H -r "TIME LIMIT" | awk '{print $1}' | sed 's/set.//g' | sed 's/.err:slurmstepd://g' | awk '{print}' ORS=',' > timed_out_list.txt
```

### Delete all lines containing a string except the first occurrence (this is useful for when you concatenate files with same header)

```
sed '1!{/^header/d;}' inputfile
```

### If you have results files named by job number, chromosome, etc. list all of those in a text file in descending order

```
### in directory containing results files
ls -1v foo*.txt > numbered_files.txt
```

Other things you may want to do with this file:

* Get a list of numbers

```
sed -i 's/.txt//g' numbered_files.txt
sed -i 's/foo//g' numbered_files.txt
```

* Get a list of numbers that are missing in the sequence

```
awk 'NR != $1 { for (i = prev + 1; i < $1; i++) {print i} } { prev = $1 + 1 }' ORS=',' numbered_files.txt > missing_numbered_files.txt
```

### Check squeue for how many jobs are running and pending

```
squeue -u saco3854 | awk '
BEGIN {
    abbrev["R"]="(Running)"
    abbrev["PD"]="(Pending)"
    abbrev["CG"]="(Completing)"
    abbrev["F"]="(Failed)"
}
NR>1 {a[$5]++}
END {
    for (i in a) {
        printf "%-2s %-12s %d\n", i, abbrev[i], a[i]
    }
}'
```
