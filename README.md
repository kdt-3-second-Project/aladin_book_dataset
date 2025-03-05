**KOR** · [ENG](./README_EN.md)

# 알라딘 베스트 셀러 및 중고 도서 데이터셋 구축

프로젝트 구성원: 오도은, 박예림, 이준성, 정홍섭 / [발표 슬라이드](https://docs.google.com/presentation/d/15EIOMGpadZQf3cT2k0pfClS9DVICLmmf5ZTH1k4XnKc/edit?usp=sharing)

**사용된 스킬 셋**: NumPy, Pandas, Matplotlib, Beautifulsoup, re, Scikit-learn

## 0. 초록

- 알라딘 00년 1월 1주차 ~ 24년 7월 2주차의 베스트셀러 목록을 크롤링하여 141.5만 행의 DB 구축
  - 15.8만 여종의 도서에 대하여, 해당 주차에서의 순위 및 도서 관련 정보를 포함
- 주간 베스트 셀러 DB를 바탕으로, 78만 행의 알라딘 중고 매장의 중고 도서 DB 구축
  - 10.3만 여종의 역대 베스트셀러 도서에 대한 중고 도서 매물 데이터

## 1. 데이터셋

### 1. [알라딘 주간 베스트셀러 데이터](https://www.aladin.co.kr/shop/common/wbest.aspx?BranchType=1)

#### 개요

- 알라딘의 주간 베스트셀러 페이지에서 제공한 1~1000위에 대한 xls 파일 데이터를 이용하여 구성
- 2000년 1월 1주차 ~ 2024년 7월 2주차까지의 데이터를 포괄하며, 24-07-10 ~ 24-07-12에 수집 진행

![image](https://github.com/user-attachments/assets/e330ca44-893c-4fad-8d91-4a2f520c13af)

*<b>도표.1</b> 알라딘 주간 베스트셀러 페이지 예시*

- 총 1,415,586개의 row와 랭킹, 구분, 도서 명, ItemId, ISBN13, 부가기호, 저자, 출판사, 출판일, 정가, 판매가, 마일리지, 세일즈 포인트, 카테고리, 날짜 12개의 column
  - **구분** : 국내도서, 외국도서 등으로 구분되어 있음
  - **ItemId** : 알라딘에서 부여한 해당 도서의 id. 숫자로만 구성
    - raw data에는 도서 외에도, 당시 베스트셀러였던 MD 굿즈, 강연 등도 포함되어 있음
  - **날짜, 랭킹** : 해당 도서가 어떤 주차의 주간 베스트셀러 목록에 몇 위로 올랐는지
  - [**ISBN13, 부가기호**](https://blog.aladin.co.kr/ybkpsy/959340) : ISBN13은 전세계에서 공통적으로 사용하는 도서에 대한 id. 발행자 등의 정보가 포함되어 있음. 부가기호는 한국 문헌 보호 센터에서 부여하는 번호로, 예상 독자층에 대한 정보 등이 포함 되어 있음
  - **카테고리** : 도서가 어떤 장르에 속하는지에 대한 정보
  - **세일즈 포인트** : 판매량과 판매기간에 근거하여 해당 상품의 판매도를 산출한 알라딘만의 판매지수이며, 매일 업데이트 됨
- 날짜 및 랭킹을 제외하고, 판매가, 세일즈 포인트 등은 크롤링 시점에서의 값이 저장됨

![image](https://github.com/user-attachments/assets/8d74d9a6-3423-4bd3-b0a0-27817761de9c)

*<b>도표.2</b> 알라딘 주간 베스트 셀러*

### 2. [알라딘 중고 도서 데이터](https://www.aladin.co.kr/shop/UsedShop/wuseditemall.aspx?ItemId=254468327&TabType=3&Fix=1)

![image](https://github.com/user-attachments/assets/e8840608-96f8-47e6-954b-5d6e08f47df9)

*<b>도표.3</b> 도서 별 중고 매물 목록 페이지 예시*

<!--위의 탭을 포함하는 이미지로 업데이트 필요-->

#### 개요

- [알라딘 온라인 중고매장(광활한 우주점)](https://www.aladin.co.kr/usedstore/wonline.aspx?start=we)에 등록 된 중고 도서 매물 데이터
- 위의 베스트셀러 데이터에 포함된 도서(ItemId)를 기준으로 크롤링한 중고도서 매물 자료

![image](https://github.com/user-attachments/assets/6bc6657e-cc45-4830-baaa-fca240733d6e)

*<b>도표.4</b> 알라딘 중고 도서 데이터*

- 총 784,213개의 row, 7개의 column으로 구성.
  - 각 row 당 중고 도서 매물 하나에 해당
    - 103,055 종의 도서에 대한 중고도서 매물 784,213건
  - ItemId (새 책 기준), 중고 번호, 중고 등급, 판매지점, 배달료, 중고가, 판매 url
  - **ItemId** : ItemId는 중고 도서를 포함하여 모든 상품에 각각 부여
  - **중고 번호** : 해당도서의 중고도서 목록 페이지에 있었던 순서
  - **중고가, 품질** : '균일가' 및 '하', '중', '상', '최상'으로 구분
  - **판매 url** : 해당 중고 매물에 대한 판매 페이지. 해당 중고 매물의 ItemId가 url에 포함되어 있음

![image](https://github.com/user-attachments/assets/caa98ef5-b5be-47d9-a9c4-9ff236ecdb48)

*<b>도표.5</b> 데이터 셋들에 포함된 주요 column 및 그에 대한 개요*

## 참고

- 

<!--
참조 : https://github.com/e9t/nsmc

데이터셋 구조
- 디렉터리 구조
- Data description : columns info
- 파일 별 Quick peek 

1. bestseller_info
각 주차 별 순위도 반영해서 concat
2. usedinfo 

characteristic

크롤링 방법 : 어떤 코드를 사용했는지만 적고, 자세한 설명은 usedbook repo를 참고하고 하고 줄임

- TODO
1. bestseller info : concat 진행
2. bookinfo : crawling에 사용한 것 기준
3. usedbook_info : 어떤 bookinfo 기준으로 했는지

-->