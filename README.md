X Assignment

##Data Inspection

### Attributes of `fang_et_al_genotypes`

Counting the number of columns:
```
awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt
```

Finding the size of the file in bytes:
```
du -h fang_et_al_genotypes.txt
```

Counting the number of lines:
```
wc -l fang_et_al_genotypes.txt
```

Counting the amount of each unique Group:
```
cut -f 3 sorted_fang_file.txt | uniq -c
```

Finding the file type:
```
file sorted_fang_file.txt
```

By inspecting this file I learned that `fang_et_al_genotypes.txt`:

1. has 986 columns
2. is 6.1M bytes
3. has 2783 lines
4. contains 290 ZMMIL, 1256 ZMMLR, 27 ZMMMR group members, which are for maize
5. contains 900 ZMPBA, 41 ZMPIL, 34 ZMPJA
6. is file-type `ASCII text, with very long lines` 

### Attributes of `snp_position.txt`

Counting the number of columns:
```
awk -F "\t" '{print NF; exit}' snp_position.txt
```

Finding the size of the file in bytes:
```
du -h snp_position.txt
```

Counting the number of lines:
```
wc -l snp_position.txt
```

By inspecting this file I learned that `snp_position.txt`:

1. has 15 columns
2. is 38K bytes
3. has 984 lines


## Data Processing

1. Prior to processing the data for maize and teosinte, I sorted the `fang_et_al` file using the following code.
```
sort -k3,3 fang_et_al_genotypes.txt > sorted_fang_file.txt
```
This sorted the data based on the Group ID in the third column of the file. It was sent to a new file called `sorted_fang_file.txt` before subsequent processing.

2. I also cut out the first `SNP_ID`, third `Chromosome` and fourth `Position` columns from the `snp_position.txt using this code:
```
cut -f 1,3,4 snp_position.txt > snp_position_columns_1_3_4.txt
```

### Maize Data

```
here is my snippet of code used for data processing
```

Here is my brief description of what this code does


### Teosinte Data

```
here is my snippet of code used for data processing
```

