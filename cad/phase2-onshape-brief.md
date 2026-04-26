# Phase 2 — Onshape CAD 작업 지시서 (프로토 1자리)

> 목적: ∅90 드럼 1개를 풀 어셈블리로 모델링하여 STEP/STL 익스포트 + 아크릴 DXF 추출. Phase 6 본편(6자리) 재사용을 고려해 **모든 치수를 글로벌 변수로 노출**하고, Part Studio 단위로 파트 분리.

---

## 0. Onshape 탭 역할과 본 브리프 매핑

| 탭 | 실제 용도 | 본 프로젝트 용도 | 해당 섹션 |
|---|---|---|---|
| **Variable Studio** | 글로벌 변수 정의 (UI 폼: Name·Value·Description 입력) | 모든 치수 변수 | §2 |
| **Feature Studio** | 커스텀 FeatureScript 코드 작성 (함수·자체 피처) | **사용 안 함** | — |
| **Part Studio** | 스케치·피처 트리로 파트 모델링 | 파트 7종 각 1 스튜디오 | §3 ~ §8 |
| **Assembly** | Part Studio 파트 인스턴스 배치 + Mate | 프로토 1자리 1 어셈블리 | §9 |

### 변수 이름 `#` 기호 규칙
- **Variable Studio UI Name 칸 (정의)**: `#` 없이 — `capThickness`
- **Variable Studio UI Value 칸 (다른 변수 참조 시)**: `#` 붙여서, **`=` 기호 없이** — `2 * #capThickness + #drumVisibleHeight`
- **Part Studio 치수 입력창**: `#` 붙여서 — `#drumDiameter_90`

> 요약: **정의할 때는 `#` 없이, 참조할 때는 무조건 `#` 붙임.** 계산식에는 `=` 를 쓰지 않는다 (Onshape가 자동 계산).

### 변수 타입 (Type 칸)

Variable Studio 각 행에는 **Type** 드롭다운이 있음. 아래 둘 중 하나로 지정.

| Type | 언제 선택 | 단위 예시 |
|---|---|---|
| **Length** | 길이·거리 (mm, inch 등) | `90 mm`, `5.2 mm` |
| **Number** | 개수·비례수 등 **단위 없는 정수·실수** | `10` (면 수), `7` |
| **Angle** | (본 프로젝트에선 사용 없음, 참고용) | `36 deg` |

> **주의**: `drumFaces_90` 같은 면 개수는 반드시 **Number** 로 지정. Length로 두면 `sin(180 deg / #drumFaces_90)` 같은 식에서 단위 불일치 오류.

---

## 1. Onshape 문서 구조

```
📄 Document: "Uplight Rotating Clock"
├── 📐 Variable Studio: "Clock Config"        (글로벌 변수 정의)
├── 🔧 Part Studio: "01 Drum ∅90 Caps"       (상·하부 캡 2파트, variant)
├── 🔧 Part Studio: "02 Acrylic Panel 90"    (27.8 × 55 × 3mm 단일파트)
├── 🔧 Part Studio: "03 Shaft & Bearing"     (Ø5×75, 625ZZ)
├── 🔧 Part Studio: "04 Coupler"             (3D프린트 헬리컬 빔)
├── 🔧 Part Studio: "05 Motor Mount"         (28BYJ-48 브라켓)
├── 🔧 Part Studio: "06 Hall Bracket"        (A3144 + 풀업)
├── 🔧 Part Studio: "07 Frame Section"       (1자리 폭 프레임)
└── 🗂 Assembly: "Proto 1-Digit"
```

> **Feature Studio 탭은 사용하지 않음**. Feature Studio는 커스텀 FeatureScript 코드(함수·자체 피처) 작성 전용이며, Phase 2에서는 불필요. Onshape의 변수는 **Variable Studio** 탭에서 관리.

### 기존 결정 참조
- 드럼 외관·치수: [`../docs/spec.md`](../docs/spec.md) §2
- 드럼 3면도: [`../images/drum_structure.svg`](../images/drum_structure.svg) Panel 1·2·3
- 캡 도면: [`../images/cap_drawings.svg`](../images/cap_drawings.svg), [`../images/bottom_cap_detail.svg`](../images/bottom_cap_detail.svg)
- D14 베어링 625ZZ, D15 3D프린트 커플러

---

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

> 드럼 직경·면 수는 ∅90과 ∅60이 다르므로 **둘 다 별개 변수로** 등록. `panelWidth` 계산식에도 하드코딩된 60 · 7이 들어가지 않도록 `_60` 변수를 참조.

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
| `slotLength_90` | Length | `2 * (#apothem_90 - #slotWidth) * tan(180 deg / #drumFaces_90) - 0.5 mm` | 자동 계산: 25.23 mm (인접 슬롯 비겹침 한계 - 0.5 안전 마진) |
| `slotLength_60` | Length | `2 * (#apothem_60 - #slotWidth) * tan(180 deg / #drumFaces_60) - 0.5 mm` | 자동 계산: 22.44 mm |
| `panelWidth_90` | Length | `#slotLength_90 - 0.2 mm` | 자동 계산: 25.03 mm (슬롯 길이 - 양쪽 0.2 clearance) |
| `panelWidth_60` | Length | `#slotLength_60 - 0.2 mm` | 자동 계산: 22.24 mm |
| `slotDepth_top` | Length | `3 mm` | 상부 캡 슬롯 깊이 (관통 X) |
| `slotDepth_bottom` | Length | `#capThickness` | 하부 캡 슬롯 깊이 = 6 mm (관통 O) |
| `hubBossHeight` | Length | `6 mm` | 캡 위로 돌출되는 허브 보스 높이 (M3 홀 자리 확보용) |
| `hubOuter_90` | Length | `20 mm` | ∅90 캡 허브 외경 |
| `hubOuter_60` | Length | `18 mm` | ∅60 캡 (D13, 인서트 OD5 수용) |
| `shaftHole` | Length | `5.2 mm` | 축 홀 (+0.2/-0) |
| `insertHole` | Length | `4.2 mm` | M3 히트 인서트 OD5×L4 |
| `magnetDiameter` | Length | `4.1 mm` | 자석 ∅4 + 0.1 여유 |
| `magnetDepth` | Length | `2.1 mm` | 자석 ∅4×2 + 0.1 여유 |
| `magnetRadius_90` | Length | `30 mm` | ∅90 드럼 중심 → 자석 중심 |
| `magnetRadius_60` | Length | `15 mm` | ∅60 드럼 |
| `baffleThickness` | Length | `2 mm` | 배플 두께 |
| `gapInner` | Length | `15 mm` | HH·MM 내부 간격 (D22) |
| `gapOuter` | Length | `20 mm` | 요일/날씨 ↔ 숫자 |
| `gapColon` | Length | `40 mm` | 콜론 |
| `shaftDiameter` | Length | `5 mm` | |
| `shaftLength` | Length | `75 mm` | |
| `bearingOD` | Length | `16 mm` | 625ZZ 외경 |
| `bearingID` | Length | `5 mm` | |
| `bearingWidth` | Length | `5 mm` | |

### 2.4 Part Studio에서 변수 참조하기

각 Part Studio 맨 위에 **Variable Studio 피처**를 삽입하여 연결:

1. Part Studio 좌상단 `Feature ▾` → `Variable Studio` 선택
2. Reference 대화상자에서 `Clock Config` 선택 → ✓
3. 이후 스케치 치수 입력창에 `#drumDiameter_90` 등으로 사용

---

## 3. Part Studio "01 Drum ∅90 Caps"

> **참조 도면**: [`../images/cap_drawings.svg`](../images/cap_drawings.svg) §1 (∅90 캡 평면도·단면도·치수표) 및 [`../images/bottom_cap_detail.svg`](../images/bottom_cap_detail.svg) (하부 캡 상세).

### 3.0 형상 요점 (모델링 전 필수 이해)

| 항목 | 값 | 모델링 영향 |
|---|---|---|
| 외형 | 정**십각형** (꼭짓점 외접원 ∅90) | "원"으로 그리지 말 것. 정다각형 도구 사용 |
| 두께 | 6 mm 평판 (보스 없음) | Extrude 한 번 |
| 허브 보스 | ∅20 × 6 mm 원기둥, 캡 위로 돌출 | M3 인서트 홀이 슬롯과 겹치지 않게 별도 피처로 추가 |
| 슬롯 | 3.2(radial) × 25.23(tangential) × 10개 | 폴리곤 변에 평행, 변에서 안쪽으로 3.2 깊이. 길이 = 비겹침 한계 (panel chord 27.81 보다 2.6 짧음). **상부 캡 깊이 3 mm (블라인드)**, 하부 캡 6 mm 관통 |
| 축 홀 | ∅5.2 관통 (+0.2/-0) | 캡 중심 |
| M3 세트스크류 | **외주 → 허브 → 축 수평 관통** | 캡 두께 중심선 (z = 3mm)에 ∅4.2 가로 홀 |
| 자석 포켓 | ∅4.1 × 깊이 2.1, R30, 2개 | 하부 캡 한정 (Configuration variant) |

### 3.1 Onshape 치수 입력 규칙 (이 섹션 내 모든 단계 공통)

| 스케치 요소 | 치수 종류 | Value 칸 입력 | 변수 형태 |
|---|---|---|---|
| 원 (Circle) | **Diameter** (기본) | 지름값 그대로 | `#drumDiameter_90`, `#shaftHole` 등 |
| 정다각형 (Polygon, **Inscribed mode**) | 꼭짓점 외접원 지름 | 지름값 그대로 | `#drumDiameter_90` |
| 직사각형 (Slot/Rectangle) | 폭(radial) / 길이(tangential) | 그대로 | `#slotWidth` × `#slotLength_90` |
| 원의 중심 거리 | **Linear** (반경 위치) | 반경값 | `30 mm` (= `#magnetRadius_90`) |

> **`/2` 거의 안 씀**. Onshape는 원에 대해 기본이 지름(Diameter). 반경이 필요한 경우는 *위치 거리*(중심에서 ~mm 떨어진 점 위치)뿐.

---

### 3.2 Feature tree (순서대로)

#### Step 1 — Variable Studio 연결
- 좌측 Feature 패널 상단 `Insert ▾` → `Variable Studio` 선택 → `Clock Config` 체크 → ✓
- 이후 모든 스케치에서 `#변수명` 참조 가능

#### Step 2 — Sketch "base" (Top plane)
빈 스케치를 Top plane에 생성 후, 아래 3개 요소 그리기:

1. **외형: 정십각형**
   - Toolbar → `Polygon` (정다각형) 선택
   - 옵션: **Number of sides = `#drumFaces_90`** (= 10)
   - 옵션: **Inscribed polygon** 선택 (Circumscribed 아님)
     - Inscribed = 폴리곤이 원 **안쪽**, 꼭짓점이 원에 닿음 → 입력값이 **꼭짓점 외접원 지름**
     - 우리 사양은 "∅90 (vertex 기준)" 이므로 Inscribed
     - Circumscribed를 고르면 ∅90이 변 중앙 거리가 되어 실제 외형이 ∅94.6mm로 커짐 (NG)
   - 중심: 원점 (0, 0)에 클릭
   - 외접원 지름 입력: `#drumDiameter_90`
   - **회전**: 슬롯 0 위치를 X축 음수 방향(전면)으로 맞추기 위해 폴리곤 한 변이 X축에 수직이 되도록 회전. 일단 기본 방향으로 그린 뒤 §3.3 검증 단계에서 조정.

2. **허브 표시 원** (구성선, 치수 참조용 — 실 형상 아님)
   - Circle 도구로 원점에 원
   - **Construction line** 으로 변경 (선택 후 `Q` 키 또는 우클릭 → Construction)
   - Diameter 치수: `#hubOuter_90` (= 20)

3. **축 홀**
   - Circle 도구로 원점에 작은 원
   - Diameter 치수: `#shaftHole` (= 5.2)

스케치 종료 후 닫기.

#### Step 3 — Extrude "cap body"
- Step 2의 스케치 선택
- 영역 선택: 정십각형 영역 - 축 홀 (도넛 형태). Onshape가 자동으로 영역 분할
- 작업: **New** (솔리드 파트 생성)
- 깊이: `#capThickness` (= 6)
- 방향: Top plane 기준 한 방향 (Symmetric 안 씀)
- → 결과: 6mm 두께의 십각 디스크, 중앙에 축 홀 관통

#### Step 4 — Sketch "slot 0" (Top face of disk)

> ⚠️ **슬롯은 폴리곤 변에 평행한 짧고 넓은 직사각형**. 패널(3 × 27.8 × 55)이 위→아래로 끼워지면 top view 단면이 그대로 슬롯 footprint가 됨.
> - 폭(radial) = 3.2 mm (= 패널 두께 + 0.2 공차)
> - 길이(tangential) = 27.8 mm (= 패널 폭, 폴리곤 변 chord와 동일)
>
> ```
> 슬롯 footprint (top view, ∅90 캡의 -Y 변):
>
>      ┌────────────────────┐
>     /                      \
>    /     ●  원점            \
>   │                          │
>   │                          │
>    \                        /
>     \  ┌──────────────┐    /
>      \ │     slot 0   │   /   ← 길이 27.8 (X 방향, tangential)
>       \└──────────────┘  /
>        ╲────────────────╱        ↕ 폭 3.2 (Y 방향, radial)
>             폴리곤 변
> ```

- 디스크 윗면(top face) 클릭 → New Sketch
- Toolbar → `Center point rectangle`
- 폴리곤 -Y 변 안쪽 적당한 위치에 직사각형을 대충 그린 뒤 (정확한 좌표 입력 불필요), 아래 두 constraint로 위치 자동 결정:
  1. **수평 정렬**: 직사각형 중심점을 Y축에 **Coincident** (또는 X = 0 치수)
  2. **외측 변 정렬**: 직사각형의 외측 긴 변(아래쪽)을 폴리곤의 -Y 변에 **Coincident**
- 직사각형 두 치수 명시:
  - **Width (X 방향, tangential)** = `#slotLength_90` (≈ 25.23 mm) ← **긴 변**
  - **Height (Y 방향, radial)** = `#slotWidth` (= 3.2 mm) ← **짧은 변**
- 결과: 슬롯 중심이 자동으로 (0, -(`#apothem_90` - `#slotWidth`/2)) ≈ (0, -41.2)에 위치, 외측 긴 변이 폴리곤 -Y 변과 겹쳐짐 (폴리곤 변 27.81 보다 슬롯이 약 2.6 mm 짧아 vertex 양쪽에 ~1.3 mm 솔리드 띠 남음 — 인접 슬롯과 겹침 방지).
- ✅ 검증: 슬롯이 "폴리곤 한 변과 평행한 작은 띠" 모양인지 눈으로 확인. "캡 중심을 향해 깊숙이 박힌 막대"면 잘못 (이건 패널 *높이* 방향과 혼동한 것).

> **Tip**: 슬롯 외측 긴 변을 폴리곤 -Y 변에 **Coincident** constraint 잡으면 Y=-41.2 자동. 슬롯 가로 길이는 폴리곤 변보다 짧으므로 끝점 Coincident는 사용 금지 (variable로 길이 결정).
> **왜 폴리곤 변 길이 그대로 안 쓰나**: panel chord 27.81 사용 시 인접 슬롯이 vertex 안쪽에서 1.88 mm 겹침 발생, 패턴 후 캡 내부 전체가 절단됨. `slotLength_90` 변수가 비겹침 한계 (= `2·(apothem-slotWidth)·tan(180°/n) - 0.5`)를 자동 계산.

#### Step 5 — Extrude Cut "slot 0"

| 캡 variant | 끝 조건 | 깊이 |
|---|---|---|
| 상부 (`isBottomCap = false`) | **Blind** | `#slotDepth_top` = 3 mm (관통 X) |
| 하부 (`isBottomCap = true`) | **Through all** 또는 Blind | `#slotDepth_bottom` = 6 mm (관통 O) |

- Step 4 스케치 선택 → 직사각형 영역
- 작업: **Remove** (= Cut)
- 방향: 디스크 두께 안쪽으로 (top face에서 -Z 방향)
- → 결과: 슬롯 0 자리에 3.2 × 27.8 footprint × 깊이 3 또는 6 의 컷

#### Step 6 — Circular Pattern "10 slots"

> ⚠️ 이 단계의 가장 흔한 실수: **회전축을 잘못 선택**해서 슬롯들이 비틀어진 링 모양으로 패턴되는 것. 아래 절차를 그대로 따라야 안전.

1. Toolbar → **Pattern ▾** → **Circular pattern** 선택 (피처 패턴, 스케치 패턴 아님)
2. 다이얼로그 입력:

| 칸 | 입력값 | 선택 방법 |
|---|---|---|
| Pattern type | `Feature pattern` | 드롭다운 |
| Features to pattern | Step 5의 "Slot 0" Extrude (Remove) | Feature 트리에서 클릭 |
| **Axis of rotation** | **Origin > Z axis** | ★ 아래 설명 ★ |
| Equal spacing | ✅ ON | 체크 |
| Number of instances | `#drumFaces_90` | (= 10) |
| Total angle | `360 deg` | |

3. **Axis of rotation 정확히 선택하는 법** (이게 가장 중요):
   - Direction 칸 클릭하여 활성화
   - **모델 화면에서 직접 클릭하지 말 것** — 모서리·면이 잡혀서 회전축이 어긋남
   - 좌측 **Feature 트리에서 `Origin` 펼치기** → `Z` 항목 클릭
   - Direction 칸에 `Origin Z` (또는 동등 표기) 들어감
4. ✓ 확정

→ 결과: 10개 슬롯이 36° 등간격으로 모두 관통

#### Step 6에서 슬롯이 비틀어진 링으로 나올 때

증상: 빨간 슬롯 와이어프레임이 캡 외곽 근처에 어긋난 각도로 겹쳐 보임 (캡 본체는 정상).
원인: 회전축이 슬롯 모서리/캡 외곽 원형 엣지/평면 등 엉뚱한 것으로 잡혔음.

해결:
1. Feature 트리에서 잘못된 `Circular Pattern` 우클릭 → Delete
2. Step 5 Extrude는 그대로 유지 (1개 슬롯이 정상이면 OK)
3. 위 Step 6를 **Origin → Z axis** 로 재실행

#### Step 6.5 — Sketch + Extrude "Hub Boss"

> 왜 추가하는가: 슬롯이 z=3~6 (상부 캡 Blind) 또는 z=0~6 (하부 캡 Through) 영역을 차지하므로, 캡 두께 안쪽에 ∅4.2 M3 인서트 홀을 끼울 자리가 없음. 캡 위로 ∅20 × 6 mm 원기둥(보스)을 돌출시켜 그 보스 측면에 M3 홀을 뚫는다.

1. 캡 **윗면(top face)** 클릭 → New Sketch
2. **Circle** 도구로 원점에 원 1개:
   - Diameter = `#hubOuter_90` (= 20)
   - 중심을 원점에 Coincident
3. Sketch 종료
4. Toolbar → **Extrude** 피처:
   - Profile: 위 원
   - Type: **Add** (기존 캡 본체에 머지)
   - End: **Blind**, Depth = `#hubBossHeight` (= 6)
   - 방향: 위로 (+Z, top face 위쪽)
5. → 결과: 캡 위에 ∅20 × 6 mm 원기둥이 캡과 일체화. 축 홀(∅5.2)은 보스를 그대로 관통 (이미 캡 base sketch의 축 홀이 위로 연장됨)

> ⚠ 만약 base sketch의 축 홀이 자동 연장되지 않으면, Step 6.5 직후에 별도 Extrude **Remove** 로 보스 정중앙에 ∅`#shaftHole` 관통 홀 추가.

#### Step 7 — M3 insert hole (Hole 피처, 보스 측면 진입)

M3 세트스크류용 ∅4.2 홀: 보스 측면에서 시작 → 축 홀까지 수평 관통.

| 항목 | 값 |
|---|---|
| 진입 면 | **허브 보스 측면(원통면)** — 보스 옆면 아무 위치 클릭 |
| Z 위치 | **`#capThickness + #hubBossHeight / 2`** = 9 mm (보스 중앙 높이) |
| Diameter | `#insertHole` (= 4.2) |
| Type | Simple |
| End condition | **Up to next** (축 홀에서 자동 정지) |
| 결과 길이 | 약 `#hubOuter_90 / 2 - #shaftHole / 2` ≈ 7.4 mm |

절차:
1. 보스 원통 측면을 클릭 → New Sketch (자동으로 그 점에 접하는 평면 생성)
2. 점 1개:
   - 보스 중심선과 X축에 Coincident (또는 보스 외곽원 가운데)
   - Z = `#capThickness + #hubBossHeight / 2` (= 9)
3. Sketch 종료
4. Toolbar → **Hole** 피처
5. 다이얼로그:
   - Hole points: 위에서 만든 점
   - Type: **Simple**
   - Diameter: `#insertHole` (= 4.2)
   - End condition: **Up to next**
6. ✓ → 보스 측면 → 축 홀까지 관통, M3 인서트 자리 완성

#### Step 8 — Configuration variant 만들기 (상부/하부)
- 우측 상단 `Configurations` 패널 → `+` → 새 Configuration 추가
- Configuration variable 이름: `isBottomCap`, Type: **Boolean**
- 두 항목:
  - `Top Cap` → `isBottomCap = false`
  - `Bottom Cap` → `isBottomCap = true`

이 설정만으로는 아직 차이가 없음. Step 9의 자석 포켓 피처를 이 변수로 suppress/enable 한다.

#### Step 9 — Sketch "magnet pockets" (Bottom face) — Bottom variant 한정
- 디스크 아랫면(Bottom face) 클릭 → New Sketch
- Center point circle 2개:
  - 첫 번째 중심: `(0, -#magnetRadius_90)` (= (0, -30))
  - 두 번째 중심: `(0, +#magnetRadius_90)` (= (0, +30))
  - 또는 Mirror 도구로 한 개만 그리고 거울 복사
- 각 원 Diameter: `#magnetDiameter` (= 4.1)

> **자석 포켓이 슬롯 0과 각도 일치**: 슬롯 0 위치(앞쪽, Y 음수 방향)에 자석 1개, 반대편(Y 양수 방향)에 자석 1개. 홈 센서가 둘 중 하나를 감지하면 절대 위치 결정 (180° 모호성은 펌웨어로 해결).

#### Step 10 — Extrude Cut "magnet pocket"
- Step 9 스케치 선택 → 두 원 영역
- 작업: **Remove**
- 끝 조건: **Blind**, 깊이: `#magnetDepth` (= 2.1)
- 방향: 디스크 안쪽으로 (Top plane 반대 방향)

#### Step 11 — Configuration: 자석 포켓 피처를 isBottomCap == true 일 때만 활성
- 피처 트리에서 Step 9 스케치 우클릭 → `Configure feature` → Suppression 설정
- `isBottomCap = false` 일 때 → **Suppressed**
- `isBottomCap = true` 일 때 → **Active**
- Step 10 Extrude Cut도 동일하게 설정

→ 이로써 Configuration 토글만으로 상부 캡 / 하부 캡 두 모델 자동 생성.

---

### 3.3 검증

- [ ] 외형이 십각형 (10 vertices, 10 sides)
- [ ] 슬롯 0이 X축 음수 방향(전면)에 정렬 — 자석 포켓 1개와 동일 각도
- [ ] 슬롯 10개 모두 관통, 36° 등간격
- [ ] 축 홀 ∅5.2 관통
- [ ] M3 홀 ∅4.2가 한쪽 다각형 변(rib)에서 축 홀까지 도달
- [ ] Configuration `Top Cap` 선택 시 자석 포켓 없음
- [ ] Configuration `Bottom Cap` 선택 시 자석 포켓 2개 (R30, ∅4.1, 깊이 2.1)
- [ ] 슬롯 길이 = 28 mm, 폭 = 3.2 mm (캘리퍼로 측정)

### 3.4 자주 막히는 지점

| 증상 | 원인 / 해결 |
|---|---|
| 정다각형 그릴 때 sides 입력란이 회색 | Polygon 도구의 sub-mode가 "circle"로 되어있음. "Inscribed/Circumscribed polygon" 모드 선택 필수 |
| `#drumFaces_90` 입력 시 "expected length" 오류 | Variable Studio에서 Type을 **Number**로 지정했는지 확인 |
| Slot Circular Pattern이 9개만 생김 | Instance count 입력 시 Onshape는 "추가 인스턴스"가 아닌 **총 개수**로 세는 모드와 헷갈림 — `Equal spacing` 모드면 총 10개로 입력 |
| Circular Pattern 결과가 비틀어진 링으로 나옴 (슬롯이 캡 외곽에 어긋난 각도로 겹침) | 회전축을 모서리·면·외곽 원으로 잘못 선택. 좌측 Feature 트리 `Origin` → `Z` 항목을 클릭해 회전축으로 지정 |
| 슬롯이 캡 중심을 향해 깊숙이 박힌 긴 막대로 그려져 회전 패턴 시 겹침 | 패널 *높이*(55)와 *폭*(27.8)을 혼동. top view 슬롯은 패널 단면 (3 × 27.8) 그대로 — **폭 3.2 (radial) × 길이 27.8 (tangential, 폴리곤 변과 평행)** |
| 슬롯 외측 긴 변이 폴리곤 변에서 떨어져 있음 | 외측 긴 변을 폴리곤 -Y 변에 Coincident constraint 안 잡음. 잡으면 자동으로 R = `#apothem_90` (42.8) 위치에 정렬 |
| M3 Hole이 디스크 통째로 뚫고 나감 | End condition 'Up to next' 또는 Blind 깊이 `#drumDiameter_90 / 2 - #shaftHole / 2` 명시 |
| 자석 포켓 깊이가 +Z (위쪽)으로 들어감 | Extrude 방향 토글 (반대 화살표 클릭) |

---

## 4. Part Studio "02 Acrylic Panel 90"

### Feature tree
1. **Sketch "panel outline"** on Front plane
   - 직사각형 `#panelWidth_90 × #panelLength` (중심 기준)
2. **Extrude** → `#panelThickness`
3. (장식) **Sketch "engrave area"** on Front face
   - 각인 영역 표시 (발주용 가이드, 실제 각인은 업체에서)

### DXF 추출
- `Right-click face → Export as DXF/DWG`
- 파일명: `cad/dxf/panel_90_blank.dxf`

---

## 5. Part Studio "03 Shaft & Bearing"

### Shaft
1. **Sketch** Ø`#shaftDiameter`, Extrude `#shaftLength`
2. Fillet 양 끝단 R0.3 (모따기 대체)

### Bearing 625ZZ (간략 모델)
1. **Sketch** OD `#bearingOD / 2`, ID `#bearingID / 2`, Extrude `#bearingWidth`
2. 링 형태. 실제 내부 볼 생략 (단순 placeholder)

---

## 6. Part Studio "05 Motor Mount"

### 28BYJ-48 치수 참조
- 본체 ∅28 × H19 mm (기어부)
- 마운트 플랜지: 2개 M4(실제 ∅3) 홀, 중심 간 35mm, 샤프트 중심 오프셋 약 8mm
- 샤프트 돌출: ∅5 × L8

### Feature tree (플레이트 브라켓)
1. **Sketch** 50 × 40 직사각형 (드럼 바닥 프레임 고정용 플레이트)
2. **Extrude** 3mm
3. **Sketch** 모터 샤프트 통과 홀 (∅6 여유) + M4×2 홀 (35mm 간격)
4. **Extrude** 관통 빼기
5. **Sketch** 프레임 고정 볼트 홀 4개 (M3) 모서리에
6. **Extrude** 관통 빼기

---

## 7. Part Studio "06 Hall Bracket"

### A3144 치수
- TO-92 패키지: 4 × 3.2 × 1.5mm + 리드 3개
- 감지 거리: 자석과 5mm 이내 권장
- 브라켓은 센서를 드럼 하부 캡 자석 궤적 바로 아래에 배치

### Feature tree
1. **Sketch** 20 × 15 플레이트
2. **Extrude** 2mm (PETG)
3. **Sketch** TO-92 슬롯 (1.5×4mm)
4. **Extrude** 관통 빼기
5. **Sketch** 프레임 고정 M3 홀 × 2
6. **Extrude** 관통 빼기

---

## 8. Part Studio "07 Frame Section" (1자리 분)

1자리 폭 = 드럼 ∅90 + 좌우 여유 7.5mm씩 (내부 간격 15mm의 절반) = **105 mm**

### 상부 바 + 하부 바 + 측면 기둥
- 상부 바: 단면 12 × 10 mm, 길이 105, 중앙에 베어링 마운트 홀 (∅16 + M3 고정)
- 하부 바: 단면 12 × 10, 길이 105, 중앙에 커플러/모터 통과 구멍
- 측면 기둥 × 2: 폭 5 × 깊이 10, 높이 140 (드럼 56 + 상·하 여유)
- LED 바 홀더: 하부 바 전면에 ∅10 장공 2개로 LED 바 안착

---

## 9. Assembly "Proto 1-Digit"

### Instance 배치
1. Frame Section (고정)
2. Bearing (상부 바 내부)
3. Shaft (Bearing 통과, 수직)
4. Top Cap (Shaft에 Concentric, 상단에서 50mm 아래)
5. Bottom Cap (Shaft에 Concentric, Top Cap에서 44mm 아래)
6. Panel × 10 (Top/Bottom Cap slot에 삽입, Circular Pattern)
7. Coupler (Shaft 하단, Motor 샤프트와 연결)
8. Motor + Motor Mount (Frame 하부 바 하단에 볼트)
9. Hall Bracket (Frame 하부 바, 자석 궤적 아래)
10. LED Bar (하부 바 전면)

### Mate 종류
- Fastened (Frame ↔ Bracket, 볼트 자리)
- Concentric (Shaft ↔ Bearing, Shaft ↔ Cap hub)
- Slider (없음, 회전 허용 위해 Concentric만)
- Revolute (Top Cap ↔ Shaft, 회전축)

### 간섭 체크
- Top Cap 꼭짓점 ↔ Frame 측면 기둥: 최소 7.5mm 이상 유지
- 자석 포켓 ↔ 베어링 하면: 최소 3mm
- Panel 하단 노출 엣지 ↔ LED 바 상면: 약 2mm (광 주입 간격)

---

## 10. Export 체크리스트

### STEP (발주·시뮬용)
- Assembly 전체 → `Export → STEP 214` → `cad/step/proto_1digit.stp`

### STL (3D 프린트용, 파트별)
| 파트 | 파일 | 프린터 설정 제안 |
|---|---|---|
| Top Cap | `cad/stl/cap_top_90.stl` | PETG, 0.2mm layer, 30% infill |
| Bottom Cap | `cad/stl/cap_bottom_90.stl` | PETG, 0.2mm, 40% infill (자석 포켓) |
| Coupler | `cad/stl/coupler_helical.stl` | PETG, 0.15mm, 100% infill, 수직 출력 |
| Motor Mount | `cad/stl/motor_mount.stl` | PETG, 0.2mm, 40% infill |
| Hall Bracket | `cad/stl/hall_bracket.stl` | PETG, 0.2mm, 30% infill |
| Frame Section | `cad/stl/frame_section_105.stl` | PETG, 0.2mm, 30% infill |

### DXF (아크릴 각인 발주용)
- `cad/dxf/panel_90_blank.dxf` — 각인 없는 원본 윤곽 (업체가 숫자 벡터 삽입)
- `cad/dxf/panel_60_blank.dxf` — (Phase 6에서)

### 폴더 생성 권장
```
cad/
├── phase2-onshape-brief.md  (본 문서)
├── step/
├── stl/
└── dxf/
```

---

## 11. DoD (Definition of Done)

- [ ] 모든 변수가 Variable Studio에 정의됨 (∅90·∅60 치수 각각 `_90`·`_60` 접미사로 구분)
- [ ] Part Studio "01 Drum ∅90 Caps"를 복제해 `_90` → `_60` 변수로 치환하면 ∅60 캡이 동일 로직으로 생성됨을 확인
- [ ] STEP 익스포트 성공
- [ ] 주요 파트 STL 6개 익스포트 성공
- [ ] 아크릴 DXF 1개 익스포트 성공
- [ ] Assembly에서 드럼 회전 테스트 (Revolute 회전 자유도 확인)
- [ ] 간섭 체크: 충돌 0건

## 12. 이후 단계 (Phase 3~4 연계)

- Phase 3: 부품 입고 후 실측 → Variable Studio 값 조정 (실제 샤프트 길이, 베어링 폭 편차 등)
- Phase 4: 이 어셈블리를 실제 조립하며 공차 검증 → 슬롯 폭·허브 크기 재조정
- Phase 6: `01 Drum ∅90 Caps` Part Studio 복제 → `08 Drum ∅60 Caps`로 개명 → 모든 `_90` 참조를 `_60`로 치환 → 프레임 6자리로 확장

---

## 13. 권장 작업 순서

| Step | Part Studio | 순 예상 |
|---|---|---|
| 1 | Variable Studio 정의 | 30분 |
| 2 | 01 Caps (상부 먼저) | 1h |
| 3 | Configuration으로 하부 자석 포켓 추가 | 30분 |
| 4 | 02 Acrylic Panel | 15분 |
| 5 | 03 Shaft & Bearing | 15분 |
| 6 | 04 Coupler (다음 세션) | 1h |
| 7 | 05 Motor Mount | 45분 |
| 8 | 06 Hall Bracket | 30분 |
| 9 | 07 Frame Section | 1h |
| 10 | Assembly + 간섭 체크 | 1.5h |
| 11 | Export (STEP/STL/DXF) | 15분 |

**총 예상**: 약 7시간 (WBS Phase 2 순공수와 일치)
