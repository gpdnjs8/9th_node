# 미션 기록

`prisma/schema.prisma`

```jsx
model User {
  id            Int      @id @default(autoincrement())
  email         String   @unique(map: "email") @db.VarChar(255)
  name          String   @db.VarChar(100)
  gender        String   @db.VarChar(15)
  birth         DateTime @db.Date
  address       String   @db.VarChar(255)
  detailAddress String?  @map("detail_address") @db.VarChar(255)
  phoneNumber   String   @map("phone_number") @db.VarChar(15)

  userFavorCategories UserFavorCategory[]
  reviews             UserStoreReview[] 
  userMissions        UserMission[]

  @@map("user")
}

model FoodCategory {
  id   Int    @id @default(autoincrement())
  name String @db.VarChar(100)

  userFavorCategories UserFavorCategory[]

  @@map("food_category")
}

model UserFavorCategory {
  id             Int          @id @default(autoincrement())
  user           User         @relation(fields: [userId], references: [id])
  userId         Int          @map("user_id")
  foodCategory   FoodCategory @relation(fields: [foodCategoryId], references: [id])
  foodCategoryId Int          @map("food_category_id")

  @@index([foodCategoryId], map: "f_category_id")
  @@index([userId], map: "user_id")
  @@map("user_favor_category")
}

model Region {
  id     Int     @id @default(autoincrement())
  name   String  @db.VarChar(100)
  stores Store[]

  @@map("region")
}

model Store {
  id        Int               @id @default(autoincrement())
  name      String            @db.VarChar(100)
  number    String?           @db.VarChar(50)
  thumbnail String?           @db.VarChar(255)
  work_time String?           @db.VarChar(255)
  address   String?           @db.VarChar(255)

  region_id Int
  region    Region            @relation(fields: [region_id], references: [id])

  reviews   UserStoreReview[]
  missions   Mission[]
  @@map("store")
}

model UserStoreReview {
  id        Int    @id @default(autoincrement())
  store     Store  @relation(fields: [storeId], references: [id])
  storeId   Int    @map("store_id")
  user      User   @relation(fields: [userId], references: [id])
  userId    Int    @map("user_id")
  content   String @db.Text

  star      Int?     @db.TinyInt
  imageUrl  String?  @db.VarChar(255)
  createdAt DateTime @default(now()) @map("created_at")

  @@map("user_store_review")
}

model Mission {
  id         Int      @id @default(autoincrement())
  store      Store    @relation(fields: [storeId], references: [id])
  storeId    Int      @map("store_id")
  status     String   @default("pending") @db.VarChar(50)
  content    String   @db.Text
  deadline   DateTime
  point      Int      @default(0)
  createdAt  DateTime @default(now()) @map("created_at")
  updatedAt  DateTime @updatedAt @map("updated_at")

  userMissions        UserMission[]
  @@map("mission")
}

enum UserMissionStatus {
  pending      // 진행 전
  in_progress  // 진행 중
  completed    // 완료
}

model UserMission {
  id        Int               @id @default(autoincrement())
  user      User              @relation(fields: [userId], references: [id])
  userId    Int               @map("user_id")
  mission   Mission           @relation(fields: [missionId], references: [id])
  missionId Int               @map("mission_id")
  status    UserMissionStatus @default(pending)
  createdAt DateTime          @default(now()) @map("created_at")

  @@map("user_mission")
  @@unique([userId, missionId]) 
}
```

- id: autoincrement로 변경
- usermission status: boolean → enum 변경
    
    
    | enum 값 | 의미 |
    | --- | --- |
    | `pending` | 진행 전 |
    | `in_progress` | 진행 중 |
    | `completed` | 완료 |

**커서 기반 페이지네이션 사용**

**클라이언트 요청 → 컨트롤러 → 서비스 → 레포지토리 → dto**


### 내가 작성한 리뷰 목록 조회

`GET /api/v1/users/:userId/reviews`

1. 클라이언트 요청
2. 컨트롤러:
    - `userId`와 `cursor` 추출
    - 서비스 호출
3. 서비스:
    - 레포지토리 호출 (`getAllUserReviews`)
    - DTO 적용 (`responseFromReviews`)
4. Repository:
    - Prisma 쿼리 수행
        
        ```bash
        export const getAllUserReviews = async (userId, cursor = 0) => {
          const reviews = await prisma.userStoreReview.findMany({
            select: {
              id: true,
              content: true,
              star: true,
              imageUrl: true,
              createdAt: true,
              storeId: true,
              store: { select: { id: true, name: true, thumbnail: true } },
            },
            where: { userId: userId, id: { gt: cursor } }, 
            orderBy: { id: "asc" },
            take: 5, 
          });
        
          return reviews;
        };
        ```
        
    - take: 5로 설정, cursor: 마지막으로 가져온 id 기준으로 다음 데이터 가져오기, userId: 특정 사용자의 리뷰만 조회
    - DB에서 리뷰 데이터 반환
5. 컨트롤러:
    - 최종 JSON 응답 전송
    - `pagination.cursor` 포함 → 다음 페이지 요청 가능

![스크린샷(231).png](https://github.com/user-attachments/assets/54ac6f12-2ba4-472f-9156-11b00a21449c)

![스크린샷(232).png](https://github.com/user-attachments/assets/a3fb1de0-0d04-44c5-ae3d-0473a5649856)

### 특정 가게의 미션 목록 조회

`GET /api/v1/stores/:storeId/missions`

![스크린샷(227).png](https://github.com/user-attachments/assets/927309a7-2711-4720-b198-3b7f4e7f1e74)

![스크린샷(228).png](https://github.com/user-attachments/assets/f810329d-69c7-4371-9657-c429c2155e2a)

![스크린샷(229).png](https://github.com/user-attachments/assets/c693b012-101c-4171-b400-a8d1c021be82)

### 내가 진행 중인 미션 목록 조회

`GET /api/v1/users/:userId/missions/inprogress`

![스크린샷(230).png](https://github.com/user-attachments/assets/0b8770c9-dcd1-4c7c-ba7b-686ffe239ff2)
