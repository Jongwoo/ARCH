
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
   - **모든 로그는 반드시 `request_id`를 포함해야 한다.**
   - 로깅 시 `extra={"request_id": request_id}` 형식으로 전달한다.

2. **로그 레벨 규칙**

   - DEBUG: 개발/디버깅 시 상세 정보
   - INFO: 정상적인 상태 변화 (생성, 수정, 주요 이벤트)
   - WARN: 예상 가능한 에러/유효성 실패
   - ERROR: 예외/예기치 못한 오류

3. **로그 포맷**

   - 로그 포맷에 `request_id`를 포함한다: `[request_id]`
   - 개발 단계에서는 단순 텍스트 포맷
   - 필수 포함: `request_id`, 선택 포함: `path`, `method`, `errorCode`(있다면)
   - 예시: `2025-11-26 10:30:45 - [abc-123] - app.service - INFO - Creating task`

4. **로깅 구현 규칙**

   - 모든 API 엔드포인트는 `request_id`를 추출하여 하위 레이어로 전달한다.
   - 도메인 서비스 메서드는 `request_id` 파라미터를 받아 로깅에 사용한다.
   - `request_id`가 없는 경우 `-` 또는 `unknown`으로 표시한다.

## 3. Rationale

- 요청 단위로 추적 가능한 로그는 장애 분석과 운영에 필수적이다.
- Copilot/LLM이 로깅 코드를 생성할 때 이 규칙을 따르게 할 수 있다.

## 4. Consequences

### Positive

- 문제 발생 시 추적이 쉬워진다.
- 나중에 중앙 로그 시스템(예: ELK, Loki 등)으로 확장할 때 도움이 된다.

### Negative

- 초기에 약간의 미들웨어/헬퍼 코드가 필요하다.
