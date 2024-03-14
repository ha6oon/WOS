# 2024 클러스터 예측모델용 WOS 데이터 준비

## 작업개요

### 주요 업무

- 데이터 가져오기 : 글로벌 R&D 분석센터 DB - WOS2300 데이터
- 데이터 전처리
- 데이터 후처리 및 기초 분석

### 사용 데이터 요약

| 이름 | 출처  | 설명 |
| --- | --- | --- |
| web_of_sciecne_2300_raw | 글로벌 R&D 분석센터 DB  | WOS 정보가 여러 table로 구성되어 있음 |
| pub_micro_level_field_assignment.txt | 외부 파일로 받음 | ut에 따른 cluster_id와 core 데이터가 담긴 파일임 |

## 데이터 추출 정보

💡 **다음과 같이 줄여서 표기**

| 대체할 이름 | 원래 이름 |
| --- | --- |
| DB  | web_of_sciecne_2300_raw  |
| P | pub_micro_level_field_assignment.txt  |
| 1 | leiden_cluster_biblio_info.txt |
| 2 | leiden_cluster_text_info.txt |
| 3  | leiden_cluster_refs_linkset.txt |
| 4 | ref_cate_cluster_weight.txt |
| 5  | Catagory Embedding |
| 5_pub_allyear | cluster_cate_weight_publevel_allyear_frame.txt |
| 5_pub_by_year | cluster_cate_weight_publevel_by_year_frame.txt |
| 5_ pub_allyear_wide | cluster_cate_weight_publevel_allyear_frame_wide.txt |
| 5_pub_by_year_wide | cluster_cate_weight_publevel_by_year_frame_wide |
| 5_ref_allyear | cluster_cate_weight_reflevel_allyear_frame.txt |
| 5_ref_by_year | cluster_cate_weight_reflevel_by_year_frame.txt |
| 5_ref_allyear_wide | cluster_cate_weight_reflevel_allyear_frame_wide.txt |
| 5_ref_by_year_wide | cluster_cate_weight_reflevel_by_year_frame_wide.txt |

💡 **데이터셋 별 부가 설명**

| 데이터셋 명 | 추가 설명 |
| --- | --- |
| 1 | cluster_id - pubyear - tc 순으로 정렬해두었습니다.  |
| 2 | 클러스터의 텍스트 관련 항목입니다. |
| 3 | 라이덴 클러스터의 uid - refs_uid 링크목록입니다. 클러스터별 BC Similarity Network 구축용입니다.  |
| 4 | 라이덴 클러스터 uid - refs_uid 링크에서 WOS Category를 붙이고, uid - refs_uid - category로 이어지는 각 링크의 weight를 계산한겁니다. 이 데이터로 WOS Category Embedding을 작업하였습니다.  |
| 5_pub | publication level의 임베딩. 이전의 작업 방식으로, 클러스터 개별 문헌에 부여된 WOS Category 정보를 Cluster 단위로 aggregate 하는 겁니다. 전체년도와 연도별 파일이 따로 있습니다. 또한 long type 버전(cluster - category pair 리스트 형태)과 wide type 버전(클러스터 당 254개 category 칼럼이 존재하는 형태) 2가지 버전 다 만들어 두었습니다. |
| 5_ref | reference level의 임베딩. 레퍼런스 수준에서 임베딩을 하는 방식입니다. 역시 전체년도와 연도별 파일이 따로 있으며, long type 버전, wide type 버전 2가지 버전 다 만들어 두었습니다.  |

💡 **데이터셋 별 컬럼 상세 설명**

- 노란색 : DB로부터 추출, 파란색 : P로부터 추출, 빨간색 : 새로 생성한 컬럼
- 중복되는 내용은 패스함.

| 데이터셋 명  | 컬럼명 | 가져온 DB 테이블 이름 | DB 위치 컬럼명 | 컬럼 설명  | 부가 설명 |
| --- | --- | --- | --- | --- | --- |
| 1 | uid  | pub_info | uid | WOS 고유 번호 |  |
|  | cluster_id |  |  | 라이덴 클러스터 번호 |  |
|  | core |  |  | CWTS에서 관리하는 core 여부 |  |
|  | n_clu |  |  | 클러스터 별 문헌 수 | 각 cluster_id별 count 수 |
|  | edition | edition | edition | WOS의 에디션 정보 | 복분류인 경우 ;으로 collapse |
|  | pubyear2 | pub_info | pubyear | 약간의 조정을 거친 년도 | 2006년 이후부터 추출 |
|  | doctype2 | doctype | doctype | Ariticle과 Review로 구분 | 다양한 복분류 문서유형을 2개 중 하나의 값으로 조정 |
|  | tc | coretc | wos_total | 해당 uid 의 누적 피인용수(time cited) |  |
|  | source | source | source | 해당 uid 출처명. 대부분 저널명이며, 북시리즈인 경우 source_abbrev가 정확. |  |
|  | source_abbrev | source | source_abbrev | JCR에서 적용하는 출처 약어명. |  |
|  | item | title | title | 해당 uid 제목 |  |
|  | issn | identifier | issn | 출판 issn |  |
|  | eissn | identifier | eissn | 전자출판 eissn |  |
|  | doi_all | identifier | xref_doi | XML에 수록된 두 개 doi 필드를 통합하고, 중복오류 제거 |  |
|  |  |  |  |  |  |
| 2 | uid |  |  |  |  |
|  | cluster_id |  |  |  |  |
|  | pubyear2 |  |  |  |  |
|  | item |  |  |  |  |
|  | aukey | keyword | keyword | 저자 키워드 | 복분류인 경우 ;으로 collapse |
|  | abstract | abstract | abstract_text | 초록 |  |
|  |  |  |  |  |  |
|  |  |  |  |  |  |
| 3 | uid |  |  |  |  |
|  | cluster_id |  |  |  |  |
|  | pubyear2 |  |  |  |  |
|  | refs_uid | reference | ref_uid | 참고문헌의 고유 id |  |
|  | refs_n | reference | ref_count | uid별 참고문헌의 개수 | uid 별 refs_uid count |
|  | within_core |  |  | refs_uid가 Clarivate의 Core Collection DB 인지를 표시 | 0과 1로 이루어짐. refs_uid가 P에 존재하고,(AND) P에서 core 값이 1이면 1의 값을 가짐. 아니면 0의 값을 가짐. |
|  |  |  |  |  |  |
| 4 | uid |  |  |  |  |
|  | cluster_id |  |  |  |  |
|  | pubyear |  |  |  |  |
|  | ref_n |  |  |  |  |
|  | refs_uid |  |  |  |  |
|  | cate_n |  |  | refs_uid별로 연결된 WOS Category 개수 | refs_uid 별 카테고리 개수 |
|  | subject_traditional |  |  | WOS Category. 총 254개 가짓수이며 XML상의 2가지 오류를 수정한 버전임. | DB의 category_subject 테이블에서 subject_ascatype이 ‘traditional’인 것만 추출 |
|  | within_core |  |  |  |  |
|  | weight |  |  | ref_n, cate_n을 반영한 개별 uid - refs_uid - category 링크의 가중치 | 보류 |
|  |  |  |  |  |  |
| 5_pub_all_year | cluster_id |  |  |  |  |
|  | subject_traditional |  |  |  |  |
|  | weight_cate |  |  | *아래에 따로 설명 |  |
|  | norm_weight_cate |  |  | *아래에 따로 설명 |  |

***weight 계산 예시**

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/95ce033c-4670-40e9-9267-84bac0a071bf/1323da05-c981-4a40-b1ca-40cf05315aaa/Untitled.png)

## 코드를 위한 정보

프로그래밍 언어인 Python과 SQL을 이용해서 필요한 데이터를 DB로부터 추출하여 전처리 작업을 통해 필요한 데이터 셋 구성함.

<aside>
🔗 주피터 노트북 워크시트 저장소 링크 : https://github.com/ha6oon/WOS

</aside>

- 2024 클러스터 예측모델용 WOS 데이터 준비 작업용 주피터 노트북 워크시트 모음집입니다.
- 자세한 DB 정보는 노션을 참조해주세요.

| 주피터 노트북 이름 | 설명 |
| --- | --- |
| Extracting data from DB | DB로부터 추출작업 |
| P-file worksheet | 외부파일 전처리 작업 |
| 1&2 worksheet | 1번과 2번 데이터셋 작업 |
| ThreadPoolExecutor | 3번 데이터셋 작업 |
| 4_worksheet | 4번 데이터셋 작업 |
| 5_all_year | 5번 데이터셋 작업 - all year 버전 |
| 5_by_year | 5번 데이터셋 작업 - by year 버전 |
| 5_check | 5번 데이터셋 작업 검증용 코드 |

## 추가 문서 안내

- 파일 경로 : `D:\23\문서함`

### 작업 보고서

다음과 같은 내용이 담겼습니다.

- 작업 과정 상세 : 데이터 추출, 전처리 과정, 중복제거
- 완성된 데이터셋의 유효성 검증 : 로우 및 컬럼 수 조사, 유일성 검증, 결측치 조사, 이상치 조사, 특정값 체크
- 전년도 샘플과 비교하는 과정 및 결과 예시
- 상세 보고사항 : 각 과정에서 발생한 문제와 해결 방법

### 추가 문서

| 문서 이름 | 상세 설명 |
| --- | --- |
| 파일_경로_안내.pdf | 파일 목록 및 저장 위치가 담긴 문서입니다. |
| Sample.pdf | 최종 완성본 데이터셋 샘플이 담긴 문서입니다.  |
| SQL 가이드.pdf | DB 커넥션 정보가 담긴 문서입니다.  |
