## 8.5 Part Studio "09 Top Plate"

상부 판. 6 베어링 시트 + 측면 판 ring 상단 결합용 둘레 홈.

### 8.5.1 Part Studio 생성 + Variable 연결
1. 탭바 `+` → **Part Studio**, 이름 `09 Top Plate`
2. Variable Studio → `Clock Config`

### 8.5.2 Feature tree

#### Step 1 — Sketch "plate outline" (Top plane)
- Rectangle, 원점 중심, `#enclosureWidth` × `#enclosureDepth` (620 × 120)

#### Step 2 — Extrude "plate body"
- New, Blind, Depth `#plateThickness` (= 5), +Z

#### Step 3 — Sketch "6 bearing seats + shaft holes" (top face)

각 드럼 위치 (X = `#drumX_1` ~ `#drumX_6`, Y = +11 — 모터 mount plate +Y 11과 동일 정렬)에 동심원 2개:

| 홀 | Diameter | End |
|---|---|---|
| 베어링 시트 (외) | `#bearingOD` (= 16) | Blind 5 |
| 샤프트 (내) | `#shaftHole` (= 5.2) | Through |

#### Step 4 — Extrude Cut "shaft holes (Through)"
샤프트 ∅5.2 원 6개 → Remove, Through all.

#### Step 5 — Extrude Cut "bearing seats (Blind 5)"
베어링 ring (∅16 ~ ∅5.2 사이) 6개 → Remove, Blind `#bearingWidth` (= 5), -Z.

#### Step 6 — Sketch "perimeter groove" (bottom face)

판 **아랫면**에 둘레 홈 (07 Bottom Plate와 동일 위치):
- 외측: 판 외곽에서 5mm 안쪽
- 내측: 외측에서 +3.4 mm 안쪽
- 직사각형 ring

#### Step 7 — Extrude Cut "perimeter groove"
- Remove, Blind, Depth `#panelGrooveDepth` (= 4)
- 방향: +Z (판 안으로)

→ 측면 판 ring 상단이 위에서 이 홈으로 끼워짐.

### 8.5.3 파트 이름 정리
Parts 패널 → Rename → **`Top Plate`**.

### 8.5.4 분할 설계 (§1.5 적용) — **외부 visible 차단 (시계 천장)**

**07 Bottom Plate와 같은 구조·같은 X 위치 분할**. 3 sections (A/B/C, X = ±103):
- Section A: 베어링 시트 D1·D2 (drumX_1, drumX_2), 우측 X=-103에 tongue
- Section B: D3·D4 (drumX_3, drumX_4), 양 끝 X=±103에 groove
- Section C: D5·D6 (drumX_5, drumX_6), 좌측 X=+103에 tongue

각 section 모델링은 §7.4와 거의 동일 — 차이점 (외부 visible 차단):
- Plate 윗면 대신 **아랫면**에 둘레 홈 (Top Plate는 측면 ring 위에 덮어씀)
- 각 베어링 시트는 §8.5.2 Step 3·4·5 그대로 (∅16 Blind 5 + ∅5.2 Through)
- **Cross-screw 방향 거꾸로** (Bottom Plate와 반대):
  - Ridge: §7.4와 동일하게 plate **하면**에 부착 (-Z, 5mm 돌출, X=-113~-93)
    → **Ridge가 드럼 영역 (= 내부) 안으로** 향함 (Top Plate의 plate-아래 = 드럼 측)
  - Heat insert: A·C section의 ridge **하면**에서 sketch (ridge 바닥에서 위로 ↑) → ∅`#insertHole` Blind Depth 5 (+Z 방향, ridge 안으로)
  - Clearance: B section plate에 ∅3.2 Through Z (위 ↔ 아래)
  - **조립**: Top Plate를 뒤집은 채로 (윗면이 아래로) 사전 조립. 이 자세에서 ridge는 위로 향함, B's plate 하면이 위에 보임. **Bolt를 B's bottom face (현재 위로 향함, 드럼 측)에서 ↓로 삽입** (실질 +Z) → A's ridge 안 heat insert에 박힘.
  - 조립 후 plate를 정상 자세로 뒤집으면 **bolt head는 plate 아랫면 = 드럼 측 = 내부**, 외부 (시계 천장)는 매끄러움.

> Top Plate split이 Bottom Plate split과 X 정확 일치 → 측면 panel ring이 두 plate 사이에서 수직 alignment 자연 보장. 조립 시 panel 둘레 홈에 정확히 들어감.
>
> **요약**: Bottom Plate ridge는 박스 cavity 측, Top Plate ridge는 드럼 영역 측. 둘 다 plate 아래로 돌출하지만 "아래"의 의미가 다름 (조립 후 wrt 시계). 외부 visible 차단을 위해 두 plate의 ridge·bolt 방향이 시계 중심 기준으로 **대칭**.

### 8.5.5 STL 출력
- PETG, Layer 0.2 mm, Infill 30%
- 분할 출력 (3등분) 권장

---
