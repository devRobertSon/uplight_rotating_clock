# Uplight Rotating Clock (아크릴 회전 숫자 시계)

24시간제 디지털 시계. 6개의 독립 회전 드럼(요일 · HH:MM · 날씨)이 수직축을 중심으로 회전하며, 전면 아크릴 패널의 레이저 각인에 앞쪽 하단 5V LED 바가 엣지라이팅으로 빛을 주입하여 산란 발광시킨다.

---

## 현재 상태 (2026-04-17 기준)

- **Phase 0 (요구·치수 확정)** ✅ 완료
- **Phase 1 (핵심 설계 결정 21건)** ✅ 완료
- **Phase 2 (프로토 1자리 CAD)** 🔜 착수 대기
- **부품 구매**: Mega 2560 WiFi R3 및 기구·전자 일체 주문 완료, 입고 대기
- **진행률**: 약 7% (순 공수 6h/94h 소진, 경과 25~27주 예상, 주당 3h 가용)

---

## 한눈에 보기

| 항목 | 값 |
|---|---|
| 드럼 구성 | 요일 ∅60 · HH·MM ∅90 × 4 · 날씨 ∅60 (총 6개) |
| 총폭 × 깊이 × 높이 | 580 × 95 × 140 mm |
| MCU | Arduino Mega 2560 WiFi R3 (ATmega2560 + ESP8266) |
| 로직 전압 | 5V 통일 (레벨 시프터 불필요) |
| WiFi | APSTA 동시 모드 (캡티브 포털) |
| 시간 소스 | NTP (1차) + DS3231 (2차) |
| 날씨 소스 | 기상청 단기예보 API |
| 언어 | Arduino C++ |

### 시스템 블록도

![시스템 블록도](images/block_diagram.svg)

> ⚠️ **주의**: 현재 블록도는 ESP32 + MCP23017 × 2 기준 **구버전**. Phase 2와 병행하여 Mega 2560 기준으로 재작성 예정.

### 레이아웃 (좌→우, 단위 mm)

```
[요일 ∅60] 20 [시십 ∅90] 10 [시일 ∅90] 40(콜론) [분십 ∅90] 10 [분일 ∅90] 20 [날씨 ∅60]
```

---

## 문서 구조

| 문서 | 내용 |
|---|---|
| [docs/spec.md](docs/spec.md) | 기구 · 전자 · 인터페이스 사양 |
| [docs/decisions.md](docs/decisions.md) | 설계 의사결정 로그 (21건 확정 + 기각 이력) |
| [docs/bom.md](docs/bom.md) | 자재 명세서 (구매 완료 / 보유 / 외주) |
| [docs/wbs.md](docs/wbs.md) | WBS · 리스크 · 다음 작업 |
| [CONTEXT.md](CONTEXT.md) | Phase 0·1 전체 컨텍스트 핸드오프 원문 |

### 이미지 산출물

| 파일 | 설명 |
|---|---|
| [images/drum_structure.svg](images/drum_structure.svg) | 드럼 전체 구조 3면도 |
| [images/cap_drawings.svg](images/cap_drawings.svg) | ∅90·∅60 캡 도면 |
| [images/bottom_cap_detail.svg](images/bottom_cap_detail.svg) | 하부 캡 상세 (자석 포켓) |
| [images/led_options_compare.svg](images/led_options_compare.svg) | LED 조명 3안 비교 |
| [images/block_diagram.svg](images/block_diagram.svg) | 시스템 블록도 (구버전) |

---

## 다음 해야 할 작업

1. 부품 입고 확인 및 DS3231 R5 저항 제거
2. Phase 2 착수 — CAD 툴 확정 후 드럼 1자리 설계 (STEP/STL + DXF)
3. 블록도 Mega 2560 기준 재작성
