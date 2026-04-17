# 설계 의사결정 로그

> 본 문서는 [context.md](context.md) §6의 의사결정 기록이다. Phase 0·1에서 확정된 21건의 설계 결정과, 도달 과정에서 기각된 대안을 보존한다.

---

## 1. 확정 결정 (21건)

| ID | 항목 | 결정 | 근거 |
|---|---|---|---|
| D01 | 기구 방식 | 수직축 다각기둥 드럼 | 숫자 높이 확보 |
| D02 | 드럼 크기 | B안 차등 (∅90×4 + ∅60×2) | 좌우 폭 60 mm 절약 |
| D03 | 면 수 | HH:MM 10면, 요일·날씨 7면 | 숫자 0~9, 요일 7 |
| D04 | 모터 | 28BYJ-48 + ULN2003 (5V) | 보유품 |
| D05 | RTC | DS3231 외장 | 오프라인 정확성 |
| D06 | LED 방식 | 앞 하단 업라이트 + 엣지라이팅 | 공간 절약 |
| D07 | 아크릴 가공 | 레이저 각인 | 솔리드 발광 |
| D08 | 아크릴 뒷면 | 미러 반사 시트 | 광 손실 최소화 |
| D09 | WiFi | **APSTA 동시** (ESP8266 AT) | 통신 순단 내성 |
| D10 | 홈 센서 | 자석 ∅4×2 + A3144 | 외광 영향 없음 |
| D11 | 캡 구조 | 공통 base, 하부만 자석 포켓 | CAD 재사용 |
| D12 | 슬롯 공차 | 3.2 mm | FDM 수축 0.2 mm 여유 |
| D13 | 캡 고정 | M3 히트 인서트 + 세트스크류 | 탈착 반복 |
| D14 | 베어링 | 625ZZ (∅5) | 보유 6001/6004ZZ 부적합 |
| D15 | 커플러 | **3D 프린트** 헬리컬 빔 | 28BYJ-48 샤프트 보호 |
| D16 | 날씨 API | 기상청 단기예보 | 한국 정확도 |
| D17 | 레이아웃 | [요일][HH]:[MM][날씨] | 시간 중앙 강조 |
| D18 | MCU | **Mega 2560 WiFi R3** | 5V + APSTA + 54 GPIO |
| D19 | 설정 UX | 캡티브 포털 | 앱 불필요 |
| D20 | 회전 단위 | 하프 스텝 4096/rev | 누적 오차 최소 |
| D21 | LED 스위칭 | IRLZ44N MOSFET PWM | 릴레이 대비 우위 |
| D22 | 드럼 간격 | HH·MM 내부 10 → **15 mm** 상향 (총폭 580 → 590 mm) | FDM 공차 누적·조립 접근성 확보, 배플 ↔ 드럼 클리어런스 4 → 6.5 mm |

---

## 2. 기각된 결정 이력

| 항목 | 초안 | 변경 사유 |
|---|---|---|
| 드럼 수 | 4 | 요일·날씨 추가 요구 |
| MCU (1차) | ESP32 DevKit | 3.3V 로직 |
| MCU (2차) | Nano RP2040 Connect | WiFiNINA APSTA 미지원 |
| MCU (3차) | UNO R4 WiFi | WiFiS3 APSTA 미지원 |
| WiFi 방식 | BLE GATT | 앱 부담 |
| WiFi 구조 | STA + 폴백 | 순단 시 모드 전환 잦음 |
| 판독성 기준 | 3 m · 시력 1.0 엄격 | 사용자 완화 지시 |
| GPIO 확장 | MCP23017 × 2 | Mega 54 GPIO로 불필요 |
| 펌웨어 언어 | MicroPython | Mega 2560은 Arduino C++ 전용 |

---

## 3. 참고 도면

- LED 조명 3안 비교 → [`images/led_options_compare.svg`](../images/led_options_compare.svg) (D06·D07·D08 근거)
- 드럼 구조 3면도 → [`images/drum_structure.svg`](../images/drum_structure.svg) (D01·D02·D03 근거)
- 캡 도면 → [`images/cap_drawings.svg`](../images/cap_drawings.svg) (D11·D12·D13 근거)
- 하부 캡 상세 → [`images/bottom_cap_detail.svg`](../images/bottom_cap_detail.svg) (D10·D11 근거)
- 시스템 블록도 → [`images/block_diagram.svg`](../images/block_diagram.svg) (D18 반영 v2.0, Mega 2560 기준)
