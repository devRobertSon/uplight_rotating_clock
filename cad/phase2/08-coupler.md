## 5.7 Part Studio "05 Coupler"

보유 커플러 (B02, ∅5↔∅5 유연 × 10) 사용. CAD에서는 **간섭 체크용 placeholder**만 모델링. 헬리컬 슬롯·M3 set screw 홀 등 디테일은 모델 생략.

### 5.7.1 보유 커플러 사양 (실측 freeze)

| 항목 | 값 | 변수 |
|---|---|---|
| 외경 (OD) | ∅19 mm | `#couplerOD` |
| 길이 (L) | 25 mm | `#couplerLength` |
| 내경 | ∅5 mm | `#shaftDiameter` (재사용) |

§2.3 변수 표에 `couplerOD = 19 mm`, `couplerLength = 25 mm` 추가 필요. 다른 보유품과 마찬가지로 freeze.

### 5.7.2 Part Studio 생성 + Variable 연결

1. 탭바 `+` → **Part Studio**, 이름 `05 Coupler`
2. `Feature ▾` → **Variable Studio** → `Clock Config`

### 5.7.3 Feature tree

#### Step 1 — Sketch "coupler outline" (Top plane)

베어링과 동일 방식 — 동심원 2개로 ring 스케치.

| 항목 | 값 |
|---|---|
| 평면 | Top |
| 도구 | Center point circle × **2** (동심원) |
| 중심 (둘 다) | 원점 (Coincident) |
| 외측 Diameter | `#couplerOD` (= 19) |
| 내측 Diameter | `#shaftDiameter` (= 5) |

→ Sketch 종료.

#### Step 2 — Extrude "coupler ring"

| 칸 | 값 |
|---|---|
| Profile | 두 원 사이 **ring(annulus) 영역만** 선택 (가운데 작은 원 안쪽 X) |
| Type | **New** (새 솔리드 파트) |
| End | **Blind** |
| Depth | `#couplerLength` (= 25) |
| Direction | +Z |

→ ∅19 외경 / ∅5 내경 × 25 mm ring (양쪽 통과 보어 자동).

> ring 영역이 한 번에 안 잡히면 두 영역(ring + 가운데 disc) 모두 New 추가 후 별도 Extrude Remove로 가운데 disc 빼기.

### 5.7.4 파트 이름 정리

Parts 패널 → `Part 1` Rename → **`Coupler (placeholder)`**.

### 5.7.5 검증

| 확인 | 기대 |
|---|---|
| 외형 | ∅19 × 25 |
| 관통 보어 | ∅5 |
| Parts 패널 | 1개 (`Coupler (placeholder)`) |

### 5.7.6 placeholder 모델 한계

실제 커플러는:
- 헬리컬 슬롯 (유연성)
- 양쪽 끝 M3 set screw 홀 (샤프트 잠금)

이 디테일은 모델 생략. Assembly에서 **외형 envelope·길이 간섭 체크용**으로만 사용.

### 5.7.7 (선택) 3D 프린트 헬리컬 커플러 — Phase 6 본편

자체 출력 시 추가 작업 (Onshape 중급, 30분):
1. **Helix feature** (Curves 메뉴) — 축, pitch 8 mm, 길이 16 mm
2. 작은 직사각형 단면 sketch (1 mm × 5 mm)
3. **Sweep Cut** (단면을 helix 따라 스윕)
4. 양쪽 끝 솔리드 영역에 M3 (∅3.2 또는 `#insertHole` for heat insert) 측면 홀

본 브리프엔 미상세. 필요 시 별도 절차.

---

