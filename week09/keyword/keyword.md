- OAuth 2.0
    
    사용자가 비밀번호를 직접 넘기지 않고도, 타 사이트의 인증을 이용해 권한을 위임받는 표준 프로토콜(비밀번호 대신 토큰으로 권한을 위임하는 로그인/인증 표준)
    
    특징
    
    - AccessToken을 발급받아 API 접근
    - 소셜 로그인(구글 로그인, 카카오 로그인) 등 다양한 서비스와 연동 쉬움
    - 다양한 Grant Type 존재(Authorization Code, Client Credentials 등)
- JWT
    
    사용자 정보를 JSON 형태로 인코딩하여 전달하는 자체 서명 토큰
    
    보통 AccessToken으로 사용됨
    
    특징
    
    - Header + Payload + Signature 구조
    - 서버가 상태(Session)를 저장할 필요x
- Bearer Token
    
    이 토큰을 가진 사람은 누구든 접근 가능하다는 방식의 단순한 인증 토큰
    
    HTTP 헤더에 `Authorization: Bearer <token>` 으로 전송