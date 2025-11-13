## 미션 기록

### 성공 응답 및 에러 처리

```jsx
export const handleAddUserMission = async (req, res, next) => {
  try {
    const dto = createUserMissionDto(req.body);
    const userMission = await addUserMission(dto);
    res.status(StatusCodes.CREATED).success(userMission);
  } catch (err) {
    next(err);
  }
};

export const handleListUserMissionsInProgress = async (req, res) => {
  const usermissions = await listUserMissionsInProgress(
    parseInt(req.params.userId),
    typeof req.query.cursor === "string" ? parseInt(req.query.cursor) : 0
  );
  res.status(StatusCodes.OK).success(usermissions);
};

```

POST api (생성)

- 성공 시 201(CREATED)
- 에러 발생 시 catch(err)로 들어옴
- catch 블록에서 next(err) 호출 시 Express가 전역 에러 핸들러로 이동(app.use)
- 전역 에러 핸들러에서는 res.error(…)를 통해 클라이언트에 최종 JSON 응답 반환

GET api (조회)

- 성공 시 200(OK)

응답 형식

- 성공 응답: `res.success(data)`
- 실패 응답: `res.error({ errorCode, reason, data })`

---

서비스 내부 동작

```jsx
export const addUserMission = async (data) => {
  const mission = await getMissionById(data.missionId);
  if (!mission) throw new MissionError("해당 미션이 존재하지 않습니다.", data);

  const existing = await getUserMission(data.userId, data.missionId);
  if (existing) throw new DuplicateMissionError("이미 도전 중인 미션입니다.", data);

  const userMissionId = await insertUserMission(data);

  const userMission = await getUserMissionById(userMissionId);
  return userMission;
};
```

각 서비스 내부에서 에러 발생 시 throw new CustomError() 형태로 커스텀 에러를 던짐

에러 발생 시 호출한 컨트롤러의 try-catch 블록으로 전달됨 → next(err) 호출

커스텀 에러

```jsx
export class DuplicateMissionError extends Error {
  errorCode = "M002";

  constructor(reason, data) {
    super(reason);
    this.reason = reason;
    this.data = data;
  }
}
```

.