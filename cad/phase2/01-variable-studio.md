## 2. Variable Studio — 글로벌 변수

### 2.1 Variable Studio 탭 만들기

1. 문서 하단 탭바 `+` → **Variable Studio** 선택
2. 이름: `Clock Config`

### 2.2 변수 입력 방법

각 변수는 UI 폼에서 **Name · Type · Value · Description** 네 칸을 채워 입력 (코드 아님).
- **Name 칸에는 `#` 를 넣지 않음** — `capThickness` 라고만 입력
- **Type 칸**은 드롭다운에서 **Length** (길이) 또는 **Number** (단위 없는 수) 선택
- **Value 칸**: 값과 단위 (`90 mm`, `10` 등). **`=` 를 쓰지 않는다**
- **Value 칸에서 다른 변수를 참조할 때는 `#` 필수** — 예: `2 * #capThickness + #drumVisibleHeight`
- Part Studio에서도 참조는 `#drumDiameter_90` 로

### 2.3 입력할 변수 목록

> 드럼 직경·면 수는 ∅90과 ∅60이 다르므로 **둘 다 별개 변수로** 등록.
>
> **🔒 D29 freeze**: `panelWidth_90` (25.032) / `panelWidth_60` (22.251) 는 외주 발주 완료로 **변경 금지**. 슬롯 길이는 패널에서 유도. drumDiameter·drumFaces 변경 시 패널이 폴리곤 변(chord)보다 작아야 함을 검증 (∅90: panel ≤ 27.812 chord ✓, ∅60: panel ≤ 26.033 chord ✓).

| Name | Type | Value | Description |
|---|---|---|---|
| `drumDiameter_90` | Length | `90 mm` | HH·MM 드럼 외경 |
| `drumDiameter_60` | Length | `60 mm` | 요일·날씨 드럼 외경 |
| `drumFaces_90` | **Number** | `10` | HH·MM 면 수 (0~9). Length 아님! |
| `drumFaces_60` | **Number** | `7` | 요일(7) / 날씨(6 + 공백) 면 수 |
| `drumVisibleHeight` | Length | `44 mm` | 가시 영역 높이 (∅90·∅60 공통) |
| `capThickness` | Length | `6 mm` | 캡 두께 (공통) |
| `drumOverallHeight` | Length | `2 * #capThickness + #drumVisibleHeight` | 자동 계산: 56 mm |
| `panelLength` | Length | `55 mm` | 상부 3 + 가시 44 + 하부 6 + 노출 2 |
| `panelThickness` | Length | `3 mm` | 아크릴 두께 |
| `apothem_90` | Length | `#drumDiameter_90 / 2 * cos(180 deg / #drumFaces_90)` | 자동 계산: 42.80 mm (변 중심 거리) |
| `apothem_60` | Length | `#drumDiameter_60 / 2 * cos(180 deg / #drumFaces_60)` | 자동 계산: 27.02 mm |
| `slotWidth` | Length | `#panelThickness + 0.2 mm` | 슬롯 radial 폭 = 3.2 mm (FDM 공차) |
| `panelWidth_90` | Length | `25.032 mm` | **🔒 Freeze (D29)** — 외주 발주 완료. 원래 유도식 (∅90, n=10): slot 비겹침 한계 `2·(apothem-slotWidth)·tan(180°/n) - 0.5` = 2·(42.798-3.2)·tan(18°) - 0.5 = 25.232, 패널은 그보다 0.2 mm 작음 → **25.032**. drumDiameter 변경되어도 이 값은 변하지 않음 (literal) |
| `panelWidth_60` | Length | `22.251 mm` | **🔒 Freeze (D29)** — 외주 발주 완료. 원래 유도식 (∅60, n=7): 2·(27.029-3.2)·tan(180°/7) - 0.5 = 22.451, 패널 = 슬롯 - 0.2 → **22.251** |
| `slotLength_90` | Length | `#panelWidth_90 + 0.2 mm` | 패널에서 유도: 25.232 mm (양쪽 0.1 clearance) |
| `slotLength_60` | Length | `#panelWidth_60 + 0.2 mm` | 패널에서 유도: 22.451 mm |
| `slotDepth_top` | Length | `3 mm` | 상부 캡 슬롯 깊이 (관통 X) |
| `slotDepth_bottom` | Length | `#capThickness` | 하부 캡 슬롯 깊이 = 6 mm (관통 O) |
| `hubBossHeight` | Length | `12 mm` | 캡 위로 돌출되는 허브 보스 높이 (M3 인서트 위·아래 4 mm 여유 확보, 1차 프린트 시 6 mm가 타이트해 12 mm로 상향) |
| `hubOuter_90` | Length | `20 mm` | ∅90 캡 허브 외경 |
| `hubOuter_60` | Length | `18 mm` | ∅60 캡 (D13, 인서트 OD5 수용) |
| `shaftHole` | Length | `5.2 mm` | 축 홀 (+0.2/-0) |
| `insertHole` | Length | `4.2 mm` | **히트 인서트 설치 홀** (보유 인서트 OD 5 mm용, OD-0.8 압입 여유). M3 set screw OD 3.0과 다름 |
| `magnetDiameter` | Length | `4.1 mm` | 자석 ∅4 + 0.1 여유 |
| `magnetDepth` | Length | `2.1 mm` | 자석 ∅4×2 + 0.1 여유 |
| `magnetRadius_90` | Length | `30 mm` | ∅90 드럼 중심 → 자석 중심 |
| `magnetRadius_60` | Length | `15 mm` | ∅60 드럼 |
| `baffleThickness` | Length | `2 mm` | 배플 두께 |
| `gapInner` | Length | `15 mm` | HH·MM 내부 간격 (D22) |
| `gapOuter` | Length | `20 mm` | 요일/날씨 ↔ 숫자 |
| `gapColon` | Length | `40 mm` | 콜론 |
| `shaftDiameter` | Length | `5 mm` | |
| `shaftLength` | Length | `100 mm` | D26 — 보스 12 mm 양쪽 적용 후 스택 97~99 mm. 75 mm 보유분 폐기, 100 mm 신규 발주 |
| `bearingOD` | Length | `16 mm` | 625ZZ 외경 |
| `bearingID` | Length | `5 mm` | |
| `bearingWidth` | Length | `5 mm` | |
| `couplerOD` | Length | `19 mm` | B02 보유 커플러 외경 (실측, 보유품 freeze) |
| `couplerLength` | Length | `25 mm` | B02 보유 커플러 길이 (실측). 내경은 `#shaftDiameter` 재사용 |
| `enclosureWidth` | Length | `620 mm` | 외부 폭 (D22 590 + 측벽·여유) |
| `enclosureDepth` | Length | `120 mm` | 외부 깊이 (드럼 95 + 여유) |
| `bottomBoxHeight` | Length | `80 mm` | 전자부 박스 높이 |
| `drumCompartmentHeight` | Length | `90 mm` | 드럼 영역 높이 (드럼 56 + 보스 12 + 베어링·여유) |
| `enclosureHeight` | Length | `#bottomBoxHeight + #drumCompartmentHeight + 2 * #plateThickness` | 자동 계산: ~180 |
| `plateThickness` | Length | `5 mm` | 상·하 판 두께 |
| `boxWallThickness` | Length | `3 mm` | 하부 박스 벽 두께 |
| `sidePanelThickness` | Length | `3 mm` | 측면 판 두께 |
| `panelGrooveWidth` | Length | `#sidePanelThickness + 0.4 mm` | 둘레 홈 폭 (3 + 0.4 clearance) |
| `panelGrooveDepth` | Length | `4 mm` | 둘레 홈 깊이 |
| `digitWindowWidth_90` | Length | `26 mm` | ∅90 디지트 창 폭 (panelWidth_90 + 1 margin) |
| `digitWindowWidth_60` | Length | `24 mm` | ∅60 디지트 창 폭 |
| `digitWindowHeight` | Length | `50 mm` | 디지트 창 높이 (drumVisibleHeight + 6 margin) |
| `drumX_1` | Length | `-265 mm` | 요일 드럼 (∅60) X 위치 |
| `drumX_2` | Length | `-170 mm` | 시십 (∅90) |
| `drumX_3` | Length | `-65 mm` | 시일 (∅90) |
| `drumX_4` | Length | `+65 mm` | 분십 (∅90) |
| `drumX_5` | Length | `+170 mm` | 분일 (∅90) |
| `drumX_6` | Length | `+265 mm` | 날씨 (∅60) |
| `splitX_1` | Length | `-103 mm` | 좌측 분할 위치 (베드 200mm 한계) |
| `splitX_2` | Length | `+103 mm` | 우측 분할 위치 |
| `splitTongueWidth` | Length | `8 mm` | 도브테일 tongue 폭 |
| `splitTongueDepth` | Length | `4 mm` | tongue 침투 깊이 |
| `dcJackDiameter` | Length | `8 mm` | DC 바렐잭 (M2.1) panel-mount 홀 |
| `dcJackX` | Length | `-200 mm` | DC잭 후면벽 X 위치 |
| `dcJackZ` | Length | `-#bottomBoxHeight / 2` | DC잭 Z 위치 (= -40, 후면벽 중앙) |
| `ventSlotWidth` | Length | `2 mm` | 통기 슬롯 두께 (Z 방향) |
| `ventSlotLength` | Length | `80 mm` | 통기 슬롯 길이 (X 방향) |
| `ventSlotCount` | **Number** | `10` | 통기 슬롯 개수 (linear pattern) |
| `ventSlotPitch` | Length | `6 mm` | 통기 슬롯 Z 간격 |
| `ventSlotZStart` | Length | `-#bottomBoxHeight + 15 mm` | 첫 슬롯 Z 중심 (= -65, 박스 바닥에서 15mm 위) |
| `rimBossWidth` | Length | `12 mm` | top edge 보강 보스 한 변 (X·Y 정사각) |
| `rimBossHeight` | Length | `12 mm` | 보스 -Z 돌출량 (벽 5 + cavity 7) |

### 2.4 Part Studio에서 변수 참조하기

각 Part Studio 맨 위에 **Variable Studio 피처**를 삽입하여 연결:

1. Part Studio 좌상단 `Feature ▾` → `Variable Studio` 선택
2. Reference 대화상자에서 `Clock Config` 선택 → ✓
3. 이후 스케치 치수 입력창에 `#drumDiameter_90` 등으로 사용

---

