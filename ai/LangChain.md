# LangChain이란?

## 개념 정의
LangChain은 **대형 언어 모델(LLM)을 활용한 애플리케이션을 쉽게 개발하기 위한 오픈소스 오케스트레이션 프레임워크**다. 복잡한 AI 워크플로를 모듈화된 컴포넌트로 분해하고, 이를 "체인(Chain)"으로 연결해 챗봇, 질의응답 시스템, 데이터 분석 도구 등을 빠르게 구축할 수 있게 돕는다.

쉽게 말해 "LLM으로 뭔가 만들 때, 프롬프트·데이터·메모리·외부 도구를 연결하는 레고 블록처럼 조립할 수 있게 해주는 개발 도구 모음"이다.

## 핵심 특징

**모듈화 및 재사용성**
- 프롬프트 템플릿, LLM 호출, 데이터 검색, 후처리 등 각 단계를 독립적인 모듈로 관리
- 모듈을 조합해 다양한 워크플로 구성 가능
- 한 번 만든 컴포넌트를 여러 프로젝트에서 재사용

**체인(Chain) 기반 아키텍처**
- 여러 작업을 순차적으로 연결해 복잡한 워크플로 구성
- 예: "문서 검색 → 요약 → 번역 → 사용자 응답 생성"을 하나의 체인으로 묶음
- 체인 재정렬로 유연한 AI 파이프라인 설계 가능

**다양한 LLM 통합**
- OpenAI GPT, Anthropic Claude, Google PaLM, AWS Bedrock, 오픈소스(LLaMA, Falcon) 등 다중 LLM 지원
- LLM 교체 시에도 코드 수정 최소화
- 비용·성능·요구사항에 따라 LLM 선택·비교 가능

**외부 데이터 소스 연결**
- RAG 구현을 위한 벡터 데이터베이스 통합(Pinecone, Weaviate, Chroma, FAISS 등)
- API, 데이터베이스, 파일, 웹 스크래핑 등 다양한 데이터 소스 연결
- 문서 로더, 텍스트 분할기(Text Splitter), 임베딩 생성 도구 내장

**메모리 관리**
- 대화형 애플리케이션에서 과거 대화 기록 유지
- 단순 메모리(최근 N개 대화), 요약 메모리, 벡터 기반 메모리 등 다양한 전략 지원
- 컨텍스트 길이 관리 및 관련성 높은 이력만 선택적 활용

**에이전트(Agent) 시스템**
- LLM이 스스로 사용할 도구를 선택하고, 다단계 추론을 수행하는 자율 에이전트 구축
- 예: "데이터 검색 → 계산 → 외부 API 호출 → 결과 종합"을 LLM이 자동으로 계획·실행
- ReAct(Reasoning + Acting), Plan-and-Execute 등 에이전트 패턴 지원

**프롬프트 관리 및 최적화**
- 재사용 가능한 프롬프트 템플릿 라이브러리
- Few-shot 예시, 시스템 메시지, 변수 치환 등 손쉬운 프롬프트 구성
- 프롬프트 허브(Prompt Hub)로 커뮤니티 템플릿 공유

## 주요 구성 요소

### 1. Models (모델)
- LLM 인터페이스 추상화: OpenAI, Hugging Face, Cohere 등
- Chat Models, Text Models, Embedding Models 지원

### 2. Prompts (프롬프트)
- 프롬프트 템플릿: 변수 삽입, 동적 프롬프트 생성
- Example Selectors: Few-shot 학습을 위한 예시 자동 선택

### 3. Chains (체인)
- Sequential Chain: 순차 실행
- Router Chain: 조건부 분기
- MapReduce Chain: 병렬 처리 후 집계
- LLMChain, RetrievalQA, ConversationalRetrievalChain 등 사전 정의 체인

### 4. Indexes (인덱스 & 검색)
- Document Loaders: PDF, Markdown, CSV, 웹페이지 등 다양한 형식 로드
- Text Splitters: 문서를 청크 단위로 분할
- Vector Stores: 벡터 DB 연동(Pinecone, Chroma, FAISS)
- Retrievers: 의미 기반 문서 검색

### 5. Memory (메모리)
- ConversationBufferMemory: 전체 대화 저장
- ConversationSummaryMemory: 요약해서 저장
- VectorStoreMemory: 벡터 기반 유사 대화 검색

### 6. Agents (에이전트)
- Tools: 계산기, 검색 엔진, API, 데이터베이스 쿼리 등
- Agent Executors: 도구 사용 계획 수립 및 실행
- 자율적 문제 해결 및 멀티 스텝 추론

### 7. Callbacks (콜백)
- 로깅, 모니터링, 디버깅용 이벤트 핸들러
- 각 단계별 실행 시간, 토큰 사용량 추적

## 실제 활용 예시

**RAG 기반 질의응답 시스템**
```python
from langchain.document_loaders import PyPDFLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain.embeddings import OpenAIEmbeddings
from langchain.vectorstores import FAISS
from langchain.chains import RetrievalQA
from langchain.llms import OpenAI

# 1. 문서 로드 및 분할
loader = PyPDFLoader("company_policy.pdf")
documents = loader.load()
splitter = RecursiveCharacterTextSplitter(chunk_size=1000)
chunks = splitter.split_documents(documents)

# 2. 벡터 DB 구축
embeddings = OpenAIEmbeddings()
vectorstore = FAISS.from_documents(chunks, embeddings)

# 3. 검색 체인 생성
qa_chain = RetrievalQA.from_chain_type(
    llm=OpenAI(),
    retriever=vectorstore.as_retriever()
)

# 4. 질의
result = qa_chain.run("회사 휴가 정책은?")
```

**대화형 챗봇 (메모리 활용)**
```python
from langchain.memory import ConversationBufferMemory
from langchain.chains import ConversationChain
from langchain.llms import OpenAI

memory = ConversationBufferMemory()
conversation = ConversationChain(
    llm=OpenAI(),
    memory=memory
)

conversation.run("내 이름은 철수야")
conversation.run("내 이름이 뭐였지?")  # "철수"라고 기억
```

**에이전트 (도구 사용)**
```python
from langchain.agents import load_tools, initialize_agent
from langchain.llms import OpenAI

llm = OpenAI()
tools = load_tools(["serpapi", "llm-math"], llm=llm)
agent = initialize_agent(tools, llm, agent="zero-shot-react-description")

agent.run("2025년 AWS re:Invent 주요 발표는? 그리고 참가비를 5로 나눈 값은?")
# → 에이전트가 자동으로 검색 + 계산 수행
```

## 주요 활용 사례

**챗봇 및 대화형 AI**
- 고객 지원 챗봇, 사내 Q&A 봇
- 메모리로 대화 컨텍스트 유지

**문서 기반 질의응답**
- 기업 내부 문서, 매뉴얼, 정책 검색·요약
- RAG 아키텍처로 정확한 답변 제공

**데이터 분석 자동화**
- SQL 쿼리 자동 생성, 데이터 해석
- 자연어로 분석 요청 → 에이전트가 실행

**콘텐츠 생성**
- 블로그, 리포트, 마케팅 카피 자동 생성
- 템플릿 기반 맞춤형 콘텐츠

**복잡한 워크플로 자동화**
- 멀티스텝 작업(검색 → 분석 → 요약 → 이메일 발송)
- 에이전트가 자율적으로 작업 수행

## LangChain vs 기타 도구

| 도구 | 특징 | 차이점 |
|------|------|--------|
| LangChain | LLM 오케스트레이션 프레임워크 | 체인·에이전트·메모리 등 종합 도구 |
| LlamaIndex | 데이터 인덱싱·검색 특화 | RAG 구현에 최적화 |
| Haystack | 검색·QA 파이프라인 | 문서 검색 중심 |
| Semantic Kernel | MS 오케스트레이션 프레임워크 | .NET 생태계 통합 |

LangChain은 **가장 범용적이고 커뮤니티가 큰** LLM 프레임워크로, Python/JavaScript 생태계에서 사실상 표준이다.

## 비용 및 운영

**오픈소스 & 무료**
- LangChain 자체는 완전 무료
- LLM API 호출 비용(OpenAI, Bedrock 등)은 별도

**상용 도구: LangSmith**
- LangChain 팀이 제공하는 유료 모니터링·디버깅·평가 플랫폼
- 프로덕션 환경에서 체인 성능 추적, A/B 테스트, 에러 분석

## 보안 및 모범 사례

**프롬프트 인젝션 방지**
- 사용자 입력 검증 및 샌드박싱
- 프롬프트 템플릿에서 신뢰할 수 있는 부분과 사용자 입력 명확히 구분

**API 키 관리**
- 환경 변수로 API 키 관리, 코드에 하드코딩 금지
- AWS Secrets Manager, Azure Key Vault 등 활용

**비용 관리**
- 토큰 사용량 모니터링(Callbacks 활용)
- 캐싱으로 중복 LLM 호출 방지
- 저렴한 모델로 먼저 필터링 후 고급 모델 사용

**성능 최적화**
- 문서 청크 크기 조정(너무 크면 컨텍스트 낭비, 너무 작으면 검색 품질 저하)
- 벡터 DB 인덱스 최적화
- 비동기 호출로 병렬 처리

## 요약

LangChain은 **LLM 기반 애플리케이션 개발을 위한 종합 도구 세트**로, 체인·에이전트·메모리·검색 등 복잡한 AI 워크플로를 모듈화하고 빠르게 구축할 수 있게 해주는 현대 LLM 개발의 핵심 프레임워크다. Python/JavaScript로 제공되며, RAG, 챗봇, 자동화 등 거의 모든 LLM 사용 사례를 지원한다.
