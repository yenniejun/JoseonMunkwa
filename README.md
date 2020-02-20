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


## Joseon Official Position Dictionary (조선시대 관직 사전 만들기)


# Data
Our data hierarchy is as follows:
* raw - the raw data files
  * nikh - 국사편찬위원회 실록 부가열람 자료 (링크:https://www.data.go.kr/dataset/3071310/fileData.do), 마지막 수정일:2019년 9월 5일
   * 관직 데이터: 관료들의 관력(career records)  -- (A)
   * 인물 데이터: 인물의 생년, 졸년, 본관 등 -- (B)
   * 선원계보 데이터: 왕실 인물 데이터
  * lnis, lnis_person - 만성대동보 자료에 기반한 조선시대 친족관계망정보시스템(링크:lnis.kr)
  * WS_Munkwa.csv - Wagner-Song Munkwa data

* preprocessing - fixing types/nulls/spellings, fixing encodings, fixing spellings
  * records.xlsx: (A)를 preprocessing. 
    * column 이름 변경해야 함.
    * 필요 없는 column 제거해야 함. 
  * figures.xlsx: (B)를 preprocessing.
    * 한자 유니코드 문제 (해결 중)
    * column 이름 변경해야 함.
    * 필요 없는 column 제거해야 함.
  * bangmok: 한국학 중앙연구원 방목 데이터 (링크:http://people.aks.ac.kr/index.aks) -- (C)
    * bangmok_person: 사람을 entity로 두고 표현할 수 있는 정보들 - bpid, chnname, korname, birth, death, plastic
    * bangmok: 과거 시험 기록을 entity로 두고 표현할 수 있는 정보들 - affilliation, rank, competitors, exam_type, pass_year, king, lid, previous, exam_class, class_rank, pass_date
    * bangmok_career: bangmok의 전력(previous job)만 따로 뗀 정보
    * bangmok_family: bangmok_person의 가족을 분리한 정보
  * Merged_Munkwa.csv: (C)를 하나로 합친 데이터
  * Lee_Munkwa.csv: ?? <- Yennie 작성
  * sillok.csv: 국사편찬위원회 조선왕조실록 원문파일(한문) + 웹페이지(한글번역) (링크: http://sillok.history.go.kr/main/main.do, created by dhchoi)
  * sillok_name.csv: sillok.csv에서 사람 ID와 기사 ID만 떼어내서 explode. co-occurence network 또는 사람 ID의 frequency 측정용.
 * reference - 실록, 방목에서 generated data는 아니지만, 참고할 용도로 만들어진 데이터셋
   * uci_change.csv: 한국학중앙연구원에서 만든 UCI 중 현재 ID가 변경된 UCI들.
   * dates.csv: 음력, 양력 conversion 충돌 문제가 있기 때문에, 이 테이블을 참조해서 양력과 음력을 conversion. rdate는 조선왕조 개국을 0일로 하고 1일씩 더한 데이터.
   * gwanjik.csv: 관직사전. 참조링크:http://ryu-nakbong.kr/xe/nakbong/clanlibrary/chosunofficials_2.htm
* linkage - linking the different files, adding columns, processing


