# AWS DeepRacer란

## 1. 개념 요약

AWS DeepRacer는 **강화 학습(Reinforcement Learning)을 누구나 재미있고 직관적으로 배울 수 있도록 설계된 AWS의 ML 교육용 플랫폼**이다. 1/18 비율의 실물 자율주행 RC카, 클라우드 기반 3D 레이싱 시뮬레이터, 글로벌 레이싱 리그 세 가지를 묶은 패키지로, **"모델을 학습시켜 레이스트랙을 가장 빨리 완주하는 차량을 만든다"는 게임적 요소로 강화 학습 개념을 실습**하는 것이 핵심이다.

***

## 2. 세 가지 구성 요소

- **AWS DeepRacer 콘솔 (클라우드 시뮬레이터)**
  - 웹 브라우저에서 바로 접근 가능한 3D 레이싱 시뮬레이션 환경이다.
  - Python으로 보상 함수(Reward Function)를 작성하고, SageMaker RL 기반으로 클라우드에서 모델을 학습·평가한다.
  - 별도 하드웨어 없이 시뮬레이터만으로 강화 학습 전 과정을 실습할 수 있다.

- **AWS DeepRacer 차량 (물리적 RC카)**
  - Intel Atom 프로세서와 4MP 전방 카메라를 탑재한 1/18 비율 RC카다.
  - 클라우드에서 학습한 모델을 차량에 배포하면, 카메라 영상만으로 실제 트랙을 자율 주행한다.
  - DeepRacer Evo 모델은 스테레오 카메라와 LiDAR 센서를 추가 지원한다.

- **AWS DeepRacer 리그**
  - 세계 최초의 글로벌 자율주행 레이싱 리그로, 2018년 re:Invent에서 처음 공개됐다.
  - **가상 서킷(Virtual Circuit)**: 온라인에서 훈련한 모델을 리더보드에 제출해 전 세계 참가자와 랩타임 경쟁
  - **오프라인 레이스**: AWS Summit 등 실제 이벤트에서 실물 차량으로 트랙 경주
  - 상위 성적자는 **World Championship Cup** 진출 자격을 얻는다.

***

## 3. 강화 학습 핵심 개념 (DeepRacer 관점)

DeepRacer는 강화 학습의 핵심 개념을 레이싱에 비유해 직관적으로 가르친다.

| RL 개념 | DeepRacer에서의 의미 |
|---|---|
| 에이전트(Agent) | DeepRacer 차량 |
| 환경(Environment) | 레이스 트랙 |
| 상태(State) | 카메라가 포착한 현재 영상 프레임 |
| 행동(Action) | 조향각·속도 결정 |
| 보상(Reward) | 트랙 이탈 없이 중앙을 유지할수록 높은 점수 |
| 정책(Policy) | 어떤 상태에서 어떤 행동을 할지 결정하는 학습된 모델 |

***

## 4. 보상 함수(Reward Function)란

DeepRacer 학습의 핵심은 **Python으로 직접 작성하는 보상 함수**다. 차량이 어떤 행동을 했을 때 점수를 줄지를 개발자가 직접 정의하며, 이 설계 방식에 따라 모델 성능이 크게 달라진다.

```python
# 보상 함수 예시: 트랙 중앙에 가까울수록 높은 보상
def reward_function(params):
    track_width = params['track_width']
    distance_from_center = params['distance_from_center']
    marker_1 = 0.1 * track_width
    marker_2 = 0.25 * track_width
    if distance_from_center <= marker_1:
        reward = 1.0
    elif distance_from_center <= marker_2:
        reward = 0.5
    else:
        reward = 1e-3  # 트랙 이탈 직전
    return float(reward)
```

입력 파라미터로 속도·조향각·트랙 중심과의 거리·진행 방향·웨이포인트 등 다양한 상태 정보가 제공된다.

***

## 5. 어디에 쓰이는가

- **ML 입문 교육**: 코딩 경험이 적어도 보상 함수 작성만으로 강화 학습의 전체 사이클을 체험
- **대학·기업 교육**: 경기대·성균관대 등 국내 대학과 기업에서 ML 교육 프로그램으로 활발히 활용
- **AWS 이벤트·해커톤**: AWS Summit·re:Invent의 대표 체험 이벤트로 전 세계에서 진행
- **AWS DeepRacer Student**: 고등학생·대학생 대상 무료 제공, 장학금 프로그램 연계

***

## 6. DeepRacer vs DeepLens vs DeepComposer 비교

| 항목 | DeepRacer | DeepLens | DeepComposer |
|---|---|---|---|
| 학습 주제 | 강화 학습 (RL) | 컴퓨터 비전 (CV) | 생성형 AI (GAN·AR) |
| 하드웨어 | 1/18 RC카 | 딥러닝 카메라 | AI 지원 음악 키보드 |
| 게임 요소 | 레이싱 리그 경쟁 | 없음 | 음악 작곡 |
| 현황 | 현재 운영 중 | 레거시 | 현재 운영 중 |

***

## 7. 한 줄 정리

AWS DeepRacer는 **1/18 RC카·3D 시뮬레이터·글로벌 레이싱 리그를 결합해 누구나 보상 함수 설계와 모델 학습으로 강화 학습 개념을 실습할 수 있는 AWS의 대표 ML 교육 플랫폼**이다.
