# GeoTools 실습 과제

## 1. 문서 개요

- 문서 목적: [_workspace/01_curriculum.md](C:\Users\jinif\Documents\workspace\harness-100\codex\geotools-online-course\_workspace\01_curriculum.md)와 [_workspace/02_lesson_plans.md](C:\Users\jinif\Documents\workspace\harness-100\codex\geotools-online-course\_workspace\02_lesson_plans.md)을 기준으로 실습 과제를 재구성한다.
- 진행 모드: 실습 모드
- 설계 원칙:
  - 난이도는 `따라하기 -> 응용 실습 -> 미니 프로젝트` 순으로 점진적으로 상승한다.
  - Shapefile 읽기, GeoJSON 처리, CRS 변환, 지도 렌더링, 간단한 공간 분석을 실습 전체에서 모두 다룬다.
  - 각 실습은 목표, 단계별 가이드, 기대 결과, 체크포인트를 포함한다.
  - 교안의 코드 흐름을 재사용하되, 실습 산출물은 바로 제출 가능한 형태로 남긴다.

## 2. 공통 실습 환경

- 기본 프로젝트: `hello-geotools`
- 권장 JDK: 17
- 권장 빌드 도구: Gradle
- 권장 실행 방식: Gradle Wrapper(`gradlew`, `gradlew.bat`)
- 필수 라이브러리:
  - `gt-shapefile`
  - `gt-geojson`
  - `gt-epsg-hsql`
  - `gt-swing`
  - `gt-render`
  - `gt-main`
- 예제 데이터:
  - `data/districts.shp`
  - `data/roads.shp`
  - `data/stations.geojson`
- 공통 출력 폴더: `output/`

## 3. 난이도 상승 설계

| 실습 ID | 형식 | 난이도 | 연결 목표 | 핵심 주제 |
| --- | --- | --- | --- | --- |
| LAB-01 | 따라하기 | 쉬움 | C-LO-01, C-LO-02, C-LO-03 | 환경 확인, Shapefile 읽기, GeoJSON 변환 |
| LAB-02 | 응용 실습 | 보통 | C-LO-04, C-LO-05 | CRS 변환, 버퍼/교차 분석, 결과 요약 |
| LAB-03 | 미니 프로젝트 | 어려움 | C-LO-03, C-LO-04, C-LO-05, C-LO-06 | 다중 데이터 결합, 분석 결과 렌더링 |

## 4. 실습 과제

### LAB-01. 따라하기 실습: Shapefile 읽기와 GeoJSON 변환

- 형식: 따라하기
- 난이도: 쉬움
- 예상 시간: 70분
- 연결 학습목표 ID:
  - C-LO-01
  - C-LO-02
  - C-LO-03
  - M1-LO-03
  - M2-LO-03
  - M3-LO-01
  - M3-LO-02
- 목표:
  - GeoTools 의존성이 정상 동작하는지 확인한다.
  - Shapefile의 스키마와 기본 Geometry를 읽어 이해한다.
  - 읽은 피처를 GeoJSON으로 내보내는 기본 파이프라인을 완성한다.
- 입력 데이터:
  - `data/districts.shp`
- 산출물:
  - `output/lab01-schema-summary.txt`
  - `output/lab01-districts.geojson`
- 단계별 가이드:
  1. `build.gradle`과 `settings.gradle`이 준비되어 있는지 확인하고 `CRS.decode("EPSG:4326")`를 실행하는 간단한 환경 확인 클래스를 만든다.
  2. `FileDataStoreFinder.getDataStore()`로 `districts.shp`를 연다.
  3. `featureSource.getSchema()`와 `getBounds()`를 이용해 타입 이름, Geometry 필드 이름, 좌표계 이름을 출력한다.
  4. 첫 5개 피처를 순회하면서 ID, 주요 속성, Geometry 타입을 `output/lab01-schema-summary.txt`에 저장한다.
  5. `SimpleFeatureCollection`을 읽어 `FeatureJSON` 또는 동등한 GeoJSON writer로 `output/lab01-districts.geojson`을 생성한다.
  6. 입력 피처 수와 출력 피처 수가 같은지 비교하고, 결과를 한 줄 메모로 남긴다.
- 기대 결과:
  - Shapefile 스키마와 범위를 설명할 수 있다.
  - `districts.shp`의 피처가 GeoJSON으로 정상 변환된다.
  - 학습자는 "파일을 바로 읽는 것"이 아니라 `FeatureSource`를 통해 접근한다는 패턴을 체감한다.
- 체크포인트:
  - `CRS.decode()`가 오류 없이 실행된다.
  - Geometry 필드 이름이 로그에 출력된다.
  - GeoJSON 출력 파일의 피처 수가 원본과 일치한다.
  - `store.dispose()` 또는 자원 해제 패턴이 코드에 포함된다.

### LAB-02. 응용 실습: CRS 변환과 간단한 공간 분석

- 형식: 응용 실습
- 난이도: 보통
- 예상 시간: 95분
- 연결 학습목표 ID:
  - C-LO-04
  - C-LO-05
  - M4-LO-03
  - M5-LO-01
  - M5-LO-02
  - M5-LO-03
- 목표:
  - GeoJSON과 Shapefile 데이터를 공통 CRS로 맞춘다.
  - 버퍼와 교차 연산으로 간단한 공간 분석 결과를 계산한다.
  - 분석 결과를 재사용 가능한 GeoJSON과 요약 보고서로 저장한다.
- 입력 데이터:
  - `data/stations.geojson`
  - `data/districts.shp`
- 산출물:
  - `output/lab02-stations-5186.geojson`
  - `output/lab02-service-buffer.geojson`
  - `output/lab02-analysis-summary.md`
- 단계별 가이드:
  1. `stations.geojson`과 `districts.shp`를 각각 읽고, 스키마의 CRS 정보를 확인한다.
  2. GeoJSON에 CRS 정보가 명시되지 않았다면 교안의 가정에 따라 `EPSG:4326`을 기준으로 해석하고, 그 근거를 주석 또는 메모에 남긴다.
  3. 두 데이터셋을 모두 `EPSG:5186`으로 재투영한다.
  4. 역 포인트마다 `700m` 버퍼를 만들고, 필요하면 `union()`으로 하나의 서비스 영역으로 합친다.
  5. 각 행정구역과 서비스 영역의 `intersection()`을 계산해 `coverage_area_m2`와 `coverage_ratio`를 구한다.
  6. 후보 행정구역이 많다면 `STRtree`를 사용해 먼저 envelope 후보를 줄인 뒤 정밀 계산을 수행한다.
  7. 재투영된 역 데이터는 `output/lab02-stations-5186.geojson`으로, 버퍼 결과는 `output/lab02-service-buffer.geojson`으로 저장한다.
  8. `output/lab02-analysis-summary.md`에 사용한 CRS, 버퍼 반경, 교차 면적 상위 3개 행정구역을 정리한다.
- 기대 결과:
  - 좌표계가 통일된 분석용 GeoJSON이 생성된다.
  - 적어도 하나 이상의 행정구역에서 `coverage_ratio`가 계산된다.
  - 학습자는 "미터 단위 분석 전에 CRS를 먼저 맞춘다"는 습관을 갖게 된다.
- 체크포인트:
  - 재투영 후 좌표값이 위경도 범위가 아니라 미터 기반 값처럼 보인다.
  - 버퍼 면적이 0보다 크다.
  - `coverage_ratio` 값이 `0` 이상 `1` 이하로 계산된다.
  - 분석 요약 문서에 CRS 선택 이유가 적혀 있다.

### LAB-03. 미니 프로젝트: 생활권 접근성 분석 지도 만들기

- 형식: 미니 프로젝트
- 난이도: 어려움
- 예상 시간: 140분
- 연결 학습목표 ID:
  - C-LO-03
  - C-LO-04
  - C-LO-05
  - C-LO-06
  - M3-LO-01
  - M3-LO-02
  - M4-LO-03
  - M5-LO-02
  - M6-LO-02
  - M6-LO-03
- 선택 확장 목표:
  - C-LO-07
  - M7-LO-03
- 프로젝트 시나리오:
  - "행정구역별 대중교통 접근 가능성을 빠르게 설명할 수 있는 지도와 분석 요약을 만든다."
- 목표:
  - 여러 공간 데이터셋을 한 프로젝트 안에서 통합한다.
  - 좌표계를 맞춘 뒤 서비스권 분석을 수행한다.
  - 분석 결과를 스타일이 적용된 지도 이미지로 렌더링한다.
  - 결과를 실무자가 바로 검토할 수 있는 산출물 형태로 정리한다.
- 입력 데이터:
  - `data/districts.shp`
  - `data/roads.shp`
  - `data/stations.geojson`
- 산출물:
  - `output/project-accessibility.geojson`
  - `output/project-accessibility.png`
  - `output/project-summary.md`
- 단계별 가이드:
  1. 분석 기준을 먼저 정한다.
     - 예시 기준: `역 700m 버퍼` 또는 `주요 도로 300m 버퍼`와 의미 있게 겹치는 행정구역을 `접근 양호`로 본다.
  2. `districts.shp`, `roads.shp`, `stations.geojson`을 읽고 스키마, Geometry 타입, CRS를 표로 정리한다.
  3. 세 데이터셋을 공통 좌표계 `EPSG:5186`으로 변환한다.
  4. 역 버퍼와 도로 버퍼를 생성하고, 필요하면 `union()`으로 통합 서비스 영역을 만든다.
  5. 각 행정구역에 대해 아래 속성을 계산한다.
     - `coverage_ratio`
     - `covered` (`true/false`)
     - `nearest_station_m`
  6. 계산 결과를 새 `SimpleFeatureType`으로 구성해 `output/project-accessibility.geojson`으로 저장한다.
  7. `MapContent`를 만들고 행정구역, 도로, 역 레이어를 순서대로 추가한다.
  8. 스타일 규칙을 적용한다.
     - `covered = true`인 행정구역: 녹색 계열 반투명 채우기
     - `covered = false`인 행정구역: 회색 또는 주황 계열 채우기
     - 도로: 대비되는 빨간색 선
     - 역: 파란색 점 심볼
  9. `StreamingRenderer`로 `output/project-accessibility.png`를 생성한다.
  10. `output/project-summary.md`에 아래 내용을 정리한다.
      - 사용한 CRS와 이유
      - 분석 기준
      - 커버리지 상위 3개 행정구역
      - 해석 시 주의할 점 2가지
  11. 선택 확장:
      - GeoServer 엔드포인트가 준비되어 있다면 WMS로 기준 레이어를 불러와 결과 PNG와 시각적으로 비교하거나, WFS로 일부 피처를 조회해 속성/Geometry가 기대와 맞는지 검증한다.
- 기대 결과:
  - 데이터 읽기 -> CRS 정규화 -> 공간 분석 -> 지도 렌더링의 전체 파이프라인이 하나의 산출물로 연결된다.
  - 결과 GeoJSON과 PNG가 동시에 생성된다.
  - 실무 담당자는 이미지와 요약 문서만 보고도 결과를 빠르게 파악할 수 있다.
- 체크포인트:
  - 결과 GeoJSON에 `coverage_ratio`, `covered`, `nearest_station_m` 속성이 포함된다.
  - `coverage_ratio`가 비어 있거나 음수가 아닌지 확인한다.
  - PNG 파일이 생성되고, 레이어 순서가 시각적으로 자연스럽다.
  - 좌표계가 맞지 않아 레이어가 서로 어긋나지 않는다.
  - 선택 확장을 수행했다면 WMS/WFS 비교 결과가 요약 문서에 기록된다.

## 5. 실습 커버리지 점검

### 5.1 필수 주제 커버리지

| 필수 주제 | LAB-01 | LAB-02 | LAB-03 | 점검 결과 |
| --- | --- | --- | --- | --- |
| Shapefile 읽기 | 충족 | 충족 | 충족 | 충족 |
| GeoJSON 처리 | 충족 | 충족 | 충족 | 충족 |
| CRS 변환 | - | 충족 | 충족 | 충족 |
| 지도 렌더링 | - | - | 충족 | 충족 |
| 간단한 공간 분석 | - | 충족 | 충족 | 충족 |

### 5.2 학습목표 커버리지

| 학습목표 ID | 커버 실습 | 점검 결과 |
| --- | --- | --- |
| C-LO-01 | LAB-01 | 충족 |
| C-LO-02 | LAB-01 | 충족 |
| C-LO-03 | LAB-01, LAB-03 | 충족 |
| C-LO-04 | LAB-02, LAB-03 | 충족 |
| C-LO-05 | LAB-02, LAB-03 | 충족 |
| C-LO-06 | LAB-03 | 충족 |
| C-LO-07 | LAB-03 선택 확장 | 부분 충족 |

### 5.3 운영 메모

- LAB-01은 실패 비용이 낮은 실습으로 설계해 초반 진입 장벽을 줄였다.
- LAB-02는 좌표계와 단위 문제를 반드시 드러내도록 설계해 실무 오개념을 예방한다.
- LAB-03은 실제 제출 가능한 결과물 중심으로 설계해 과정 후반의 총괄평가 역할도 겸한다.
- C-LO-07은 사용자의 요청 범위상 필수 주제에서 제외돼 선택 확장으로 배치했다. 운영자가 서비스 연동까지 실습 증거로 요구한다면 짧은 보조 과제를 추가하는 편이 좋다.
