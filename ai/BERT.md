# BERT란?

## 개념 정의
BERT(Bidirectional Encoder Representations from Transformers)는 구글이 2018년 10월에 발표한 **양방향 사전 학습 언어 모델**이다. Transformer의 인코더 구조만을 사용하여 텍스트의 문맥을 양방향(좌→우, 우→좌)으로 이해하며, 다양한 자연어 처리(NLP) 작업에서 높은 성능을 보이는 범용 언어 모델이다.

쉽게 말해 "문장의 앞뒤 문맥을 모두 고려해 단어의 의미를 이해하는 사전 학습 AI 모델로, 검색·번역·질의응답·감성분석 등 거의 모든 NLP 작업에 전이학습(Transfer Learning)으로 활용할 수 있는 범용 언어 이해 엔진"이다.

## 핵심 특징

**양방향(Bidirectional) 문맥 이해**
- 기존 모델(GPT 등)은 왼쪽→오른쪽 단방향으로만 문맥을 읽음
- BERT는 문장의 앞뒤 양쪽 문맥을 동시에 고려해 단어의 의미를 파악
- 예: "I feel **fine** today"와 "She has **fine** hair"에서 'fine'의 다른 의미를 문맥으로 구분

**Transformer 인코더 기반**
- Transformer의 인코더 부분만 사용(디코더는 사용 안 함)
- Self-Attention 메커니즘으로 문장 내 모든 단어 간 관계를 동시에 학습
- RNN, LSTM 없이도 긴 문맥 의존성을 효과적으로 처리

**사전 학습(Pre-training) + 미세 조정(Fine-tuning) 전이학습 방식**
- 대량의 레이블 없는 텍스트 데이터로 사전 학습(Wikipedia + BookCorpus, 총 33억 단어)
- 사전 학습된 모델에 소량의 라벨링 데이터로 특정 작업에 미세 조정
- 적은 데이터와 짧은 학습 시간으로도 높은 성능 달성

**두 가지 사전 학습 과제**

1. **MLM (Masked Language Model)**
   - 입력 문장의 일부 단어를 [MASK] 토큰으로 가리고, 원래 단어를 예측
   - 양방향 문맥 학습의 핵심 메커니즘
   - 예: "The cat [MASK] on the mat" → "sat" 예측

2. **NSP (Next Sentence Prediction)**
   - 두 문장이 실제로 연결된 문장인지, 무작위로 선택된 문장인지 예측
   - 문장 간 관계 이해 능력 학습

**세 가지 임베딩 합성**
- Token Embedding: 단어 자체의 의미 벡터
- Segment Embedding: 문장 A/B 구분(질문-답변, 문장1-문장2 등)
- Position Embedding: 단어의 순서 정보
- 세 임베딩을 합쳐 Layer Normalization + Dropout 적용 후 입력으로 사용

## 모델 구조

### 입력 구성
```
[CLS] 질문 문장 [SEP] 답변 문장 [SEP]
```
- [CLS]: 분류 작업용 특수 토큰(문장 전체 정보 집약)
- [SEP]: 문장 구분 토큰
- [PAD]: 패딩 토큰

### BERT 아키텍처
```
입력 토큰
↓
Token Embedding + Segment Embedding + Position Embedding
↓
Layer Normalization + Dropout
↓
Transformer Encoder Layer 1
  - Multi-Head Self-Attention
  - Add & Norm
  - Feed Forward Network
  - Add & Norm
↓
... (여러 층 반복)
↓
Transformer Encoder Layer N
↓
출력: 각 토큰의 문맥 임베딩
```

### 두 가지 모델 크기

| 모델 | Transformer 블록(L) | Hidden Size(H) | Attention Head(A) | 파라미터 |
|------|-------------------|----------------|------------------|---------|
| BERT-Base | 12층 | 768 | 12 | 110M |
| BERT-Large | 24층 | 1024 | 16 | 340M |

## 주요 활용 사례

**텍스트 분류**
- 감성 분석(긍정/부정), 스팸 분류, 주제 분류
- [CLS] 토큰의 출력 벡터를 분류층에 입력

**질의응답(QA)**
- SQuAD 등 질문에 대한 답변 위치 찾기
- 문맥에서 답변 시작·끝 위치 예측

**개체명 인식(NER)**
- 문장에서 사람, 장소, 조직 등 개체 태깅
- 각 토큰의 출력을 분류

**자연어 추론(NLI)**
- 두 문장 간 논리 관계(참/거짓/중립) 판단

**검색 및 정보 추출**
- 검색 쿼리와 문서 간 유사도 계산
- 문서 랭킹, 추천 시스템

**번역 및 요약**
- 인코더 출력을 디코더에 연결해 Seq2Seq 작업 수행

## GPT vs BERT 비교

| 특징 | GPT | BERT |
|------|-----|------|
| 아키텍처 | Transformer 디코더 | Transformer 인코더 |
| 학습 방향 | 단방향(좌→우) | 양방향(좌↔우) |
| 사전 학습 과제 | 다음 단어 예측(Auto-regressive) | MLM + NSP |
| 강점 | 텍스트 생성, 대화 | 문맥 이해, 분류, QA |
| 대표 응용 | 챗봇, 자동 완성, 창작 | 검색, 분류, 추출 |

## 실무 활용 팁

**사전 학습 모델 활용**
```python
from transformers import BertTokenizer, BertModel

tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
model = BertModel.from_pretrained('bert-base-uncased')
```

**미세 조정(Fine-tuning) 전략**
- 사전 학습 가중치 동결 후 상위 분류층만 학습(빠르지만 성능 제한적)
- 전체 모델 미세 조정(느리지만 최고 성능)
- 학습률은 사전 학습보다 작게 설정(2e-5 ~ 5e-5 권장)

**다국어 BERT**
- mBERT(Multilingual BERT): 104개 언어 동시 학습
- 한국어 특화: KoBERT, KcBERT, KR-BERT 등

**최적화**
- DistilBERT: BERT 크기 40% 축소, 속도 60% 향상, 성능 97% 유지
- ALBERT: 파라미터 공유로 모델 크기 대폭 축소
- TinyBERT, MobileBERT: 모바일·엣지 환경용 경량화

## BERT의 영향과 후속 모델

**BERT 이후 발전**
- RoBERTa: NSP 제거, 더 큰 배치·데이터로 성능 개선
- ELECTRA: MLM 대신 토큰 교체 탐지로 학습 효율 향상
- DeBERTa: Disentangled Attention으로 성능 강화
- BERT → GPT-3 → ChatGPT → LLaMA → GPT-4 등 대형 언어 모델(LLM) 발전의 초석

**"BERTology" 연구**
- BERT가 무엇을 학습했는지 해석하는 연구 분야
- 각 층이 구문·의미·상식 등 어떤 정보를 담고 있는지 분석

## 요약

BERT는 NLP 분야의 패러다임을 "특정 작업용 모델 설계"에서 "범용 사전 학습 모델 + 미세 조정"으로 전환시킨 혁신적 모델이며, 현대 언어 AI의 핵심 토대를 마련했다. 양방향 문맥 이해, Transformer 인코더, 전이학습 방식을 결합해 적은 데이터로도 다양한 NLP 작업에서 최고 수준의 성능을 달성할 수 있게 했다.
