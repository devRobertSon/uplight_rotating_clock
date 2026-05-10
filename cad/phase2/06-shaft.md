## 5. Part Studio "03 Shaft"

샤프트 단일 파트만 모델링. 베어링은 §5.5에서 별도 Part Studio로.

### 5.1 Part Studio 생성 + Variable 연결

1. 탭바 `+` → **Part Studio**, 이름 `03 Shaft`
2. `Feature ▾` → **Variable Studio** → `Clock Config`

### 5.2 Feature tree

#### Step 1 — Sketch on Top plane

| 항목 | 값 |
|---|---|
| 평면 | Top |
| 도구 | Center point circle |
| 중심 | 원점 (Coincident) |
| Diameter | `#shaftDiameter` (= 5) |

→ Sketch 종료.

#### Step 2 — Extrude "Shaft"

| 칸 | 값 |
|---|---|
| Profile | Step 1 sketch |
| Type | **New** (새 솔리드 파트) |
| End | **Blind** |
| Depth | `#shaftLength` (= 100) |
| Direction | +Z (위쪽) |

→ ∅5 × 100 mm 원기둥.

#### Step 3 (선택) — Chamfer 양 끝

조립 시 끼움 용이하게 양 끝 edge에 0.3 mm Chamfer (또는 Fillet).
- 실제 SUS304 연마봉은 보통 모따기 되어 있어 생략 가능.

### 5.3 파트 이름 정리
Parts 패널의 `Part 1` 우클릭 → Rename → **`Shaft`**.

---

