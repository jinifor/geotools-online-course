# GeoTools 교육과정 입력 기준서

## 1. 문서 목적

이 문서는 [_workspace/01_curriculum.md](C:\Users\jinif\Documents\workspace\harness-100\codex\geotools-online-coourse\_workspace\01_curriculum.md)를 기준으로 정리한 입력 기준서다.  
후속 산출물(`02_lesson_plans.md`, `03_quizzes.md`, `04_labs.md`)은 본 문서의 요구사항과 커리큘럼의 학습목표 ID를 함께 따른다.

## 2. 과정 기본 정보

- 과정명: GeoTools로 시작하는 Java GIS 개발 입문
- 주제: GeoTools (Java GIS Toolkit)
- 과정 모드: 커리큘럼 기반 설계
- 대상 학습자: Java 개발 경험이 있는 초급~중급 개발자
- 총 학습 시간: 7시간 40분 (460분)
- 운영 원칙: 실습 중심
- 이론:실습 비율: 약 37:63

## 3. 학습자 프로파일

- Java 기본 문법, 객체지향 프로그래밍, 컬렉션 사용에 익숙한 학습자
- Gradle 사용 경험이 있거나 제공된 빌드 스크립트를 읽고 실행할 수 있는 학습자
- GIS 및 GeoTools 경험은 초급 수준이거나 제한적인 학습자
- 공간 데이터 처리와 시각화를 코드로 구현해보고 싶은 개발자

## 4. 과정 수준 목표

- C-LO-01: GeoTools 프로젝트를 설정하고 OGC 핵심 개념을 설명할 수 있다.
- C-LO-02: JTS와 GeoTools 구조를 사용해 Geometry와 Feature를 모델링할 수 있다.
- C-LO-03: Shapefile, GeoJSON, WKT 중심의 데이터 읽기/쓰기 흐름을 구현하고 DataStore API 패턴을 설명할 수 있다.
- C-LO-04: EPSG 코드와 CRS를 해석하고 GeoTools로 좌표 변환을 수행할 수 있다.
- C-LO-05: 기본 공간 관계와 공간 연산을 사용해 간단한 분석 로직을 구현할 수 있다.
- C-LO-06: GeoTools로 레이어를 렌더링하고 스타일을 적용해 지도를 이미지 또는 간단한 UI로 출력할 수 있다.
- C-LO-07: WMS/WFS와 Filter Encoding을 활용해 외부 공간 서비스를 조회하고 GeoServer와 연동할 수 있다.

## 5. 확정 모듈 구성

| 모듈 | 시간 | 연결 목표 | 핵심 결과 |
| --- | --- | --- | --- |
| Module 1. 과정 소개 및 환경 설정 | 45분 | C-LO-01 | `hello-geotools` 프로젝트 실행 |
| Module 2. 공간 데이터 기초 | 55분 | C-LO-02 | Geometry/Feature 모델링 예제 |
| Module 3. 데이터 읽기 / 쓰기 | 70분 | C-LO-03 | Shapefile-GeoJSON 변환 예제 |
| Module 4. 좌표계(CRS)와 좌표 변환 | 60분 | C-LO-04 | 재투영 예제 |
| Module 5. 공간 연산 | 75분 | C-LO-05 | 버퍼/교차 기반 분석 예제 |
| Module 6. 지도 렌더링과 스타일링 | 85분 | C-LO-06 | 스타일 적용 지도 렌더링 |
| Module 7. OGC 웹 서비스 연동 | 70분 | C-LO-07 | WMS/WFS 클라이언트 데모 |

## 6. 모듈별 필수 포함 요소

### Module 1. 과정 소개 및 환경 설정

- GeoTools의 역할, 생태계, 활용 사례
- Java, Gradle, VS Code 기반 환경 구성
- GeoTools 의존성 추가 및 첫 실행
- OGC 핵심 개념 소개
  - Feature
  - Geometry
  - CRS

### Module 2. 공간 데이터 기초

- Geometry 모델 이해
  - `Point`
  - `LineString`
  - `Polygon`
  - `Multi*`
- JTS 기초
- `Feature`, `FeatureType` 구조 이해
- 속성 데이터와 공간 데이터의 결합

### Module 3. 데이터 읽기 / 쓰기

- Shapefile 읽기/쓰기
- GeoJSON, KML, WKT 입출력
- `DataStore` API 패턴 이해
- PostGIS 또는 공간 DB 연동 흐름 소개

### Module 4. 좌표계(CRS)와 좌표 변환

- EPSG 코드와 CRS 개념
- CRS 정의 및 파싱
- 좌표 변환(Reproject) 실습
- 한국 좌표계 활용
  - EPSG:5186
  - UTM-K

### Module 5. 공간 연산

- 공간 관계 판별
  - `intersects`
  - `contains`
  - `within`
- 공간 연산
  - `buffer`
  - `union`
  - `intersection`
  - `difference`
- 거리 계산 및 면적 계산
- 공간 인덱스 활용
  - `STRtree`

### Module 6. 지도 렌더링과 스타일링

- `MapContent` 구조 이해
- SLD(`Styled Layer Descriptor`) 기초
- 레이어 스타일 적용
  - 색상
  - 선
  - 심볼
- 지도 이미지 출력
  - `PNG`
  - `SVG`
- Swing `MapPane` 기반 인터랙티브 지도 소개

### Module 7. OGC 웹 서비스 연동

- WMS 연결 및 레이어 호출
- WFS 피처 요청
- Filter Encoding 기반 공간 쿼리 작성
- GeoServer 연동 실습

## 7. 핵심 실습 요구사항

- 실습은 환경 설정 -> Geometry/Feature 모델링 -> 파일 입출력 -> CRS 변환 -> 공간 분석 -> 렌더링 -> 서비스 연동 순으로 진행한다.
- 아래 항목은 반드시 핸즈온 실습에 포함한다.
  - Shapefile 처리
  - GeoJSON 처리
  - CRS 변환
  - `buffer`
  - `intersection`
  - 지도 렌더링
- 아래 항목은 입문 수준의 연결 실습 또는 가이드 데모로 제한한다.
  - KML
  - PostGIS
  - Swing `MapPane`
  - WMS/WFS
  - GeoServer

## 8. 실습 환경 및 준비 조건

- 필수 환경:
  - JDK 17
  - Gradle
  - GeoTools
  - 예제 공간 데이터셋(Shapefile, GeoJSON)
- 선택 환경:
  - 사전 구성된 PostGIS 엔드포인트
  - 사전 구성된 GeoServer 엔드포인트
- 권장 도구:
  - VS Code

## 9. 범위와 제외 범위

- 포함 범위:
  - GeoTools 기반 Java GIS 개발 입문
  - 공간 데이터 처리와 시각화의 전체 흐름 이해
  - 기본 공간 분석과 기본 OGC 서비스 소비(client) 경험
- 제외 범위:
  - GeoServer 서버 구축 및 운영 자동화
  - PostGIS 심화 튜닝 및 운영
  - 현대 웹 지도 프론트엔드 통합
  - LMS 구축, 영상 제작, 플랫폼 배포

## 10. 설계 가정

- 본 과정은 6~8시간 제약 내에서 폭넓은 입문을 제공하는 과정이다.
- 모든 모듈은 생산환경 수준의 깊이보다 실습 흐름 이해와 API 사용 경험 확보를 우선한다.
- 외부 서비스 연동 실습은 사전 준비된 로컬 또는 원격 엔드포인트가 있다고 가정한다.
- 한국 좌표계는 대표 예시 중심으로 다루며, 현업의 세부 투영 이슈는 심화 학습으로 분리한다.

## 11. 후속 산출물 작성 기준

- `02_lesson_plans.md`는 `01_curriculum.md`의 모듈/레슨 순서와 목표 ID를 그대로 따른다.
- `03_quizzes.md`의 모든 문항은 `C-LO` 또는 `M-LO`에 매핑한다.
- `04_labs.md`는 커리큘럼의 권장 실습 흐름을 기준으로 따라하기 -> 가이드 실습 -> 챌린지 순으로 설계한다.
- 커리큘럼과 충돌하는 신규 요구가 생기면 먼저 `01_curriculum.md`를 기준 문서로 갱신한 뒤 후속 문서를 수정한다.
