# AWS AppSync란?

## 개념 정의
AWS AppSync는 GraphQL API를 구축하고 관리하는 완전 관리형 서버리스 서비스로, 여러 데이터 소스(DynamoDB, Aurora, Lambda, HTTP 엔드포인트 등)를 하나의 GraphQL API로 통합하고, WebSocket 기반 실시간 데이터 동기화와 오프라인 지원을 제공한다.

쉽게 말해 "GraphQL API를 서버 없이 만들고, 여러 DB·서비스를 하나의 API로 묶으며, 실시간 업데이트(채팅, 협업 앱 등)를 WebSocket으로 자동 처리해주는 서버리스 GraphQL 서비스"다.

## 핵심 특징

### GraphQL API 자동 생성 및 관리
DynamoDB 테이블이나 Aurora 데이터베이스에서 GraphQL 스키마를 자동 생성하고, 여러 GraphQL API를 페더레이션으로 하나의 슈퍼그래프로 결합할 수 있다.

### 실시간 데이터 동기화
GraphQL Subscription과 WebSocket을 통해 클라이언트가 데이터 변경사항을 실시간으로 수신하며, AppSync가 연결 관리와 확장을 자동으로 처리한다. 채팅 앱, 협업 도구, 라이브 스포츠 점수 등에 적합하다.

### 오프라인 지원
로컬 캐시에 데이터를 저장해 오프라인에서도 작동하며, 네트워크 재연결 시 자동으로 동기화한다.

### 다양한 데이터 소스 통합
DynamoDB, Aurora(RDS), Lambda, Elasticsearch, HTTP 엔드포인트, Amazon Bedrock(AI) 등 여러 데이터 소스를 하나의 GraphQL API로 통합하며, 리졸버로 각 필드의 데이터 소스를 지정한다.

### 서버리스 및 Auto Scaling
인프라 관리 불필요하며, 요청량에 따라 자동 확장되고 사용한 만큼만 비용을 지불한다.

## GraphQL 기본 개념

### GraphQL이란?
API를 위한 쿼리 언어로, 클라이언트가 필요한 데이터만 정확히 요청할 수 있어 REST API보다 효율적이다. 여러 리소스를 한 번의 요청으로 가져올 수 있다.

### 3가지 주요 작업
- **Query**: 데이터 조회 (REST의 GET)
- **Mutation**: 데이터 생성·수정·삭제 (REST의 POST, PUT, DELETE)
- **Subscription**: 실시간 데이터 스트리밍 (WebSocket)

### GraphQL 예시
**스키마 정의**
```graphql
type Post {
  id: ID!
  title: String!
  author: User!
}

type User {
  id: ID!
  name: String!
  posts: [Post]
}

type Query {
  getPost(id: ID!): Post
  listPosts: [Post]
}

type Mutation {
  createPost(title: String!, authorId: ID!): Post
}

type Subscription {
  onCreatePost: Post
}
```

**쿼리 예시**
```graphql
query {
  getPost(id: "123") {
    id
    title
    author {
      name
    }
  }
}
```

클라이언트가 필요한 필드(id, title, author.name)만 요청하면 서버가 해당 데이터만 반환한다.

## 주요 구성 요소

### 1. GraphQL Schema
API의 데이터 타입과 작업(Query, Mutation, Subscription)을 정의하는 청사진이다.

### 2. Resolver
각 GraphQL 필드가 어떤 데이터 소스에서 데이터를 가져올지 정의하는 함수다. VTL(Velocity Template Language) 또는 JavaScript로 작성한다.

### 3. Data Source
DynamoDB, Lambda, RDS, HTTP 엔드포인트 등 실제 데이터를 저장하거나 처리하는 백엔드 서비스다.

### 4. API Key / Cognito / IAM
GraphQL API 접근 인증·인가를 위한 보안 메커니즘으로, API Key, Cognito User Pools, IAM, OIDC를 지원한다.

## 실시간 데이터 작동 방식

### Subscription 플로우
1. 클라이언트가 Subscription 요청 → AppSync가 WebSocket 연결 수립
2. 다른 클라이언트가 Mutation 실행 → 데이터 변경
3. AppSync가 해당 Subscription을 구독 중인 모든 클라이언트에게 실시간 푸시
4. 클라이언트가 자동으로 UI 업데이트

### 예시: 채팅 앱
```graphql
# 새 메시지 생성 (Mutation)
mutation {
  sendMessage(text: "Hello", userId: "user1") {
    id
    text
    createdAt
  }
}

# 새 메시지 구독 (Subscription)
subscription {
  onSendMessage {
    id
    text
    userId
    createdAt
  }
}
```

사용자가 메시지를 보내면 구독 중인 모든 사용자가 즉시 새 메시지를 받는다.

## 주요 활용 사례

### 실시간 협업 앱
여러 사용자가 동시에 문서 편집, 대시보드 업데이트, 코드 공유 등을 할 때 Subscription으로 실시간 동기화를 구현한다.

### 채팅·메시징 앱
1:1 채팅, 그룹 채팅, 알림 등 실시간 메시지 전송에 WebSocket 기반 Subscription을 활용한다.

### 라이브 스포츠·주식 가격
경기 점수, 주식 시세, 위치 추적 등 빈번히 변경되는 데이터를 실시간으로 푸시한다. PGA Tour가 라이브 골프 점수 업데이트에 AppSync를 사용한다.

### 모바일·웹 앱 백엔드
Amplify와 통합해 React, Angular, iOS, Android 앱의 GraphQL 백엔드를 빠르게 구축하며, 오프라인 동기화도 지원한다.

### AI 게이트웨이
Amazon Bedrock 같은 AI 모델과 통합해 GraphQL API로 AI 기능을 제공하고, 실시간 스트리밍 응답을 구현한다.

## AppSync vs API Gateway

| 항목 | AWS AppSync | API Gateway |
|------|------------|-------------|
| API 타입 | GraphQL | REST, WebSocket, HTTP |
| 실시간 | WebSocket Subscription 자동 | WebSocket API 수동 구성 |
| 데이터 소스 통합 | 네이티브 지원 (DynamoDB, Lambda 등) | Lambda로 중개 |
| 오프라인 지원 | 자동 | 별도 구현 필요 |
| 쿼리 유연성 | 클라이언트가 필요한 필드만 선택 | 엔드포인트별 고정 응답 |
| 복잡한 데이터 조회 | 한 번에 여러 리소스 조회 | 여러 API 호출 필요 |

## 비용
- API 요청당 과금: Query/Mutation $4.00 / 백만 요청
- 실시간 업데이트: 연결 분당 $0.08 / 백만 분, 메시지 $2.00 / 백만 건
- 무료 티어: 월 25만 Query/Mutation, 60만 실시간 업데이트

## 요약
AWS AppSync는 GraphQL API를 서버리스로 구축하는 완전 관리형 서비스로, DynamoDB·Aurora·Lambda·HTTP 엔드포인트 등 여러 데이터 소스를 하나의 GraphQL API로 통합하고, WebSocket 기반 Subscription으로 실시간 데이터 동기화(채팅, 협업, 라이브 점수)와 오프라인 지원을 제공하며, Amplify와 통합해 모바일·웹 앱 백엔드를 빠르게 구축하고, Amazon Bedrock 연동으로 AI 게이트웨이 역할도 수행한다.
