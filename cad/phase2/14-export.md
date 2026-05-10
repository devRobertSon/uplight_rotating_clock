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

