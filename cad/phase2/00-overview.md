# Phase 2 Onshape 브리프 — Overview

> 본 문서는 Phase 2 작업 전체의 메타 정보 (탭 역할, 분할 전략, DoD, 작업 순서). 각 Onshape 탭별 상세 절차는 같은 디렉토리의 다른 파일 참조 — [`README.md`](./README.md) 색인.

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
├── 🔧 Part Studio: "03 Shaft"               (Ø5×100mm 연마봉)
├── 🔧 Part Studio: "04 Bearing 625ZZ"       (∅16/∅5×5mm)
├── 🔧 Part Studio: "05 Coupler"             (보유 placeholder, 3D 프린트는 Phase 6 옵션)
├── 🔧 Part Studio: "06 Bottom Box"          (전자부 enclosure, 뚜껑 없음)
├── 🔧 Part Studio: "07 Bottom Plate"        (박스 뚜껑 + 6 모터 마운트 + 6 Hall + 6 LED + 둘레 홈)
├── 🔧 Part Studio: "08 Side Panels"         (전·후·좌·우 4 piece, 코너 tongue-groove)
├── 🔧 Part Studio: "09 Top Plate"           (6 베어링 시트 + 둘레 홈)
├── 🔧 Part Studio: "10 Drum ∅60 Caps"       (요일·날씨 드럼, 01 복제 + _60 치환)
├── 🔧 Part Studio: "11 Acrylic Panel 60"    (22.251 × 55 × 3, 요일·날씨 패널)
└── 🗂 Assembly: "Full 6-Digit Clock"
```

> **Feature Studio 탭은 사용하지 않음**. Feature Studio는 커스텀 FeatureScript 코드(함수·자체 피처) 작성 전용이며, Phase 2에서는 불필요. Onshape의 변수는 **Variable Studio** 탭에서 관리.

### 기존 결정 참조
- 드럼 외관·치수: [`../docs/spec.md`](../docs/spec.md) §2
- 드럼 3면도: [`../images/drum_structure.svg`](../images/drum_structure.svg) Panel 1·2·3
- 캡 도면: [`../images/cap_drawings.svg`](../images/cap_drawings.svg), [`../images/bottom_cap_detail.svg`](../images/bottom_cap_detail.svg)
- D14 베어링 625ZZ, D15 3D프린트 커플러

---

## 1.5 분할 출력 전략 (3D 프린터 베드 한계)

베드 크기 가정 **200 × 200 mm** (가장 보수적, 일반 hobbyist 프린터). 가로 620mm 부품들은 분할 출력 필수.

### 분할 대상 (X 방향 길이 > 200mm)

| Part | 길이 | 분할 |
|---|---|---|
| 06 Bottom Box (620 × 120 × 80) | 620 | ✅ 3 sections |
| 07 Bottom Plate (620 × 120 × 5) | 620 | ✅ 3 sections |
| 08 Front Panel (614 × 90 × 3) | 614 | ✅ 3 sections |
| 08 Back Panel (614 × 90 × 3) | 614 | ✅ 3 sections |
| 09 Top Plate (620 × 120 × 5) | 620 | ✅ 3 sections |
| 08 Left/Right Panel (114 mm) | 114 | ❌ 단일 출력 OK |
| 캡·샤프트·베어링·커플러·드럼 60 등 | < 100 | ❌ 단일 |

### 분할 위치 (5개 부품 모두 통일)

X 방향으로 **±103 mm** 두 곳에서 절단 → 3 sections 각 ~207 mm:
- Section A (좌): X = -310 ~ -103 (포함 D1 요일, D2 시십)
- Section B (중): X = -103 ~ +103 (포함 D3 시일, 콜론, D4 분십)
- Section C (우): X = +103 ~ +310 (포함 D5 분일, D6 날씨)

> 모든 layer (box, bottom plate, side panels, top plate)가 **같은 X 위치에서 분할** → split line이 수직으로 일관, 조립 시 정렬 자연 보장.

### 분할 위치 선정 근거

드럼 X 좌표: -265, -170, -65, +65, +170, +265.
분할선 ±103은 D2-D3 사이 ((−170 + −65)/2 = −117.5 근처) 와 D4-D5 사이 ((+65 + +170)/2 = +117.5 근처) 의 빈 영역. 드럼·홀과 겹치지 않음.

### 분할 결합 방식 — **사각 tongue-groove + M3 cross-screw**

FDM 정밀도 한계(±0.3mm)로 사다리꼴 도브테일은 fit 어려움 → **직각 사각 tongue-groove** 채택. M3 cross-screw로 잠금.

#### 결합 형상 (단면도)

```
[Top view, joint area at split line, 5mm plate 기준]

좌 Section (예: A)        우 Section (예: B)

  ┌──────────────────┐      ┌──────────────────────┐
  │            tongue│      │groove                │
  │            ┌──┐  │      │ ┌──┐                 │
  │            │■■│■■│■■    │ │  │                 │
  │       y=0 ─┤■■│■■│──────┤─┤  ├─                │  ← tongue 8mm wide × 4mm out
  │            │■■│■■│      │ │  │                 │
  │            └──┘  │      │ └──┘                 │
  │       ●          │      │       ●              │  ← M3 cross-screw (Y=±40)
  └──────────────────┘      └──────────────────────┘
        X=-107 ↑    ↑          ↑ X=-103
                X=-103         (groove inner end at X=-99)
       ▲                       ▲
       Section A 외측 끝        Section B 외측 끝
       (tongue은 +X로 4mm 돌출)  (groove는 +X로 4mm 패임)

조립: A의 tongue이 B의 groove에 슬라이드 삽입 → A 우측 끝 X=-103과 B 좌측 끝 X=-103이 일치
```

#### 치수 정의

| 요소 | 값 | 변수 |
|---|---|---|
| Tongue 폭 (Y 방향) | 8 mm | `#splitTongueWidth` |
| Tongue 길이 (X 돌출량) | 4 mm | `#splitTongueDepth` |
| Groove 폭 (clearance 0.4) | 8.4 mm | `splitTongueWidth + 0.4` |
| Groove 깊이 (clearance 0.2) | 4.2 mm | `splitTongueDepth + 0.2` |
| Tongue 세로 (Z) — plate | 5 mm = `#plateThickness` | (전 두께 통과) |
| Tongue 세로 (Z) — 박스 벽 | 80 mm = `#bottomBoxHeight` | (전 두께 통과) |

#### Tongue/Groove 할당 (3 sections)

| Section | 좌측 끝 | 우측 끝 | 비고 |
|---|---|---|---|
| A (좌) | (외부 — 가공 X) | tongue | tongue 1개 |
| B (중) | groove | groove | groove 양쪽 |
| C (우) | tongue | (외부 — 가공 X) | tongue 1개 |

**B section이 모든 tongue을 받는 "허브" 역할**. A·C는 양 끝에 tongue 1개씩.

#### M3 Cross-screw 위치 — 모두 **내부 face** (외부 visible 차단)

> **원칙**: 외부에서 보이는 볼트 헤드 0개. 모든 cross-screw는 조립 후 드럼·plate·박스 cavity로 가려지는 내부 face에서 진입한다.

| 부품 | 볼트 진입 face (내부) | 외부 = 가시 면 (clean) | 은폐 메커니즘 |
|---|---|---|---|
| 06 Bottom Box | **Cavity 내부** (수평 X방향, 내부 보스) | 박스 외측 4면 + 바닥 | Bottom Plate가 cavity 위 덮음 |
| 07 Bottom Plate | **Top face** (드럼 측, 수직 Z↓) | Plate 하면 (박스 cavity 천장) | 드럼·모터·Hall이 위 가림 |
| 09 Top Plate | **Bottom face** (드럼 측, 수직 Z↑) | Plate 윗면 (시계 천장) | 드럼·베어링이 아래 가림 |
| 08 Front/Back/Left/Right Panel | **(제거됨)** — cross-screw 없음 | 패널 외부 4면 모두 매끄러움 | Plate 둘레 홈 클램핑만으로 ring 고정 |

각 cross-screw 결합 (제거된 panel 제외):
- 한쪽 section: ∅3.2 clearance 홀 (M3 통과)
- 다른쪽 section: heat insert 홀 (`#insertHole` = 4.2)
- M3 6mm 볼트로 잠금

##### Plate (07/09): Side ridge 방향 — 모두 **드럼 측으로**

5mm 두께 plate에 직접 X·Y 방향 cross-screw 어렵 → 한쪽 section 끝에 **ridge (under-flange)** 부착, Z방향 볼트 사용. 두 plate 모두 ridge가 **드럼 측 (= 내부)** 으로 향함:

```
[07 Bottom Plate split 단면 — X방향]

       Section A          Section B
   ┌──────────────┐  ┌──────────────────┐  ← Top face (드럼 측, 내부)
   │   plate 5    │  │    plate 5       │
   └────┬─────────┘  └──────────────────┘  ← Bottom face (외부 = 가시)
        │ridge below A│        ↑ bolt from TOP↓
        │extends to   │        (B's plate top
        │X=-93 (under │         → B's plate 5
        │ B's plate)  │         → A's ridge below B's plate)
        └─────────────┘  heat insert in A's ridge
                         **Bolt head on B's TOP face = 내부, 가려짐**

[09 Top Plate split 단면 — X방향]

   Bottom face (드럼 측, 내부)  ← bolt from BELOW↑
        ↑                                      ↑
   ┌────┴─────────┐  ┌──────────────────┐
   │   plate 5    │  │    plate 5       │
   └──────────────┘  └──────────────────┘     ← Top face (외부 = 시계 천장, 가시)
        │ridge below A│
        │extends under│       Bolt: B's bottom↑ → B's plate 5 → A's ridge
        │ B's plate   │       **Bolt head on B's BOTTOM face = 드럼 측, 가려짐**
        └─────────────┘
```

요약:
- **두 plate 모두 ridge가 plate 아래쪽 (-Z)** 으로 5mm 돌출.
- Bottom Plate의 ridge는 박스 cavity 안에 위치, Top Plate의 ridge는 드럼 영역 안에 위치 — 둘 다 내부.
- **Bottom Plate**: 볼트 위→아래 (Top face = 드럼 측이 헤드)
- **Top Plate**: 볼트 아래→위 (Bottom face = 드럼 측이 헤드)
- 결과: Bottom Plate **하면**과 Top Plate **윗면** = 외부 = 매끄러운 볼트-없음 표면.

##### 06 Bottom Box: Cavity 내부 cross-screw

박스 벽 5mm가 얇아 외벽에 직접 cross-screw 어렵 + 외부 visible → 각 section의 **cavity 내부에 보스(rib)** 부착하여 X방향 수평 cross-screw로 결합:

- Section A 우측 끝 (X=-103): cavity 안쪽 (Y=0, Z=**-20·-60** 두 위치 — 박스 cavity Z=-77~0 범위 안)에 보스 8 × 8 × 8mm 추가, X방향으로 +4mm 오버행 (split 너머 4mm 돌출 → tongue과 같은 방향).
- Section B 좌측 끝 (X=-103): 매칭 자리에 8 × 8 × 4mm 패임 (보스 슬라이드 자리).
- A의 보스에 ∅3.2 clearance, B의 매칭 자리 안쪽에 heat insert.
- Bolt: cavity 안에서 X방향 (수평)으로 삽입, 헤드는 A 보스의 cavity 측면에 안착. 박스 위가 Bottom Plate로 덮이면 외부 visible 차단.

> **사전 조립 필수** (§9 참조): plate/box 각 sections는 본 조립 전에 작업대에서 **사전 결합 (sub-assembly)** 하여 cross-screw 잠금. 이 단계에서 plate를 뒤집거나 cavity를 위로 향해 두면 내부 face에서 볼트 접근이 가능.

### Onshape 모델링 옵션

| 옵션 | 장단점 |
|---|---|
| **A. Slicer 분할** (모놀리식 모델 → Cura/PrusaSlicer로 분할) | 빠름. Joint 약함. 정렬 어려움. |
| **B. Onshape에서 처음부터 3 section 모델링** ✅ | 도브테일·M3 fit 정밀. 시간 더 소요. |

**B 권장**. 각 §6/§7/§8/§9 절차에 split section 별도 sketch + extrude 작업 추가.

### 신규 변수 (§2.3에 추가)

| Name | Value | 비고 |
|---|---|---|
| `splitX_1` | `-103 mm` | 좌측 split 위치 |
| `splitX_2` | `+103 mm` | 우측 split 위치 |
| `splitTongueWidth` | `8 mm` | 도브테일 tongue 폭 |
| `splitTongueDepth` | `4 mm` | tongue 침투 깊이 |
| `splitJointInsertHole` | `4.2 mm` | M3 heat insert (= insertHole) |

### 출력 파일 명명 규칙

각 부품 STL 분할 시:
```
bottom_plate_A.stl  ← 좌측 section
bottom_plate_B.stl  ← 중간 section
bottom_plate_C.stl  ← 우측 section
```

---

## 11. DoD (Definition of Done)

- [ ] 모든 변수가 Variable Studio에 정의됨 (∅90·∅60 치수 각각 `_90`·`_60` 접미사로 구분)
- [ ] Part Studio "01 Drum ∅90 Caps"를 복제해 `_90` → `_60` 변수로 치환하면 ∅60 캡이 동일 로직으로 생성됨을 확인
- [ ] STEP 익스포트 성공
- [ ] 주요 파트 STL 익스포트 성공 (캡 4종 + 06 Box + 07 Plate + 08 측면 4 + 09 Plate = ~10개)
- [ ] 아크릴 DXF 익스포트 성공 (∅90/∅60 마스터 시트)
- [ ] Assembly에서 6 드럼 회전 테스트 (각 Revolute 회전 자유도 확인)
- [ ] 간섭 체크: 충돌 0건 (특히 인접 드럼 15mm gap, 측면 판 ↔ 둘레 홈 fit)
- [ ] 측면 판 4 piece tongue-groove 결합 시뮬

## 12. 이후 단계 (Phase 3~4 연계)

- Phase 3: 부품 입고 후 실측 → Variable Studio 값 조정 (실제 샤프트 길이, 베어링 폭 편차 등)
- Phase 4: 6자리 통합 어셈블리를 실제 조립하며 공차 검증 → 슬롯 폭·허브 크기·둘레 홈 폭 재조정
- D30 이후 단일 자리 프로토 단계 폐기 — Phase 2 → Phase 6 직접 진입
- ∅60 캡(`10 Drum ∅60 Caps`)은 D30 6자리 설계의 필수 — `01 Drum ∅90 Caps` 복제 후 `_90` → `_60` 치환

---

## 13. 권장 작업 순서

| Step | Part Studio | 순 예상 |
|---|---|---|
| 1 | Variable Studio (~50개 변수) | 45분 |
| 2 | 01 Drum ∅90 Caps | 1h |
| 3 | Configuration으로 하부 자석 포켓 추가 | 30분 |
| 4 | 02 Acrylic Panel 90 + 02b Engraving Sheet (DXF) | 30분 |
| 5 | 03 Shaft | 10분 |
| 6 | 04 Bearing 625ZZ | 10분 |
| 7 | 05 Coupler (placeholder) | 10분 |
| 8 | **06 Bottom Box** (전자부 enclosure) | 1.5h |
| 9 | **07 Bottom Plate** (6 모터·Hall·LED + 둘레 홈) | 2h |
| 10 | **08 Side Panels** (4 piece, 코너 결합) | 1.5h |
| 11 | **09 Top Plate** (6 베어링 + 둘레 홈) | 1h |
| 12 | **10 Drum ∅60 Caps** (01 복제·치환) | 30분 |
| 13 | **11 Acrylic Panel 60** (+ 11b Engraving Sheet) | 30분 |
| 14 | Assembly + 간섭 체크 (6자리) | 2h |
| 15 | Export (STEP/STL/DXF) | 30분 |
| **합계** | | **~12.5h** |

**총 예상**: 약 7시간 (WBS Phase 2 순공수와 일치)
