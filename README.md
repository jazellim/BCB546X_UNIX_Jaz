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
6. is file-type _ASCII text, with very long lines_ 


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
4. is file-type _ASCII text_



## Data Processing

1. Prior to processing the data for maize and teosinte, I sorted the `fang_et_al.txt` file using the following code.
```
sort -k3,3 fang_et_al_genotypes.txt > sorted_fang_file.txt
```
*This sorted the data based on the Group ID in the third column of the file. It was sent to a new file called `sorted_fang_file.txt` before subsequent processing.*


2. I also cut out the first *SNP_ID*, third *Chromosome* and fourth *Position* columns from the `snp_position.txt` using this code:
```
cut -f 1,3,4 snp_position.txt > snp_position_columns_1_3_4.txt
```
*This cut out columns 1,3, and 4 from `snp_position.txt` and saved them to the new file `snp_position_columns_1_3_4.txt`*



### Maize Data

1. First, I cut out the rows of data for Maize groups, excluding the first two columns from the file, using the following code:
```
grep -E "(Sample_ID|ZMMIL|ZMMLR|ZMMMR)" sorted_fang_file.txt | cut -f 3-986 > maize_fang_file.txt
```
_The first portion of this code grabs all the rows containing **Sample-ID** or **ZMMIL**,**ZMMLR**, or **ZMMMR**, the groups associated with maize. This gets piped to the second command, which cuts columns 3-986 (all except the first two columns) and saves it to a new file_ `maize_fang_file.txt`.



2. Next, I transposed the file using the code given in the instructions.
```
awk -f transpose.awk maize_fang_file.txt > maize_transposed.txt
```
_This code transposed the file,_ `maize_fang_file.txt`_, such that the group names were across the top row and the SNP sites were down the first column. The resulting output was saved to a new file,_ `maize_transposed.txt`



3. The transposed file was then stripped of its header and sorted by SNP name using the following code:
```
grep -v "Group" maize_transposed.txt |sort -k1,1 > maize_organized_snps_noHeader.txt
```
_The first part grabs everything except lines containing "Group" (everything except for the top row). The remaining rows are then sorted by the 1st column, which contains the names of all the snps. The sorted data was saved to file_ `maize_organized_snps_noHeader.txt`.



4. To add the header row back to the organized snps file, I first created a txt file for the header using the following code:
```
head -n 1 maize_transposed.txt > maize_organized_snps_header.txt
```
_This took the first row of the file and sent it to file_ `maize_organized_snps_header.txt`



5. To combine the files i combined them with the following code:
```
cat maize_organized_snps_header.txt maize_organized_snps_noHeader.txt > maize_organized_snps.txt
```
_The code concatenates the two text files in the order they are given, so placing the header first, then the noHeader data returns the top row to the sorted snp data. This output is sent to_ `maize_organized_snps.txt`



6. In order for the organized maize data and the SNP position data to be joined, the header for the first column needed to be changed from "Group" to "SNP_ID", so that the first column in both files would be identical. I did this using the following code:
```
sed 's/Group/SNP_ID/' maize_organized_snps.txt > maize_organized_snps_Groups_to_SNP_ID.txt
```
_I used the sed command to replace the word 'Group' with my desired header name, and then I sent the output to a new file called_ `maize_organized_snps_Groups_to_SNP_ID.txt`



7. I then joined the snp position file and the organized maize file with the new header name using the following code:
```
join -1 1 -2 1 -t $'\t' snp_position_columns_1_3_4.txt maize_organized_snps_Groups_to_SNP_ID.txt > maize_joined.txt
```
_At the beginning, -1 1 & -2 1 denote that the common column for both file 1 and file 2 is Column1. The flag -t allows me to choose the delimitation of my new file using $'\t' for tab. The output is sent to a new file_ `maize_joined.txt`



8. The joined data was then separated into new files, corresponding to the chromosome number. I completed this using the following code:
```
for i in {1..10}; do awk '$2 =='$i'' maize_joined.txt > maize_chr_"$i".txt; done
```
_The first part of this code sets up a for-loop from 1-10. For each iteration of the loop, awk looks for places in column $2 (the Chromosome column) of the joined maize file that are equal to $i (an integer 1-10). All of the rows where this is true are sent to a file_ `maize_chr_"$i".txt` _where $i in the name changes in every iteration to the value of variable i._

8. For the chromosome designations 'multiple' and 'unknown', I could not figure out how to include them in the loop, so I did them individually using the following code:
```
awk '$2 ~ /multiple/' maize_joined.txt > maize_chr_multiple.txt
```
```
awk '$2 ~ /unknown/' maize_joined.txt > maize_chr_unknown.txt
```
_The awk command checks the 2nd column of_ *maize_joined* _for 'multiple' and 'unknown', respectively, in these two lines of code. In each case the rows found by awk are sent to files called_ `maize_chr_multiple.txt` _and_ `maize_chr_unknown.txt`_, respectively._ 
***These two files, having no more additional modifications needed, were transferred to the newly-made `final_assignment_files/maize` folder.***



10. The separated chromosome files for maize were then sorted in increasing order based on their position in the chromosome using the following code:
```
for i in {1..10}; do sort -k3,3n maize_chr_"$i".txt > ./final_assignment_files/maize/maize_chr_"$i"_increasing.txt;done
```
_The first part sets up a loop from 1-10 of variable i. The 'i'th chromosome is then sorted based on column 3, which is numerical._ **The output of this sort is sent to the maize folder in the *'final_assignment_files'* folder, with the output files being titled *`maize_chr_"$i"_increasing.txt`* where '$i' is the number of the current loop.** _The loop is then concluded with a 'done'._ 



11. The chromosome files were then sorted in decreasing order and had their markers for 'unknown' changed from '?' to '-'. This was done using this code: 
```
for i in {1..10}; do sort -k3,3nr maize_chr_"$i".txt | sed 's/?/-/g' > ./final_assignment_files/maize/maize_chr_"$i"_decreasing.txt;done
```
_A loop is established from 1-10. The 'i'th file is sorted according to its 3rd column, position, as before, only this time there is an additional 'r' denoting it is being sorted in reverse order. The 'sed' command changes ? to -, with 'g' ensuring all ?'s are replaced._ **The output is sent to the maize folder in the *final_assignment_files* folder with the title *`maize_chr_"$i"_decreasing.txt`* where once again '$i' is the number of the current loop.** _The loop is then concluded with a 'done'._

### Teosinte Data

1. At the same time as I did for the maize, I cut out the rows of data for the Teosinte groups, excluding the first two columns from the file, using the following code:
```
grep -E "(Sample_ID|ZMPBA|ZMPIL|ZMPJA)" sorted_fang_file.txt | cut -f 3-986 > teosinte_fang_file.txt
```
_The first portion of this code grabs all the rows containing **Sample-ID** or **ZMPBA**,**ZMPIL**, or **ZMPJA**, the groups associated with teosinte. This gets piped to the second command, which cuts columns 3-986 (all except the first two columns) and saves it to a new file_ `teosinte_fang_file.txt`.


2. Next, I transposed the file using the code given in the instructions.
```
awk -f transpose.awk teosinte_fang_file.txt > teosinte_transposed.txt
```
_This code transposed the file,_ `teosinte_fang_file.txt`_, such that the group names were across the top row and the SNP sites were down the first column. The resulting output was saved to a new file,_ `teosinte_transposed.txt`



3. Like the maize file in the previous section, the transposed teosinte file was then stripped of its header and sorted by SNP name using the following code:
```
grep -v "Group" teosinte_transposed.txt |sort -k1,1 > teosinte_organized_snps_noHeader.txt
```
_The first part grabs everything except lines containing "Group" (everything except for the top row). The remaining rows are then sorted by the 1st column, which contains the names of all the snps. The sorted data was saved to file_ `teosinte_organized_snps_noHeader.txt`


4. To add the header row back to the organized snps file, I first created a txt file for the header using the following code:
```
head -n 1 teosinte_transposed.txt > teosinte_organized_snps_header.txt
```
_This took the first row of the file and sent it to file_ `teosinte_organized_snps_header.txt`



5. To combine the files I combined them with the following code:
```
cat teosinte_organized_snps_header.txt teosinte_organized_snps_noHeader.txt > teosinte_organized_snps.txt
```
_The code concatenates the two text files in the order they are given, so placing the heade
r first, then the noHeader data returns the top row to the sorted snp data. This output is
 sent to_ `teosinte_organized_snps.txt`


6. In order to be able to join my teosinte file and the SNP positions file, I needed to replace the header of the first column to "SNP_ID" so that the corresponding columns in the two files would match. I achieved this with the following code:
```
sed 's/Group/SNP_ID/' teosinte_organized_snps.txt > teosinte_organized_snps_Groups_to_SNP_ID.txt
```
_I used the 'sed' command to replace 'Group' with_ *'SNP_ID' in the teosinte data file and sent that output to the file* `teosinte_organized_snps_Groups_to_SNP_ID.txt`



7. The teosinte data file and the snp position file were then joined using the following code:
```
join -1 1 -2 1 -t $'\t' snp_position_columns_1_3_4.txt teosinte_organized_snps_Groups_to_SNP_ID.txt > teosinte_joined.txt
```
_The first part of this code notes that the common column for both file 1 & 2 is Column1. The -t flag denotes that I am choosing the delimitation on the joined file, and $'\t' indicates the delimiter will be tabs. After joining, the output is sent to the file_ `teosinte_joined.txt`



8. Just like in the maize processing, the joined teosinte data was separated by Chromosome number with the following code:
```
for i in {1..10}; do awk '$2 =='$i'' teosinte_joined.txt > teosinte_chr_"$i".txt; done
```
_The first part of this code sets up a for-loop from 1-10. For each iteration of the loop, awk looks for places in column $2 (the Chromosome column) of the joined teosinte file that are equal to $i (an integer 1-10). All of the rows where this is true are sent to a file_ `teosinte_chr_"$i".txt` _where $i in the name changes in every iteration to the value of variable i._



9.  As with the maize data, the 'multiple' and 'unknown' chromosome designations were dealt with separately withe following code:
```
awk '$2 ~ /multiple/' teosinte_joined.txt > teosinte_chr_multiple.txt
```
```
awk '$2 ~ /unknown/' teosinte_joined.txt > teosinte_chr_unknown.txt
```
_The awk command checks the 2nd column of_ *teosinte_joined* _for 'multiple' and 'unknown', respectively, in these two lines of code. In each case the rows found by awk are sent to files called_ `teosinte_chr_multiple.txt` _and_ `teosinte_chr_unknown.txt`_, respectively._
***These two files, having no more additional modifications needed, were transferred to the newly-made `final_assignment_files/teosinte` folder.***




10. I established a loop to sort each teosinte chromosome file in increasing order. The loop is seen in this code: 
```
for i in {1..10}; do sort -k3,3n teosinte_chr_"$i".txt > ./final_assignment_files/teosinte/teosinte_chr_"$i"_increasing.txt;done
```
_A loop is created from 1-10 in variable 'i'. In each loop, the 'i'th chromosome file gets sorted numerically by its 3rd column, the position data._ **The output is sent to the Teosinte folder inside the *final_assignment_files* folder under the name *`teosinte_chr_"$i"_increasing.txt`* where '$i' is the number of the current loop.** _The loop is then completed with 'done'._




11. The chromosome files for teosinte were then sorted in decreasing order in the same fashion as maize, and their markers for 'unknown' changed from '?' to '-'. This was done using this code:
```
for i in {1..10}; do sort -k3,3nr maize_chr_"$i".txt | sed 's/?/-/g' > ./final_assignment_files/maize/maize_chr_"$i"_decreasing.txt;done
```
_A loop is established from 1-10 for the variable 'i'. The 'i'th file is sorted based on its 3rd column, position, as it was in step 10, only this time there is an additional 'r' denoting it is being sorted in reverse order. The 'sed' command changes ? to -, with 'g' ensuring all ?'s are replaced._ **The output is sent to the teosinte folder in the *final_assignment_files* folder with the title *`teosinte_chr_"$i"_decreasing.txt`* where once again '$i' is the number of the current loop.** _The loop is then concluded with a 'done'._

## Final Touches
### Organizing Files
* The files outside of the 'final' folder are renamed slightly to reflect at which step they were created. For example:
```
mv teosinte_fang_file.txt step1_teosinte_fang_file.txt
```
_Similar renamings are done to the rest of the files generated throughout the process in steps1-7._

* The step8 files are renamed using a loop:
```
for i in {1..10}; do mv teosinte_chr_"$i".txt step8_teosinte_chr_"$i".txt; done
```
_A loop is made from 1-10, and the 'i'th number file is moved to a new file with the same name, only with a 'step8' attached to the beginning of the name._

