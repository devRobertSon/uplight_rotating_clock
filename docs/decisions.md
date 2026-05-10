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
| D23 | 슬롯·패널 폭 | 슬롯 길이 = `2·(apothem-slotWidth)·tan(180°/n) - 0.5`. 패널 폭 = 슬롯 - 0.2. ∅90: 25.032 mm, ∅60: 22.251 mm (3자리 정밀도) | 슬롯 길이를 폴리곤 변과 동일하게 두면 인접 슬롯이 vertex 안쪽에서 겹쳐 캡 절단 (Phase 2 CAD 검증 중 발견) |
| D24 | 슬롯 깊이 | 상부 캡 3 mm Blind (관통 X), 하부 캡 6 mm Through | 패널 위쪽 끝을 상부 캡이 잡아주고, 하부에서 LED 광 주입 위해 관통 |
| D25 | 허브 보스 | ∅20 × **12 mm** 원기둥을 캡 위로 돌출 (M3 인서트 홀 자리용). M3 홀은 보스 중앙 z = 12 mm | 슬롯이 캡 두께 전체(하부) 또는 상반(상부)을 차지해 M3 홀을 끼울 z 자리 부족. 보스로 z 자리 확보. 1차 6 mm 시도 시 인서트 위·아래 1 mm씩만 남아 프린트 시 타이트, 12 mm로 상향 (인서트 위·아래 4 mm 여유) |
| D26 | 샤프트 길이 | 75 mm → **100 mm** 신규 발주 (75 mm × 10개 폐기) | D25 양 보스 12 mm 적용으로 스택 12+12+6+44+6+12+5 = 97 mm. 75 mm로는 22 mm 부족, 100 mm로 약 3 mm 마진 |
| D27 | Frame 측면 기둥 높이 | 140 mm → **160 mm** (D28에 의해 140 mm로 회복) | D25/D26 후 전체 envelope 재계산: 모터 19 + 커플러 25 + 보스 12 + 캡 6 + 드럼 44 + 캡 6 + 보스 12 + 베어링 5 + 상하 plate ≈ 147 mm. 160 mm로 상향해 약 13 mm 조립 버퍼 확보 (시계 외형 높이 590 × 95 × 160) |
| D28 | 하부 보스 방향 | 하부 캡의 보스를 **드럼 내부 방향(위)** 으로 배치, 드럼 가시 영역(R<10 중심부)과 높이 공유. 양 캡 모두 M3 heat insert + set screw 사용 (접착제 X) — 하부 캡은 조립 단계 ②(패널 삽입 전)에서 set screw 잠그고, 이후 드럼 닫히면서 M3 홀이 드럼 내부로 갇혀 외부 접근 불가. 결과적으로 분해 불가 (의도된 영구 잠금) | 보스 24 mm 외부 추가 → 12 mm 절감. envelope 147 → 127 mm. Frame 측면 기둥 160 → 140 mm 회복. 상부 캡은 보스 외부 유지 → M3 접근·패널 교체 가능 (D13 탈착 반복 부합). CAD 모델 변경 없음 (조립 방향만 변경 — 하부 캡을 뒤집지 않음) |
| D29 | 패널 폭 freeze (3자리) | `panelWidth_90` = **25.032 mm**, `panelWidth_60` = **22.251 mm** 고정. 변수 의존 방향 반전: slot ← panel (slotLength = panelWidth + 0.2). drumDiameter·drumFaces·slotWidth 변경 시 panelWidth는 변하지 않음. **유도 근거** (D29 freeze 시점): ∅90/10면 → `2·(apothem_90 - slotWidth)·tan(180°/10) - 0.5` = `2·(42.798-3.2)·tan(18°) - 0.5` = 25.232 슬롯, 패널 = 슬롯 - 0.2 = **25.032**. ∅60/7면 → `2·(27.029-3.2)·tan(180°/7) - 0.5` = 22.451 슬롯, 패널 = **22.251** | 외주 가공 업체에 3자리 정밀도 사양으로 발주 완료. 사후 변경 불가. 향후 어떤 설계 변경도 패널 폭에 영향 주면 안 됨 — drumDiameter 변경 시 panel ≤ chord 조건 검증 필요 (∅82 미만 시 panel이 chord 초과) |
| D30 | 통합 enclosure 구조, 6자리 직접 진입 | **단일 자리 프로토 폐기**, 6자리 통합 설계로 직접 진입. 4개 신규 Part Studio: **06 Bottom Box** (전자부 enclosure, 뚜껑 없음) + **07 Bottom Plate** (박스 뚜껑 + 6 모터 마운트 + 6 Hall + 6 LED 홀 + 둘레 홈) + **08 Side Panels** (전·후·좌·우 4 piece, 코너 tongue-groove 결합) + **09 Top Plate** (6 베어링 시트 + 둘레 홈). 측면 판은 코너에서 서로 결합 후 ring 형태로 plate에 끼움. **§6 Motor Mount, §7 Hall Bracket 별개 Part Studio 제거** — 07 Bottom Plate에 통합. 시계 외형 620 × 120 × ~180 mm | (1) 단일 자리 프로토 의미 없음 — plate가 6 자리 통합이라 1자리만 만들 수 없음 (2) 외관 깔끔: 정면 6 디지트 창만 노출, 나머지 닫힘 (3) 모터/드럼 격벽 자연 형성 — 광 누설·먼지 차단 (4) 케이블 관리 내장 — Hall·모터 와이어가 plate 통해 박스로 정리 (5) tongue-groove 결합 — 무도구 분해 가능 (6) Phase 2 → Phase 6 본편 단축 |
| D31 | 분할 출력 전략 (3D 프린터 베드 200mm 한계) | 가로 620mm 부품(06·07·09)은 X = ±103 두 곳에서 절단 → 3 sections (A·B·C, 각 ~207mm). 결합: **사각 tongue-groove (8 × 4mm) + M3 cross-screw**. FDM 정밀도 한계(±0.3mm)로 사다리꼴 도브테일 부적합. **Onshape Option B** — 처음부터 3 section 모델링 (slicer 분할 X). 5개 부품(06 박스, 07 하부 plate, 08 전·후 패널, 09 상부 plate)이 모두 같은 X 위치에서 분할되어 split line 수직 일관 | 일반 hobbyist 프린터 베드 200×200mm로 출력 가능. Joint 강도·정렬 정밀 |
| D32 | Cross-screw 위치 = 내부 face | 외부에서 보이는 볼트 헤드 0개. **06 Bottom Box**: cavity 내부 보스에 X방향 수평 cross-screw. **07 Bottom Plate**: top face(드럼 측)에서 ↓ 진입, 한쪽 section 하면 ridge로 다른 section을 받침. **09 Top Plate**: bottom face(드럼 측)에서 ↑ 진입, ridge가 하면(드럼 영역 측). **08 Side Panels**: cross-screw 제거 — plate 둘레 홈 클램핑만으로 ring 고정. 본 조립 전 **plate 사전 조립 (Phase 0)** 단계에서 plate를 뒤집고 cross-screw 잠금 | 외부 visible 차단으로 시계 외관 깔끔. 사전 조립으로 내부 face 접근 가능 |
| D33 | Section 분할에 split 벽 추가 + cable passthrough | 초안: cavity가 split 면(X=±103)까지 도달 → tongue 8×80이 바닥 strip 3mm만 접촉, 76mm 부유 → 3D 프린트 부러짐. **수정**: 각 section split-end에 3mm 벽 유지하여 tongue/groove 결합부로 작동. Cavity X 범위: A=-307~-106, B=-100~+100, C=+106~+307. 단 풀 벽이 Mega 2560 + 6 ULN2003 와이어를 막음 → **H-shape 골격**으로 축소: center 수직 strip (Y=±12, 24mm 폭, tongue/groove 지지) + top 림 + back boss pad + 퍼리미터. Front/back 통로(45×74 + 37×74 = 6068 mm²) cut으로 와이어 통과. 벽 부피 28800 → 9800 mm³ (34%) | Tongue 부유 결함 발견 → 벽 추가 → 와이어 막힘 발견 → 최소치만 남기는 H-shape으로 균형 |
| D34 | Section C는 A의 Mirror | A를 모델링한 후 Onshape **Mirror feature** (Right plane, X=0)로 C 솔리드 통째 복사. A 절차 재실행 불필요. DC잭도 mirror 결과로 X=+200에 복제 (예비/패스스루로 활용). A 후속 변경 시 C 자동 갱신 (parametric link) | 작업 시간 절반, A·C 일관성 자동 보장 |
| D35 | Phase 2 브리프를 14개 탭별 파일로 분리 | 기존 1793줄 단일 `cad/phase2-onshape-brief.md` → `cad/phase2/` 디렉토리로 분리: `00-overview` (메타·분할 전략·DoD·작업 순서) + `01-variable-studio` + `02-drum-90` ~ `14-export`. Onshape 탭 1개 = md 1개. 기존 brief는 색인 redirect (37줄)로 축소 | read·edit 비효율 해소 (각 파일 40~300줄), edit 충돌 면적 최소화 |

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
