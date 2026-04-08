# GeoTools 온라인 강의


## 🧩 1단계 — 커리큘럼 생성 (가장 중요)
👉 여기서 전체 방향이 결정됨
```
GeoTools(Java GIS Toolkit) 온라인 교육 과정의 커리큘럼을 설계해줘.

조건:

* 커리큘럼 모드로 진행
* _workspace/01_curriculum.md 생성
* 학습 목표(ID 포함) 반드시 정의
* 모듈/레슨 구조로 설계 (레슨 15~30분 기준)
* 실습 흐름까지 고려

강조:

* Java 개발자 기준으로 GIS 개념 최소한만 설명
* 실무 활용 중심 구성
* 점진적 난이도 상승

마지막에 간단한 리뷰도 포함해줘.
```


## 📚 2단계 — 교안 생성
👉 커리큘럼 기반으로만 생성 (핵심 규칙)

```
GeoTools(Java GIS Toolkit) 온라인 교육 과정의 커리큘럼을 설계해줘.

조건:

* 커리큘럼 모드로 진행
* _workspace/01_curriculum.md 생성
* 학습 목표(ID 포함) 반드시 정의
* 모듈/레슨 구조로 설계 (레슨 15~30분 기준)
* 실습 흐름까지 고려

강조:

* Java 개발자 기준으로 GIS 개념 최소한만 설명
* 실무 활용 중심 구성
* 점진적 난이도 상승

마지막에 간단한 리뷰도 포함해줘.
```

```
Module 1. 과정 소개 및 환경 설정
- GeoTools란? — 역할, 생태계, 활용 사례
- Java / Gradle / VS Code 환경 구성
- GeoTools 의존성 추가 및 첫 실행
- OGC 표준 개요 (Feature, Geometry, CRS 개념 소개)

Module 2. 공간 데이터 기초
- Geometry 모델 이해 (Point, LineString, Polygon, Multi*)
- JTS (Java Topology Suite) 기초
- Feature & FeatureType 구조 이해
- 속성 데이터와 공간 데이터의 결합

Module 3. 데이터 읽기 / 쓰기
- Shapefile 읽기·쓰기
- GeoJSON, KML, WKT 입출력
- PostGIS / 공간 DB 연동
- DataStore API 패턴 이해

Module 4. 좌표계(CRS)와 좌표 변환
- EPSG 코드와 좌표 참조 시스템 개념
- CRS 정의 및 파싱
- 좌표 변환 (Reproject) 실습
- 한국 좌표계 (EPSG:5186, UTM-K 등) 활용

Module 5. 공간 연산 (Spatial Operations)
- 공간 관계 판별 (intersects, contains, within 등)
- 버퍼, 유니온, 교차, 차집합 연산
- 거리 계산 및 면적 계산
- 공간 인덱스 활용 (STRtree)

Module 6. 지도 렌더링과 스타일링
- MapContent 구조 이해
- SLD (Styled Layer Descriptor) 기초
- 레이어 스타일 적용 (색상, 선, 심볼)
- 이미지로 지도 출력 (PNG/SVG export)
- Swing MapPane을 이용한 인터랙티브 지도

Module 7. OGC 웹 서비스 연동
- WMS (Web Map Service) 연결 및 레이어 호출
- WFS (Web Feature Service) 피처 요청
- Filter Encoding으로 공간 쿼리 작성
- GeoServer와의 연동 실습
```


## 🧪 3단계 — 퀴즈 생성
👉 학습 목표와 반드시 매핑

```
기존 커리큘럼과 교안을 기반으로 퀴즈를 생성해줘.

조건:

* 퀴즈 모드
* _workspace/03_quizzes.md 생성
* 모든 문제는 학습목표 ID와 매핑
* Bloom's taxonomy 수준 명시

포함:

* 개념 이해 문제
* 코드 이해 문제
* 실무 적용 문제

난이도는 쉬움 → 보통 → 어려움 순으로 구성

작성 후 커버리지 검토 포함해줘.
```

## 🛠 4단계 — 실습 과제 생성 (핵심🔥)
👉 이 단계가 강의 퀄리티를 결정함
```
기존 커리큘럼과 교안을 기반으로 실습 과제를 설계해줘.

조건:

* 실습 모드
* _workspace/04_labs.md 생성

구성:

1. 따라하기 실습
2. 응용 실습
3. 미니 프로젝트

GeoTools 기반으로:

* Shapefile 읽기
* GeoJSON 처리
* CRS 변환
* 지도 렌더링
* 간단한 공간 분석

각 실습에:

* 목표
* 단계별 가이드
* 기대 결과
* 체크포인트 포함

난이도는 점진적으로 상승하도록 설계
```

## 🔍 5단계 — 전체 리뷰 (마무리)
👉 반드시 해야 하는 단계
```
전체 산출물을 리뷰해줘.

대상:

* _workspace/01_curriculum.md
* _workspace/02_lesson_plans.md
* _workspace/03_quizzes.md
* _workspace/04_labs.md

조건:

* _workspace/05_review_report.md 생성

검토 항목:

* 학습목표 커버리지 (교안/퀴즈/실습 모두)
* 난이도 흐름
* 누락된 개념
* 실습 적절성
* 실무 활용 가능성

문제 있으면 "수정 필요"로 명시하고 개선안 제시
```

## 🧠 전체 흐름 요약
```
00_input → 01_curriculum → 02_lesson → 03_quiz → 04_lab → 05_review
```
