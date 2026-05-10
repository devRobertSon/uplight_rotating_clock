## 8. Part Studio "08 Side Panels"

전·후·좌·우 4 piece. 코너에서 tongue-groove로 결합되어 ring 형성.

### 8.1 Part Studio 생성 + Variable 연결
1. 탭바 `+` → **Part Studio**, 이름 `08 Side Panels`
2. Variable Studio → `Clock Config`

### 8.2 4개 파트 설계

| Piece | 길이 (X) | 깊이 (Y) | 높이 (Z) | 끝단 가공 |
|---|---|---|---|---|
| 전면 패널 (front, 6 디지트 창) | `#enclosureWidth - 2 × #sidePanelThickness` (614) | 3 | `#drumCompartmentHeight` (90) | 양 끝 groove |
| 후면 패널 (back, 솔리드) | 614 | 3 | 90 | 양 끝 groove |
| 좌측 패널 (left) | 3 | `#enclosureDepth - 2 × #sidePanelThickness` (114) | 90 | 양 끝 tongue |
| 우측 패널 (right) | 3 | 114 | 90 | 양 끝 tongue |

> **결합 원리**: 좌·우 패널 양 끝의 tongue (3 × 4 × 90 돌출)이 전·후 패널 양 끝의 groove (3.4 × 4 × 90 홈)에 삽입됨.

### 8.3 Feature tree (전면 패널 예시)

#### Step 1 — Sketch "front panel outline" (Front plane)
- 직사각형 614 (W) × 90 (H), 원점 중심
- Sketch 종료

#### Step 2 — Extrude "front panel"
- New, Symmetric, Depth `#sidePanelThickness` (= 3)

→ 614 × 90 × 3 솔리드.

#### Step 3 — Sketch "groove on each end" (양 끝면)
양 끝 X = ±307 면에 sketch:
- 직사각형 holes: 3.4 × 4 mm (groove 단면)
- 위치: 패널 두께 중심에 정렬, 깊이 4mm

#### Step 4 — Extrude Cut "end grooves"
- Blind, Depth 4mm
- → 양 끝에 tongue 삽입용 홈

#### Step 5 — Sketch "6 digit windows" (front face)
6개 직사각형 창 — 각 드럼 위치에 디지트 가시:

| 드럼 | 위치 X | 폭 |
|---|---|---|
| D1 (요일 ∅60) | `#drumX_1` (-265) | `#digitWindowWidth_60` (24) |
| D2 (시십 ∅90) | `#drumX_2` (-170) | `#digitWindowWidth_90` (26) |
| D3 (시일 ∅90) | `#drumX_3` (-65) | 26 |
| D4 (분십 ∅90) | `#drumX_4` (+65) | 26 |
| D5 (분일 ∅90) | `#drumX_5` (+170) | 26 |
| D6 (날씨 ∅60) | `#drumX_6` (+265) | 24 |

각 창 높이: `#digitWindowHeight` (= 50). 위치 Z: 패널 중앙.

#### Step 6 — Extrude Cut "windows"
- Remove, Through all → 6개 창 관통.

#### Step 7~ — 후면·좌측·우측 패널 추가

각 패널을 별도 New 파트로 생성:
- 후면: 전면과 동일 외곽·groove, 디지트 창만 없음
- 좌·우: 외곽 3 × 114 × 90, 양 끝에 **tongue** (groove 반대) 추가
  - Tongue: 3 × 4 × 90 돌출 (sketch + extrude add)

### 8.4 검증
| 확인 | 기대 |
|---|---|
| 4개 파트 (front/back/left/right) | Parts 패널에 4개 |
| 전·후 양 끝 groove | 3.4 × 4 × 90 |
| 좌·우 양 끝 tongue | 3 × 4 × 90 |
| 4 piece 결합 시 ring 형성 | 614 × 114 외곽 |

### 8.4 분할 설계 (§1.5 적용)

**전·후면 패널만 분할**. 좌·우는 단일 출력 (114mm).

| Side | 길이 | 분할 |
|---|---|---|
| Front | 614 | A·B·C 3 sections (X = ±103에서 절단) |
| Back | 614 | A·B·C 3 sections |
| Left | 114 | 단일 |
| Right | 114 | 단일 |

**디지트 창 위치**: front panel의 6 창은 split line과 충돌 안 함:
- Section A: 창 1 (요일, X = -265), 창 2 (시십, X = -170)
- Section B: 창 3 (시일, X = -65), 창 4 (분십, X = +65)
- Section C: 창 5 (분일, X = +170), 창 6 (날씨, X = +265)

#### Front Panel 각 section 모델링

| Section | X 범위 | 디지트 창 | Tongue/Groove |
|---|---|---|---|
| A (좌) | -310 ~ -103 | 창 1·2 (D1·D2) | 우측 (X=-103) tongue |
| B (중) | -103 ~ +103 | 창 3·4 (D3·D4) | 양 끝 groove |
| C (우) | +103 ~ +310 | 창 5·6 (D5·D6) | 좌측 (X=+103) tongue |

각 section:
1. Front plane → Sketch outline (X 범위에 맞는 직사각형 × 90 H)
2. Extrude `#sidePanelThickness` (= 3)
3. 디지트 창 sketch + Through Cut (해당 section의 창 위치)
4. Tongue / Groove 추가:
   - Tongue: 우측 끝 면(X = -103 또는 +103)에 sketch 8 × 90 → Extrude Add, Depth 4 (+X 또는 -X)
   - Groove: 끝 면에 sketch 8.4 × 90 → Extrude Cut, Depth 4.2

> **Cross-screw 없음** (§1.5 hidden-bolt 규칙). 3mm 얇은 panel에 cross-screw를 박으면 외부 visible + 약한 결합. 대신:
> - Tongue-groove로 X·Y 정렬
> - 상·하 둘레 홈 (07 Bottom Plate의 윗면 둘레 홈 + 09 Top Plate의 아랫면 둘레 홈)이 panel ring 전체를 위·아래에서 클램핑
> - 결과: panel 외부 4면 모두 매끄러운 볼트-없음 표면.

#### Back Panel
Front와 동일 구조, 디지트 창 없음. Cross-screw 없음.

> 측면 panel ring은 plate 둘레 홈에 끼움으로써 정렬·고정 → **panel split joint는 tongue/groove만으로 충분**. Cross-screw 없음 → 외부 visible 0.

### 8.5 STL 출력
- 각 패널 분리 출력
- PETG, Layer 0.2 mm, Infill 25%
- 전·후면은 분할 출력 (3등분, 도브테일)
- 좌·우는 단일 출력 (114mm, 베드 안)

---

