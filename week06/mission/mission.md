# 미션 기록

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
