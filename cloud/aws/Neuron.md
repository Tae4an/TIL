# AWS Neuron이란
## 1. 개념 요약
AWS Neuron은 **AWS가 자체 설계한 AI 전용 칩(Trainium·Inferentia)에서 딥러닝·생성형 AI 워크로드를 실행하기 위한 소프트웨어 개발 스택(SDK)** 이다. 하드웨어(칩) 자체가 아니라 **컴파일러·런타임·라이브러리·개발 도구를 하나로 묶은 소프트웨어 레이어**이며, 개발자가 기존 PyTorch·JAX 코드를 거의 수정하지 않고 AWS 전용 칩에서 실행할 수 있게 해준다.

***
## 2. Neuron이 지원하는 AWS 전용 칩
Neuron은 AWS의 두 가지 자체 ML 칩을 위한 통합 SDK다.

- **AWS Inferentia (Inf1, Inf2)**
  - 추론(Inference) 전용 칩. 학습된 모델을 프로덕션에 배포해 예측을 실행하는 워크로드에 최적화.
  - GPU 대비 최대 70% 낮은 비용으로 추론 처리량 제공.

- **AWS Trainium (Trn1, Trn2, Trn3)**
  - 모델 학습(Training) 전용 칩. 트랜스포머 기반 대형 모델 훈련에 특화.
  - Trn2는 1세대 대비 4배 성능 향상. Trn3(2025년 출시)는 TSMC 3nm 공정 기반으로 칩당 2.52 PFLOPS(FP8) 성능을 제공.
  - Anthropic의 Claude 모델 학습에 Trainium2 50만 개 클러스터(Project Rainier)가 사용됨.

***
## 3. Neuron SDK 구성 요소
- **Neuron 컴파일러**
  - PyTorch·JAX 모델 그래프를 Trainium/Inferentia 칩에서 실행 가능한 최적화된 바이너리로 변환한다. NVIDIA의 TensorRT와 유사한 역할.

- **Neuron 런타임**
  - 컴파일된 모델을 실제 칩에서 실행·관리하는 실행 환경이다.

- **프레임워크 통합**
  - PyTorch(torch-neuronx), JAX 등 주요 ML 프레임워크와 네이티브로 통합된다.
  - HuggingFace, vLLM, PyTorch Lightning 같은 상위 라이브러리도 별도 코드 수정 없이 사용 가능하다.

- **모니터링·프로파일링 도구**
  - `neuron-top`, `neuron-monitor` 등으로 NeuronCore 사용률, 메모리 현황, 로드된 모델 상태를 실시간 확인할 수 있다.

***
## 4. 어디에 쓰이는가
- **생성형 AI 추론 배포**: Llama 4, FLUX.1 이미지 생성 모델 등 대형 LLM을 Inferentia/Trainium 인스턴스에 vLLM으로 서빙
- **대규모 모델 학습**: 수십~수백억 파라미터 트랜스포머 모델을 GPU 대비 저비용으로 학습
- **비용 최적화**: GPU(NVIDIA A100/H100) 대비 낮은 단가로 추론·학습 비용 절감이 필요한 워크로드
- **온디맨드 ML 파이프라인**: SageMaker·ECS·EKS와 연동해 Neuron 기반 학습·추론 파이프라인 자동화

***
## 5. Neuron 관련 EC2 인스턴스 패밀리
| 인스턴스 | 칩 | 용도 | 특징 |
|---|---|---|---|
| inf1 | Inferentia 1 | 추론 | 저비용 추론, 1세대 |
| inf2 | Inferentia 2 | 추론 | 대형 모델 추론, 고대역폭 메모리 |
| trn1 | Trainium 1 | 학습 | 트랜스포머 모델 학습 |
| trn2 | Trainium 2 | 학습·추론 | 4x 성능 향상, UltraServer 구성 |
| trn3 | Trainium 3 | 학습·추론 | 3nm 공정, 2.52 PFLOPS/칩 (2025 출시) |

***
## 6. NVIDIA GPU와의 비교 포지션
Neuron은 NVIDIA GPU를 대체하기보다는 **비용 효율이 중요한 대규모 학습·추론 워크로드에서 GPU 대안**으로 포지셔닝된다. 코드 호환성은 PyTorch 기반으로 높아졌지만, CUDA 생태계만큼 넓은 범용 지원은 아직 발전 중이다. Anthropic처럼 AWS와 긴밀히 협력하는 대형 AI 회사들이 주요 실사용 사례를 만들어가고 있다.

***
## 7. 한 줄 정리
AWS Neuron은 **AWS 자체 설계 AI 칩(Trainium·Inferentia)에서 PyTorch·JAX 모델을 코드 수정 없이 컴파일·실행·모니터링할 수 있게 해주는 엔드투엔드 ML SDK로, GPU 대비 저비용 대규모 학습·추론을 목표로 하는 AWS의 자체 AI 실리콘 소프트웨어 스택**이다.
