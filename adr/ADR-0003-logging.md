
---

### 3-3. ADR-0003: 로깅 규칙

`adr/ADR-0003-logging.md`

```md
# ADR-0003: 로깅 규칙

- Status: Accepted
- Date: 2025-11-25
- System: task-service
- Quality Attributes: observability, operability

## 1. Context

초기에는 단일 서비스지만, 나중에 서비스가 늘어나면
요청 단위로 로그를 추적하기 어려울 수 있다.

## 2. Decision

1. **요청 ID**

   - 각 HTTP 요청마다 `X-Request-Id` 헤더를 사용한다.
   - 요청에 헤더가 없으면 서버에서 UUID를 생성한다.
   - 로그에는 항상 `request_id` 필드를 포함한다.

2. **로그 레벨 규칙**

   - DEBUG: 개발/디버깅 시 상세 정보
   - INFO: 정상적인 상태 변화 (생성, 수정, 주요 이벤트)
   - WARN: 예상 가능한 에러/유효성 실패
   - ERROR: 예외/예기치 못한 오류

3. **로그 포맷**

   - 개발 단계에서는 단순 텍스트 포맷
   - 가능하면 `request_id`, `path`, `method`, `errorCode`(있다면)를 포함

## 3. Rationale

- 요청 단위로 추적 가능한 로그는 장애 분석과 운영에 필수적이다.
- Copilot/LLM이 로깅 코드를 생성할 때 이 규칙을 따르게 할 수 있다.

## 4. Consequences

### Positive

- 문제 발생 시 추적이 쉬워진다.
- 나중에 중앙 로그 시스템(예: ELK, Loki 등)으로 확장할 때 도움이 된다.

### Negative

- 초기에 약간의 미들웨어/헬퍼 코드가 필요하다.
