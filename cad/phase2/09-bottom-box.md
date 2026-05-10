## 6. Part Studio "06 Bottom Box" (3 sections)

전자부 enclosure. 뚜껑 없는 상자 형태 — 07 Bottom Plate가 뚜껑 역할. 베드 200mm 한계 → **§1.5 Option B 채택**: 처음부터 3 sections (A·B·C)를 같은 Part Studio 안 별도 파트로 모델링.

### 6.1 Part Studio 생성 + Variable 연결

1. 탭바 `+` → **Part Studio**, 이름 `06 Bottom Box`
2. `Feature ▾` → **Variable Studio** → `Clock Config`

### 6.2 기준 형상 spec (참조 — 직접 모델링 없음)

3 section을 결합했을 때 만들어져야 하는 통합 형상. 본 §6.2는 **검증·참조용 spec**일 뿐, 실제 모델링은 §6.4에서 각 section 별도로 진행.

#### 좌표·치수

| 항목 | 값 | 변수 |
|---|---|---|
| 외형 | 620 × 120 × 80 (X × Y × Z) | `enclosureWidth` × `enclosureDepth` × `bottomBoxHeight` |
| 좌표 원점 | 박스 윗면 중심 (Z=0) | - |
| 바닥 | Z = -80 | -`bottomBoxHeight` |
| 외벽 두께 | 3 mm | `boxWallThickness` |
| Cavity Y 범위 | -57 ~ +57 (모든 section 공통) | (-`enclD`/2 + `bWT`) ~ (+`enclD`/2 - `bWT`) |
| Cavity Z 범위 | -77 ~ 0 (윗면 open) | -`bbH`+`bWT` ~ 0 |
| Cavity X 범위 (A) | -307 ~ -106 | -`enclW`/2 + `bWT` ~ `splitX_1` - `bWT` |
| Cavity X 범위 (B) | -100 ~ +100 | `splitX_1` + `bWT` ~ `splitX_2` - `bWT` |
| Cavity X 범위 (C) | +106 ~ +307 | `splitX_2` + `bWT` ~ +`enclW`/2 - `bWT` |

> **분할 면 (X=±103)에 3mm 벽 — H-shape 골격**. 벽은 tongue/groove 견고 유지의 최소치만 남기고 cable passthrough cut 적용:
> - 남김: center 수직 strip (Y=±12, 24mm 폭, tongue/groove 지지) + top 림 (Z=-3~0, 강성) + back boss pad (Y=-57~-49, cross-screw 보스 지지) + 퍼리미터 strips (front/back/bottom 외벽)
> - 자름: front 통로 (Y=+12~+57, Z=-77~-3, 3330 mm²) + back 통로 (Y=-49~-12, Z=-77~-3, 2738 mm²)
> - 통로 총 ~6068 mm² → DC잭→Mega→6 ULN2003 드라이버 와이어 (~12-15 가닥) 통과 충분.

#### 분할 영역

| Section | Outline X | 길이 | 외측 외벽 |
|---|---|---|---|
| A (좌) | -310 ~ -103 | 207 | 좌(-310) + **분할(-103)** + 후·전·바닥 |
| B (중) | -103 ~ +103 | 206 | **양 분할(±103)** + 후·전·바닥 |
| C (우) | +103 ~ +310 | 207 | 우(+310) + **분할(+103)** + 후·전·바닥 |

> 모든 section이 6면 (4 외측 + 바닥 + 분할 벽) 닫힌 box. 윗면만 open (plate가 덮음).

#### 기능 분배

| 기능 | A | B | C |
|---|---|---|---|
| Outer shell + cavity | ✓ | ✓ | ✓ |
| DC jack hole (X=∓200, Z=-40, ∅8) | ✓ (X=-200) | - | ✓ (X=+200, mirror 결과 — 예비) |
| Vent slots ×10 (X=0 중심 80mm long, Z=-65~-11) | - | ✓ | - |
| Rim bosses + insert holes (12개 분배) | 4 | 4 | 4 |
| Split tongue (분할 면 8×80, 4mm 돌출) | 우측 (+X) | - | 좌측 (-X) |
| Split groove (분할 면 8.4×80, 4.2mm 패임) | - | 양측 | - |
| Split cross-screw 보스 + clearance (cavity 내부 Z=-20·-60) | 우측 ×2 | - | 좌측 ×2 |
| Split cross-screw 매칭 보스 + heat insert | - | 양측 ×4 | - |

#### 12 Rim boss 좌표 (전체 — §6.4에서 section별 4개씩 작업)

| Section | X 중심 | Y 중심 | 우상단 모서리 |
|---|---|---|---|
| A 외측 후 | -301 | -51 | (-295, -45) |
| A 외측 전 | -301 | +51 | (-295, +57) |
| A 분할안쪽 후 | -120 | -51 | (-114, -45) |
| A 분할안쪽 전 | -120 | +51 | (-114, +57) |
| B 좌 후 | -85 | -51 | (-79, -45) |
| B 좌 전 | -85 | +51 | (-79, +57) |
| B 우 후 | +85 | -51 | (+91, -45) |
| B 우 전 | +85 | +51 | (+91, +57) |
| C 분할안쪽 후 | +120 | -51 | (+126, -45) |
| C 분할안쪽 전 | +120 | +51 | (+126, +57) |
| C 외측 후 | +301 | -51 | (+307, -45) |
| C 외측 전 | +301 | +51 | (+307, +57) |

> 각 12×12 사각형이 박스 inner 벽(X=±307, Y=±57)에 정확히 flush. 좌표 도출은 이전 절차 §6.2 Step 9 동일 (PR #54).

### 6.3 파트 이름 정리

같은 Part Studio "06 Bottom Box" 안 3 솔리드 (Parts 패널):
- `Bottom Box A` (좌)
- `Bottom Box B` (중)
- `Bottom Box C` (우)

> 같은 Part Studio에 두면 변수·좌표 일관 + Assembly에서 한 번에 import. 별도 Part Studio (`06a Box A`, `06b`, `06c`) 분리도 가능하나 변수 sync 부담.

### 6.4 각 Section 모델링 절차

**원칙**: 각 section은 별도 New Solid로 시작. 후속 Extrude Add/Remove는 **Merge scope** 또는 **Boolean target**으로 해당 section만 지정. 다른 section과 절대 merge 안 함.

---

#### 6.4.1 Section A — 좌측 (DC잭 포함)

##### Step A1·A2 — Outer shell

1. Top plane → Sketch "A_outline":
   - Center point rectangle, 중심 (-206.5, 0), 치수 207 × 120 (X × Y)
   - X 범위 -310 ~ -103, Y 범위 -60 ~ +60
2. Extrude **New Solid**, Blind Depth `#bottomBoxHeight` (= 80), -Z
   - → Bottom Box A 솔리드 207 × 120 × 80

##### Step A3·A4 — Cavity (분할 면 X=-103에 3mm 벽 유지)

1. Top plane → Sketch "A_cavity":
   - Center point rectangle, 중심 (**-206.5**, 0), 치수 **201 × 114** (X × Y)
   - X 범위 -307 ~ -106 (좌 외벽 3mm + 분할 벽 3mm)
2. Extrude Cut, **target = A solid only**, Blind Depth 77 (= `#bottomBoxHeight - #boxWallThickness`), -Z
   - → A 내부 cavity. 분할 면(X=-103)에 3mm 벽 유지 (X=-106 ~ -103, full Y·Z 솔리드) — tongue·groove·split 보스 결합부로 작동.

##### Step A5·A6 — DC jack

1. 후면벽 외측 면 (Y=-60, A 영역 내) 클릭 → Sketch "A_DC_jack":
   - ∅`#dcJackDiameter` (= 8) 원
   - 중심 (X = `#dcJackX` = -200, Z = `#dcJackZ` = -40)
2. Extrude Cut, target = A, Blind Depth `#boxWallThickness` (= 3), +Y
   - **Through all 금지** — 전면벽까지 뚫림 사고

##### Step A7~A10 — Rim bosses 4개 + insert holes

1. Top 면 (Z=0, A solid) 클릭 → Sketch "A_rim_bosses": 12×12 mm 사각형 4개:
   - (-301, -51), (-301, +51), (-120, -51), (-120, +51)
2. Extrude Add, target = A, Blind 12, -Z → 보스 4개 fused
3. Top 면 → Sketch "A_insert_holes": ∅`#insertHole` (= 4.2) 원 4개 (보스 중심)
4. Extrude Cut, target = A, Blind 6, -Z → 인서트 홀 4개

##### Step A10.5 — Split 벽 cable passthrough cuts (와이어 통로)

A의 분할 벽(X=-106~-103, 3mm slab)에서 tongue·boss 지지 영역을 제외한 부분을 잘라 와이어 통로 형성. **벽은 tongue/groove 견고 유지의 최소치**.

남기는 영역:
- **Center 수직 strip** Y=-12~+12 (24mm 폭): tongue 지지
- **Top 림** Z=-3~0: center strip ↔ 퍼리미터 연결, 강성
- **Bottom strip** Z=-77~-80: section 바닥 벽 (자동)
- **Front strip** Y=+57~+60, **Back strip** Y=-60~-57: section 전·후 벽 (자동)

자르는 영역 (cable passthrough):

1. A 우측 면 (X=-103) 클릭 → Sketch "A_split_passthrough":
   - 사각형 2개 (Y × Z 평면):
     - **Front 통로**: 중심 (Y=+34.5, Z=-40), 치수 45 × 74
       (Y 범위 +12~+57, Z 범위 -77~-3)
     - **Back 통로**: 중심 (Y=-30.5, Z=-40), 치수 37 × 74
       (Y 범위 -49~-12, Z 범위 -77~-3)
2. Extrude Cut, target = A, **Blind 3mm** (정확히 split 벽 두께만), -X 방향
   - **Through all 금지** — sketch가 cavity (X=-106~-307) 통과 후 좌측 외벽 (X=-307~-310)도 뚫음. Blind 3mm이 안전.
   - → 분할 벽이 H-shape에 가까운 골격 형태로 변신
   - 통로 총 면적 ≈ 6068 mm² (Mega↔A 드라이버 12-15 wire 통과 충분)

> **Back 통로는 cross-screw 보스 영역(Y=-57~-49) 회피** (Y=-49까지만 cut). 보스 main의 X=-106~-103 부분이 split 벽과 일체로 유지됨 (Step A14 검증 필요).

##### Step A11·A12 — Tongue (우측 분할 면)

A의 분할 벽 (X=-106 ~ -103) 외측 면(X=-103)에서 +X로 돌출.

1. A 우측 면 (X = -103) 클릭 → Sketch "A_tongue":
   - Center rectangle, 중심 (Y=0, Z=-40), 치수 `#splitTongueWidth` × `#bottomBoxHeight` (= 8 × 80)
2. Extrude Add, target = A, Blind `#splitTongueDepth` (= 4), +X
   - → A 우측 끝에서 +X로 4mm tongue 돌출 (Y=-4~+4, Z=-80~0)
   - **Tongue 전체 8×80 면이 분할 벽 솔리드(Y=-4~+4 ⊂ Y=-60~+60, Z=-80~0 풀)와 접촉** → 부유 없음, 견고히 부착

##### Step A13·A14 — Split cross-screw 보스 (cavity 내부, ×2)

목표: A의 split 보스가 cavity 안쪽으로 3mm + split 벽 안 3mm + split 너머 4mm = 10mm 돌출, 중심에 ∅3.2 X-관통 clearance. B의 매칭 보스가 X=-99 위치에서 heat insert로 받음.

1. A의 후면벽 cavity 측 inner 면 (Y=-57, A 영역) 클릭 → Sketch "A_split_boss_main":
   - 사각형 2개, 6 × 8 (X × Z), 중심 (X=-106, Z=-20) / (X=-106, Z=-60)
   - X 범위 -109 ~ -103: -109 ~ -106이 cavity, -106 ~ -103이 split 벽 안에 매립 (Onshape Add는 기존 솔리드와 자동 merge)
2. Extrude Add, target = A, Blind 8, +Y
   - → 보스 main body 2개, 6×8×8 mm (X×Y×Z), Y=-57 ~ -49
   - **X=-106 ~ -103 영역은 split 벽과 동일 material — 추가 변화 없음 (이미 솔리드)**. 실질 cavity 돌출 부분은 X=-109 ~ -106 (3mm).

3. 위 보스 main body의 +X 면 (X=-103, Y=-57~-49, Z=-24~-16 / Z=-64~-56) 각각 클릭 → Sketch "A_split_overhang":
   - 8 × 8 (Y × Z) 사각형으로 면 전체 덮음
4. Extrude Add, target = A, Blind 4, +X
   - → 각 보스에 4mm overhang 추가 (X=-103 ~ -99, B 영역으로 돌출하지만 A solid에 merged)
   - 최종 split 보스 A: 10×8×8 mm 통합 영역, X=-109 ~ -99, ×2 (그 중 X=-106 ~ -103는 split 벽과 일체)

##### Step A15·A16 — Split cross-screw clearance 홀

1. 각 split 보스의 -X 끝 면 (X=-109, cavity 측) → Sketch "A_split_clearance":
   - ∅3.2 원 1개, 보스 중심 (Y=-53, Z=-20 / -60)
2. Extrude Cut, target = A, Blind 10 (= 보스 X 길이 -109~-99), +X
   - → 각 보스에 X-관통 ∅3.2 clearance
   - **Bolt head 진입 면 = X=-109 (cavity 안쪽)** = 내부 face

---

#### 6.4.2 Section B — 중앙 (vent slots 포함)

##### Step B1·B2 — Outer shell
1. Top plane → Sketch "B_outline": 중심 (0, 0), 치수 206 × 120
2. Extrude New Solid, Blind 80, -Z → Bottom Box B 솔리드 206×120×80

##### Step B3·B4 — Cavity (양 분할 면에 3mm 벽 유지)
1. Sketch "B_cavity": 중심 (0, 0), 치수 **200 × 114** (X·Y 모두 외벽 차감)
   - X 범위 -100 ~ +100 (양 분할 벽 3mm씩 유지)
2. Extrude Cut, target = B, Blind 77, -Z
   - → B 내부 cavity. 양 분할 면(X=±103)에 3mm 벽 유지 — groove·매칭 보스 결합부.

##### Step B5·B6·B7 — Vent slots ×10

1. 후면벽 외측 (Y=-60, B 영역) → Sketch "B_vent_slot1":
   - Center rectangle, 중심 (X=0, Z=`#ventSlotZStart` = -65), 치수 `#ventSlotLength` × `#ventSlotWidth` (= 80 × 2)
2. Extrude Cut, target = B, Blind `#boxWallThickness` (= 3), +Y
3. Linear Pattern (feature toolbar): 전 단계 Extrude Cut feature 선택, +Z 방향, Count `#ventSlotCount` (= 10), Spacing `#ventSlotPitch` (= 6)
   - → 슬롯 10개 Z 중심: -65, -59, …, -11

##### Step B8~B11 — Rim bosses 4개 + insert holes

1. Top 면 → Sketch "B_rim_bosses": 12×12 사각형 4개:
   - (-85, -51), (-85, +51), (+85, -51), (+85, +51)
2. Extrude Add, target = B, Blind 12, -Z
3. Top 면 → Sketch "B_insert_holes": ∅4.2 원 4개
4. Extrude Cut, target = B, Blind 6, -Z

##### Step B11.5 — Split 벽 cable passthrough cuts (양측)

B의 양 분할 벽 (좌·우)에 동일 cable passthrough 패턴 적용. §A10.5와 동일 형상 — center strip (24mm 폭) + top 림 (3mm) + back boss pad 유지, 나머지 cut.

1. 좌측 X=-103 면 → Sketch "B_split_passthrough_L":
   - Front 통로: 중심 (Y=+34.5, Z=-40), 치수 45 × 74
   - Back 통로: 중심 (Y=-30.5, Z=-40), 치수 37 × 74
2. Extrude Cut, target = B, **Blind 3mm**, **+X** 방향 (벽 안쪽으로) — Through all 금지
3. 우측 X=+103 면 → Sketch "B_split_passthrough_R": 동일 좌표
4. Extrude Cut, target = B, **Blind 3mm**, **-X** 방향 — Through all 금지

> 양 split 벽 모두 H-shape 골격. 와이어가 B의 cavity 안에서 자유롭게 좌우로 이동.

##### Step B12·B13 — Grooves 양측

B의 양 분할 벽 (X=-103~-100 좌, X=+100~+103 우) 외측 면에 groove cut.

1. 좌측 X=-103 면 → Sketch "B_groove_L": center rect 중심 (Y=0, Z=-40), 치수 `#splitTongueWidth + 0.4` × `#bottomBoxHeight` (= 8.4 × 80)
2. Extrude Cut, target = B, Blind 4.2 (= `#splitTongueDepth + 0.2`), **+X** (벽 안쪽으로 파임)
   - Cut 범위 X=-103 ~ -98.8: 분할 벽 (3mm) + cavity 1.2mm 진입. 벽 부분(3mm)이 실제 groove로 작동, cavity 부분은 빈 공간이라 영향 없음.
3. 우측 X=+103 면 동일 → 중심 (Y=0, Z=-40), 8.4×80 → Extrude Cut, **-X** 방향 4.2mm

##### Step B14·B15 — Split cross-screw 매칭 보스 + heat insert (×4)

목표: B에 4개 보스 (좌·우 split 각 2개), A·C의 split 보스 overhang(X=-99 좌, X=+99 우)이 도달하는 위치에서 heat insert로 받음.

1. B의 후면벽 cavity 측 inner 면 (Y=-57, B 영역) → Sketch "B_split_match_main":
   - 사각형 4개, 6 × 8 (X × Z):
     - 좌측 매칭: 중심 (X=-96, Z=-20) / (X=-96, Z=-60) — X 범위 -99 ~ -93
     - 우측 매칭: 중심 (X=+96, Z=-20) / (X=+96, Z=-60) — X 범위 +93 ~ +99
2. Extrude Add, target = B, Blind 8, +Y
   - → B에 보스 4개 6×8×8 mm

3. 각 보스의 분할 측 끝 면 (좌측 매칭은 -X 면 X=-99, 우측 매칭은 +X 면 X=+99) → Sketch "B_split_insert":
   - ∅`#insertHole` (= 4.2) 원 1개, 보스 중심 (Y=-53, Z=-20 / -60)
4. Extrude Cut, target = B, Blind 6, +X (좌측) / -X (우측, 보스 안으로)
   - → 4개 heat insert 홀 (인서트 6mm 매립)

> **조립 정렬 검증**: A 보스 overhang 끝 X=-99 ↔ B 매칭 보스 -X 면 X=-99 정확히 abutting. M3 8mm 볼트가 A 보스 -X 면(X=-109)에서 들어가 A 10mm + 매칭 보스 인서트 0~6mm 영역까지 진입. 6mm 정도 engagement ≈ M3 6mm 분이 인서트와 결합. 우측 동일.

---

#### 6.4.3 Section C — 우측 (A의 Mirror로 생성)

A를 모델링한 후 **Mirror feature**로 YZ-plane (X=0) 기준 통째 복사. A 절차 재실행 불필요.

##### Step C1 — Mirror feature 추가

1. Feature toolbar → **Mirror**
2. Mirror plane: **Right plane** (= YZ-plane, X=0 — 박스 중심선)
3. Parts to mirror: **`Bottom Box A` 솔리드**만 선택 (B는 제외)
4. Confirm → C 솔리드 생성

→ A의 **모든 features 통째 mirror** (cavity, DC잭, rim bosses, tongue, split 보스 main+overhang, clearance 홀). DC잭 hole도 X=+200 위치에 그대로 복제됨 — 의도적으로 유지 (필요 시 패스스루·예비 잭으로 활용).

##### Step C2 — 파트 이름 정리

Parts 패널에서 mirror 결과 파트 → Rename → **`Bottom Box C`**.

##### 검증 (mirror 후 자동 결과)

| 항목 | 값 |
|---|---|
| Outline 중심 X | +206.5 |
| Cavity X 범위 | +103 ~ +307 |
| Rim boss X 중심 | +301, +120 |
| Tongue 위치 | 좌측 X=+103, **-X 4mm 돌출** (mirror 자동 반전) |
| Split 보스 main X 범위 | +103 ~ +109 |
| Split 보스 overhang | X=+99 ~ +103, **-X 4mm** (mirror 자동 반전) |
| Clearance ∅3.2 진입 면 | X=+109 (cavity 안쪽) |
| **DC잭** | **있음 — X=+200 후면벽 외측** (예비/패스스루) |
| Vents | 없음 (B에만) |

> Mirror feature는 A의 후속 변경 시 C가 자동 갱신되는 **parametric link**. A의 boss 위치·크기 수정하면 C도 즉시 반영.

---

#### 6.4.4 정렬 검증 (모델링 후)

| 검증 | 기대 |
|---|---|
| A·B·C 3 솔리드 분리 | Parts 패널에 3개, 서로 미접촉 |
| A 우측 끝 X=-103 ↔ B 좌측 끝 X=-103 | tongue/groove 위치 일치 |
| A tongue X=-103~-99 ↔ B 좌 groove X=-103~-99 | 4mm 오버랩 |
| A split 보스 끝 X=-99 ↔ B 좌 매칭 보스 시작 X=-99 | abutting |
| A split clearance ∅3.2 (X-관통) ↔ B 좌 매칭 insert ∅4.2 | 동축 |
| Rim bosses 12개 전체 inner 벽 flush | abutment 정상 |
| Cavity 분리 (각 section) | A: X=-307~-106, B: X=-100~+100, C: X=+106~+307 |
| 분할 벽 H-shape | center strip (Y=±12) + top 림 (Z=-3~0) + back boss pad — 최소 골격 |
| Cable passthrough | A↔B 1쌍, B↔C 1쌍 (front+back 통로) — 통로 ~6000 mm²/side |
| Mega 2560 fit | B cavity 200×114×77 ⊃ Mega 102×53×16 — 권장 위치 B |

### 6.5 STL 출력

각 section은 **별도 STL 파일**:
- `cad/stl/bottom_box_A.stl`
- `cad/stl/bottom_box_B.stl`
- `cad/stl/bottom_box_C.stl`

설정:
- 재료 PETG, Layer 0.2 mm, Infill 25%
- 출력 방향: 윗면(Z=0) 트인 채로 베드 위 — 서포트 최소화
- 베드 200mm 한계 내 (각 section 207mm는 빠듯) → 베드 중심 정렬, 필요 시 약간 회전 또는 베드 250mm 프린터 권장

---

