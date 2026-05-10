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

