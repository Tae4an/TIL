# Amazon Forecast란?

## 개념 정의
Amazon Forecast는 Amazon.com에서 사용하는 것과 동일한 머신러닝 기술을 기반으로 구축된 완전 관리형 시계열 예측 서비스다. 과거 시계열 데이터(판매량, 트래픽, 재고 등)와 관련 변수(날씨, 휴일, 프로모션 등)를 입력하면 자동으로 머신러닝 모델을 선택·훈련·조정하여 미래 데이터 포인트를 예측하며, 기존 통계적 방법보다 최대 50% 더 정확한 예측을 제공한다.

쉽게 말해 "과거 판매 데이터, 날씨, 휴일 정보 등을 넣으면 AI가 자동으로 분석해서 내일·다음 주·다음 달의 수요를 예측해주는 서버리스 예측 서비스"다.

## 핵심 특징

### 완전 관리형 서비스

**머신러닝 전문 지식 불필요**
- 알고리즘 선택·모델 훈련·하이퍼파라미터 튜닝 자동화
- AutoML로 최적 알고리즘 자동 선택
- 데이터 전처리·특성 엔지니어링 자동
- 사용자는 데이터만 제공하면 됨

**서버리스 아키텍처**
- 인프라 프로비저닝·관리 불필요
- 자동 확장
- 사용한 만큼만 비용 지불

### 높은 정확도

**Amazon.com 검증 기술**
- 수백만 개 상품 수요 예측에 사용
- 기존 통계 방법 대비 최대 50% 정확도 향상
- 딥러닝·통계 알고리즘 조합

**확률적 예측 (Probabilistic Forecasting)**
- 단일 값이 아닌 예측 분포 제공
- 다양한 분위수(quantiles) 예측
- 불확실성 정량화
- 리스크 기반 의사결정 가능

### 다양한 알고리즘 지원

**AutoML 알고리즘 선택**
- 데이터 특성에 가장 적합한 알고리즘 자동 선택
- 여러 알고리즘으로 모델 훈련 후 가장 정확한 것 선택

**지원 알고리즘**
- CNN-QR (Convolutional Neural Network - Quantile Regression): 딥러닝 기반
- DeepAR+: 순환 신경망 (RNN) 기반
- Prophet: Facebook에서 개발한 통계 알고리즘
- NPTS (Non-Parametric Time Series): 부트스트랩 기반
- ARIMA (AutoRegressive Integrated Moving Average): 전통 통계 모델
- ETS (Exponential Smoothing): 지수 평활법

### 관련 데이터 통합

**Related Time Series (관련 시계열)**
- 날씨, 기온
- 휴일, 이벤트
- 가격, 프로모션
- 웹 트래픽

**Item Metadata (항목 메타데이터)**
- 제품 카테고리, 브랜드
- 색상, 크기
- 지역, 매장

## 작동 방식

### 예측 생성 프로세스

**1. 데이터 준비**
- 과거 시계열 데이터를 CSV 형식으로 준비
- S3 버킷에 업로드

**필수 데이터: Target Time Series (대상 시계열)**
- `item_id`: 항목 식별자 (제품 ID, 매장 ID 등)
- `timestamp`: 시간 스탬프
- `target_value`: 예측할 값 (판매량, 방문자 수 등)

**예시**
```csv
item_id,timestamp,target_value
item_01,2023-01-01,100
item_01,2023-01-02,120
item_01,2023-01-03,95
item_02,2023-01-01,50
item_02,2023-01-02,55
```

**선택 데이터: Related Time Series (관련 시계열)**
```csv
item_id,timestamp,weather,promotion
item_01,2023-01-01,sunny,0
item_01,2023-01-02,rainy,1
```

**선택 데이터: Item Metadata (항목 메타데이터)**
```csv
item_id,category,brand
item_01,electronics,BrandA
item_02,apparel,BrandB
```

**2. Dataset Group 생성**
- Forecast 콘솔 또는 API로 Dataset Group 생성
- 예측 도메인 선택 (Retail, Inventory, Metrics 등)

**3. Dataset 생성 및 데이터 가져오기**
- Target Time Series Dataset 생성
- S3에서 데이터 임포트
- 선택적으로 Related Time Series, Item Metadata 추가

**4. Predictor 훈련**
- Predictor(예측 모델) 생성
- AutoML 또는 수동 알고리즘 선택
- Forecast Horizon 지정 (예측 기간, 예: 7일)
- Forecast Frequency 지정 (예측 간격, 예: 1일)

**AutoML 예시**
```python
import boto3

forecast = boto3.client('forecast')

response = forecast.create_auto_predictor(
    PredictorName='my-predictor',
    ForecastHorizon=7,  # 7일 예측
    ForecastFrequency='D',  # 일별
    ForecastDimensions=['item_id'],
    DataConfig={
        'DatasetGroupArn': 'arn:aws:forecast:us-east-1:123456789012:dataset-group/my-dataset-group'
    }
)
```

**5. 예측 생성**
- Predictor 훈련 완료 후 Forecast 생성
- 분위수 지정 (예: p10, p50, p90)

**6. 예측 조회**
- Query Forecast API로 특정 항목 예측 조회
- 또는 전체 예측을 S3로 내보내기

**7. 결과 활용**
- QuickSight, Tableau 등으로 시각화
- 비즈니스 애플리케이션과 통합
- 재고·인력 계획에 활용

## 주요 구성 요소

### 1. Dataset Group

**개념**
예측에 사용되는 데이터셋의 컬렉션이다.

**포함 Dataset**
- Target Time Series (필수): 예측할 데이터
- Related Time Series (선택): 관련 변수
- Item Metadata (선택): 항목 정보

**도메인**
- Retail: 소매·재고 예측
- Custom: 사용자 정의
- Inventory Planning: 재고 계획
- EC2 Capacity: EC2 용량 예측
- Work Force: 인력 계획
- Web Traffic: 웹 트래픽 예측
- Metrics: 운영 메트릭

### 2. Dataset

**Target Time Series Dataset**
- 과거 시계열 데이터
- 최소 300개 데이터 포인트 권장
- 시간 간격 일관성 필요 (1시간, 1일, 1주 등)

**Related Time Series Dataset**
- 예측에 영향을 주는 외부 변수
- 예: 가격, 날씨, 휴일, 프로모션
- 과거 + 미래 데이터 제공 가능

**Item Metadata Dataset**
- 항목의 정적 속성
- 예: 제품 카테고리, 브랜드, 색상

### 3. Predictor

**개념**
훈련된 머신러닝 모델이다.

**AutoML vs 수동 알고리즘**
- AutoML: 자동으로 최적 알고리즘 선택 (권장)
- 수동: ARIMA, ETS, DeepAR+ 등 특정 알고리즘 선택

**Forecast Horizon**
- 예측할 미래 기간
- 예: 7일, 30일, 12주

**Forecast Frequency**
- 예측 간격
- 예: 1분(1min), 15분(15min), 1시간(H), 1일(D), 1주(W), 1개월(M)

**Featurization**
- 자동 특성 추출
- 휴일 캘린더 (미국, 영국 등 60개국 이상)
- 날씨 인덱스

### 4. Forecast

**개념**
Predictor로 생성된 실제 예측 결과다.

**분위수 예측**
- p10: 10% 확률로 실제 값이 이보다 낮음 (낙관적)
- p50: 중앙값 (median)
- p90: 90% 확률로 실제 값이 이보다 낮음 (보수적)

**예시**
```
날짜: 2026-01-14
item_01 판매량 예측:
- p10: 80개
- p50: 100개 (중앙값)
- p90: 120개
```

**해석**
- 10% 확률로 80개 미만 판매
- 50% 확률로 100개 근처 판매
- 90% 확률로 120개 미만 판매
- 보수적으로 120개 재고 준비하면 안전

### 5. Explainability

**Forecast Explainability**
- 예측에 가장 영향을 준 변수 식별
- Impact Score 제공 (0~1)
- 예: 프로모션이 60%, 날씨가 20% 영향

**활용**
- 예측 신뢰도 향상
- 비즈니스 인사이트 도출
- 데이터 품질 개선

## 주요 활용 사례

### 1. 소매·재고 예측

**시나리오**
- 제품별 수요 예측
- 재고 최적화
- 재고 부족·과잉 방지

**입력 데이터**
- 과거 판매 데이터
- 가격, 프로모션
- 휴일, 계절성
- 날씨

**결과**
- 제품별·매장별·일별 수요 예측
- p90 분위수로 안전 재고 수준 결정
- 재고 회전율 향상, 폐기 감소

**예시: 전자상거래**
- 수천 개 SKU의 일별 수요 예측
- 물류 센터별 재고 배치 최적화
- 블랙 프라이데이 등 이벤트 대비

### 2. 인력 계획 (Workforce Planning)

**시나리오**
- 콜센터 인력 배치
- 매장 직원 스케줄링
- 배달 기사 수요 예측

**입력 데이터**
- 과거 통화량, 방문자 수
- 요일, 시간대
- 휴일, 이벤트

**결과**
- 15분 단위 인력 수요 예측
- 피크 타임·비수기 인력 최적화
- 고객 대기 시간 감소

**예시: 콜센터**
- 시간대별 통화량 예측
- 필요 상담원 수 자동 계산
- 인건비 절감, 고객 만족도 향상

### 3. 공급망 최적화

**시나리오**
- 원자재 수요 예측
- 생산 계획
- 배송량 예측

**입력 데이터**
- 과거 생산량, 주문량
- 납기, 리드 타임
- 계절성

**결과**
- 원자재 구매 시기·수량 최적화
- 생산 라인 가동 계획
- 재고 비용 절감

### 4. 에너지 수요 예측

**시나리오**
- 전력 소비 예측
- 발전소 가동 계획

**입력 데이터**
- 과거 전력 사용량
- 기온, 날씨
- 요일, 시간대

**결과**
- 시간별 전력 수요 예측
- 발전 용량 최적화
- 에너지 비용 절감

### 5. 웹 트래픽 예측

**시나리오**
- 웹사이트 방문자 수 예측
- Auto Scaling 계획

**입력 데이터**
- 과거 트래픽 데이터
- 마케팅 캠페인
- 계절성

**결과**
- 일별·시간별 트래픽 예측
- 서버 용량 계획
- 인프라 비용 최적화

### 6. 재무 계획

**시나리오**
- 매출 예측
- 현금 흐름 예측
- 예산 계획

**입력 데이터**
- 과거 매출 데이터
- 경제 지표
- 계절성

**결과**
- 월별·분기별 매출 예측
- 투자 계획
- 리스크 관리

## 예측 정확도 평가

### 평가 지표

**WAPE (Weighted Absolute Percentage Error)**
- 가중 절대 백분율 오차
- 낮을수록 좋음
- Forecast 기본 지표

**RMSE (Root Mean Squared Error)**
- 평균 제곱근 오차
- 큰 오차에 더 민감

**MAPE (Mean Absolute Percentage Error)**
- 평균 절대 백분율 오차

**MASE (Mean Absolute Scaled Error)**
- 평균 절대 스케일 오차

### Backtesting

**개념**
과거 데이터를 훈련·테스트 세트로 분할하여 정확도 검증한다.

**방법**
- 과거 데이터의 마지막 N일을 테스트 세트로 분리
- 그 이전 데이터로 모델 훈련
- 테스트 기간 예측 후 실제 값과 비교

**예시**
- 2023년 1월~11월: 훈련 데이터
- 2023년 12월: 테스트 데이터 (예측 vs 실제 비교)

## 비용

### 요금 구조

**데이터 스토리지**
- 월 GB당 $0.088

**훈련 시간**
- 시간당 $0.24
- Predictor 훈련 시간에 따라 과금

**예측 생성**
- 1,000개 예측당 $0.60
- 1,000개 = 1,000개 time series × 1개 forecast horizon

**예측 조회**
- 1,000개 쿼리당 $0.60

### 무료 티어
- 처음 2개월 동안:
  - 최대 10,000개 예측 무료
  - 최대 10시간 훈련 무료

### 예시 계산

**시나리오: 100개 제품, 30일 예측, 월 1회 재훈련**
- 데이터 저장: 1GB → $0.088
- 훈련: 2시간 → $0.48
- 예측 생성: 100개 제품 × 30일 = 3,000개 → $1.80
- 예측 조회: 3,000개 쿼리 → $1.80
- 총 월 비용: 약 $4.20

## Forecast vs 다른 AWS 서비스

### Forecast vs SageMaker

| 항목 | Amazon Forecast | Amazon SageMaker |
|------|----------------|-----------------|
| 용도 | 시계열 예측 전용 | 범용 머신러닝 플랫폼 |
| ML 전문 지식 | 불필요 (AutoML) | 필요 (코드 작성) |
| 알고리즘 선택 | 자동 또는 제한된 선택 | 무제한 (커스텀 가능) |
| 데이터 타입 | 시계열만 | 모든 타입 |
| 사용 편의성 | 매우 높음 | 중간~낮음 |
| 유연성 | 제한적 | 매우 높음 |
| 적합한 경우 | 수요 예측, 재고 계획 | 복잡한 커스텀 ML 모델 |

### Forecast vs QuickSight

| 항목 | Amazon Forecast | Amazon QuickSight |
|------|----------------|------------------|
| 목적 | 예측 | 시각화·BI |
| 기능 | 미래 값 예측 | 과거 데이터 분석·대시보드 |
| ML 예측 | 고급 (전용) | 기본적인 ML Insights |
| 통합 | Forecast → QuickSight로 시각화 | 데이터 소스에서 읽기 |

## 모범 사례

### 1. 충분한 과거 데이터 제공

**최소 요구사항**
- 최소 300개 데이터 포인트 권장
- Forecast Horizon의 3배 이상

**예시**
- 30일 예측: 최소 90일 과거 데이터

### 2. 데이터 품질 확보

**결측값 처리**
- Forecast가 자동으로 보간 (interpolation)
- 하지만 결측값 최소화가 좋음

**이상값(Outliers) 처리**
- 명백한 오류는 제거
- 실제 이벤트는 유지 (예: 블랙 프라이데이 급증)

**일관된 시간 간격**
- 시계열 데이터의 간격 일정하게 유지
- 예: 매일 1개 데이터 포인트

### 3. 관련 변수 추가

**효과적인 Related Time Series**
- 가격, 프로모션
- 날씨, 기온
- 휴일, 이벤트

**Item Metadata 활용**
- 제품 카테고리, 브랜드
- 유사 항목 간 패턴 학습

### 4. AutoML 사용

**권장**
- 대부분의 경우 AutoML이 최적
- 여러 알고리즘 자동 테스트
- 가장 정확한 알고리즘 선택

**수동 선택**
- 특정 알고리즘 성능 비교 시
- 도메인 지식 기반 선택

### 5. 적절한 분위수 선택

**보수적 계획**
- p90: 안전 재고 수준
- 재고 부족 리스크 최소화

**공격적 계획**
- p10~p50: 평균 수준
- 재고 비용 최소화

**여러 분위수 활용**
- p10, p50, p90 동시 생성
- 시나리오별 계획

### 6. 정기 재훈련

**모델 드리프트 방지**
- 월 1회 또는 분기 1회 재훈련
- 최신 데이터로 모델 업데이트
- 정확도 유지

### 7. Explainability 활용

**예측 이해**
- 어떤 변수가 가장 영향 큰지 확인
- 비즈니스 인사이트 도출

### 8. MLOps 파이프라인 구축

**자동화**
- Step Functions로 워크플로우 자동화
- Lambda로 데이터 수집·전처리
- EventBridge로 정기 재훈련 스케줄링

**모니터링**
- CloudWatch로 훈련·예측 작업 모니터링
- SNS로 완료·실패 알림

## 제한 사항

### 데이터 크기
- Dataset당 최대 3GB
- 더 큰 데이터는 전처리 필요

### 예측 기간
- Forecast Horizon: 최대 500개 time step
- 예: 일별 예측 시 최대 500일

### Item 수
- 수천~수백만 개 item 지원
- 단, 훈련 시간 증가

### Frequency
- 최소: 1분 (1min)
- 최대: 1년 (1Y)

## 통합 서비스

### S3
- 데이터 입력·출력

### QuickSight
- 예측 결과 시각화

### Athena
- 예측 데이터 쿼리

### Step Functions
- 예측 워크플로우 오케스트레이션

### Lambda
- 데이터 전처리·후처리

### CloudWatch
- 모니터링·알림

## 요약

Amazon Forecast는 Amazon.com의 검증된 머신러닝 기술을 기반으로 과거 시계열 데이터(판매량, 트래픽 등)와 관련 변수(날씨, 휴일, 프로모션)를 입력받아 AutoML로 최적 알고리즘(CNN-QR, DeepAR+, Prophet, ARIMA, ETS)을 자동 선택·훈련·튜닝하여 미래를 예측하는 완전 관리형 서버리스 서비스로, 기존 통계 방법 대비 최대 50% 더 정확한 확률적 예측(p10, p50, p90 분위수)을 제공한다. S3에 CSV 데이터 업로드 → Dataset Group·Dataset 생성 → Predictor 훈련 → Forecast 생성 → 결과 조회·내보내기 순서로 작동하며, 소매·재고 예측, 인력 계획, 공급망 최적화, 에너지 수요, 웹 트래픽, 재무 계획 등에 활용되고, 데이터 저장 GB당 $0.088/월, 훈련 시간당 $0.24, 1,000개 예측당 $0.60으로 과금되며, 충분한 과거 데이터(최소 300개), 관련 변수 추가, AutoML 사용, 정기 재훈련, Explainability 활용, MLOps 파이프라인 구축이 모범 사례다.
