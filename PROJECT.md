## Project
Roblox Studio를 이용한 TPS 게임 제작 (CityHunter)

이 문서는 CityHunter의 공통 게임 룰과 구조의 기준 문서이며, 구현 변경 시 함께 갱신한다.

작업의 대부분은 Roblox Studio에서 진행한다. 이 저장소에는 `.rbxl` 파일과 프로젝트 문서를 두며, Roblox Studio 내부 스크립트가 별도 소스 파일로 export되어 있지 않을 수 있다.

---

## 게임 흐름
- **Lobby (60초)** -> **City (180초)** 무한 반복
- Lobby에서 모든 플레이어가 Ready Zone(중앙 반경 13스터드)에 3초 이상 머물면 즉시 출발
- City 스테이지 종료 후 다시 Lobby로 복귀, 매치 점수 초기화

---

## 서비스 구조

### ServerScriptService
| 스크립트 | 종류 | 역할 |
|---|---|---|
| `GunServer` | Script | 서버 사이드 총기 히트 판정, 데미지, 점수 처리 |
| `MapManager` | Script | 게임 루프, 맵 로드/언로드, 텔레포트, 총 지급 |
| `DummyManager` | Script | City 맵 더미 NPC 관리 (스폰·리스폰·HP바) |
| `RankingService` | ModuleScript | DataStore 기반 일간/주간/월간 랭킹 |
| `LobbyRankingDisplay` | ModuleScript | Lobby에 물리 랭킹 보드·포디움 생성 |
| `Server` | Script | (미구현 플레이스홀더) |

### StarterPlayer / StarterPlayerScripts
| 스크립트 | 역할 |
|---|---|
| `TPSCamera` | 3인칭 숄더 카메라, 줌, 반동, 충돌 감지 |
| `DeathCountdownClient` | 사망 시 5초 카운트다운 UI |
| `DisableDefaultMobileJumpButton` | 모바일 기본 점프 버튼 비활성화 |
| `MapClientReadyReporter` | 맵 로드 완료를 서버에 신호 |
| `Client` | (미구현 플레이스홀더) |

### StarterCharacterScripts
| 스크립트 | 역할 |
|---|---|
| `StaminaSystem` | 스태미너, 스프린트, 더블점프, 스태미너 HUD |

### ReplicatedStorage
```
GunRemotes/
  Fire          -- RemoteEvent: 클라 -> 서버 발사
  Hit           -- RemoteEvent: 서버 -> 클라 피격 확인
  ScoreUpdate   -- RemoteEvent: 랭킹 브로드캐스트
  Trail         -- RemoteEvent: 총알 궤적 시각화
  GetDummyInfo  -- RemoteFunction
LobbyRemotes/
  LobbyUpdate       -- 로비 타이머/레디 현황
  MapChanged        -- 맵 전환 알림 (mapName, serial)
  StageUpdate       -- 스테이지 남은 시간
  MapClientReady    -- 클라 로드 완료 핸드셰이크 (동적 생성)
  DeathCountdown    -- 리스폰 카운트다운 (동적 생성)
CurrentMap          -- StringValue: 현재 맵 이름
Assets/models, Assets/sounds
```

### StarterGui
- `LobbyUI` (ScreenGui): 타이머, 레디 버튼, 상단 카운트다운 프레임

### ServerStorage (런타임 참조)
- `Maps/Lobby` - 로비 맵 템플릿
- `Maps/City` - 시티 맵 템플릿 (Dummies 폴더 포함)
- `StarterPack/Gun` - 총기 Tool 템플릿

---

## 핵심 수치

### GunServer
| 항목 | 값 |
|---|---|
| 더미 바디/헤드샷 데미지 | 25 / 50 |
| PvP 바디/헤드샷 데미지 | 5 / 10 |
| 발사 쿨다운 | 0.09초 |
| 최대 사거리 | 500스터드 |
| 조준 허용 오차 | 12스터드 |
| 클라 총구 위치 허용 거리 | 12스터드 (머리 기준 18) |
| 카메라 위치 허용 거리 | 30스터드 |

### TPSCamera
| 항목 | 값 |
|---|---|
| 카메라 거리 (일반/줌) | 8 / 4.5 |
| 카메라 높이 | 2.5 |
| 카메라 옆 오프셋 | 2.35 (줌: 1.55) |
| FOV (일반/줌) | 70 / 42 |
| 마우스 감도 | 0.003 |
| 터치 감도 | 0.004 |

### StaminaSystem
| 항목 | 값 |
|---|---|
| 걷기 속도 | 32 |
| 달리기 속도 | 64 |
| 스태미너 최대 | 100 |
| 달릴 때 소모 | 18/초 |
| 이동 중 회복 | 12/초 |
| 정지 시 회복 | 20/초 |
| 완전 소진 후 대기 | 1.5초 |
| 최대 점프 횟수 | 2 (더블점프) |

### MapManager
| 항목 | 값 |
|---|---|
| 로비 시간 | 60초 |
| 스테이지 시간 | 180초 |
| Ready Zone 반경 | 13스터드 |
| Ready Zone 체류 시간 | 3초 |
| 리스폰 시간 | 5초 |
| 맵 전환 홀드 위치 | Y=35 (공중 대기) |

### RankingService
| 항목 | 값 |
|---|---|
| 랭킹 기간 | 일간 / 주간 / 월간 |
| 리셋 기준 시간대 | KST (UTC+9) |
| DataStore prefix | `GunRanking_v1` |
| OrderedDataStore 사용 | 예 |
| 상위 표시 인원 | 6명 |
| DataStore 불가 시 | 세션 내 메모리 폴백 |

---

## 더미 NPC (DummyManager)
- City 맵의 `Dummies` 폴더 내 Model 감시
- 사망 시 1초 후 새 랜덤 위치에 리스폰
- 스폰 위치: 6x6 블록 그리드, 건물과 겹치지 않도록 후보 풀 구성
- 더미 간 최소 거리: 20스터드
- HP 100, 머리 위 HP바 표시 (BillboardGui)
- 기여도 비례 점수 분배: 데미지 비율 x 더미 포인트(4~10)

---

## 서버 사이드 히트 판정 흐름
1. 클라이언트가 `Fire` 이벤트로 `(clientHitPos, direction, cameraOrigin, clientMuzzlePos)` 전송
2. 서버가 발사 쿨다운 검증
3. 클라 총구 위치 검증 (서버 총구와 오차 허용 범위 확인)
4. 클라 조준점이 카메라 시야에서 도달 가능한지 Raycast 검증
5. 서버 Raycast로 최종 히트 결정
6. `Trail` 이벤트로 모든 클라에 총알 궤적 복제
7. 피격 대상이 더미/플레이어에 따라 데미지 분기

---

## 맵 전환 동기화 절차
1. 서버가 `MapChanged(mapName, serial)` 브로드캐스트
2. 서버가 캐릭터를 Y=35 공중에 고정(Anchored)
3. 클라이언트가 맵 로드 완료 후 `MapClientReady(mapName, serial)` 전송
4. 서버가 확인 후 캐릭터 Anchored 해제
5. 타임아웃 4초 (미확인 시 강제 해제)

---

## 로비 물리 랭킹 보드
- Lobby 맵에 Daily / Weekly / Monthly 3개 보드
- 각 보드에 SurfaceGui 리더보드 + 1·2·3위 포디움
- 로비 진입 시 `LobbyRankingDisplay.Update(lobbyMap, payload)` 호출로 생성

---

## 라이팅
- **Lobby**: ClockTime 12, Brightness 2.2, 따뜻한 낮 조명
- **Stage**: 스테이지 진행 시간에 따라 ClockTime 6 -> 6+24 (시간 흐름 시뮬레이션)
  - 일출·일몰 시 황금빛 색조 자동 적용
