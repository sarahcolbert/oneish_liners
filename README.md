# Helpful One-ish Liners in the Shell


#### How to delete all files in a directory that contain a string

```
### use awk to print "rm filename" to remove.sh so that you can first check you are only removing the files you want
find . | xargs grep -l STRING | awk '{print "rm "$1}' > remove.sh
bash remove.sh
```



#### How to identify error files that were halted

```
### in outerr directory
find . | xargs grep -l halted| awk '{print $1}'
```



#### How to add an N, or sample size column

In this example, we want to create a column with the same sample size (80000) for each SNP and name that column N.

```
awk '{print $1,$2,$3,$4,$5,80000}' file.txt > file_with_N.txt
sed -i '1s/83566/N/' file_with_N.txt
```

#### How to swap allele columns that are in the wrong order

Say for example you have a file which looks like:
```
SNP CHROM POS A1  A2
rs10875231  1 100000012 T G
rs186077422 1 10000006  A G
rs114947036 1 100000135 T A
rs6678176 1 100000827 T C
```

And A2 is actually the effect/reference allele and A1 is the non-reference allele so you want to switch these.

```
head -1 input_file.txt > output_file.txt; awk 'FNR > 1 { t = $4; $4 = $5; $5 = t; print; }' input_file.txt >> output_file.txt

```
#### Check outerr directory for jobs that may have been cancelled due to time limit and get a list of those jobs

```
grep -H -r "CANCELLED" | awk '{print $1}' | sed 's/set.//g' | sed 's/.err:slurmstepd://g' | awk '{print}' ORS=',' > failed_list.txt
```

#### If you have files with numbers, list all of those in a text file in descending order

```
ls -1v *.txt > ../numbered_files.txt
```

Other things you may want to do with this file:

Get just a list of numbers

```
sed -i 's/.txt//g' numbered_files.txt
```

Get a list of missing files

```
awk '{for(i=p+1; i<$1; i++) print i} {p=$1}' numbered_files.txt > missing_numbered_files.txt
```
