## 7. Part Studio "07 Bottom Plate"

박스 뚜껑 + 6 모터 마운트 + 6 Hall 센서 자리 + 6 LED 슬롯 + 측면 판 둘레 홈.

### 7.1 Part Studio 생성 + Variable 연결
1. 탭바 `+` → **Part Studio**, 이름 `07 Bottom Plate`
2. Variable Studio → `Clock Config`

### 7.2 Feature tree

#### Step 1 — Sketch "plate outline" (Top plane)

| 항목 | 값 |
|---|---|
| 도구 | Center point rectangle, 원점 중심 |
| Width (X) | `#enclosureWidth` (= 620) |
| Depth (Y) | `#enclosureDepth` (= 120) |

#### Step 2 — Extrude "plate body"
- Type: New, Blind, Depth `#plateThickness` (= 5), +Z

→ 620 × 120 × 5 plate.

#### Step 3 — Sketch "6 motor + Hall + LED holes" (top face)

윗면에 6세트의 홀 패턴. 각 드럼 위치 (X = `#drumX_1` ~ `#drumX_6`)에서:

**모터 샤프트 통과 (X, +11)** — D28 motor mount plate +Y 11 정렬:
- ∅6 (∅5 모터 샤프트 clearance)

**모터 마운트 4 코너 (X, +11±15) and (X±20, +11±15)** — 모터 마운트 plate 코너:
- 4× ∅3.2 at (X-20, +4), (X+20, +4), (X-20, -26), (X+20, -26)

**Hall 센서 통과 슬롯** — 자석 궤적 R 위치:
- ∅90 드럼 (D2-D5): 자석 R=30, sensor 위치 (X, +30)
- ∅60 드럼 (D1, D6): 자석 R=15, sensor 위치 (X, +15)
- 슬롯: 5 × 8 mm 직사각형 (TO-92 본체 + 와이어)

**LED 와이어 통과 홀** — 각 드럼 전면 LED 바 와이어용:
- ∅5 hole at (X, -45) 정도 (드럼 정면, 판 끝 가까이)

> 위치는 6 드럼 모두 반복. 1 세트 만든 뒤 Linear Pattern으로 6 복제 가능 (각자 X 위치 차이).

#### Step 4 — Extrude Cut (Through all)
모든 홀·슬롯 선택 → Remove → Through all.

#### Step 5 — Sketch "perimeter groove for side panels" (top face)

측면 판이 끼워질 둘레 홈:
- 외측: 판 외곽에서 안쪽으로 5mm 들어간 위치 (groove 외측 라인)
- 내측: 외측에서 +`#panelGrooveWidth` 만큼 안쪽 (= 8.4mm)
- 직사각형 ring 영역

#### Step 6 — Extrude Cut "perimeter groove"
- Type: Remove
- End: Blind, Depth `#panelGrooveDepth` (= 4)
- 방향: -Z (판 안으로 4mm 깎임)

→ 판 윗면 둘레 ring 형태의 홈. 측면 판이 위에서 끼워짐.

### 7.3 파트 이름 정리
Parts 패널 → Rename → **`Bottom Plate`**.

### 7.4 분할 설계 (§1.5 적용)

Plate 가로 620mm → **3 sections (A/B/C, 각 ~207mm)**. 분할선 X = ±103.

각 section은 별도 Onshape 파트로 모델링:
- Section A (좌): 드럼 D1·D2 모터·Hall·LED 홀 포함 (drumX_1 = -265, drumX_2 = -170)
- Section B (중): D3·D4 (drumX_3 = -65, drumX_4 = +65) + 콜론 영역
- Section C (우): D5·D6 (drumX_5 = +170, drumX_6 = +265)

5mm 두께 plate라 박스보다 단순. 각 section 별도 sketch + extrude:

#### Step S1 — Section A (좌) 모델링
1. Top plane → Sketch: Center point rectangle, 중심 (-206.5, 0), 207 × 120
2. Extrude New, Blind, Depth `#plateThickness` (= 5)
3. 모터·Hall·LED·둘레 홈은 §7.2와 같으나 X 범위 -310 ~ -103 한정 (D1, D2 드럼만)
4. **Tongue**: 우측 X = -103 면 → Sketch 사각형 8 × 5 (`splitTongueWidth × plateThickness`) → Extrude Add, Depth 4 (+X 방향)
5. **Cross-screw용 ridge (under-flange) — §1.5 hidden-bolt 규칙**:
   - Plate 두께 5mm 직접 cross-screw 어려움 → **drum 측 (= 내부)에서 Z방향으로 볼트 잠금**, 한쪽 section에 ridge 부착하여 결합부 형성
   - Plate **하면**에 ridge 추가: Sketch on bottom face, X = **-113 ~ -93** (= split 너머 +10mm, B section 아래로 연장) × Y 폭 30 (둘레 홈 안쪽 영역, 모터·홀 회피)
   - Extrude Add, Depth 5 (-Z), plate 아래로 5mm 돌출
   - → Ridge가 split 지나 B section plate 아래까지 연장되어 받침
   - Ridge에 heat insert 홀 2개 (Y = ±40, ridge 상면에서 sketch): ∅`#insertHole` (= 4.2), Blind Depth 5 (-Z, ridge 안으로)
   - **B section 쪽** (다음 S2 단계): plate에 ∅3.2 clearance 홀, Through Z (위→아래)
   - **조립**: B's plate를 A's ridge 위에 정렬 → **B의 top face (드럼 측 = 내부)에서 ↓로 M3 볼트 삽입** → B's plate 5mm 통과 → A's ridge 안 heat insert에 박힘
   - **Bolt head는 B's plate 윗면 = 드럼 측 = 내부, 가시 영역 아님**

#### Step S2 — Section B (중)
1. Top plane → Sketch 206 × 120, 원점 중심
2. Extrude New, Blind, Depth 5
3. D3·D4 드럼 위치의 모터·Hall·LED·둘레 홈
4. **Groove 양 끝**: 양 X = ±103 면에 sketch 8.4 × 5 → Extrude Cut, Depth 4.2 (-X 또는 +X 방향)
5. **Cross-screw clearance** (§1.5 hidden-bolt — heat insert는 A·C ridge에, B는 clearance만):
   - Plate 윗면 (드럼 측, 내부)에 sketch
   - 양 끝 (X = -100, +100 — split 안쪽 3mm 위치) × Y = ±40, 총 4개
   - ∅3.2 clearance, Through Z (위→아래) — 5mm plate 관통
   - 조립 시 A·C section의 ridge가 B's plate 아래로 연장되어 받침. B의 top face에서 ↓ M3 볼트 삽입.
   - **Bolt head는 B's top face = 드럼 측 = 내부**

#### Step S3 — Section C (우)
Section A 거울. X 범위 +103 ~ +310, D5·D6 드럼 부분.

> 둘레 홈은 split line과 만나도 그대로 통과 (각 section의 perimeter groove가 독립적으로 이어짐).

### 7.5 STL 출력
- 재료: PETG, Layer 0.2 mm, Infill 30~40%
- 분할 출력 (3등분 도브테일) 권장 — 일반 베드(200~250mm)에서 출력 가능
- 분할 위치: 드럼 사이 빈 공간 (D2-D3 사이, D4-D5 사이)

---

