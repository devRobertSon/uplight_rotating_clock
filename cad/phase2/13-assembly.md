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

