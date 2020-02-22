# Joseon Civil Official Roster Data

In this project, we are attempting to unify and match records of civil officials from the [Korean Joseon Dynasty](https://en.wikipedia.org/wiki/Joseon) (1392-1897) across various sources. Our two main sources of record data come the Joseon Munkwa Bangmok (문과 방목), which is the civil service roster data and of which there are multiple versions, and the Annals of the Joseon Dynasty (조선왕조실록). More details about the data are mentioned below. 

The project will follow the following stages:

1. Disambiguation of Bangmok data
2. Disambiguation of Sillok data
3. Record linkage of civil officials between the two sources


## 1. Joseon Munkwa Bangmok (조선 문과 방목) Data
There are currently two main versions of the bangmok data. Each lists a set of unique civil service officials that are mostly aligned. We attempt to merge these two versions based on the official's Korean name, Hanja (Chinese) name, and the year they took the test. Due to differences between the two versions in character encoding, mispellings, or typos, we compare the names in each list with a similarity index, and can be found in `/code/rawToPreprocess/MunkwaVersionMerge.ipynb`.

### Versions
|Version  | Number of people | Notes|
|------|-----|--------|
| Lee Version (이제옥)| 14638 | More recently updated than Wagner-Song Version. [Link from AKS](http://dh.aks.ac.kr/~sonamu5/wiki/index.php/SEDB:%EA%B3%BC%EA%B1%B0_%ED%95%A9%EA%B2%A9%EC%9E%90_%EC%A0%95%EB%B3%B4_%EB%94%94%EC%A7%80%ED%84%B8_%EC%95%84%EC%B9%B4%EC%9D%B4%EB%B8%8C) |
| Wagner-Song Version | 14607 | To be merged into Lee Version |


### Data Schema for Merged Bangmok Table
The final merged version can be found at `/data/preprocessing/Merged_Munkwa.csv` and includes 15152 people. This slightly inflated number is due to the number of repeat testtakers in Lee Version (same person who took the test in the same year multiple times) not present in WS Version, and vice versa, as well as due to the number of people we could not match due to encoding or typo errors, or simply due to not existing in one version or the other.

The two versions were merged on the Korean name, Chinese name, and the year the exam was taken

|Name  | Type | Notes|
|------|-----|--------|
| lee_uuid| UUID | unique UUID for each entry from the Lee Version |
| ws_uuid | UUID | unique UUID for each entry from the Wagner-Song Version |


## 2. Disambiguation of Sillok Data
[The Annals of the Joseon Dynasty](http://sillok.history.go.kr/main/main.do), also known as the [Veritable Records of the Joseon Dynasty](http://esillok.history.go.kr/front/aboutSillok/translationProject/TranslationProjectInfo.do?menuNo=7000000&leftMenuNo=7020000), are authentic records of the Joseon Dynasty documenting its five-century history. The Sillok comprises of 1983 books covering the entire 472 years of the dynasty. 

In the Sillok, officials' names are mentioned many times with unique IDs associated with each unique person. However, there are many errors, four of which we mention below, and which we aim to resolve using a co-occurence network.


|Case  | Notes |
|------|-----|
| `to_split`| This is when there are two people in the Sillok with the same name and same ID, but are in actuality two different people (for example, in the more likely scenario, there were two people with the same name operating in different centuries, and the translator or inscriptor erroneously marked them with the same ID). We need to split these occurences into two separate IDs to represent two different people. |
| `to_merge` | This is when there are two people in the Sillok with the same name and different IDs, but are in actuality the same person (for example, person with name A has ID 1 and another person with name A has ID 2, but they should actually be represented as the same person). We need to merge these occurences into one ID to represent one single person. |
| 동명이인 (同名異人)  | Same name, different person: This is when there are two people in the Sillok with the same name but are in actuality different people |
| 이명동인 (異名同人)  | Different name, same person: This is when there are two people in the SIllok with different names but are in actuality the same person |


## 3. Record Linkage
Our goal is to connect the names of the officials mentioned in the Bangmok (문과 방목) with the officials mentioned in the Sillok (조선왕조실록). 

* Currently, about 4000 in munkwa can be identified in sillok.
* Can use different ML techniques to fill in the gaps, and have professionals eye-check to confirm which results are the most valid.
* We plan to use the [Python Record Linkage Toolkit](https://recordlinkage.readthedocs.io/en/latest/about.html)



## Joseon Official Position Dictionary (조선시대 관직 사전 만들기)



# Data Hierarchy
Our data hierarchy is as follows:

* `raw`: Raw data files
    * nikh - Sillok data ([국사편찬위원회 실록 부가열람 자료](https://www.data.go.kr/dataset/3071310/fileData.do)). Last updated: 2019년 9월 5일
    * lnis - Joseon family relations (만성대동보 자료에 기반한 조선시대 친족관계망정보시스템). [Source](lnis.kr)
        * `lnis_family.csv` 
        * `lnis_person.csv` 
    * `WS_Munkwa.csv` - Wagner-Song Munkwa data
    * bangmok 
        * (A) bangmok career records - officials' and their career (관직 데이터: 관료들의 관력) (A)
        * (B) bangmok person data - officials' birth year, death year, etc (인물 데이터: 인물의 생년, 졸년, 본관 등)
    * 선원계보 데이터: 왕실 인물 데이터



  * `preprocessing`: Fixing types, nulls, spellings, encodings, spellings, etc. 
      * `records.xlsx`: Preprocess (A) 
        * Alter column names
        * Remove unnecessary columns
      * `figures.xlsx`: Preprocess (B)
        * Fix Hanja unicode issue (in progress)
        * Alter column names (in progress)
        * Remove unnecessary columns (in progress)
    * bangmok: [Source](http://people.aks.ac.kr/index.aks) (한국학 중앙연구원 방목 데이터)
        * `bangmok_person.csv`: the person entity (bpid, chnname, korname, birth, death, plastic)
        * `bangmok_career.csv`: the career entity (bangmok의 전력(previous job)만 따로 뗀 정보)
        * `bangmok_family.csv`: the family entity (family members related to the bangmok_person)
        * `bangmok.csv`: the exam entity (affilliation, rank, competitors, exam_type, pass_year, king, lid, previous, exam_class, class_rank, pass_date)
    * `Merged_Munkwa.csv`: merging matching people from `bangmok` and `WS_Munkwa.csv`
    * `sillok.csv`: Original Sillok file from 국사편찬위원회 with original Hanja text ([Source](http://sillok.history.go.kr/main/main.do)) and Korean translation (crawled from the webpage, created by @dhchoi)
    * `sillok_name.csv`: the name ID (이름) and recorder ID (기사) extracted from `sillok.csv`. We will need to use co-corruence network or person_id frequency 
    * `munkwa_ws_uuid_dict.csv`: Mapping of `lee_uuid` in `Merged_Munkwa` to the `bpid` in `bangmok`
    * `munkwa_lee_uuid_dict.csv`: Mapping of `ws_uuid` in `Merged_Munkwa` to the `id1` in `WS_Munkwa.csv`

 * `reference`: Not necessarily data generated directly from Sillok or Bangmok, but data created to help with analysis and processing 
    * `uci_change.csv`: Mapping of UCI Ids (한국학중앙연구원에서 만든 UCI 중 현재 ID가 변경된 UCI들)
    * `dates.csv`: Conversion between lunar and solar calendar (음력, 양력 conversion 충돌 문제가 있기 때문에, 이 테이블을 참조해서 양력과 음력을 conversion. rdate는 조선왕조 개국을 0일로 하고 1일씩 더한 데이터)
    * `gwanjik.csv`: Gwanjik (officials' position) dictionary (관직사전). [Source](http://ryu-nakbong.kr/xe/nakbong/clanlibrary/chosunofficials_2.htm)


