# Joseon Civil Official Roster Data

There are multiple steps for this project.

## Joseon Munkwa Bangmok (문과 방목) Data
Analyzing Korean Joseon-Dynasty Civil Service Bangmok (roster) data 조선 문과 방목 데이타

### Versions

#### Lee Version
* 이재옥 
* From [AKS website](http://dh.aks.ac.kr/~sonamu5/wiki/index.php/SEDB:%EA%B3%BC%EA%B1%B0_%ED%95%A9%EA%B2%A9%EC%9E%90_%EC%A0%95%EB%B3%B4_%EB%94%94%EC%A7%80%ED%84%B8_%EC%95%84%EC%B9%B4%EC%9D%B4%EB%B8%8C)
* 14638 명
* Need to join all of the CSVs into a single CSV. This is the more recent version.
* Includes family information.

#### Wagner-Song Version
* 14607 명
* Will be merged into the Lee version

### TO DO for Version Merging
1. Merge the Lee versions into one table
2. 한자명 unicode, 한글명 통일 (use Lee version as source of truth) for each dataset
3. Create unique GUID for each person in Lee version
4. Create unique GUID for each person in WS version
5. Merge Lee and WS tables with new unique GUID for each person. Mark which people are missing from WS that are in Lee


### Data Schema


## Record Linkage

Connecting Joseon Munkwa (문과) and Sillok (실록)

* Currently, about 4000 in munkwa can be identified in sillok.
* Can use different ML techniques to fill in the gaps, and have professionals eye-check to confirm which results are the most valid.
* An issue we currently have: for each bangmok ID, we can have multiple sillok IDs (one-to-many)


### TO DO
1. Identify the missing munkwa not in sillok by matching names, years, etc. 


## Joseon Official Position Dictionary (조선시대 관직 서전 만들기)

