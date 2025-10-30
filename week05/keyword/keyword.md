## 🎯 핵심 키워드

- 환경 변수
    
    프로그램 실행 환경에 따라 달라질 수 있는 설정값(변수)을 코드 바깥에서 관리하기 위한 방법
    
    예시: DB 비밀번호, API 키, 포트 번호 등
    
    보통 .env 파일에 저장하고 process.env로 접근함
    
    ```bash
    PORT=8080
    DB_PASSWORD=pwd123
    ```
    
    ```jsx
    const port = process.env.PORT;
    ```
    
- CORS
    
    다른 출처의 서버 자원에 접근할 수 있도록 허용하는 웹 보안 정책
    
    예시:
    
    - 클라이언트: `http://localhost:3000`
    - 서버: `http://localhost:4000`
        
        → 서로 다른 출처이므로 기본적으로 브라우저가 차단
        
    
    서버에서 응답 헤더를 통해 허용 설정:
    
    ```jsx
    res.setHeader("Access-Control-Allow-Origin", "*");
    ```
    
    또는 특정 도메인만 허용:
    
    ```jsx
    res.setHeader("Access-Control-Allow-Origin", "https://myapp.com");
    ```
    
    프론트엔드 요청 시 자동으로 “Preflight Request (OPTIONS)”를 보냄
    
- DB Connection, DB Connection Pool
    
    `DB Connection`: 애플리케이션이 데이터베이스와 연결을 맺는 통신 통로
    
    `DB Connection Pool`: 연결을 매번 새로 만드는 대신 미리 일정 개수의 연결을 만들어두고 재사용하는 방식
    
    일반 연결 예시:
    
    ```jsx
    const conn = mysql.createConnection({ host, user, password });
    ```
    
    풀(pool) 방식 예시:
    
    ```jsx
    const pool = mysql.createPool({ connectionLimit: 10, host, user, password });
    pool.query("SELECT * FROM users", (err, rows) => {});
    ```
    
    - 풀은 요청 시 연결 → 사용 → 반환의 순서로 관리됨
- 비동기 (async, await)
    
    비동기 처리: 코드가 실행되는 동안 기다리지 않고 다른 작업을 동시에 처리할 수 있는 방식
    
    `async/await`는 비동기 코드를 동기 코드처럼 읽기 쉽게 작성할 수 있도록 도와줌
    
    ```jsx
    async function fetchData() {
      try {
        const res = await fetch("https://api.example.com/data");
        const data = await res.json();
        console.log(data);
      } catch (err) {
        console.error(err);
      }
    }
    ```
    
    `async`: 함수가 항상 Promise를 반환함
    
    `await`: Promise가 해결될 때까지 기다림
    
- try/catch/finally
    
    예외 처리(예외 핸들링) 구문
    
    프로그램 실행 중 오류가 발생해도 전체 프로그램이 중단되지 않도록 처리함
    
    ```jsx
    try {
      // 에러 발생 가능 코드
      riskyFunction();
    } catch (error) {
      // 에러 발생 시 실행
      console.error("Error:", error.message);
    } finally {
      // 에러 발생 여부와 상관없이 항상 실행
      cleanup();
    }
    ```
    

.