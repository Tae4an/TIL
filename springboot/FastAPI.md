# FastAPI

FastAPI는 Python으로 작성된 현대적이고 빠른 웹 프레임워크로, API를 쉽게 만들 수 있도록 설계되었습니다. FastAPI는 ASGI(Asynchronous Server Gateway Interface) 표준을 기반으로 하며, 동기 및 비동기 요청을 모두 지원합니다.

## 주요 특징

### 빠른 성능
- FastAPI는 Starlette과 Pydantic을 기반으로 구축되어 매우 높은 성능을 제공합니다.
- 비동기 기능을 활용하여 높은 처리량과 낮은 지연 시간을 제공합니다.

### 타입 힌트 사용
- Python의 타입 힌트를 적극적으로 사용하여 코드 자동 완성과 오류 검사를 지원합니다.
- 타입 힌트를 사용한 경로 매개변수, 요청 본문, 쿼리 매개변수 등의 선언이 가능합니다.

### 자동화된 문서화
- FastAPI는 API 문서를 자동으로 생성합니다.
- Swagger UI와 ReDoc을 통해 실시간으로 API 문서를 확인할 수 있습니다.

### 간편한 검증
- Pydantic을 사용하여 데이터 검증 및 설정 관리를 쉽게 처리할 수 있습니다.
- 입력 데이터의 유효성을 검증하고, 오류 메시지를 자동으로 생성합니다.

## 설치
```bash
pip install fastapi
pip install uvicorn[standard]