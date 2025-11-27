
## 사용자 정보 관련하여 수정

### 내가 작성한 리뷰 목록 조회 API, 내가 진행 중인 미션 목록 조회 API

path parameter (:userId) 제거

- 로그인한 사용자의 ID는 이미 isLogin 미들웨어를 통해 req.user에 담겨 있기 때문

controller 수정

- 기존
    
    ```jsx
    export const handleListUserReviews = async (req, res, next) => {
     const reviews = await listUserReviews( parseInt(req.params.userId), typeof req.query.cursor === "string" ? parseInt(req.query.cursor) : 0 ); 
     res.status(StatusCodes.OK).success(reviews); }
    };
    ```
    
- 수정
    
    로그인한 사용자 id(req.user.id) 사용
    
    ```jsx
    export const handleListUserReviews = async (req, res, next) => {
      try {
        const userId = req.user.id; 
        const cursor = typeof req.query.cursor === "string" ? parseInt(req.query.cursor) : 0;
    
        const reviews = await listUserReviews(userId, cursor);
    
        res.status(StatusCodes.OK).success(reviews);
      } catch (err) {
        next(err);
      }
    };
    ```
    

### 리뷰 추가 API, 가게의 미션을 도전 중인 미션에 추가 API

controller 수정

- 기존
    
    ```jsx
    try {
      const dto = createUserMissionDto(req.body);
      const userMission = await addUserMission(dto);
      res.status(StatusCodes.CREATED).success(userMission);
    } catch (err) {
      next(err);
    }
    ```
    
- 수정
    
    로그인한 사용자 id 사용
    
    ```jsx
    try {
      const dto = {
        ...createUserMissionDto(req.body),
        userId: req.user.id, 
      };
    
      const userMission = await addUserMission(dto);
      res.status(StatusCodes.CREATED).success(userMission);
    } catch (err) {
      next(err);
    }
    ```
    

---

## 내 정보 수정 API

`PATCH /api/v1/users/my`

![스크린샷(293).png](https://github.com/user-attachments/assets/d642ef5b-5cad-4a08-880e-6dc80a268548)

성공 응답, 실패 응답(커스텀에러: 401로 처리)

![스크린샷(283).png](https://github.com/user-attachments/assets/2b98af66-8e6a-44ea-9f17-609782aebea8)

![스크린샷(284).png](https://github.com/user-attachments/assets/066b4c3d-00e1-49a7-b522-6c04cc8a5de2)

실행 결과

![스크린샷(282).png](https://github.com/user-attachments/assets/09178d86-8a60-4658-9a80-2fc00c952699)

![스크린샷(295).png](https://github.com/user-attachments/assets/9ee553ab-946d-499a-8185-09346e3e1119)

Postman GET /mypage 실행

내 정보 수정 API로 수정한 값으로 바뀐 것을 확인할 수 있다. 

---

## 기존 API에 JWT 적용하여 로그인한 사용자만 쓸 수 있도록 보호

### 미들웨어를 라우터에 적용

```jsx
app.post("/api/v1/stores", isLogin, handleAddStore);
...
```

- isLogin 미들웨어를 로그인이 필요한 API 라우터에 적용 (`index.js`)

### swagger

![스크린샷(285).png](https://github.com/user-attachments/assets/89a4415d-92b4-436f-8f70-1d321247c37c)

```jsx
const doc = {
    info: {
      title: "UMC 9th",
      description: "UMC 9th Node.js 테스트 프로젝트입니다.",
    },
    host: "localhost:3000",
    components: {
	    securitySchemes: {
	      BearerAuth: {
	        type: "http",
	        scheme: "bearer",
	        bearerFormat: "JWT",
	      },
	    },
    },
  };
```

- 기존 doc 객체에 `components.securitySchemes` 추가 (`index.js`)
- 로그인이 필요한 API 주석에 `#swagger.security = [{ "BearerAuth": [] }]`  추가 (`controller.js`)