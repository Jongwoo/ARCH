# ADR-0002: HTTP 에러 응답 포맷 표준화

- Status: Accepted
- Date: 2025-11-25
- System: task-service
- Quality Attributes: usability, reliability

## 1. Context

클라이언트(웹/모바일/다른 서비스)가 task-service를 사용할 때,
에러 응답 포맷이 제각각이면 처리 로직이 복잡해진다.

## 2. Decision

1. **에러 응답 공통 스키마**

   모든 에러 응답은 다음 JSON 포맷을 따른다.

   ```json
   {
     "errorCode": "TASK_NOT_FOUND",
     "message": "Task not found",
     "details": {
       "taskId": "123"
     }
   }
   ```

   - `errorCode`: UPPER_SNAKE_CASE 문자열 (예: `TASK_NOT_FOUND`, `INVALID_INPUT`)
   - `message`: 사람이 읽을 수 있는 에러 설명
   - `details`: 선택적, 추가 컨텍스트 정보
