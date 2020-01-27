# Joseon Civil Official Roster Data

There are multiple steps for this project.

## Joseon Munkwa Bangmok (문과 방목) Data
Analyzing Korean Joseon-Dynasty Civil Service Bangmok (roster) data 조선 문과 방목 데이타

### Versions

#### Lee Version (이재옥)
* From [AKS website](http://dh.aks.ac.kr/~sonamu5/wiki/index.php/SEDB:%EA%B3%BC%EA%B1%B0_%ED%95%A9%EA%B2%A9%EC%9E%90_%EC%A0%95%EB%B3%B4_%EB%94%94%EC%A7%80%ED%84%B8_%EC%95%84%EC%B9%B4%EC%9D%B4%EB%B8%8C)
* 14638 people
* Need to join all of the CSVs into a single CSV. This is the more recent version.
* Includes (extra) family information.

#### Wagner-Song Version
* 14607 people
* Will be merged into the Lee version

### Merged Version (Final version)
Code can be found in the `Munkwa Version Merge` Jupyter notebook.

This is the final version with the Lee and Wagner Song (WS) versions combined.

Steps I took:
1. Merge the Lee versions into one table (currently, there are 3 tables)
2. Clean up WS and Lee versions (encodings). Unique GUID for each entry in Lee version. Unique GUID for each entry in WS version.
3. Create merged "master" table. Steps for this (unfortunatley, some were more manual than others):
    * In the same year, Chinese names are same, Korean names are more than 50% similar
    * In the same year, Korean names are same, Chinese names are more than 50% similar
    * In the same year, Chinese names are same, Korean names are 50% similar
    * In the same year, Korean names are same, Chinese names are 50% similar
    * In the same year, both Korean and Chinese names are 50% similar
    * Manually look at the leftovers and try to match them together
 4. Append a unique GUID for each entry in merged master table. There are some repeats of `lee_uuid` and `ws_uuid` because there were entries of people taking a test more than once in the same year in Lee table, and people doing same thing in WS table, but these people did not necessarily always overlap
 5. Export to CSV. This is the `/data/Merged_Munkwa.csv`

### Data Schema
This is the data schema for the final merged master table. 

(Note: I am not including all of the columns of the dataframe. There are too many, so I am only including the most important ones)

|name  | type | notes|
|------|-----|--------|
| uuid| UUID | unique UUID for each entry in the merged table |
| pass_year | Year | The year that the person passed the exam |
| chi_name| Hanja | Exam-taker's name in Chinese characters |
| kor_name | Korean | Exam-taker's name in Korean characters |
| birth | Year | The year the person was born |
| death | Year | The year the person was born |
| bid| URL | unique URL for each person from the Lee table |
| bpid | Code | unique code for each person from the Lee table | 
| id1 | Integer | Id from the WS table |
| source | code | Source of the person from the WS table |
| lee_uuid| UUID | Unique UUID from Lee table. Unique for each exam taken | 
| ws_uuid | UUID | Unique UUID from WS table. Unique for each exam taken | 
| repeated_testtaker_x | Boolean | True if the person took the test more than once, False otherwise (Lee table) | 
| repeated_testtaker_x | Boolean | True if the person took the test more than once, False otherwise (WS table) | 
| other_kor_name_in_WS_version | Korean | Another spelling of the person's name in Korean, from WS table |
| other_kor_name_in_WS_version_uuid | UUID | UUID of the corresponding row for the name in `other_kor_name_in_WS_version` |
| other_chi_name_in_WS_version | Hanja | Another spelling of the person's name in Chinese, from WS table |
| other_chi_name_in_WS_version_uuid | UUID | UUID of the corresponding row for the name in `other_chi_name_in_WS_version` |



## Record Linkage

Connecting Joseon Munkwa (문과) and Sillok (실록)

* Currently, about 4000 in munkwa can be identified in sillok.
* Can use different ML techniques to fill in the gaps, and have professionals eye-check to confirm which results are the most valid.
* An issue we currently have: for each bangmok ID, we can have multiple sillok IDs (one-to-many)


### TO DO
1. Identify the missing munkwa not in sillok by matching names, years, etc. 


## Joseon Official Position Dictionary (조선시대 관직 서전 만들기)

