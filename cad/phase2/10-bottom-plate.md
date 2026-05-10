## 7. Part Studio "07 Bottom Plate" (3 sections)

박스 뚜껑 + 6 모터 마운트 + 6 Hall 센서 자리 + 6 LED 슬롯 + 측면 판 둘레 홈. **§1.5 Option B**: 처음부터 3 sections (A·B·C)를 같은 Part Studio 안 별도 솔리드로 모델링.

### 7.1 Part Studio 생성 + Variable 연결

1. 탭바 `+` → **Part Studio**, 이름 `07 Bottom Plate`
2. `Feature ▾` → **Variable Studio** → `Clock Config`

### 7.2 기준 형상 spec (참조 — 직접 모델링 없음)

3 section 합쳤을 때 통합 형상. 검증·참조용.

#### 좌표·치수

| 항목 | 값 | 변수 |
|---|---|---|
| 외형 | 620 × 120 × 5 (X × Y × Z) | `enclosureWidth` × `enclosureDepth` × `plateThickness` |
| 좌표 원점 | plate 윗면 중심 (Z=5)? — 본 절차는 **윗면 = Z=plateThickness=5, 아랫면 = Z=0** 사용 (모터·Hall·LED는 윗면 기준) |
| 둘레 홈 폭 | 3.4 mm | `panelGrooveWidth` (= sidePanelThickness + 0.4) |
| 둘레 홈 깊이 | 4 mm | `panelGrooveDepth` |
| 둘레 홈 외측 라인 | 외곽에서 5mm 안쪽 | (literal 5) |

> **Z 축 주의**: §6 Bottom Box는 윗면 Z=0 기준이지만 plate는 윗면을 +Z 측으로 두는 게 모터·Hall sketching 시 직관적. 본 §7은 **plate 아랫면 Z=0, 윗면 Z=5** 가정.

#### 분할 영역

| Section | Outline X | 길이 | 포함 드럼 |
|---|---|---|---|
| A (좌) | -310 ~ -103 | 207 | D1 (요일 ∅60, X=-265) + D2 (시십 ∅90, X=-170) |
| B (중) | -103 ~ +103 | 206 | D3 (시일 ∅90, X=-65) + D4 (분십 ∅90, X=+65) |
| C (우) | +103 ~ +310 | 207 | D5 (분일 ∅90, X=+170) + D6 (날씨 ∅60, X=+265) |

#### 드럼 1세트 당 hole 패턴 (모든 드럼 공통)

| Feature | 위치 (X·Y) | 치수 | 비고 |
|---|---|---|---|
| **모터 샤프트 hole** | (drumX, 0) | ∅6 (∅5 shaft + 1 clearance) | Through |
| **모터 마운트 hole 1** | (drumX **- 17.5**, **-11**) | ∅3.2 | M3 clearance, 28BYJ-48 native 35mm pitch |
| **모터 마운트 hole 2** | (drumX **+ 17.5**, -11) | ∅3.2 | 동일 |
| **Hall 센서 slot** | (drumX, **+30** for ∅90, **+15** for ∅60) | 5 × 8 mm rect | TO-92 sensor + 와이어 통과 |
| **LED 와이어 hole** | (drumX, **+50**) | ∅5 | LED 바 와이어 → MOSFET |

> **모터 방향**: 28BYJ-48 본체는 plate 아래로 매달림 (-Y 방향 11mm 오프셋). 샤프트가 +Z로 plate 관통하여 위로 올라감. 마운트 hole 2개는 motor mounting plate의 native 35mm pitch.

> **Hall·LED Y 위치**: drum center Y=0 기준. Hall은 자석 회전 궤도(R=15·30) 위치, LED는 drum 앞쪽 Y=+50 (front bezel 가까이).

#### 둘레 홈 (perimeter groove for side panel ring)

- 통합 형상 기준: outer rect 610 × 110 (X=±305, Y=±55), 그 안쪽 3.4mm 폭 ring
- 각 section은 자기 X 범위 안의 ring 부분만 (B는 양 X 끝 open ring, A·C는 한쪽 X만 닫힌 ring)
- 깊이: -Z 방향 4mm (윗면에서 4mm 깎임)

### 7.3 파트 이름 정리

같은 Part Studio 안 3 솔리드:
- `Bottom Plate A` (좌)
- `Bottom Plate B` (중)
- `Bottom Plate C` (우)

---

### 7.4 각 Section 모델링 절차

각 section은 별도 New Solid + Merge scope = section 한정.

---

#### 7.4.1 Section A — 좌측 (D1·D2 드럼)

##### Step A1·A2 — Outline + Extrude

1. Top plane → Sketch "A_outline":
   - Center point rectangle, 중심 (-206.5, 0), 치수 207 × 120
   - X 범위 -310 ~ -103, Y 범위 -60 ~ +60
2. Extrude **New Solid**, Blind Depth `#plateThickness` (= 5), +Z
   - → Bottom Plate A 솔리드 207 × 120 × 5

##### Step A3·A4 — D1 (요일 ∅60, drumX_1 = -265) hole 패턴

Plate 윗면 (Z=5) 클릭 → Sketch "A_D1_holes":
1. ∅6 원, 중심 (`#drumX_1`, 0) = (-265, 0) — 모터 샤프트
2. ∅3.2 원 2개, 중심 (-265 - 17.5, -11), (-265 + 17.5, -11) = (-282.5, -11), (-247.5, -11) — 모터 마운트
3. Center rect 5 × 8, 중심 (-265, **+15**) — Hall slot (∅60: R=15)
4. ∅5 원, 중심 (-265, +50) — LED 와이어

→ Extrude Cut, target = A, **Through (5mm)**, -Z 방향

##### Step A5·A6 — D2 (시십 ∅90, drumX_2 = -170) hole 패턴

윗면 → Sketch "A_D2_holes":
1. ∅6 at (-170, 0)
2. ∅3.2 × 2 at (-187.5, -11), (-152.5, -11)
3. 5 × 8 rect at (-170, **+30**) — Hall slot (∅90: R=30)
4. ∅5 at (-170, +50)

→ Extrude Cut, target = A, Through, -Z

##### Step A7·A8 — Perimeter groove (C-shape, split 측 open)

A는 둘레 ring의 좌측·후·전 3변만 (우측 split 측은 B로 연결됨).

1. Plate 윗면 → Sketch "A_perimeter_groove":
   - Outer rect: X = -305 ~ -103 (length 202), Y = -55 ~ +55 — 단 우측 끝 X=-103은 그냥 open
   - 실용적으로 sketch는 polyline 또는 corner rect로 3변 frame:
     - 좌변: X = -305 ~ -301.6, Y = -55 ~ +55 (3.4 폭 × 110 길이)
     - 후변(아래): X = -305 ~ -103, Y = -55 ~ -51.6 (202 길이 × 3.4 폭)
     - 전변(위): X = -305 ~ -103, Y = +51.6 ~ +55
   - 또는 outer rect (-305~-103, -55~+55) - inner rect (-301.6~-103, -51.6~+51.6) 두 폐곡선으로 그리고 ring 영역만 빼냄
2. Extrude Cut, target = A, **Blind Depth 4** (= `#panelGrooveDepth`), -Z
   - → 윗면에 둘레 홈 (4mm 깊이)

##### Step A9·A10 — Tongue (우측 split 면)

1. A 우측 면 (X = -103) 클릭 → Sketch "A_tongue":
   - Center rect, 중심 (Y=0, **Z=2.5** = plate 두께 중앙), 치수 `#splitTongueWidth` × `#plateThickness` (= 8 × 5)
2. Extrude Add, target = A, Blind `#splitTongueDepth` (= 4), +X
   - → A 우측 끝에서 +X로 4mm tongue 돌출 (Y=-4~+4, Z=0~5)

##### Step A11·A12 — Cross-screw under-ridge + heat insert

목표: A 하면에 ridge 부착, B 하부로 연장하여 받침. B의 top face에서 ↓ M3 볼트로 ridge 안 heat insert에 박힘 (§1.5 hidden-bolt).

1. Plate **하면** (Z=0) 클릭 → Sketch "A_ridge":
   - Center rect, 중심 (X=**-103**, Y=0), 치수 **20 × 30** (X 폭 × Y 폭)
   - X 범위 -113 ~ -93 (10mm A 안쪽 + 10mm B 측 연장), Y 범위 -15 ~ +15 (둘레 홈 안쪽 + 모터 회피)
2. Extrude Add, target = A, Blind 5 (= plate 두께), -Z
   - → Plate 아래로 5mm ridge 돌출, X=-113 ~ -93, Y=±15, Z=-5 ~ 0
3. Ridge 하면 (Z=-5) 클릭 → Sketch "A_ridge_inserts":
   - ∅`#insertHole` (= 4.2) 원 2개, 중심 (X=**-98**, Y=±10) — split 너머 5mm 위치, B의 plate 바로 아래
4. Extrude Cut, target = A, Blind 5 (-Z 방향, ridge 안으로) — 또는 +Z 방향으로 cut depth 5
   - → ridge 안 heat insert 자리 2개

> **조립**: B의 plate가 A의 ridge 위에 놓임 (X=-103~-93에서 B 하면이 ridge 상면에 닿음). B의 윗면 (드럼 측)에서 ↓ M3 6mm 볼트 → B plate 5mm 통과 → ridge 안 insert에 박힘.

---

#### 7.4.2 Section B — 중앙 (D3·D4 드럼)

##### Step B1·B2 — Outline + Extrude
1. Sketch "B_outline": 중심 (0, 0), 치수 206 × 120
2. Extrude New Solid, Blind 5, +Z

##### Step B3·B4 — D3 (시일 ∅90, drumX_3 = -65) hole 패턴
1. ∅6 at (-65, 0)
2. ∅3.2 × 2 at (-82.5, -11), (-47.5, -11)
3. 5 × 8 rect at (-65, +30)
4. ∅5 at (-65, +50)
→ Extrude Cut Through, -Z

##### Step B5·B6 — D4 (분십 ∅90, drumX_4 = +65) hole 패턴
1. ∅6 at (+65, 0)
2. ∅3.2 × 2 at (+47.5, -11), (+82.5, -11)
3. 5 × 8 rect at (+65, +30)
4. ∅5 at (+65, +50)
→ Extrude Cut Through, -Z

##### Step B7·B8 — Perimeter groove (직사각 ring, X 양 끝 open)

1. Sketch "B_perimeter_groove":
   - Outer rect: X = -103 ~ +103 (206), Y = ±55 (110)
   - Inner rect: X = -103 ~ +103, Y = ±51.6 (103.2)
   - 두 폐곡선 사이 ring 영역만 빼냄 (전·후 변, X 끝 open)
2. Extrude Cut, target = B, Blind 4, -Z

##### Step B9·B10 — Grooves 양측 (split 면)

1. B 좌측 면 (X = -103) → Sketch "B_groove_L":
   - Center rect, 중심 (Y=0, Z=2.5), 치수 `#splitTongueWidth + 0.4` × `#plateThickness` (= 8.4 × 5)
2. Extrude Cut, target = B, **Blind 4.2** (= splitTongueDepth + 0.2), +X (벽 안쪽으로)
3. 우측 X = +103 동일 → Extrude Cut Blind 4.2, -X 방향

##### Step B11 — Cross-screw clearance (drum 측 ↓ 볼트 진입)

1. Plate 윗면 (Z=5) → Sketch "B_clearance":
   - ∅3.2 원 4개:
     - 좌측 split 매칭: (X=-98, Y=-10), (-98, +10) — A의 ridge insert 위치와 정렬
     - 우측 split 매칭: (X=+98, -10), (+98, +10)
2. Extrude Cut, target = B, **Through (5mm)**, -Z
   - → B plate 관통 ∅3.2 holes 4개

> **조립**: A의 ridge가 B 하면 X=-103~-93 영역을 받침. B 윗면에서 (-98, ±10)으로 M3 6mm 볼트 ↓ → B plate 5mm 통과 → A의 ridge 안 insert에 박힘. 우측 (+98, ±10)도 C의 ridge로 동일.

---

#### 7.4.3 Section C — 우측 (A의 Mirror)

##### Step C1 — Mirror feature

1. Feature toolbar → **Mirror**
2. Mirror plane: **Right plane** (YZ-plane, X=0)
3. Parts to mirror: **`Bottom Plate A` 솔리드**만 (B 제외)
4. Confirm → C 솔리드 생성

→ A의 모든 features (D1·D2 hole 패턴, perimeter groove C-shape, tongue, ridge + insert) X 부호 자동 반전:
- D1 → D6 (X=-265 → +265, ∅60)
- D2 → D5 (X=-170 → +170, ∅90)
- Tongue: X=+103 face, **-X 방향** 4mm
- Ridge: 하면 X=+93 ~ +113 영역, insert at (+98, ±10)

##### Step C2 — 파트 이름

Parts 패널 → Mirror 결과 → Rename **`Bottom Plate C`**.

---

#### 7.4.4 정렬 검증 (모델링 후)

| 검증 | 기대 |
|---|---|
| A·B·C 3 솔리드 분리 | Parts 패널 3개, 미접촉 |
| A tongue X=-103~-99 ↔ B 좌 groove X=-103~-98.8 | 4mm 오버랩 + 0.2 clearance |
| A ridge X=-113~-93 영역 ↔ B 하면 X=-103~-93 받침 | 10mm 받침 |
| A insert (X=-98, ±10) ↔ B clearance (-98, ±10) | 동축 |
| 둘레 홈 (3 section 합) | 직사각 ring 610 × 110, 폭 3.4, 깊이 4 |
| 모든 모터 샤프트 hole 6개 | drumX_n에 ∅6 |
| 모든 Hall slot 6개 | (drumX, ±15 or ±30) |

---

### 7.5 STL 출력

각 section 별도 STL:
- `cad/stl/bottom_plate_A.stl`
- `cad/stl/bottom_plate_B.stl`
- `cad/stl/bottom_plate_C.stl`

설정:
- 재료: PETG, Layer 0.2 mm, Infill 30~40% (모터·plate 결합 인서트 응력)
- 출력 방향: 윗면(드럼 측)이 위 — 모터 마운트 hole이 정밀 출력
- 베드 200mm 한계 안 (각 section 207mm) — 빠듯하면 베드 중심 정렬

---
