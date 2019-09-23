# UNIX Assignment

## Data Inspection

### Attributes of `fang_et_al_genotypes`

**Counting the number of columns:**
```
awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt
```
_This code counts the number of tabs to determine the number of columns._

**Finding the size of the file in bytes:**
```
du -h fang_et_al_genotypes.txt
```
_This code uses the du command with the human-readable tag -h to show the size of the file in bytes._

**Counting the number of lines:**
```
wc -l fang_et_al_genotypes.txt
```
_This code uses word count with the tag -l to find the line count for the file._

**Counting the amount of each unique Group:**
```
cut -f 3 sorted_fang_file.txt | uniq -c
```
_This code cuts out file 3 (Group) of the sorted file and then counts how many of each unique Group name there are._

**Finding the file type:**
```
file sorted_fang_file.txt
```
_This code gives the file-type of the file it is given._

By inspecting this file I learned that `fang_et_al_genotypes.txt`:

1. has 986 columns
2. is 6.1M bytes
3. has 2783 lines
4. contains 290 ZMMIL, 1256 ZMMLR, 27 ZMMMR group members, which are for maize
5. contains 900 ZMPBA, 41 ZMPIL, 34 ZMPJA, which are for teosinte
6. is file-type "_ASCII text, with very long lines_" 

### Attributes of `snp_position.txt`

**Counting the number of columns:**
```
awk -F "\t" '{print NF; exit}' snp_position.txt
```
_This code counts the number of tabs to determine the number of columns._

**Finding the size of the file in bytes:**
```
du -h snp_position.txt
```
_This code uses the du command with the human-readable tag -h to show the size of the file in bytes._

**Counting the number of lines:**
```
wc -l snp_position.txt
```
_This code uses word count with the tag -l to find the line count for the file._

**Finding the file type:**
```
file sorted_fang_file.txt
```
_This code gives the file-type of the file it is given._

By inspecting this file I learned that `snp_position.txt`:

1. has 15 columns
2. is 38K bytes
3. has 984 lines
4. is file-type '_ASCII text_'


## Data Processing

1. Prior to processing the data for maize and teosinte, I sorted the `fang_et_al.txt` file using the following code.
```
sort -k3,3 fang_et_al_genotypes.txt > sorted_fang_file.txt
```
This sorted the data based on the Group ID in the third column of the file. It was sent to a new file called `sorted_fang_file.txt` before subsequent processing.

2. I also cut out the first *SNP_ID*, third *Chromosome* and fourth *Position* columns from the `snp_position.txt using this code:
```
cut -f 1,3,4 snp_position.txt > snp_position_columns_1_3_4.txt
```

### Maize Data

1. First, I cut out the rows of data for Maize groups, excluding the first two columns from the file, using the following code:
```
grep -E "(Sample_ID|ZMMIL|ZMMLR|ZMMMR)" sorted_fang_file.txt | cut -f 3-986 > maize_fang_file.txt
```

Here is my brief description of what this code does


### Teosinte Data

1. At the same time as I did for the maize, I cut out the rows of data for the Teosinte groups, excluding the first two columns from the file, using the following code:
```
grep -E "(Sample_ID|ZMPBA|ZMPIL|ZMPJA)" sorted_fang_file.txt | cut -f 3-986 > teosinte_fang_file.txt
```

