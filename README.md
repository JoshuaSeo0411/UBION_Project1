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
- 파생변수 제작시 발생한 결측치 제거

#### 이상치
- 개봉일 데이터 없는 영화 삭제(4개)
- 영화명이 중복인 데이터 삭제
- 무한값(inf, -inf), 결측치 0으로 대체
- 양극단치 5%씩 Winsorizing

## 2. Labeling
- Label1 : 영화산업지수 12일 수익률 > 코스닥 12일 수익률 영화
- Label0 : 영화산업지수 12일 수익률 <= 코스닥 12일 수익률 영화

## 3. Data split & Scaling
- Train set 7: Test set 3 비율 랜덤 분할
- 영화데이터의 경우 시계열데이터가 아니기 때문에 임의분리, Test set 활용 백테스팅 진행
- Scaling : Train set, test set 각각 Standard scaling(Data Leakage 방지)

## 4. Feature Selection
- Train set으로만 Feature Selection
- 검정
  - 정규성검정(Shapiro-Wilks, Anderson, Dist plot, qq plot)
  - 독립성검정(Durbin-Watson)
    - 분류 모형에서는 통계검정이 중요하지 않기에 등분산성 검증 따로 진행 X
- 아래 4개 모델 중 3개 이상 중복 피쳐를 최종 선택
  - T-test(통계분석)
  - Lasso(Wrapper Method)
  - Stepwise(Embbeded Method)
  - SelectKbest(Filter Method)

## 5. Modeling

[단일분류]
- Logistic
- KNN
- Decision Tree
- SVM(SVC 활용)

[Ensamble]
- Random Forest
- **XGB**

**-> 최종모델 : XGBClassifier**


## 6. Evaluation(Test set)
- ACC : 0.64
- Precision : 0.64
- Recall : 0.64
- **F1-score : 0.64**
- AUC : 0.64

[F1-score가 상대적으로 높은 성능을 보이는 모델]
- 수익을 극대화하기 위해서는 수익이 날 때, 손실이 날 때 모두를 분류해야 하기에 정밀도와 재현율의 조화평균으로 선택

[PPT 예시]

![git5](https://user-images.githubusercontent.com/124761683/236629729-243c906a-b132-4cca-aa11-f6464187f632.JPG)

![git6](https://user-images.githubusercontent.com/124761683/236629733-7d128070-b4d8-47dc-a748-30e75dcb1b19.JPG)

![git7](https://user-images.githubusercontent.com/124761683/236629734-7d95c298-3d7c-4bc3-ae78-a323374ca843.JPG)

![git8](https://user-images.githubusercontent.com/124761683/236629741-d884ecf0-2d31-4cc5-9985-14f75ce0d4ab.JPG)

![git9](https://user-images.githubusercontent.com/124761683/236629743-cff92e7c-e418-4194-953a-233a9233d20d.JPG)
