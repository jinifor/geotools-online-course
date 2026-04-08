# GeoTools 산출물 리뷰 보고서

## 1. 검토 개요

- 검토 일자: 2026-04-09
- 검토 대상:
  - [_workspace/01_curriculum.md](C:\Users\jinif\Documents\workspace\harness-100\codex\geotools-online-course\_workspace\01_curriculum.md)
  - [_workspace/02_lesson_plans.md](C:\Users\jinif\Documents\workspace\harness-100\codex\geotools-online-course\_workspace\02_lesson_plans.md)
  - [_workspace/03_quizzes.md](C:\Users\jinif\Documents\workspace\harness-100\codex\geotools-online-course\_workspace\03_quizzes.md)
  - [_workspace/04_labs.md](C:\Users\jinif\Documents\workspace\harness-100\codex\geotools-online-course\_workspace\04_labs.md)
- 검토 기준:
  - 학습목표 커버리지
  - 난이도 흐름
  - 누락된 개념
  - 실습 적절성
  - 실무 활용 가능성

## 2. 종합 판정

- 판정: 사용 가능
- 치명적 누락: 없음
- 수정 필요 여부: 없음
- 운영 전 확인 필요:
  - 예제 데이터의 실제 필드명과 CRS 메타데이터
  - GeoServer/PostGIS 등 외부 엔드포인트 준비 여부
  - `build.gradle`, `settings.gradle`, Gradle Wrapper 기준의 실행 명령 통일 여부

## 3. 항목별 리뷰

### 3.1 학습목표 커버리지

| 학습목표 ID | 커리큘럼 | 교안 | 퀴즈 | 실습 | 판정 |
| --- | --- | --- | --- | --- | --- |
| C-LO-01 | 명시 | Module 1 | Q01 | LAB-01 | 충족 |
| C-LO-02 | 명시 | Module 2 | Q02, Q07 | LAB-01 | 충족 |
| C-LO-03 | 명시 | Module 3 | Q03, Q08, Q13 | LAB-01, LAB-03 | 충족 |
| C-LO-04 | 명시 | Module 4 | Q04, Q09 | LAB-02, LAB-03 | 충족 |
| C-LO-05 | 명시 | Module 5 | Q10, Q14 | LAB-02, LAB-03 | 충족 |
| C-LO-06 | 명시 | Module 6 | Q05, Q11, Q15 | LAB-03 | 충족 |
| C-LO-07 | 명시 | Module 7 | Q06, Q12, Q16 | LAB-03 선택 확장 | 충족(실습은 확장형) |

- 검토 의견:
  - 교안과 퀴즈는 C-LO-01부터 C-LO-07까지 모두 직접 커버한다.
  - 실습은 사용자 요청 범위에 맞춰 핵심 오프라인 흐름을 중심으로 재구성됐고, C-LO-07은 선택 확장으로 남겨 두었다.
  - 입력서, 커리큘럼, 교안, 실습서의 빌드 도구 전제는 Gradle 기준으로 통일돼 문서 간 충돌이 없다.
  - 전체 산출물 관점에서는 목표 누락이 없다.

### 3.2 난이도 흐름

| 산출물 | 흐름 | 판정 |
| --- | --- | --- |
| 커리큘럼 | 환경 설정 -> 모델링 -> I/O -> CRS -> 분석 -> 렌더링 -> 서비스 연동 | 충족 |
| 교안 | 모듈 내부에서 개념 -> 코드 -> 확인 포인트 순으로 전개 | 충족 |
| 퀴즈 | 쉬움 -> 보통 -> 어려움 순으로 배치 | 충족 |
| 실습 | 따라하기 -> 응용 실습 -> 미니 프로젝트 순으로 상승 | 충족 |

- 검토 의견:
  - 난이도 흐름은 문서 간 일관성이 높다.
  - CRS를 공간 분석보다 앞에 배치한 점이 초급자 오개념 예방에 특히 유효하다.

### 3.3 누락된 개념

| 항목 | 판정 | 근거 |
| --- | --- | --- |
| GeoTools 핵심 개념 | 충족 | Feature, Geometry, CRS, DataStore, MapContent, WMS/WFS가 모두 반영됨 |
| 데이터 처리 흐름 | 충족 | 읽기/쓰기, 변환, 재투영, 분석, 렌더링이 모두 연결됨 |
| 현업 연계 개념 | 부분 제한 | KML, PostGIS, GeoServer 운영 심화는 범위상 축소됨 |

- 검토 의견:
  - 치명적인 개념 누락은 없다.
  - KML/PostGIS/GeoServer 운영 심화는 원래 입문 과정 범위 밖이므로 현재 수준의 언급은 적절하다.

### 3.4 실습 적절성

| 항목 | 판정 | 근거 |
| --- | --- | --- |
| 실습 난이도 조절 | 충족 | LAB-01에서 기본 패턴, LAB-02에서 CRS/분석, LAB-03에서 통합 적용 |
| 단계별 가이드 명확성 | 충족 | 각 실습에 목표, 단계, 기대 결과, 체크포인트가 명시됨 |
| 필수 주제 반영 | 충족 | Shapefile, GeoJSON, CRS, 렌더링, 공간 분석이 모두 포함됨 |
| 서비스 연동 실습 강도 | 부분 제한 | C-LO-07은 선택 확장으로 배치됨 |

- 검토 의견:
  - 사용자가 요청한 실습 범위에는 잘 맞는다.
  - 만약 과정 운영자가 "모든 C-LO에 대해 필수 실습 증거"를 요구한다면, WFS 또는 WMS 미리보기 보조 과제 1개를 추가하는 개선이 가능하다.

### 3.5 실무 활용 가능성

| 항목 | 판정 | 근거 |
| --- | --- | --- |
| 실무형 산출물 | 충족 | GeoJSON, PNG, 분석 요약 문서 등 실제 전달 가능한 형태를 요구함 |
| 현업 의사결정 연결성 | 충족 | 버퍼/교차/가시화 조합이 실무 시나리오와 연결됨 |
| 운영 리스크 | 관리 필요 | 외부 엔드포인트와 데이터 메타데이터 준비 상태에 영향받음 |

- 검토 의견:
  - 단순 API 연습을 넘어서 "결과 전달"까지 요구한다는 점에서 실무 활용성이 높다.
  - 특히 LAB-03은 과정의 총괄평가 역할을 수행하기에 적절하다.

## 4. 개선 권장 사항

- GeoServer 환경이 준비되어 있다면 LAB-03 선택 확장을 필수 체크포인트로 승격해 C-LO-07 실습 증거를 강화한다.
- `data/` 예제 파일의 실제 CRS와 속성명을 운영 전에 고정해 교안 코드와 실습 안내의 차이를 없앤다.
- 퀴즈를 LMS에 옮길 경우 Q13~Q16은 해설을 축약하고, 오답 피드백만 별도 필드로 분리하면 자동 채점 운영이 쉬워진다.

## 5. 최종 결론

- 현재 산출물 세트는 입문 GeoTools 과정용으로 바로 사용 가능한 수준이다.
- `수정 필요` 상태의 치명적 문제는 없고, 남은 이슈는 운영 준비와 선택 확장 범위의 문제다.
