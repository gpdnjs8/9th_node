## 특정 지역에 가게 추가하기 API

**Controller → Service → Repository → DB로 이어지는 요청 흐름**

1. 사용자 → Controller
    
    `POST /api/v1/stores` 요청을 보냄
    
    Controller는 요청 받고, Service에 데이터 전달 후 결과를 응답으로 반환
    
2. Controller → Service
    
    Service는 로직 담당
    
    ```jsx
    import { insertStore, getStoreById } from "../repositories/store.repository.js";
    
    export const addStore = async (data) => {
      const storeId = await insertStore(data);       // DB에 저장
      const store = await getStoreById(storeId);    // 저장 후 데이터 조회
      return store;
    };
    ```
    
3. Service → Repository
    
    Repository는 SQL 실행
    
    ```jsx
    SELECT MAX(id) as maxId FROM store;   // 1
    
    INSERT INTO store 
    (id, region_id, name, number, thumbnail, work_time, region, address) // 2
    VALUES (?, ?, ?, ?, ?, ?, ?, ?);
    
    SELECT * FROM store WHERE id = ?;  // 3
    ```
    
    - id: AUTO_INCREMENT를 사용하지 않아서, id 계산 필요(MAX(id)+1)
    - INSERT: DB에 실제 값 추가
    - SELECT: 추가한 가게 정보 조회, id 기준으로 조회
4. Repository → DB
    
    실제 데이터 저장/조회
    

**결과**

![Image](https://github.com/user-attachments/assets/df4839e0-660e-44de-b1bb-3dfafa5658c3)

![스크린샷(184).png](https://github.com/user-attachments/assets/ab62575d-a177-445e-a05d-330f3392a225)

## 가게에 리뷰 추가하기 API

- ~~리뷰를 추가하려는 가게가 존재하는지 검증이 필요합니다.~~

**Controller → Service → Repository → DB로 이어지는 요청 흐름**

1. 사용자 → Controller
    
    `POST /api/v1/reviews` 요청을 보냄
    
    Controller는 요청 받고, Service에 데이터 전달 후 결과를 응답으로 반환
    
2. Controller → Service
    
    가게 존재 여부 검증, Repository 호출, 결과 반환
    
    ```jsx
    import { insertReview, getStoreById, getReviewById } from "../repositories/review.repository.js";
    
    export const addReview = async (data) => {
      // 리뷰를 추가할 가게가 존재하는지 확인
      const store = await getStoreById(data.storeId);
      if (!store) throw new Error("해당 가게가 존재하지 않습니다.");
    
      // 가게가 존재하면 리뷰 추가
      const reviewId = await insertReview(data);
    
      // 추가한 리뷰 조회
      return await getReviewById(reviewId);
    };
    ```
    
    - getStoreById(storeId)를 호출하여 DB에서 가게 존재 여부 확인
    - 존재하지 않으면 throw해서 Controller가 에러 메시지를 클라이언트로 반환
    - 존재하면 insertReview를 호출하여 DB에 저장
3. Service → Repository
    
    Repository는 SQL 실행
    
    ```jsx
    SELECT * FROM store WHERE id = ?;  // 가게 존재 여부 확인
    
    INSERT INTO review (user_id, store_id, content, star, imageUrl, created_at)
    VALUES (?, ?, ?, ?, ?, NOW());  // 새로운 리뷰 추가
    
    SELECT * FROM review WHERE id = ?;  // 추가한 리뷰 조회
    ```
    
4. Repository → DB
    
    실제 데이터 저장/조회
    

**결과**

![스크린샷(188).png](https://github.com/user-attachments/assets/b0d9919d-2652-40e9-a1dd-8b19fa99c4f0)

![스크린샷(190).png](https://github.com/user-attachments/assets/b44f6dbb-7293-49ce-b054-d71c1f9452e9)

존재하지 않는 가게에 리뷰를 추가하려는 경우, 오류 메시지 반환

![스크린샷(191).png](https://github.com/user-attachments/assets/c4b1ed4b-ea93-4c97-9bf5-9c48b7a54c48)

## 가게에 미션 추가하기 API

**Controller → Service → Repository → DB로 이어지는 요청 흐름**

1. 사용자 → Controller
    
    `POST /api/v1/missions` 요청을 보냄
    
    Controller는 요청 받고, Service에 데이터 전달 후 결과를 응답으로 반환
    
2. Controller → Service
    
    가게 존재 여부 검증, Repository 호출, 결과 반환
    
    - getStoreById(storeId)를 호출하여 DB에서 가게 존재 여부 확인
    - 존재하지 않으면 throw해서 Controller가 에러 메시지를 클라이언트로 반환
    - 존재하면 insertReview를 호출하여 DB에 저장
3. Service → Repository
    
    status 컬럼 초기값 pending → 미션 진행 전 상태
    
    진행 중 → ongoing, 완료 → completed
    
    ```jsx
    SELECT * FROM store WHERE id = ?;           // 가게 존재 여부 확인
    
    INSERT INTO mission 
    (id, store_id, status, content, deadline, point, created_at)
    VALUES (?, ?, 'pending', ?, ?, ?, NOW());  // 새로운 미션 추가
    
    SELECT * FROM mission WHERE id = ?;        // 추가한 미션 조회
    ```
    
4. Repository → DB
    
    실제 데이터 저장/조회
    

**결과**

![스크린샷(192).png](https://github.com/user-attachments/assets/8e67f701-c6da-4474-864e-3c913f20fd11)

![스크린샷(195).png](https://github.com/user-attachments/assets/f3a9d494-4db3-4098-8a33-9ef69f131d9b)

존재하지 않는 가게에 미션을 추가하려는 경우, 오류 메시지 반환

![스크린샷(200).png](https://github.com/user-attachments/assets/c17e8ce8-e335-4fea-a65d-09ae8a3509a5)

## 가게의 미션을 도전 중인 미션에 추가 API

- ~~도전하려는 미션이 이미 도전 중이지는 않은지 검증이 필요합니다.~~
- `status` 컬럼은 `true`로 세팅 → 진행 중인 미션 표시

**Controller → Service → Repository → DB로 이어지는 요청 흐름**

1. 사용자 → Controller
    
    `POST /api/v1/users/missions` 요청을 보냄
    
    Controller는 요청 받고, Service에 데이터 전달 후 결과를 응답으로 반환
    
2. Controller → Service
    
    ```jsx
    import { insertUserMission, getUserMissionByUserIdAndMissionId } from "../repositories/userMission.repository.js";
    
    export const addUserMission = async (data) => {
      // 이미 도전 중인지 확인
      const existing = await getUserMissionByUserIdAndMissionId(data.userId, data.missionId);
      if (existing) throw new Error("이미 도전 중인 미션입니다.");
    
      // 진행 중 상태로 추가
      const userMissionId = await insertUserMission(data);
      return await getUserMissionById(userMissionId);
    };
    ```
    
    `status` 컬럼:
    
    - 진행 중인 미션 → `true` (DB에는 `1`)
    - 완료된 미션 → `false` 또는 완료 시점에 업데이트
3. Service → Repository
    
    ```jsx
    SELECT * FROM user_mission 
    WHERE user_id = ? AND mission_id = ?;        -- 이미 도전 중인지 확인
    
    INSERT INTO user_mission 
    (user_id, mission_id, status, created_at) 
    VALUES (?, ?, true, NOW());                  -- 새로운 도전 중인 미션 추가
    
    SELECT * FROM user_mission WHERE id = ?;    -- 추가한 도전 중인 미션 조회
    ```
    
4. Repository → DB
    
    실제 데이터 저장/조회
    

**결과**

![스크린샷(196).png](https://github.com/user-attachments/assets/cf2be717-eb7f-4360-bb52-206dc457c9f8)

![스크린샷(198).png](https://github.com/user-attachments/assets/0e6f4d06-69ef-4cd5-ba1d-8f628f0c99e7)

이미 도전 중인 미션인 경우

![스크린샷(201).png](https://github.com/user-attachments/assets/01b9159d-b863-4bb8-ab9b-22873575958b)

