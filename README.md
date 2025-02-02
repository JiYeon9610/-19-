# 2020 경기도 데이터 기반 코로나19 예측 공모전
2020.09.25 제출

목적: 9/30 ~ 10/4 연휴 기간의 경기도 코로나 확진자 수 예측 모델






## 활용 데이터 설명
9월 23일까지의 일일 데이터를 활용하여 9/30~10/4까지의 확진자 수 예측

Y 레이블: 경기도 일일 확진자수




### 1. 코로나의 직접적인 요인

코로나 관련 주요 이슈들, 정부 정책 단위의 통제, 시민들의 경각심이 확진자 추이에 직접적인 영향을 미쳤을 것이라 판단.

이에 따라 관련 정보를 대변할 수 있는 데이터를 수집한 후, 시각화 진행.


#### 1-1. 가설: 코로나 확산 방지 대책과 관련한 정부의 정책이 확진자 추이에 선행하여 유의미한 영향을 미칠 것이다.

데이터: 정부 정책 관련 뉴스 기사의 개수 데이터 크롤링

 - 코로나_단계 : 코로나에 대응하여 정부가 선언하는 거리두기 단계의 변동 관련 데이터 수집 ("코로나+단계"를 제목에 포함하고 잇는 일별 뉴스 개수 수집)

 - 마스크_의무화 : 코로나에 대응하여 정부가 선언하는 마스크 의무화 정책 시행 데이터 수집("마스크+의무화"를 제목에 포함하고 있는 일별 뉴스 개수 수집)

 - 코로나_고위험_시설 : 코로나 전파 위험성이 높은 고위험 시설에 대한 이슈, 이와 관련된 정부의 제재 정책 시행 데이터 수집("코로나+고위험+시설"을 내용에 포함하고 있는 일별 뉴스 개수 수집)

-> Data_collection_preprocessing.ipynb



#### 1-2. 가설: 시민들의 경각심(사회적 거리두기) / 코로나 쥬요 이슈(집단 감염,발병)가 확진자 추이에 선행하여 유의미한 영향을 미칠 것이다.

데이터: 코로나 관련 키워드의 네이버 검색어 추이 데이터 수집

 - 사회적_거리두기: 코로나 관련하여 시민들의 경각심 정도를 측정하기 위한 지표 데이터 수집('사회적 거리두기', '거리두기', '사회적 거리' 키워드)

 - 집단_감염: 코로나 확산의 주요 원인이자 이슈가 되는 집단 감염 사태의 심각성을 측정하기 위한 지표 데이터 수집('집단 감염', '집단 발병' 키워드)

-> https://datalab.naver.com/




### 2. 코로나의 외부적인 요인

사람들이 이동하는 것이 코로나 확산의 주요 원인 중 하나라고 판단.

이에 따라 코로나에 직접적인 영향을 행사하지는 않더라도, 인구의 이동에 영향을 미쳤을 법한 외부적인 요인도 확진자 추이에 영향을 미쳤을 것이라고 판단하여 관련 데이터 수집한 후, 시각화 진행.

#### 2-1. 가설: 날씨의 컨디션이 사람들의 이동에 영향을 미쳤을 것이다.

기상청 기상자료개방포털 데이터 활용(https://data.kma.go.kr). 

경기도에 해당하는 지역의 지표들의 평균값 도출.

'평균기온' 변수값과 '평균 상대습도' 변수 값을 불쾌지수 공식에 대입하여 값 산출.



#### 2-2. 가설: 주말 및 공휴일 여부가 사람들의 이동에 영향을 미칠 것이다.

주말을 포함한 공휴일 데이터 작성.

평일보다는 쉬는 날에 고위험 시설 방문 등, 코로나 전파 위험성을 높일 수 있는 유동 인구가 많이 발생할 것이라고 가정하였다.




### 3. 경기도 감염병 관리지원단 기본 제공 데이터

'확진자의 개별 특성 정보'를 포함하고 있는 경기도 감염병 관리지원단 기본 제공데이터로부터 추가적으로 의미 있는 변수를 뽑아낼 수 있을 것이라고 판단.
이에 기본 제공 데이터에서 전처리 과정을 거쳐 네 가지 변수를 도출

- 3-1. 무증상 환자 비율
무증상인 환자가 확진자가 되었을 때, 확산 위험성에 더 큰 영향을 줄 것이라고 가정하였다.
이에 따라 확진자 중 무증상 환자가 차지하는 비율을 변숫값으로 설정하였다.

- 3-2. 해외 감염자 비율
감염 경로 변수가 최근 날짜일수록 세분화된 정보를 포함하고 있지 못하는 것을 확인하였다.
이에 따라 해외 감염자인지 아닌지의 여부로 간단하게 나누어 그 비율을 변숫값으로 설정하였다.

- 3-3. 연령대 별 과거 확진자 수
연령대 별로 다른 확진자가 추후의 확진자 수 추이에 다른 영향력을 행사할 수 있을 것이라고 가정하였다.

- 3-4. 지역 별 과거 확진자 수
지역 별로 다른 확진자가 추후의 확진자수 추이에 다른 영향력을 행사할 수 있을 것이라고 가정하였다.





## 변수 전처리 과정

### Age / Area Clustering

앞서 언급한 연령별 과거 확진자와 지역별 과거 확진자가 추후의 확진자 추이에 특성에 따라 개별적인 영향력을 행사할 것이라 가정함.
하지만 모든 연령별, 지역별 확진자 변수를 사용할 시, 설명 변수의 차원이 지나치게 커질 것을 우려하여 연령별/지역별로 유사한 추이와 특성을 가지는 차원들을 클러스터로 묶어 군집화함.

- 활용 모델: TimeSeriesKMeans(tslearn 패키지)
일반적인 연속형 변수 사이의 거리 값을 측정하는 Euclidean 거리 대신에 "Dynamic Time Warping(DTW)" 방법을 활용하였다.
DTW는 데이터 간 연속된 시계열성을 고려하여 시퀀스 간의 유사성을 함께 측정해주기 때문에 확진자 수 데이터에 적용하기에 적합한 방법이라고 판단하였다.

- 결론: 연령대, 지역별 데이터를 최종적으로 시각화 한 결과, 분석에 따라 나뉘어진 군집별 데이터들은 유사한 분포를 가지며, 군집 간에는 상이한 추이를 보이고 있음을 확인하였다.
따라서, 연령대/지역별 시계열 데이터를 군집화하여 변수로 활용하여, 미래 확진자 수를 최종적으로 예측할 모델이 Cluster변수마다 각기 다른 가중치를 학습할 수 있도록 유도하였다
.



## 모델링 

### 변수 검정(Correlation test & feature selection)

수집한 13개의 변수 후보 시계열(X)의 확진자 시계열(Y)의 상관 검정을 진행하였다.
23일까지의 데이터를 활용해 9/30~ 10/4일의 데이터를 예측해야 하므로, X와 Y 간에 최소 7일부터 15까지의 time-lag를 적용하여 두 시계열의 pearson 상관 계수를 산출하고 검정을 진행하였다.

예) 7일의 time-lag를 갖는 1/26 ~ 9/7일의 '집단 감염' 검색어 트렌드(X)와 2/2 ~ 9/14의 확진자(Y)의 correlation 계산

검정 결과, 7~10일 사이의 timelag에 대해 많은 변수들이 가장 큰 correlation을 가졌고 유의한 p-value를 가졌다. 앞서 언급한 변수들 중 유의한 p-value를 갖는 변수들을 최종 X변수로 선정해 예측하는데 활용하였다.

-> VariableSelection.ipynb


### Many-To-One LSTM
시계열 분석에 용이하며 RNN 기반의 NeuralNetwork 모델인 LSTM을 적용하였다.

->RNN_based.py

예측 모델을 위해 2가지 Framework를 논의하였다.
- Framework1: 11일 뒤의 확진자를 예측하도록 학습된 하나의 LSTM 모델을 학습하여 예측 (ex. 9/19일까지의 데이터를 이용하여 9/30일의 확진자를, 9/20일까지의 데이터를 이용하여 10/1의 확진자를....)
- Framework2: 7,8,9,10,11일 뒤의 확진자를 예측하는 5개의 모델1~5를 학습하여 예측(9/23일까지의 데이터를 활용하여 7일 뒤인 9/30일을 예측하는 model1, 9/23일까지의 데이터를 활용하여 8일 뒤인 10/1일을 예측하는 model2 ...)
- 학습 이터가 적어 시간이 크게 소요되지 않고, 9/23일까지의 최신 데이터를 최대한 활용할 수 있다는 장점을 고려하여, Framework2를 선택

- Preprocessing: Sklearn의 preprocessing 활용하여 X 에는 MinMaxScaling, Y에는 극댓값 처리를 위해 Log-transformation 처리

#### Training:
- 1) 분할 전 Dataset을 모델의 시차에 따라 달리 준비
- 2) 앞부분을 차지하는 95%의 X, Y 에 대해 학습한 뒤, 마지막 5%의 시계열을 통해 Evaluation 진행
- 3) Epoch 마다 모델의 Evaluation loss(MSELoss) 계산 후 최적 모델 선정에 활용

-> Training_Prediction.ipynb

