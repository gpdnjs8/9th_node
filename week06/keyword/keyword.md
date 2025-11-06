- ORM
    
    SQL을 직접 작성하지 않고도 DB를 조작할 수 있게 해주는 기술
    
    DB 독립성, 타입 안정성
    
- Prisma 문서 살펴보기
    - ex. Prisma의 Connection Pool 관리 방법
        
        Prisma는 내부적으로 DB 드라이버의 connection pool을 사용함
        
        직접 관리하지 않아도 되지만, `pool_timeout`, `connection_limit`, `timeout` 같은 옵션을 환경 변수(`DATABASE_URL`)로 조정 가능
        
        ```bash
        DATABASE_URL="postgresql://user:password@localhost:5432/db?connection_limit=5&pool_timeout=10"
        ```
        
        Prisma Client는 각 요청마다 새로운 연결을 만들지 않고, 연결 풀에서 연결을 재사용하여 효율성 유지
        
    - ex. Prisma의 Migration 관리 방법
        
        Prisma Migrate 명령어를 통해 스키마 변경을 추적하고 자동으로 SQL 마이그레이션 생성
        
        ```bash
        npx prisma migrate dev --name add-user-table
        ```
        
        `prisma/migrations` 폴더에 SQL 파일이 생성되어, DB 스키마 버전을 관리할 수 있음
        
- ORM(Prisma)을 사용하여 좋은 점과 나쁜 점
    
    장점
    
    - 마이그레이션 자동 관리
    - 쿼리 빌더보다 단순하고 직관적
    - 타입 안정성
    
    단점
    
    - 복잡한 SQL 쿼리는 직접 작성해야 함(집계, 서브쿼리 등)
    - Prisma 전용 스키마 파일(schema.prisma)을 유지해야
- 다양한 ORM 라이브러리 살펴보기
    
    
    | 언어 | ORM 라이브러리 | 특징 |
    | --- | --- | --- |
    | JavaScript / TypeScript | **Sequelize** | JS에서 가장 오래된 ORM, 유연하지만 타입 안전성 낮음 |
    |  | **TypeORM** | 데코레이터 기반, 클래스 중심 ORM |
    |  | **Prisma** | 스키마 기반, 타입 안전성 최고, 최근 가장 인기 많음 |
    | Python | **SQLAlchemy** | 강력한 ORM + 세밀한 쿼리 제어 가능 |
    | Java | **Hibernate** | JPA 구현체, 대규모 엔터프라이즈 환경에 적합 |
    | Ruby | **ActiveRecord** | Rails의 기본 ORM, 직관적인 문법 |
- 페이지네이션을 사용하는 다른 API 찾아보기
    - ex. https://docs.github.com/en/rest/using-the-rest-api/using-pagination-in-the-rest-api?apiVersion=2022-11-28
        
        결과를 페이지 단위로 나누어 일부만 반환
        
        기본적으로 한 API 호출당 30개 항목 반환
        
        `per_page` 매개변수로 페이지당 최대 100개 항목까지 설정 가능
        
        동작 방식
        
        - 응답 헤더에 `link` 필드 포함
        - `link`에는 다음 페이지를 요청할 수 있는 URL이 들어 있음
            - `rel="next"` → 다음 페이지
            - `rel="prev"` → 이전 페이지
            - `rel="first"` → 첫 페이지
            - `rel="last"` → 마지막 페이지
    - ex. https://developers.notion.com/reference/intro#pagination
        
        커서 기반 페이지네이션
        
        기본적으로 한 API 호출당 10개 항목 반환
        
        `page_size`로 최대 100개까지 설정 가능
        
        요청 흐름
        
        - 처음 요청 → `page_size` 지정
        - 응답의 `has_more` 확인
        - `has_more: true` → `next_cursor` 사용해 다음 페이지 요청
        - `has_more: false` → 마지막 페이지 도달
    - https://developer.spotify.com/documentation/web-api/concepts/api-calls?utm_source=chatgpt.com
        
        offset 기반 페이지네이션
        
        offset(시작 위치)과 limit(가져올 항목 수) 파라미터 사용
        
        offset: 데이터를 불러올 시작 인덱스
        
        limit: 한 번에 가져올 최대 항목 수, 기본값 20, 최대 50
        
