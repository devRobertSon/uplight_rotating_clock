## 5.5 Part Studio "04 Bearing 625ZZ"

베어링도 단일 파트만. 샤프트와 별개 Part Studio.

### 5.5.1 Part Studio 생성 + Variable 연결
1. 탭바 `+` → **Part Studio**, 이름 `04 Bearing 625ZZ`
2. `Feature ▾` → **Variable Studio** → `Clock Config`

### 5.5.2 Feature tree

#### Step 1 — Sketch on Top plane

| 항목 | 값 |
|---|---|
| 평면 | Top |
| 도구 | Center point circle × **2** (동심원) |
| 중심 (둘 다) | 원점 (Coincident) |
| 외측 Diameter | `#bearingOD` (= 16) |
| 내측 Diameter | `#bearingID` (= 5) |

→ Sketch 종료.

#### Step 2 — Extrude "Bearing 625ZZ"

| 칸 | 값 |
|---|---|
| Profile | 두 원 사이 **ring(annulus) 영역만** 선택 (가운데 작은 원 안쪽 X) |
| Type | **New** |
| End | **Blind** |
| Depth | `#bearingWidth` (= 5) |
| Direction | +Z |

→ ∅16 외경 / ∅5 내경 × 5 mm ring 1개.

> ring 영역이 한 영역으로 안 잡히면: 두 영역 모두 New 추가 후 별도 Extrude Remove로 가운데 disc 빼기.

### 5.5.3 파트 이름 정리
Parts 패널의 `Part 1` → Rename → **`Bearing 625ZZ`**.

> 실제 부품은 내부 볼·실드 있으나 모델은 외형 ring만 (간섭 체크 충분).

---

