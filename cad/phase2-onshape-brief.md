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

> 드럼 직경·면 수는 ∅90과 ∅60이 다르므로 **둘 다 별개 변수로** 등록.
>
> **🔒 D29 freeze**: `panelWidth_90` (25.032) / `panelWidth_60` (22.251) 는 외주 발주 완료로 **변경 금지**. 슬롯 길이는 패널에서 유도. drumDiameter·drumFaces 변경 시 패널이 폴리곤 변(chord)보다 작아야 함을 검증 (∅90: panel ≤ 27.812 chord ✓, ∅60: panel ≤ 26.033 chord ✓).

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
| `panelWidth_90` | Length | `25.032 mm` | **🔒 Freeze (D29)** — 외주 발주 완료. 원래 유도식 (∅90, n=10): slot 비겹침 한계 `2·(apothem-slotWidth)·tan(180°/n) - 0.5` = 2·(42.798-3.2)·tan(18°) - 0.5 = 25.232, 패널은 그보다 0.2 mm 작음 → **25.032**. drumDiameter 변경되어도 이 값은 변하지 않음 (literal) |
| `panelWidth_60` | Length | `22.251 mm` | **🔒 Freeze (D29)** — 외주 발주 완료. 원래 유도식 (∅60, n=7): 2·(27.029-3.2)·tan(180°/7) - 0.5 = 22.451, 패널 = 슬롯 - 0.2 → **22.251** |
| `slotLength_90` | Length | `#panelWidth_90 + 0.2 mm` | 패널에서 유도: 25.232 mm (양쪽 0.1 clearance) |
| `slotLength_60` | Length | `#panelWidth_60 + 0.2 mm` | 패널에서 유도: 22.451 mm |
| `slotDepth_top` | Length | `3 mm` | 상부 캡 슬롯 깊이 (관통 X) |
| `slotDepth_bottom` | Length | `#capThickness` | 하부 캡 슬롯 깊이 = 6 mm (관통 O) |
| `hubBossHeight` | Length | `12 mm` | 캡 위로 돌출되는 허브 보스 높이 (M3 인서트 위·아래 4 mm 여유 확보, 1차 프린트 시 6 mm가 타이트해 12 mm로 상향) |
| `hubOuter_90` | Length | `20 mm` | ∅90 캡 허브 외경 |
| `hubOuter_60` | Length | `18 mm` | ∅60 캡 (D13, 인서트 OD5 수용) |
| `shaftHole` | Length | `5.2 mm` | 축 홀 (+0.2/-0) |
| `insertHole` | Length | `4.2 mm` | **히트 인서트 설치 홀** (보유 인서트 OD 5 mm용, OD-0.8 압입 여유). M3 set screw OD 3.0과 다름 |
| `magnetDiameter` | Length | `4.1 mm` | 자석 ∅4 + 0.1 여유 |
| `magnetDepth` | Length | `2.1 mm` | 자석 ∅4×2 + 0.1 여유 |
| `magnetRadius_90` | Length | `30 mm` | ∅90 드럼 중심 → 자석 중심 |
| `magnetRadius_60` | Length | `15 mm` | ∅60 드럼 |
| `baffleThickness` | Length | `2 mm` | 배플 두께 |
| `gapInner` | Length | `15 mm` | HH·MM 내부 간격 (D22) |
| `gapOuter` | Length | `20 mm` | 요일/날씨 ↔ 숫자 |
| `gapColon` | Length | `40 mm` | 콜론 |
| `shaftDiameter` | Length | `5 mm` | |
| `shaftLength` | Length | `100 mm` | D26 — 보스 12 mm 양쪽 적용 후 스택 97~99 mm. 75 mm 보유분 폐기, 100 mm 신규 발주 |
| `bearingOD` | Length | `16 mm` | 625ZZ 외경 |
| `bearingID` | Length | `5 mm` | |
| `bearingWidth` | Length | `5 mm` | |
| `couplerOD` | Length | `19 mm` | B02 보유 커플러 외경 (실측, 보유품 freeze) |
| `couplerLength` | Length | `25 mm` | B02 보유 커플러 길이 (실측). 내경은 `#shaftDiameter` 재사용 |
| `enclosureWidth` | Length | `620 mm` | 외부 폭 (D22 590 + 측벽·여유) |
| `enclosureDepth` | Length | `120 mm` | 외부 깊이 (드럼 95 + 여유) |
| `bottomBoxHeight` | Length | `80 mm` | 전자부 박스 높이 |
| `drumCompartmentHeight` | Length | `90 mm` | 드럼 영역 높이 (드럼 56 + 보스 12 + 베어링·여유) |
| `enclosureHeight` | Length | `#bottomBoxHeight + #drumCompartmentHeight + 2 * #plateThickness` | 자동 계산: ~180 |
| `plateThickness` | Length | `5 mm` | 상·하 판 두께 |
| `boxWallThickness` | Length | `3 mm` | 하부 박스 벽 두께 |
| `sidePanelThickness` | Length | `3 mm` | 측면 판 두께 |
| `panelGrooveWidth` | Length | `#sidePanelThickness + 0.4 mm` | 둘레 홈 폭 (3 + 0.4 clearance) |
| `panelGrooveDepth` | Length | `4 mm` | 둘레 홈 깊이 |
| `digitWindowWidth_90` | Length | `26 mm` | ∅90 디지트 창 폭 (panelWidth_90 + 1 margin) |
| `digitWindowWidth_60` | Length | `24 mm` | ∅60 디지트 창 폭 |
| `digitWindowHeight` | Length | `50 mm` | 디지트 창 높이 (drumVisibleHeight + 6 margin) |
| `drumX_1` | Length | `-265 mm` | 요일 드럼 (∅60) X 위치 |
| `drumX_2` | Length | `-170 mm` | 시십 (∅90) |
| `drumX_3` | Length | `-65 mm` | 시일 (∅90) |
| `drumX_4` | Length | `+65 mm` | 분십 (∅90) |
| `drumX_5` | Length | `+170 mm` | 분일 (∅90) |
| `drumX_6` | Length | `+265 mm` | 날씨 (∅60) |
| `splitX_1` | Length | `-103 mm` | 좌측 분할 위치 (베드 200mm 한계) |
| `splitX_2` | Length | `+103 mm` | 우측 분할 위치 |
| `splitTongueWidth` | Length | `8 mm` | 도브테일 tongue 폭 |
| `splitTongueDepth` | Length | `4 mm` | tongue 침투 깊이 |
| `dcJackDiameter` | Length | `8 mm` | DC 바렐잭 (M2.1) panel-mount 홀 |
| `dcJackX` | Length | `-200 mm` | DC잭 후면벽 X 위치 |
| `dcJackZ` | Length | `-#bottomBoxHeight / 2` | DC잭 Z 위치 (= -40, 후면벽 중앙) |
| `ventSlotWidth` | Length | `2 mm` | 통기 슬롯 두께 (Z 방향) |
| `ventSlotLength` | Length | `80 mm` | 통기 슬롯 길이 (X 방향) |
| `ventSlotCount` | **Number** | `10` | 통기 슬롯 개수 (linear pattern) |
| `ventSlotPitch` | Length | `6 mm` | 통기 슬롯 Z 간격 |
| `ventSlotZStart` | Length | `-#bottomBoxHeight + 15 mm` | 첫 슬롯 Z 중심 (= -65, 박스 바닥에서 15mm 위) |
| `rimBossWidth` | Length | `12 mm` | top edge 보강 보스 한 변 (X·Y 정사각) |
| `rimBossHeight` | Length | `12 mm` | 보스 -Z 돌출량 (벽 5 + cavity 7) |

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
| 허브 보스 | ∅20 × **12 mm** 원기둥, 캡 위로 돌출 | M3 인서트 홀이 슬롯과 겹치지 않게 별도 피처로 추가, 인서트 위·아래 4 mm 여유 |
| 슬롯 | 3.2(radial) × 25.232(tangential) × 10개 | 폴리곤 변에 평행, 변에서 안쪽으로 3.2 깊이. 길이 = 비겹침 한계 (panel chord 27.812 보다 2.58 짧음). **상부 캡 깊이 3 mm (블라인드)**, 하부 캡 6 mm 관통 |
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
  - **Width (X 방향, tangential)** = `#slotLength_90` (= 25.232 mm) ← **긴 변**
  - **Height (Y 방향, radial)** = `#slotWidth` (= 3.2 mm) ← **짧은 변**
- 결과: 슬롯 중심이 자동으로 (0, -(`#apothem_90` - `#slotWidth`/2)) ≈ (0, -41.2)에 위치, 외측 긴 변이 폴리곤 -Y 변과 겹쳐짐 (폴리곤 변 27.81 보다 슬롯이 약 2.6 mm 짧아 vertex 양쪽에 ~1.3 mm 솔리드 띠 남음 — 인접 슬롯과 겹침 방지).
- ✅ 검증: 슬롯이 "폴리곤 한 변과 평행한 작은 띠" 모양인지 눈으로 확인. "캡 중심을 향해 깊숙이 박힌 막대"면 잘못 (이건 패널 *높이* 방향과 혼동한 것).

> **Tip**: 슬롯 외측 긴 변을 폴리곤 -Y 변에 **Coincident** constraint 잡으면 Y=-41.2 자동. 슬롯 가로 길이는 폴리곤 변보다 짧으므로 끝점 Coincident는 사용 금지 (variable로 길이 결정).
> **왜 폴리곤 변 길이 그대로 안 쓰나**: panel chord 27.81 사용 시 인접 슬롯이 vertex 안쪽에서 1.88 mm 겹침 발생, 패턴 후 캡 내부 전체가 절단됨. `slotLength_90` 변수가 비겹침 한계 (= `2·(apothem-slotWidth)·tan(180°/n) - 0.5`)를 자동 계산.

#### Step 5 — Extrude Cut "slot 0"

| 캡 variant | 끝 조건 | 깊이 |
|---|---|---|
| 상부 (`hasMagnet` = OFF) | **Blind** | `#slotDepth_top` = 3 mm (관통 X) |
| 하부 (`hasMagnet` = ON) | **Through all** 또는 Blind | `#slotDepth_bottom` = 6 mm (관통 O) |

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

> 왜 추가하는가: 슬롯이 z=3~6 (상부 캡 Blind) 또는 z=0~6 (하부 캡 Through) 영역을 차지하므로, 캡 두께 안쪽에 ∅4.2 M3 인서트 홀을 끼울 자리가 없음. 캡 위로 ∅20 × **12 mm** 원기둥(보스)을 돌출시켜 그 보스 측면 중앙에 M3 홀을 뚫는다 (1차 6 mm 시도 시 인서트 위·아래 여유 부족, 12 mm로 상향).

1. 캡 **윗면(top face)** 클릭 → New Sketch
2. **Circle** 도구로 원점에 동심 원 **2개**:
   - 외측 원 Diameter = `#hubOuter_90` (= 20)
   - 내측 원 Diameter = `#shaftHole` (= 5.2) ← **반드시 함께 그려야 축 홀 유지**
   - 두 원 중심을 원점에 Coincident
3. Sketch 종료
4. Toolbar → **Extrude** 피처:
   - Profile: 위 두 원 사이 **ring(annulus) 영역만 선택** (가운데 작은 원의 안쪽은 선택 X)
   - Type: **Add** (기존 캡 본체에 머지)
   - End: **Blind**, Depth = `#hubBossHeight` (= 12)
   - 방향: 위로 (+Z, top face 위쪽)
5. → 결과: 캡 위에 ∅20 × 12 mm 원기둥, 가운데 ∅5.2 축 홀 그대로 관통 유지

> 만약 ring 영역이 한 영역으로 안 잡히면: 두 영역(ring + inner) 모두 선택 후 Type을 **Add**로, 그다음 별도 Extrude **Remove**로 inner ∅5.2를 Through로 빼는 방식도 가능.

#### Step 7 — Heat insert hole (Hole 피처, 보스 측면 진입)

M3 heat insert(보유품 OD 5 × L 4) 설치용 **∅4.2** 홀: 보스 측면에서 시작 → 축 홀까지 수평 관통. 이후 보유품 인서트를 인두로 압입, 외부에서 M3 set screw(OD 3)를 인서트 내부 나사에 체결해 샤프트와 캡 잠금.

> **∅4.2 = heat insert 설치 홀이지 set screw 지름 아님**. set screw 자체는 OD 3.0 mm.

| 항목 | 값 |
|---|---|
| 진입 면 | **허브 보스 측면(원통면)** — 보스 옆면 아무 위치 클릭 |
| Z 위치 | **`#capThickness + #hubBossHeight / 2`** = 12 mm (보스 중앙 높이, 인서트 ±2 mm + 여유 ±4 mm) |
| Diameter | `#insertHole` (= 4.2) |
| Type | Simple |
| End condition | **Up to next** (축 홀에서 자동 정지) |
| 결과 길이 | 약 `#hubOuter_90 / 2 - #shaftHole / 2` ≈ 7.4 mm |

절차:
1. 보스 원통 측면을 클릭 → New Sketch (자동으로 그 점에 접하는 평면 생성)
2. 점 1개:
   - 보스 중심선과 X축에 Coincident (또는 보스 외곽원 가운데)
   - Z = `#capThickness + #hubBossHeight / 2` (= 12)
3. Sketch 종료
4. Toolbar → **Hole** 피처
5. 다이얼로그:
   - Hole points: 위에서 만든 점
   - Type: **Simple**
   - Diameter: `#insertHole` (= 4.2)
   - End condition: **Up to next**
6. ✓ → 보스 측면 → 축 홀까지 관통, M3 인서트 자리 완성

#### Step 8 — Configuration Checkbox로 자석 포켓 토글

Onshape `Configure Part Studio` 버튼 옆 ▼ 메뉴에 **List · Checkbox · Configuration variable** 3종이 있음.

| 옵션 | 역할 | 본 단계 사용 |
|---|---|---|
| List | 명명된 dropdown 옵션 | — |
| **Checkbox** | true/false 토글 | ✅ **사용** |
| Configuration variable | Length/Angle 등 값 변수 (Boolean 없음) | — |

절차:
1. Configurations 패널의 ▼ → **Checkbox** 클릭
2. 다이얼로그:

| 칸 | 값 |
|---|---|
| Name | `hasMagnet` |
| Default value | **unchecked** (= 상부 캡 기본) |

3. ✓ → 패널 상단에 `☐ hasMagnet` 토글 생성

```
hasMagnet 체크 OFF  →  상부 캡 (자석 포켓 없음)
hasMagnet 체크 ON   →  하부 캡 (자석 포켓 활성)
```

Step 11에서 자석 포켓 피처 두 개의 Suppression을 `not #hasMagnet`에 묶어 동작 완성.

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

#### Step 11 — Configuration: 자석 포켓 피처를 `hasMagnet` 체크박스에 묶기
1. 피처 트리에서 Step 9 sketch + Step 10 extrude 두 피처 선택 (Shift+클릭)
2. 우클릭 → **`Configure feature`** → Suppression 옵션 활성
3. Suppression 표현식: **`not #hasMagnet`** (체크박스 OFF 시 suppress, ON 시 active)

→ 결과: `hasMagnet` 체크 OFF (상부 캡) = 자석 포켓 없음 / ON (하부 캡) = 자석 포켓 ∅4.1×2 활성.

Slot extrude(Step 5)의 깊이도 캡별로 다른 값(3 / 6 mm)이 필요한 경우, 동일 방식으로 슬롯 Extrude 피처의 End condition + Depth를 우클릭 → `Configure feature` 로 묶을 수 있음 (선택사항, 프로토 단계에서는 6 mm Through로 통일해도 무방).
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

패널 1장 모델링 (각인은 업체 처리). 결과: **25.032 × 55 × 3 mm 직육면체** + 발주용 DXF 1장.

### 4.1 Part Studio 생성 + Variable 연결

1. 문서 하단 탭바 `+` → **Part Studio**, 이름 `02 Acrylic Panel 90`
2. Feature 트리 좌상단 `Feature ▾` → **Variable Studio** → `Clock Config` 선택

### 4.2 Feature tree

#### Step 1 — Sketch "panel outline" (Front plane)

| 항목 | 값 |
|---|---|
| 작업 평면 | **Front plane** (XZ — 패널이 세워진 자세, 두께가 Y로 빠짐) |
| 도구 | Center point rectangle |
| 중심점 | 원점 (Coincident) |
| 가로 (X) | `#panelWidth_90` (= 25.032) |
| 세로 (Z) | `#panelLength` (= 55) |

→ fully-defined 확인 (모든 선 검은색) 후 Sketch 종료.

#### Step 2 — Extrude "panel thickness"

| 칸 | 값 |
|---|---|
| Profile | Step 1 sketch |
| Type | **New** (새 솔리드 파트) |
| End condition | **Symmetric** (양방향) — 또는 Blind 한 방향 |
| Depth | `#panelThickness` (= 3) |

→ 25.032 × 55 × 3 mm 직육면체 1개.

#### Step 3 (선택) — 각인 영역 가이드

각인 발주용 참고선. 실제 각인은 업체 폰트로 진행하므로 모델에 안 넣어도 무관.
- 패널 앞면 클릭 → New Sketch
- 상하 여백 3 mm 빼고 19 × 49 직사각형을 **construction line** 으로 그림
- Sketch 종료 (extrude 안 함)

### 4.3 DXF 추출 (각인 발주용 — 단일 패널)

1. 패널 **앞면(25.032 × 55 평면)** 우클릭 → **Export as DXF/DWG**
2. 파일명: `panel_90_blank.dxf` → `cad/dxf/` 에 저장
3. 발주처 요청: "각인 폰트로 숫자 0~9 입력 후 각 1장씩 출력" (40장)

### 4.4 발주 수량 — 숫자별 필요 장수

24시간제 4 드럼(시십·시일·분십·분일) × 10면 = 40장. 자리별 사용 범위가 달라 수량 가변.

| 숫자 | 들어갈 드럼 | 수량 |
|---|---|---|
| 0 | 4 드럼 모두 | **4** |
| 1 | 4 드럼 모두 | **4** |
| 2 | 4 드럼 모두 | **4** |
| 3 | 시일·분십·분일 (시십 미사용) | **3** |
| 4 | 동일 | **3** |
| 5 | 동일 | **3** |
| 6 | 시일·분일 (시십·분십 미사용) | **2** |
| 7 | 동일 | **2** |
| 8 | 동일 | **2** |
| 9 | 동일 | **2** |
| 빈 패널 (각인 없음) | 시십 7 + 분십 4 | **11** |
| **합계** | | **40 장** |

> 대안 (A안): 모든 드럼에 0~9 풀 각인 → 각 숫자 4장씩 × 10 = 40장, 빈 패널 0. 단순하나 미사용 자리도 각인비 발생.

### 4.5 마스터 시트 제작 (10개 숫자 패널을 단일 DXF로)

발주처에 보낼 단일 DXF에 10개 숫자 패널을 한 시트로 배치. 폰트는 sketch text → outline으로 변환되어 호환 문제 없음.

#### Step 1 — 별도 Part Studio 생성

1. 탭바 `+` → Part Studio, 이름 `02b Panel 90 Engraving Sheet` (기존 §4 단일 패널과 분리)
2. Variable Studio `Clock Config` 연결

#### Step 2 — Sketch 격자 (Front plane, 2행 × 5열)

1. Front plane → New Sketch
2. 좌상단에 첫 사각형 1개: `#panelWidth_90` × `#panelLength` (25.032 × 55)
3. **Sketch Linear Pattern** (Toolbar):
   - 패턴 대상: 사각형 4변
   - 방향 1: X축, 거리 30.032 (= 25.032 + 5 mm 간격), 5개
   - 방향 2: Y축, 거리 60 (= 55 + 5 mm 간격), 2개
   - → 10개 사각형 격자
4. 시트 전체 크기: 145.15 × 115 mm (300 × 200 일반 기판에 적합)

#### Step 3 — Text 10개 (0~9)

1. Toolbar → **Text** (Sketch 도구)
2. 첫 사각형(좌상단) 가운데 클릭 → 텍스트 `0` 입력
3. 옵션:
   - Font: **Arial Bold** (또는 발주처 호환 폰트)
   - Height: 38 mm (가시 영역 44 mm 안의 max digit)
4. Coincident: 텍스트 중심 ↔ 사각형 중심
5. **반복** 1~9까지 9번 더 (각자 위치, 좌→우 첫 행 0,1,2,3,4 / 둘째 행 5,6,7,8,9)

> Text는 sketch pattern에 포함 불가 (각 문자가 다름). 10번 수동 배치.

#### Step 4 — (선택) 빈 패널 1개

레이아웃 한쪽에 11번째 사각형 (Text 없음) 추가. 발주서에 "이 사각형은 11장 빈 패널로 cut만, 각인 X" 표기.

#### Step 5 — DXF Export

1. Sketch 또는 평면 우클릭 → **Export as DXF/DWG**
2. 옵션:
   - Format: DXF
   - Units: **mm**
   - **Include sketch text**: ✅ ON (텍스트가 outline 벡터로 변환되어 폰트 호환 문제 없음)
3. 파일명: `panel_90_engraving_sheet.dxf` → `cad/dxf/`

#### Step 6 — 발주 사양 텍스트 (DXF 함께 보낼 문서)

```
아크릴 3 mm 투명, 25.032 × 55 패널 컷 + 각인
각 패널 수량:
  0·1·2 — 각 4장
  3·4·5 — 각 3장
  6·7·8·9 — 각 2장
  빈 패널 (각인 X) — 11장
총 40장
각인: DXF 안의 sketch text (outline 변환 완료)
```

---

## 4.5 Part Studio "10 Drum ∅60 Caps" (D30 — 6자리 통합 설계의 요일·날씨 드럼)

D30 통합 enclosure 채택으로 단일 자리 프로토 폐기 → 6자리 직접 진입. 요일(D1)·날씨(D6) 드럼은 ∅60이므로 본 Part Studio 필수.

### 가장 간단한 방법 — Part Studio 복사 후 `_90` → `_60` 치환

1. 문서 하단 탭바에서 `01 Drum ∅90 Caps` **우클릭** → **Duplicate** (또는 Copy & Paste)
2. 복사본 이름 → `10 Drum ∅60 Caps`
3. 복사본의 Feature 트리에서 각 피처를 더블클릭하여 편집, 아래 표대로 변수만 일괄 교체:

| 변수 (∅90) | 교체 (∅60) | 사용 위치 |
|---|---|---|
| `#drumDiameter_90` | `#drumDiameter_60` | Step 2 base sketch 외경 |
| `#drumFaces_90` | `#drumFaces_60` | Step 2 polygon 면 수, Step 6 Circular Pattern instances |
| `#hubOuter_90` | `#hubOuter_60` | Step 2 허브 원, Step 6.5 보스 외경 |
| `#magnetRadius_90` | `#magnetRadius_60` | Step 9 자석 포켓 위치 |
| `#apothem_90` | `#apothem_60` | (참조하는 곳 있다면) |
| `#slotLength_90` | `#slotLength_60` | Step 4 슬롯 가로 치수 |

**그대로 둘 변수** (양 캡 공통): `slotWidth`, `capThickness`, `panelThickness`, `slotDepth_top/bottom`, `hubBossHeight`, `shaftHole`, `insertHole`, `magnetDiameter`, `magnetDepth`.

4. Regenerate (자동) → 다음 값이 자동 계산되어 들어가야 정상:

| 항목 | ∅60 자동값 |
|---|---|
| 폴리곤 면 수 | 7 (heptagon) |
| 패널 폭 | 22.251 mm |
| 슬롯 길이 | 22.451 mm |
| 슬롯 피치 | 360° / 7 ≈ 51.43° |
| 자석 위치 | R = 15 mm |
| 보스 외경 | ∅18 |

값이 안 맞으면 변수 교체 누락된 곳이 있다는 뜻. 해당 피처를 다시 편집.

### Phase 2 단계 권장

- D30 6자리 통합 설계 적용으로 **∅60 Part Studio 필수**. ∅90 캡 출력으로 슬롯 공차·베어링 끼워맞춤 검증 후 ∅60 일괄 출력.

---

## 4.6 Part Studio "11 Acrylic Panel 60"

요일·날씨 드럼용 ∅60 패널. ∅90 패널(§4)과 구조 동일, 폭만 다름.

### 4.6.1 Part Studio 생성 + Variable 연결

1. 탭바 `+` → **Part Studio**, 이름 `11 Acrylic Panel 60`
2. `Feature ▾` → **Variable Studio** → `Clock Config`

### 4.6.2 Feature tree (§4와 동일, panelWidth만 다름)

#### Step 1 — Sketch on Front plane

| 항목 | 값 |
|---|---|
| 평면 | Front |
| 도구 | Center point rectangle |
| 중심 | 원점 |
| 가로 (X) | `#panelWidth_60` (= 22.251) |
| 세로 (Z) | `#panelLength` (= 55) |

#### Step 2 — Extrude

- Profile: Step 1 sketch
- Type: **New**, Symmetric, Depth `#panelThickness` (= 3)

→ 22.251 × 55 × 3 mm 직육면체 1개.

### 4.6.3 발주 수량 (∅60 드럼)

각 ∅60 드럼은 7면 (요일 7개 = 일·월·화·수·목·금·토 / 날씨 6개 + 빈 패널 1개).

| 패널 종류 | 수량 |
|---|---|
| 요일 (일·월·화·수·목·금·토 각 1장) | 7 |
| 날씨 (☀·☁·☂·❄·⚡·☁☂ 등 6 + 빈 1) | 7 |
| **합계 (D1 + D6 = 2 드럼분)** | **14** |

### 4.6.4 마스터 시트 제작 (선택, §4.5 미러)

별도 Part Studio `11b Panel 60 Engraving Sheet` 권장 — 14개 패널을 한 시트에 sketch + Text:

- 레이아웃: 2행 × 7열
- 패널 크기: 22.251 × 55, 간격 5mm
- 시트 전체: `7 × 22.251 + 6 × 5 = 185.76` × `2 × 55 + 5 = 115` mm
- Text: 일·월·화·수·목·금·토 (요일 행) + 날씨 아이콘·빈칸 (날씨 행)
- 폰트: Arial Bold (요일은 한글 폰트 — 나눔고딕 Bold 등)

> 날씨 아이콘은 Onshape Text로 표현 어려움 → 외주 업체에 SVG/EPS 별도 전달 권장. Master sheet에는 outline만 + 라벨(예: "rain", "clear") 텍스트로 구분.

### 4.6.5 DXF Export

1. 패널 앞면 우클릭 → **Export as DXF/DWG** → `cad/dxf/panel_60_blank.dxf`
2. 마스터 시트 sketch 우클릭 → Export DXF → `cad/dxf/panel_60_engraving_sheet.dxf`
3. 발주처 요청 (D29 freeze: panelWidth_60 = **22.251 mm**)

---

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

> **분할 면 (X=±103)에 3mm 벽**. 각 section의 split-end 외벽이 tongue/groove 결합부로 작동. 내부 공간은 split 벽으로 단절되지만, 와이어 통로 등 필요 시 추가 hole로 연결 가능.

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
| 분할 벽 (X=±103 양측) | 각 section의 split-end 외벽 3mm — tongue/groove 결합부 |

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
## 9. Assembly "Full 6-Digit Clock"

### 조립 순서 (D30 통합 enclosure)

> D28 캡 방향: 하부 캡·상부 캡 모두 CAD 그대로 (보스 위로). 하부 캡은 조립 중 M3 잠금 후 영구 고정.

#### Phase 0 — Plate·Box 사전 조립 (Sub-assembly, 작업대 위)

> 각 split sections를 본 조립 전에 작업대 위에서 united로 만든다. 이 단계에서만 cross-screw 내부 face 접근이 가능 (§1.5 hidden-bolt 규칙). 본 조립 시작 후엔 plate가 닫혀 있어 접근 불가.

0a. **07 Bottom Plate 사전 조립** (정상 방향, 드럼 측 = top face가 위로)
   - 작업대에 A·B·C 3 sections를 X 순서대로 배치, top face 위로
   - Tongue-groove 정렬 (A 우측 tongue → B 좌측 groove, C 좌측 tongue → B 우측 groove)
   - A·C section의 ridge가 B section plate 아래로 미끄러져 들어감
   - **B의 top face (드럼 측)에서 ↓로 M3 6mm 볼트 4개** 삽입 (Y=±40 × split 양쪽 = 4개)
   - A·C ridge 안 heat insert에 박힘 → 3 sections united, bolt head는 모두 top face 내부 face
0b. **09 Top Plate 사전 조립** (뒤집어서, 드럼 측 = bottom face가 위로)
   - 작업대에 A·B·C를 **뒤집어** (윗면이 아래로) 배치 → ridge가 위로 향함
   - Tongue-groove 정렬 동일
   - **B의 bottom face (현재 위로 향함, 드럼 측)에서 ↓로 M3 6mm 볼트 4개** 삽입
   - A·C ridge 안 heat insert에 박힘 → 3 sections united
   - 정상 방향으로 다시 뒤집음 → bolt head는 plate 아랫면 (드럼 측, 내부)
0c. **06 Bottom Box 사전 조립** (cavity가 위로 향한 정상 방향)
   - 작업대에 A·B·C를 cavity 위로 향한 채로 배치
   - Tongue-groove 정렬 (X 끝면)
   - 각 split의 cavity 내부 보스 (Z=-20·-60 두 위치, 양 split 합 4개)에 X방향 ↔ M3 6mm 볼트 삽입
   - Cavity 안에서 손이 닿음 (위가 열려있음). Bolt head는 cavity 내벽 = 내부 face

#### Phase 1 — 본 조립

1. 사전 조립 완료된 **06 Bottom Box** 내부에 Mega 2560 + ULN2003 × 6 + MOSFET + LED 바 와이어·전원 어댑터 잭 배치
2. 사전 조립 완료된 **07 Bottom Plate** 위 6 모터 마운트 (4× M3 each), Hall 센서 6개 (인쇄면 위), LED 바 6개 (양면테이프 또는 슬롯)
3. Plate 와이어를 박스 측 슬롯·홀로 통과시킴
4. 07 Bottom Plate를 06 Bottom Box 위에 결합 (M3 + heat insert × 8) — 박스 cavity의 cross-screw 헤드가 plate에 의해 영구 은폐
5. 각 모터축 (총 6개)에 **05 Coupler** 끼움
6. 각 커플러에 **03 Shaft** (∅5×100) 삽입
7. 각 샤프트에 **하부 캡** (D28 — 보스 위, 자석 아래) → **M3 set screw로 샤프트 잠금** (이후 분해 불가)
8. 각 캡 슬롯에 **02 Acrylic Panel** 삽입 (∅90 = 10장, ∅60 = 7장)
9. 각 샤프트에 **상부 캡** 슬라이드 (보스 위) → 임시로 위에 둠 (아직 잠금 X)
10. **08 Side Panels** 4 piece 코너 tongue-groove로 결합 → 사각 ring 형성 (cross-screw 없음, plate clamp만)
11. Ring을 07 Bottom Plate 둘레 홈에 떨어뜨려 끼움 (위에서 ↓)
12. 사전 조립 완료된 **09 Top Plate**의 6 베어링 시트에 **04 Bearing 625ZZ** 6개 압입
13. 09 Top Plate를 위에서 떨어뜨려 측면 판 ring 상단에 끼움 (둘레 홈 정렬) — top plate 하면의 cross-screw 헤드가 드럼 영역에 의해 영구 은폐
14. 각 드럼의 보스가 베어링 안으로 들어가도록 정렬, 상부 캡의 M3 set screw 잠금 (역시 분해 시 풀어야 함)

### 분해 (패널 교체)

1. 09 Top Plate 들어 올림 (둘레 홈에서 분리)
2. 베어링 보스에서 빼냄
3. 상부 캡 M3 set screw 풀고 캡 슬라이드 → 패널 교체 → 상부 캡 재장착
4. 09 Top Plate 다시 끼움

### Mate 종류
- **Fastened**: 06 Box ↔ 07 Plate (insert+screw), Plate ↔ Motor mount points
- **Concentric**: Shaft ↔ Bearing, Shaft ↔ Cap hub
- **Revolute**: Cap ↔ Shaft (회전축)
- **Sliding/snap**: Side Panels ↔ Plates (groove 결합)

### 간섭 체크
- 6 드럼 외경 envelope: 인접 드럼 간 15 mm 클리어런스 (D22)
- Bottom plate 모터 마운트 4 홀: motor body footprint와 충돌 X
- LED 와이어 통과 홀 ↔ 측면 판 위치: 충분한 거리
- 측면 판 두께 (3) + 둘레 홈 폭 (3.4) → 0.4 mm slip fit

---

## 10. Export 체크리스트

### STEP (발주·시뮬용)
- Assembly 전체 → `Export → STEP 214` → `cad/step/full_6digit.stp`

### STL (3D 프린트용, 파트별 — D30 통합 enclosure)
| 파트 | 파일 | 프린터 설정 |
|---|---|---|
| ∅90 Top Cap × 4 | `cad/stl/cap_top_90.stl` | PETG, 0.2mm, 30% |
| ∅90 Bottom Cap × 4 | `cad/stl/cap_bottom_90.stl` | PETG, 0.2mm, 40% (자석 포켓) |
| ∅60 Top Cap × 2 | `cad/stl/cap_top_60.stl` | 동일 |
| ∅60 Bottom Cap × 2 | `cad/stl/cap_bottom_60.stl` | 동일 |
| **06 Bottom Box** (3 split, §1.5) | `cad/stl/bottom_box_A.stl`, `_B.stl`, `_C.stl` | PETG, 0.2mm, 25% |
| **07 Bottom Plate** (3 split) | `cad/stl/bottom_plate_A.stl`, `_B.stl`, `_C.stl` | PETG, 0.2mm, 30~40% |
| **08 Front Panel** (3 split, 디지트 6창 분할 포함) | `cad/stl/side_front_A.stl`, `_B.stl`, `_C.stl` | PETG, 0.2mm, 25% |
| **08 Back Panel** (3 split) | `cad/stl/side_back_A.stl`, `_B.stl`, `_C.stl` | 동일 |
| **08 Left/Right Panel** | `cad/stl/side_left.stl`, `side_right.stl` | 단일 출력 (114mm, 분할 불필요) |
| **09 Top Plate** (3 split) | `cad/stl/top_plate_A.stl`, `_B.stl`, `_C.stl` | PETG, 0.2mm, 30% |

### DXF (아크릴 각인 발주용)
- `cad/dxf/panel_90_engraving_sheet.dxf` — 0~9 마스터 시트 (업체에 발주 완료)
- `cad/dxf/panel_60_blank.dxf` — ∅60 단일 패널 (22.251 × 55) outline
- `cad/dxf/panel_60_engraving_sheet.dxf` — 요일 7 + 날씨 7 = 14장 마스터 시트 (Phase 7)

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
