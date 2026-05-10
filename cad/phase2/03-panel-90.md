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

