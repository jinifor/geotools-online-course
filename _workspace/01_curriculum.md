# GeoTools 커리큘럼

## 1. 과정 개요

- 과정명: GeoTools로 시작하는 Java GIS 개발 입문
- 모드: 커리큘럼 모드
- 대상: Java 개발 경험이 있는 초급~중급 개발자
- 총 학습 시간: 7시간 40분 (460분)
- 이론:실습 비율: 172분 : 288분 = 약 37 : 63
- 권장 선수지식:
  - Java 기본 문법 및 컬렉션
  - Gradle 기본 사용
  - 좌표와 파일 입출력에 대한 기초 이해
- 실습 환경:
  - JDK 17
  - Gradle
  - GeoTools
  - 예제용 공간 데이터 (Shapefile, GeoJSON)
  - 선택 사항: 사전 구성된 PostGIS/GeoServer 엔드포인트

## 2. 과정 수준 학습목표

| ID | 학습목표 | 블룸 수준 | 성취 증거 |
| --- | --- | --- | --- |
| C-LO-01 | GeoTools 프로젝트를 설정하고 OGC 핵심 개념을 설명할 수 있다. | 이해 | 의존성을 추가하고 Feature, Geometry, CRS 역할을 구분한다. |
| C-LO-02 | JTS와 GeoTools 구조를 사용해 Geometry와 Feature를 모델링할 수 있다. | 적용 | Point, Polygon, SimpleFeatureType, SimpleFeature를 생성한다. |
| C-LO-03 | Shapefile, GeoJSON, WKT 중심의 데이터 읽기/쓰기 흐름을 구현하고 DataStore API 패턴을 설명할 수 있다. | 적용 | 파일 로드, 변환, 저장 파이프라인을 구현한다. |
| C-LO-04 | EPSG 코드와 CRS를 해석하고 GeoTools로 좌표 변환을 수행할 수 있다. | 적용 | `CRS.decode`, `MathTransform` 기반 재투영을 실행한다. |
| C-LO-05 | 기본 공간 관계와 공간 연산을 사용해 간단한 분석 로직을 구현할 수 있다. | 분석 | `intersects`, `buffer`, `intersection`, 거리/면적 계산을 조합한다. |
| C-LO-06 | GeoTools로 레이어를 렌더링하고 스타일을 적용해 지도를 이미지 또는 간단한 UI로 출력할 수 있다. | 적용 | `MapContent`, SLD, PNG/SVG export, Swing `MapPane` 예제를 완성한다. |
| C-LO-07 | WMS/WFS와 Filter Encoding을 활용해 외부 공간 서비스를 조회하고 GeoServer와 연동할 수 있다. | 적용 | 서비스에 연결하고 피처를 조회하거나 필터 기반 요청을 수행한다. |

## 3. 설계 원칙

- 실습 우선: 각 모듈은 개념 설명 직후 바로 코드 실습으로 연결한다.
- 점진적 난이도: 환경 설정 -> 데이터 구조 -> 파일 I/O -> CRS -> 공간 분석 -> 렌더링 -> 서비스 연동 순으로 확장한다.
- breadth-first 범위 통제: PostGIS, GeoServer, Swing `MapPane`, KML은 입문 수준의 연결 실습으로 제한한다.
- 용어 명시: 첫 등장에서 핵심 용어를 정의하고 문서 하단 용어집에 정리한다.

## 4. 모듈 개요

| 모듈 | 시간 | 레슨 수 | 이론/실습 | 연결 학습목표 | 핵심 산출물 |
| --- | --- | --- | --- | --- | --- |
| Module 1. 과정 소개 및 환경 설정 | 45분 | 3 | 20 / 25 | C-LO-01 | 실행 가능한 `hello-geotools` 프로젝트 |
| Module 2. 공간 데이터 기초 | 55분 | 3 | 25 / 30 | C-LO-02 | Geometry/Feature 모델링 예제 |
| Module 3. 데이터 읽기 / 쓰기 | 70분 | 3 | 25 / 45 | C-LO-03 | Shapefile-GeoJSON 변환기 |
| Module 4. 좌표계(CRS)와 좌표 변환 | 60분 | 3 | 20 / 40 | C-LO-04 | 재투영 유틸리티 예제 |
| Module 5. 공간 연산 | 75분 | 3 | 25 / 50 | C-LO-05 | 버퍼/교차 기반 분석 예제 |
| Module 6. 지도 렌더링과 스타일링 | 85분 | 3 | 32 / 53 | C-LO-06 | 스타일 적용 지도 렌더러 |
| Module 7. OGC 웹 서비스 연동 | 70분 | 3 | 25 / 45 | C-LO-07 | WMS/WFS 클라이언트 데모 |

## 5. 모듈 상세

### Module 1. 과정 소개 및 환경 설정

- 모듈 목표:
  - M1-LO-01: GeoTools의 역할과 GIS 개발 생태계에서의 위치를 설명할 수 있다.
  - M1-LO-02: JDK 17, Gradle, VS Code 기반의 실습 환경을 구성할 수 있다.
  - M1-LO-03: GeoTools 의존성을 추가하고 첫 예제를 실행할 수 있다.
- 레슨 구성:
  - Lesson 1. GeoTools와 OGC 개요 (15분)
    - 핵심 내용: GeoTools, OGC, Feature, Geometry, CRS 정의와 활용 사례
    - 활동: 샘플 아키텍처 다이어그램 해설
  - Lesson 2. 개발 환경 구성 (15분)
    - 핵심 내용: 프로젝트 구조, 빌드 파일, 의존성 저장소 설정
    - 활동: Gradle 프로젝트 생성
  - Lesson 3. 첫 실행 실습 (15분)
    - 핵심 내용: GeoTools 의존성 추가, 기본 클래스 실행
    - 활동: 콘솔에 Geometry/Feature 정보 출력
- 핵심 실습: `hello-geotools` 프로젝트 생성 후 샘플 Feature를 읽어 메타데이터를 출력한다.

### Module 2. 공간 데이터 기초

- 모듈 목표:
  - M2-LO-01: `Point`, `LineString`, `Polygon`, `Multi*` 구조를 구분할 수 있다.
  - M2-LO-02: JTS를 사용해 기본 Geometry를 생성하고 검토할 수 있다.
  - M2-LO-03: `Feature`, `FeatureType`, 속성 데이터의 관계를 설명하고 예제를 구성할 수 있다.
- 레슨 구성:
  - Lesson 1. Geometry 모델 이해 (20분)
    - 핵심 내용: Geometry 계층 구조, 좌표 배열, 토폴로지 기초
    - 활동: 도형 타입별 사용 시나리오 비교
  - Lesson 2. JTS 기초 실습 (20분)
    - 핵심 내용: `GeometryFactory`, WKT 기반 생성, 검증
    - 활동: Point, LineString, Polygon 생성 코드 작성
  - Lesson 3. Feature 구조 이해 (15분)
    - 핵심 내용: `SimpleFeatureType`, `SimpleFeature`, 속성 결합
    - 활동: 속성 + Geometry를 가진 피처 생성
- 핵심 실습: 하나의 `SimpleFeatureType`에 속성과 Geometry를 결합해 샘플 도시 데이터셋을 구성한다.

### Module 3. 데이터 읽기 / 쓰기

- 모듈 목표:
  - M3-LO-01: Shapefile을 읽고 주요 속성과 Geometry를 조회할 수 있다.
  - M3-LO-02: GeoJSON, KML, WKT 입출력 흐름을 설명하고 기본 변환을 수행할 수 있다.
  - M3-LO-03: `DataStore` API 패턴과 PostGIS 연동 흐름을 설명할 수 있다.
- 레슨 구성:
  - Lesson 1. Shapefile 읽기/쓰기 (20분)
    - 핵심 내용: `ShapefileDataStore`, 스키마, 피처 반복
    - 활동: Shapefile 로드 후 속성 요약 출력
  - Lesson 2. GeoJSON, KML, WKT 변환 (25분)
    - 핵심 내용: 포맷별 특성, 직렬화/역직렬화
    - 활동: WKT -> Geometry -> GeoJSON 변환
  - Lesson 3. DataStore API와 공간 DB 연결 (25분)
    - 핵심 내용: `DataStoreFinder`, 연결 파라미터, PostGIS 접근 개념
    - 활동: 사전 준비된 PostGIS 또는 예시 설정으로 테이블 목록 조회
- 핵심 실습: Shapefile을 읽어 GeoJSON으로 내보내고, 동일 데이터를 WKT로 확인하는 변환기를 구현한다.
- 범위 메모: KML과 PostGIS는 심화 설정 대신 연결 패턴과 기본 조회에 집중한다.

### Module 4. 좌표계(CRS)와 좌표 변환

- 모듈 목표:
  - M4-LO-01: EPSG 코드와 CRS 개념을 설명할 수 있다.
  - M4-LO-02: GeoTools에서 CRS를 정의, 파싱, 디코드할 수 있다.
  - M4-LO-03: 한국 좌표계를 포함한 재투영 실습을 수행할 수 있다.
- 레슨 구성:
  - Lesson 1. CRS와 EPSG 코드 이해 (20분)
    - 핵심 내용: 지리좌표계, 투영좌표계, EPSG 레지스트리
    - 활동: 자주 쓰는 EPSG 코드 비교
  - Lesson 2. CRS 정의와 파싱 (20분)
    - 핵심 내용: `CRS.decode`, WKT 기반 CRS, `MathTransform`
    - 활동: 소스 CRS와 대상 CRS 선언
  - Lesson 3. 재투영 실습과 한국 좌표계 (20분)
    - 핵심 내용: EPSG:4326 -> EPSG:5186, UTM-K 활용
    - 활동: 좌표 또는 피처 컬렉션 재투영
- 핵심 실습: GeoJSON 또는 Shapefile 데이터를 WGS84에서 한국 좌표계로 재투영하고 결과 좌표를 비교한다.

### Module 5. 공간 연산 (Spatial Operations)

- 모듈 목표:
  - M5-LO-01: 주요 공간 관계(`intersects`, `contains`, `within`)를 판별할 수 있다.
  - M5-LO-02: `buffer`, `union`, `intersection`, `difference` 연산을 코드로 구현할 수 있다.
  - M5-LO-03: 거리/면적 계산과 `STRtree` 기반 인덱싱을 적용할 수 있다.
- 레슨 구성:
  - Lesson 1. 공간 관계 판별 (20분)
    - 핵심 내용: 위상 관계, 술어 기반 판단
    - 활동: 폴리곤-포인트 포함 관계 코드 작성
  - Lesson 2. 공간 연산 실습 (25분)
    - 핵심 내용: 버퍼, 교차, 합집합, 차집합
    - 활동: 도로 주변 버퍼와 관심 영역 교차 계산
  - Lesson 3. 측정과 공간 인덱스 (30분)
    - 핵심 내용: 거리, 면적, 탐색 최적화, `STRtree`
    - 활동: 후보 Geometry를 인덱스로 좁혀 분석 속도 비교
- 핵심 실습: 버퍼 생성 후 교차 영역을 계산하고, 거리/면적 결과를 리포트하는 간단한 분석 도구를 만든다.

### Module 6. 지도 렌더링과 스타일링

- 모듈 목표:
  - M6-LO-01: `MapContent`와 레이어 구조를 설명할 수 있다.
  - M6-LO-02: SLD 기반 스타일을 작성하고 레이어에 적용할 수 있다.
  - M6-LO-03: 지도를 이미지 또는 Swing `MapPane`으로 출력할 수 있다.
- 레슨 구성:
  - Lesson 1. MapContent 구조 이해 (25분)
    - 핵심 내용: Layer, FeatureSource, 렌더링 파이프라인
    - 활동: 단일 레이어 맵 구성
  - Lesson 2. SLD와 스타일 적용 (30분)
    - 핵심 내용: 색상, 선, 심볼, 규칙 기반 스타일
    - 활동: 포인트/라인/폴리곤 스타일 각각 적용
  - Lesson 3. 지도 출력과 인터랙션 (30분)
    - 핵심 내용: PNG/SVG export, Swing `JMapPane` 또는 `MapPane`
    - 활동: 렌더된 지도를 파일로 저장하고 확대/이동 데모 실행
- 핵심 실습: 하나의 Shapefile 또는 GeoJSON 데이터셋을 스타일링해 PNG로 내보내고 간단한 Swing 뷰어로 표시한다.
- 범위 메모: Swing `MapPane`은 개념 확인 수준으로 다루며 현대 UI 프레임워크 통합은 범위에서 제외한다.

### Module 7. OGC 웹 서비스 연동

- 모듈 목표:
  - M7-LO-01: WMS와 WFS의 역할 차이를 설명할 수 있다.
  - M7-LO-02: Filter Encoding으로 기본 공간 쿼리를 작성할 수 있다.
  - M7-LO-03: GeoServer와 연동해 레이어 또는 피처를 조회할 수 있다.
- 레슨 구성:
  - Lesson 1. WMS/WFS 연결 개요 (20분)
    - 핵심 내용: 서비스 엔드포인트, 레이어 조회, 요청/응답 구조
    - 활동: 공개 또는 사전 구성된 서비스 접속
  - Lesson 2. Filter Encoding 실습 (25분)
    - 핵심 내용: 속성 필터, 공간 필터, CQL/OGC 필터 개념
    - 활동: BBOX 또는 속성 기반 피처 조회
  - Lesson 3. GeoServer 연동 가이드 실습 (25분)
    - 핵심 내용: 레이어 호출, 피처 수집, 클라이언트 통합 흐름
    - 활동: GeoServer의 WMS/WFS를 호출해 결과를 화면 또는 로그로 확인
- 핵심 실습: GeoServer에서 노출한 레이어를 WMS/WFS로 호출하고 조건 필터를 적용해 조회 결과를 검증한다.
- 범위 메모: 서버 구축과 운영 자동화는 범위에서 제외하고, 소비자(client) 관점의 연동에 집중한다.

## 6. 권장 실습 흐름

| 실습 ID | 형식 | 연결 목표 | 설명 |
| --- | --- | --- | --- |
| LAB-01 | 따라하기 | C-LO-01 | GeoTools 의존성을 추가하고 첫 실행 로그를 확인한다. |
| LAB-02 | 따라하기 | C-LO-02 | Geometry와 `SimpleFeature`를 생성해 메모리상 데이터셋을 만든다. |
| LAB-03 | 가이드 실습 | C-LO-03 | Shapefile을 읽어 GeoJSON과 WKT로 변환한다. |
| LAB-04 | 가이드 실습 | C-LO-04 | WGS84 데이터를 EPSG:5186으로 재투영한다. |
| LAB-05 | 가이드 실습 | C-LO-05 | 버퍼 및 교차 분석 결과를 계산하고 요약한다. |
| LAB-06 | 가이드 실습 | C-LO-06 | SLD를 적용해 PNG 지도를 내보내고 Swing 뷰어에 표시한다. |
| LAB-07 | 챌린지 | C-LO-07 | GeoServer WFS를 조회하고 필터 조건으로 결과를 제한한다. |

## 7. 평가 방향

- 형성평가:
  - 각 모듈 종료 시 2~3문항 개념 확인
  - 실습 결과 체크리스트 기반 코드 확인
- 총괄평가:
  - 미니 프로젝트: 지정 데이터셋을 읽고, 재투영 후, 버퍼/교차 분석을 수행하고, 결과를 시각화 또는 서비스 조회 결과와 함께 제출
- 평가 증거:
  - 실행 가능한 코드
  - 결과 이미지 또는 로그
  - 좌표계 및 연산 선택 이유에 대한 짧은 설명

## 8. 용어집

- GeoTools: Java 기반의 오픈소스 GIS 개발 툴킷
- OGC: 공간정보 상호운용 표준을 정의하는 국제 표준화 기구
- Feature: Geometry와 속성(attribute)을 함께 가지는 공간 객체
- Geometry: 공간 형상을 표현하는 객체 (`Point`, `LineString`, `Polygon` 등)
- CRS: 좌표 참조 시스템. 좌표가 어떤 기준으로 해석되는지 정의한다.
- EPSG 코드: 널리 사용되는 CRS 식별 코드
- DataStore: 공간 데이터 소스에 접근하기 위한 GeoTools 추상화 계층
- SLD: 지도 레이어 스타일을 기술하는 OGC 스타일 표현 규격
- WMS: 지도 이미지를 제공하는 OGC 웹 서비스
- WFS: 피처 데이터를 제공하는 OGC 웹 서비스

## 9. 가정 및 리스크

- 본 과정은 6~8시간 제약 때문에 폭넓은 입문을 목표로 하며, 모든 주제를 생산환경 수준으로 깊게 다루지 않는다.
- PostGIS, GeoServer, WMS/WFS 실습은 네트워크 또는 사전 구성된 로컬 서비스가 준비되어 있다고 가정한다.
- 한국 좌표계 예시는 EPSG:5186, UTM-K를 중심으로 다루되, 실제 현업 데이터의 세부 투영 이슈는 후속 심화 과정으로 넘긴다.
- Swing `MapPane`은 GeoTools 렌더링 구조 이해를 위한 보조 수단이며, 최신 데스크톱/웹 UI 통합은 범위에서 제외한다.
