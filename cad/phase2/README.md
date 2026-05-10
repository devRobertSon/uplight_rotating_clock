# Phase 2 Onshape 브리프 — 색인

Onshape 탭 단위로 분리된 브리프. 작업할 탭에 해당하는 파일만 열어 진행.

## 메타

- [`00-overview.md`](./00-overview.md) — 탭 역할, 문서 구조, **§1.5 분할 출력 전략**, DoD, 작업 순서

## Variable Studio

- [`01-variable-studio.md`](./01-variable-studio.md) — `Clock Config` 글로벌 변수 (~50개)

## Part Studios

| 파일 | Onshape 탭 |
|---|---|
| [`02-drum-90.md`](./02-drum-90.md) | `01 Drum ∅90 Caps` (HH·MM 드럼 캡) |
| [`03-panel-90.md`](./03-panel-90.md) | `02 Acrylic Panel 90` (∅90 패널 + DXF 마스터 시트) |
| [`04-drum-60.md`](./04-drum-60.md) | `10 Drum ∅60 Caps` (요일·날씨 드럼 캡) |
| [`05-panel-60.md`](./05-panel-60.md) | `11 Acrylic Panel 60` (∅60 패널 + DXF) |
| [`06-shaft.md`](./06-shaft.md) | `03 Shaft` (∅5×100 연마봉) |
| [`07-bearing.md`](./07-bearing.md) | `04 Bearing 625ZZ` |
| [`08-coupler.md`](./08-coupler.md) | `05 Coupler` (보유품 placeholder) |
| [`09-bottom-box.md`](./09-bottom-box.md) | `06 Bottom Box` (3 sections, A·B·C) |
| [`10-bottom-plate.md`](./10-bottom-plate.md) | `07 Bottom Plate` (3 sections, 모터·Hall·LED·둘레 홈) |
| [`11-side-panels.md`](./11-side-panels.md) | `08 Side Panels` (전·후·좌·우 4 piece) |
| [`12-top-plate.md`](./12-top-plate.md) | `09 Top Plate` (3 sections, 6 베어링) |

## Assembly

- [`13-assembly.md`](./13-assembly.md) — `Full 6-Digit Clock` 조립 + 간섭 체크

## Export

- [`14-export.md`](./14-export.md) — STEP / STL / DXF 내보내기

---

## 작업 순서 (요약)

1. `01-variable-studio` — 변수 정의 (먼저 끝내야 모든 Part Studio가 참조 가능)
2. `02-drum-90` → `03-panel-90` (DXF 발주는 이미 완료)
3. `06-shaft` → `07-bearing` → `08-coupler`
4. **`09-bottom-box`** → **`10-bottom-plate`** → **`11-side-panels`** → **`12-top-plate`** (D30 통합 enclosure, 모두 분할 설계)
5. `04-drum-60` → `05-panel-60`
6. `13-assembly` → `14-export`

전체 순공수 ~12.5h, 자세한 일정은 [`00-overview.md` §13](./00-overview.md#13-권장-작업-순서) 참조.

## 외부 참조

- 결정 로그: [`../../docs/decisions.md`](../../docs/decisions.md)
- 사양: [`../../docs/spec.md`](../../docs/spec.md)
- WBS: [`../../docs/wbs.md`](../../docs/wbs.md)
- BOM: [`../../docs/bom.md`](../../docs/bom.md)
