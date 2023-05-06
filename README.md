# [영화 개봉이 "영화산업지수"에 미치는 영향] by Literacy

UBION Project1 / 2023.02.27 ~ 2023.03.26

![git1](https://user-images.githubusercontent.com/124761683/236624988-7dc2530a-45b9-46fd-81ba-382bfbef9583.JPG)

![git2](https://user-images.githubusercontent.com/124761683/236624989-3c15466f-fad0-467a-b088-aa66dc535107.JPG)

![git3](https://user-images.githubusercontent.com/124761683/236624993-cb1f6e1e-8a4b-4048-99db-e98955ae21ef.JPG)

![git4](https://user-images.githubusercontent.com/124761683/236624996-41e45999-5981-4774-9081-be483a018385.JPG)


---
# **프로젝트 소개**
 본 프로젝트는 영화개봉이 영화주가지수에 미치는 영향을 분석하기 위해 영화개봉에 따른 배급사 주식의 주가상승과 하락으로 분류 예측하고자 한다. 이 과정에서 14개의 최적 피쳐를 선정하였고, 총 6개의 분류 모델을 활용해 가장 뛰어난 성능을 보인 XGB Classfier를 최종 모델로 선정하였다. 최종 모델을 통해 주가 상승을 예측할 때에 있어 상승과 하락을 모두 잘 분류하는 것을 중요하다고 판단하여 정밀도(Precision)과 재현율(Recall)의 조화평균으로 이루어진 F1-score가 중요하다고 판단하여 이에 집중하였다. 또한 본 연구를 위해 만든 새로운 영화 산업 업종지수를 통해 영화 업종 시장, 투자 전략 성과를 측정하기 위한 벤치마크로 활용이 가능하다는 의의가 있다.

---
# **구조**


## **0. 데이터 수집**
- 대상 : 국내 개봉영화(해외영화 포함)
- 제외 : 손익분기점 미달 영화(손익분기점은 미공개 자료로 영화진흥위원회의 보고서를 참고 연평균 제작비보다 수익 미달시 제외)
- 기간 : 2010 ~ 2019년
- 지수 : 코스닥 지수(배급사 관련 14개 종목)
- 최초 영화개수 : 11,819개 영화(흥행영화 914개)
- 출처 : 영화진흥위원회, KRX, FDR

## **1. Preprocessing**
#### 결측치
- 결측치가 없음
- 파생변수 제작시 발생한 결측치 제거

#### 이상치
- 거래소코드가 2개인 기업 중복 제거
- 회사명이 2개인 거래소코드 중복 확인
- 999999 (inf, -inf) 제거
- 양극단치 0.5%씩 Trimming(IQR 0.5% 이하, 99.5% 이상 값들 제거)

## 2. Labeling
- Label1 : 상장폐지(자발적 상장폐지 제외) + 관리종목 지정 기업 t-1기, t-2기
  * 상장폐지가 4월 전에 이루어진 경우 t-1기 데이터가 없기에 t-2기, t-3기에 1부여
  * 관리종목의 경우 최초 지정된 년도를 t기로 판단
- Label0 : 정상기업(1에 해당하지 않는 기업)

## 3. Data split & Scaling
- Train set : 2011 ~ 2017 (7년)
- Test set : 2018 ~ 2019 (2년)
- Scaling : Train set, test set 각각 Standard scaling(Data Leakage 방지)

## 4. Feature Selection
- Train set으로만 Feature Selection
- 검정
  - 정규성검정(Shapiro-Wilks, Anderson, Dist plot, qq plot)
  - 독립성검정(Durbin-Watson)
    - 분류 모형에서는 통계검정이 중요하지 않기에 등분산성 검증 따로 진행 X
- 아래 3개 모델 중 3개 이상 중복 피쳐를 최종 선택
  - T-test(통계분석)
  - Lasso(Wrapper Method)
  - Stepwise(Embbeded Method)

## 5. Modeling

[단일분류]
- Logistic
- Decision Tree
- SVM(SVC 활용)

[Ensamble]
- Random Forest
- **XGB**
- Stacking (Logistic + SVC + XGBoost)

[Deep Learning]
- TabNet

**-> 최종모델 : XGBClassifier**


## 6. Evaluation(Test set)
- ACC : 0.65
- Precision : 0.07
- **Recall : 0.92**
- F1-score : 0.13
- AUC : 0.77

[Recall이 상대적으로 높은 성능을 보이는 모델]
- 부실기업을 정상기업으로 판단하는 2종 오류 해소
- 기업 부실을 판단하는 입장에서 위험 최소화

[정상기업과 부실기업의 차이 및 거래량 확인]
