# Helpful One-ish Liners in the Shell


#### How to delete all files in a directory that contain a string

```
### use awk to print "rm filename" to doit.sh so that you can first check you are only removing the files you want
find . | xargs grep -l STRING | awk '{print "rm "$1}' > remove.sh
bash remove.sh
```


#### How to identify error files that were halted

```
### in outerr directory
find . | xargs grep -l halted| awk '{print $1}'
```
