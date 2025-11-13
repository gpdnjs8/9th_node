## 핵심 키워드

- 미들웨어
    
    요청과 응답 사이에서 실행되는 함수
    
    요청을 가로채서 로깅, 인증, 데이터 변환 등의 작업을 수행한 후 다음 단계로 전달함
    
    ```jsx
    app.use((req, res, next) => {
      console.log('요청 받음');
      next(); // 다음 미들웨어로
    });
    ```
    
- HTTP 상태 코드
    
    서버의 응답 상태를 나타내는 3자리 숫자
    
    - **2xx (성공)**: 200 OK, 201 Created
    - **3xx (리다이렉션)**: 301 Moved Permanently, 302 Found
    - **4xx (클라이언트 에러)**: 400 Bad Request, 401 Unauthorized, 404 Not Found
    - **5xx (서버 에러)**: 500 Internal Server Error, 503 Service Unavailable
- 에러 핸들링(Error Handling)
    
    프로그램 실행 중 발생하는 오류를 처리하는 방법
    
    - **try-catch**: 예외를 잡아서 처리
    - **에러 미들웨어**: Express에서 전역 에러 처리
    - **Promise rejection**: `.catch()` 또는 `try-catch with async/await`