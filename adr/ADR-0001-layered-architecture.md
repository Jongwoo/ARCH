# ADR-0001: Task 서비스의 레이어드 아키텍처와 DI 규칙

- Status: Accepted
- Date: 2025-11-25
- System: task-service
- Quality Attributes: modifiability, testability

## 1. Context

task-service는 간단한 REST API지만, 이후 기능 확장을 고려하여
레이어를 나누고 테스트 가능한 구조를 미리 잡고자 한다.

## 2. Decision

1. **레이어 구분**

   - `app/api`: HTTP 엔드포인트 (FastAPI router 등)
   - `app/domain`: 도메인 모델, 도메인 서비스 (비즈니스 로직)
   - `app/infra`: 외부 의존성 (DB, 외부 API, in-memory repository 등)

2. **참조 방향**

   - `api` → `domain` → `infra` 방향으로만 참조한다.
   - 하위 레이어가 상위 레이어를 참조하면 안 된다.

3. **DI(의존성 주입)**

   - 도메인 서비스는 구체 구현 대신 **인터페이스(프로토콜 수준)**에 의존한다.
   - 예: `TaskRepository` 인터페이스를 `domain`에 정의하고,
     구체 구현(`InMemoryTaskRepository`)은 `infra`에 둔다.
   - API 레벨에서 구체 구현을 생성하여 서비스에 주입한다.

## 3. Rationale

- 레이어를 나누면 이후 DB 교체, 외부 API 연동 시 변경 범위가 줄어든다.
- 인터페이스 기반 DI는 테스트에서 fake/stub을 쉽게 사용하게 해준다.
- 아주 작은 서비스라도 구조를 잡아두면 Copilot/LLM이 일관된 패턴으로 코드를 생성하게 만들 수 있다.

## 4. Consequences

### Positive

- 도메인 로직과 인프라 코드가 분리되어 테스트와 유지보수가 쉬워진다.
- LLM/Copilot에게 "ADR-0001을 따르라"고 지시하면, 구조가 통일된다.

### Negative

- 초기 코드량이 약간 늘어난다.
- 아주 단순한 스크립트보다 진입 장벽이 약간 높다.
