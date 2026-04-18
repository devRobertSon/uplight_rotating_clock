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
- **Variable Studio UI Name 칸**: `#` 없이 — `drumDiameter`
- **Part Studio 치수 입력창**: `#` 붙여서 — `#drumDiameter`

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

각 변수는 UI 폼에서 **Name · Value · Description** 세 칸을 채워 입력 (코드 아님).
- **Name 칸에는 `#` 를 넣지 않음** — `drumDiameter` 라고만 입력
- **Value 칸**에는 값과 단위를 함께 (`90 mm`, `10`, `180 deg` 등)
- 참조는 Part Studio에서 `#drumDiameter` 로

### 2.3 입력할 변수 목록

| Name | Value | Description |
|---|---|---|
| `drumDiameter` | `90 mm` | 드럼 외경 (∅60 variant 존재) |
| `drumFaces` | `10` | 면 수 (∅60은 7) |
| `drumVisibleHeight` | `44 mm` | 가시 영역 높이 |
| `capThickness` | `6 mm` | 캡 두께 |
| `drumOverallHeight` | `= 2 * capThickness + drumVisibleHeight` | 자동 계산: 56 mm |
| `panelLength` | `55 mm` | 상부 3 + 가시 44 + 하부 6 + 노출 2 |
| `panelThickness` | `3 mm` | 아크릴 두께 |
| `panelWidth_90` | `= drumDiameter * sin(180 deg / drumFaces)` | 27.81 mm |
| `panelWidth_60` | `= 60 mm * sin(180 deg / 7)` | 26.04 mm |
| `slotWidth` | `= panelThickness + 0.2 mm` | 3.2 mm (FDM 공차) |
| `hubOuter_90` | `20 mm` | ∅90 캡 허브 외경 |
| `hubOuter_60` | `18 mm` | ∅60 캡 (D13, 인서트 OD5 수용) |
| `shaftHole` | `5.2 mm` | 축 홀 (+0.2/-0) |
| `insertHole` | `4.2 mm` | M3 히트 인서트 OD5×L4 |
| `magnetDiameter` | `4.1 mm` | 자석 ∅4 + 0.1 여유 |
| `magnetDepth` | `2.1 mm` | 자석 ∅4×2 + 0.1 여유 |
| `magnetRadius_90` | `30 mm` | ∅90 드럼 중심 → 자석 중심 |
| `magnetRadius_60` | `15 mm` | ∅60 드럼 |
| `baffleThickness` | `2 mm` | 배플 두께 |
| `gapInner` | `15 mm` | HH·MM 내부 간격 (D22) |
| `gapOuter` | `20 mm` | 요일/날씨 ↔ 숫자 |
| `gapColon` | `40 mm` | 콜론 |
| `shaftDiameter` | `5 mm` | |
| `shaftLength` | `75 mm` | |
| `bearingOD` | `16 mm` | 625ZZ 외경 |
| `bearingID` | `5 mm` | |
| `bearingWidth` | `5 mm` | |

### 2.4 Part Studio에서 변수 참조하기

각 Part Studio 맨 위에 **Variable Studio 피처**를 삽입하여 연결:

1. Part Studio 좌상단 `Feature ▾` → `Variable Studio` 선택
2. Reference 대화상자에서 `Clock Config` 선택 → ✓
3. 이후 스케치 치수 입력창에 `#drumDiameter` 등으로 사용

---

## 3. Part Studio "01 Drum ∅90 Caps"

### Feature tree (순서대로)
1. **Sketch "base"** on Top plane
   - 외경 원: `#drumDiameter / 2`
   - 허브 원: `#hubOuter_90 / 2`
   - 축 홀 원: `#shaftHole / 2`
2. **Extrude "cap body"** → `#capThickness`, 솔리드
3. **Sketch "slots"** on Top face
   - `#drumFaces` 개의 슬롯을 원주 상에 배열 (Linear/Circular Pattern)
   - 각 슬롯: 두께 `#slotWidth`, 폭 `#panelWidth_90 + 1mm` (패널보다 약간 긴 여유)
   - 허브 면까지 관통 배치하고 Circular Pattern 10개 (면 간 36°)
4. **Extrude "slot cut"** → Through All → 파트 빼기
5. **Sketch "insert hole"** on 옆면 허브
   - 축 직교 방향으로 M3 세트스크류 입구
   - 원 `#insertHole / 2`
6. **Extrude** → `#hubOuter_90 / 2` depth → 파트 빼기
7. **Configuration: "variant"**
   - `isBottomCap = false`(상부) / `true`(하부)
   - Feature suppress/enable로 자석 포켓 2개 제어

### 자석 포켓 (하부 variant만)
8. **Sketch "magnet pockets"** on Bottom face
   - 원 2개, 대각선 배치: (`+#magnetRadius_90`, 0) 및 (`-#magnetRadius_90`, 0)
   - 각각 `#magnetDiameter / 2`
9. **Extrude "magnet pocket"** → `#magnetDepth`, 파트 빼기
   - Configured: `isBottomCap == true` 일 때만 enable

### 검증
- 슬롯 중심 원주상 각도 = 360/`#drumFaces` = 36° (∅60은 51.43°)
- 슬롯이 패널 폭과 정확히 정렬되는지 (아크릴 시뮬 placement 후)

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

- [ ] 모든 변수가 Variable Studio에 정의됨
- [ ] `#drumDiameter` 를 60으로 바꾸면 ∅60 캡도 동일 Part Studio에서 생성 가능
- [ ] STEP 익스포트 성공
- [ ] 주요 파트 STL 6개 익스포트 성공
- [ ] 아크릴 DXF 1개 익스포트 성공
- [ ] Assembly에서 드럼 회전 테스트 (Revolute 회전 자유도 확인)
- [ ] 간섭 체크: 충돌 0건

## 12. 이후 단계 (Phase 3~4 연계)

- Phase 3: 부품 입고 후 실측 → Variable Studio 값 조정 (실제 샤프트 길이, 베어링 폭 편차 등)
- Phase 4: 이 어셈블리를 실제 조립하며 공차 검증 → 슬롯 폭·허브 크기 재조정
- Phase 6: `#drumDiameter` 60 variant 추가 + 프레임 6자리로 확장

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
