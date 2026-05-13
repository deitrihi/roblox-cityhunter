# CityHunter

Roblox Studio로 제작한 3인칭 슈팅(TPS) 게임입니다.

---

## 게임 개요

도시 맵에서 더미 NPC와 다른 플레이어를 사격해 점수를 쌓는 멀티플레이 TPS 게임입니다.  
로비와 시티 스테이지가 무한 반복되며, 랭킹은 일간 / 주간 / 월간으로 집계됩니다.

---

## 게임 흐름

```
Lobby (60초) → City Stage (180초) → Lobby → ...
```

- 로비에서 모든 플레이어가 중앙 Ready Zone(반경 13스터드)에 3초 이상 머물면 즉시 스테이지 시작
- 스테이지 종료 후 로비로 복귀, 매치 점수 초기화

---

## 조작 방법

| 동작 | 입력 |
|---|---|
| 발사 | 마우스 좌클릭 |
| 줌 | 마우스 우클릭 |
| 달리기 | Shift |
| 재장전 | R |
| 점프 | Space |
| 더블점프 | Space × 2 |

모바일에서는 화면 내 가상 버튼으로 동일한 조작이 가능합니다.

---

## 핵심 수치

### 총기 (GunServer)
| 항목 | 값 |
|---|---|
| 더미 바디샷 / 헤드샷 | 25 / 50 |
| PvP 바디샷 / 헤드샷 | 5 / 10 |
| 발사 쿨다운 | 0.09초 |
| 최대 사거리 | 500스터드 |

### 이동 (StaminaSystem)
| 항목 | 값 |
|---|---|
| 걷기 속도 | 32 |
| 대시 수평 속도 | 86 |
| 대시 지속 시간 | 0.28초 |
| 스태미너 최대 | 100 |
| 최대 점프 횟수 | 2 (더블점프) |

### 카메라 (TPSCamera)
| 항목 | 값 |
|---|---|
| 카메라 거리 (일반 / 줌) | 8 / 4.5 |
| FOV (일반 / 줌) | 70° / 42° |
| 마우스 감도 | 0.003 |

---

## 더미 NPC

- City 맵에 자동 스폰, 사망 1초 후 랜덤 위치 리스폰
- HP 100, 머리 위 HP바 표시
- 스폰 위치 반경 28스터드 내 로밍 (속도 9스터드/초)
- 데미지 기여도에 비례한 점수 분배 (더미 포인트 4~10)

---

## 랭킹 시스템

- **기간:** 일간 / 주간 / 월간 (KST 기준 리셋)
- **DataStore:** `GunRanking_v1` prefix, OrderedDataStore 사용
- **표시:** 로비 물리 랭킹 보드(Daily / Weekly / Monthly) + 1~3위 포디움
- DataStore 불가 시 세션 내 메모리 폴백

---

## 서비스 구조

### ServerScriptService
| 스크립트 | 역할 |
|---|---|
| `GunServer` | 서버 사이드 총기 히트 판정, 데미지, 점수 처리 |
| `MapManager` | 게임 루프, 맵 로드/언로드, 텔레포트, 총 지급 |
| `DummyManager` | City 맵 더미 NPC 스폰 · 리스폰 · HP바 관리 |
| `RankingService` | DataStore 기반 일간/주간/월간 랭킹 |
| `LobbyRankingDisplay` | 로비 물리 랭킹 보드 · 포디움 생성 |

### StarterPlayerScripts
| 스크립트 | 역할 |
|---|---|
| `TPSCamera` | 3인칭 숄더 카메라, 줌, 반동, 충돌 감지 |
| `DeathCountdownClient` | 사망 시 5초 카운트다운 UI |
| `UILocalizer` | 화면 문구 로케일 변환 (ko / en) |
| `MapClientReadyReporter` | 맵 로드 완료 신호 송신 |

### StarterCharacterScripts
| 스크립트 | 역할 |
|---|---|
| `StaminaSystem` | 스태미너, 스프린트, 더블점프, 스태미너 HUD |

---

## 저장소 구성

```
cityhunter/
├── cityhunter.rbxl   # Roblox Studio 프로젝트 파일
├── PROJECT.md        # 게임 룰·구조 기준 문서
└── README.md
```

스크립트 대부분은 `.rbxl` 파일 내부에 포함되어 있으며, Roblox Studio에서 열어서 확인합니다.

---

## 개발 환경

- **엔진:** Roblox Studio
- **언어:** Luau
- **로케일:** 한국어(ko) / 영어(en) 지원
