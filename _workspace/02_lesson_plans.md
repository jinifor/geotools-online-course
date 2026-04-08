# GeoTools 교안

## 1. 문서 개요

- 문서 목적: [_workspace/01_curriculum.md](C:\Users\jinif\Documents\workspace\harness-100\codex\geotools-online-course\_workspace\01_curriculum.md)를 기준으로 초급 학습자용 레슨 교안을 상세화한다.
- 진행 모드: 교안 모드
- 기준 문서:
  - [_workspace/00_input.md](C:\Users\jinif\Documents\workspace\harness-100\codex\geotools-online-course\_workspace\00_input.md)
  - [_workspace/01_curriculum.md](C:\Users\jinif\Documents\workspace\harness-100\codex\geotools-online-course\_workspace\01_curriculum.md)
- 작성 원칙:
  - 설명보다 "왜 이 API를 지금 써야 하는가"를 먼저 말한다.
  - 각 레슨은 GeoTools 실제 코드 예제를 반드시 포함한다.
  - 초급자가 막히지 않도록 코드 경로를 작게 나누고, 매 단계의 확인 포인트를 제공한다.
  - 같은 데이터가 다음 모듈에도 이어지도록 실습 흐름을 연결한다.

## 2. 가정 및 검증 기준

- 학습자는 Java 문법, 컬렉션, Gradle 기본 사용은 가능하지만 GIS와 GeoTools는 처음이라고 가정한다.
- 실습 기본 빌드 도구는 Gradle로 둔다. 모든 실행 예시는 Gradle Wrapper(`gradlew`, `gradlew.bat`) 기준으로 안내한다.
- 예제 데이터는 아래와 같이 준비되어 있다고 가정한다.
  - `data/cities.geojson`
  - `data/districts.shp`
  - `data/roads.shp`
  - `data/stations.geojson`
- 외부 연동 실습은 사전 준비된 엔드포인트가 있다고 가정한다.
  - PostGIS: `localhost:5432/gis`
  - GeoServer WMS/WFS: `http://localhost:8080/geoserver`
- API 패턴 검증 기준 시점: 2026-04-09
- 검증 출처: GeoTools 공식 quickstart, user guide, javadocs의 stable 34.x / latest 35.x 문서에서 공통으로 확인되는 API만 사용한다.

## 3. 공통 실습 환경

### 3.1 실습 프로젝트 구조

```text
hello-geotools/
  build.gradle
  settings.gradle
  gradlew
  gradlew.bat
  gradle/
    wrapper/
  data/
    cities.geojson
    districts.shp
    roads.shp
    stations.geojson
  output/
  src/main/java/org/example/geotoolscourse/
```

### 3.2 공통 Gradle 설정 예시

아래 설정은 과정 전체에서 반복해서 쓴다. 초급자에게는 "GeoTools는 모듈을 필요한 만큼만 추가한다"는 감각을 먼저 주는 것이 중요하다. 예시는 `build.gradle` Groovy DSL 기준이다.

먼저 `settings.gradle`에서 프로젝트 이름을 고정한다.

```groovy
rootProject.name = 'hello-geotools'
```

그 다음 `build.gradle`에서 공통 플러그인, 저장소, 의존성, 실행 구성을 정의한다.

```groovy
plugins {
    id 'application'
}

group = 'org.example'
version = '1.0.0'

ext {
    gtVersion = '34.0'
}

java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(17)
    }
}

repositories {
    mavenCentral()
    maven {
        url = uri('https://repo.osgeo.org/repository/release/')
    }
}

dependencies {
    implementation platform("org.geotools:gt-bom:${gtVersion}")
    implementation 'org.geotools:gt-shapefile'
    implementation 'org.geotools:gt-geojson'
    implementation 'org.geotools:gt-epsg-hsql'
    implementation 'org.geotools:gt-swing'
    implementation 'org.geotools:gt-wms'
    implementation 'org.geotools:gt-wfs-ng'
    implementation 'org.geotools:gt-cql'
}

application {
    mainClass = 'org.example.geotoolscourse.EnvironmentCheck'
}

tasks.withType(JavaCompile).configureEach {
    options.encoding = 'UTF-8'
}
```

### 3.3 공통 운영 메모

- 코드 블록은 강의용 발췌본이다. 패키지 선언과 import는 실습 단계에서 같이 완성한다.
- 파일을 읽는 코드에서는 `try-with-resources`와 `dispose()` 호출을 함께 강조한다.
- 좌표 단위가 도(degree)인지 미터(meter)인지 항상 먼저 확인하게 한다.
- 초급자에게는 "지금 다 외우기"보다 "어느 모듈이 어느 역할인지 기억하기"를 목표로 안내한다.

## 4. 레슨 플랜

## Module 1. 과정 소개 및 환경 설정

- 모듈 시간: 45분
- 연결 목표:
  - C-LO-01
  - M1-LO-01
  - M1-LO-02
  - M1-LO-03
- 모듈 산출물: 실행 가능한 `hello-geotools` 프로젝트
- 모듈 메시지: "GeoTools는 GIS 이론을 길게 공부한 다음 쓰는 도구가 아니라, 표준 구조를 가진 공간 데이터를 코드로 다루기 위한 Java 개발 도구다."

### Lesson 1. GeoTools와 OGC 개요

- 시간: 15분
- 연결 목표:
  - C-LO-01
  - M1-LO-01
- 왜 이걸 쓰는가:
  - 초급자가 가장 먼저 막히는 지점은 `Geometry`, `Feature`, `CRS`를 섞어 생각하는 것이다.
  - 이 레슨의 목적은 GIS 이론을 넓게 설명하는 것이 아니라, 이후 모든 GeoTools 코드가 이 세 개념 위에 올라간다는 점을 체감하게 만드는 것이다.
- 핵심 개념:
  - `Geometry`: 점, 선, 면처럼 공간 형상을 표현한다.
  - `Feature`: Geometry와 속성 데이터를 함께 묶는 단위다.
  - `CRS`: 좌표 숫자를 어떤 기준으로 해석할지 정의한다.
- 코드 예제:

```java
SimpleFeatureType cityType = DataUtilities.createType(
    "city",
    "geom:Point:srid=4326,name:String,population:Integer"
);

GeometryFactory geometryFactory = JTSFactoryFinder.getGeometryFactory();
Point seoulPoint = geometryFactory.createPoint(new Coordinate(126.9784, 37.5667));

SimpleFeature city = SimpleFeatureBuilder.build(
    cityType,
    new Object[] {seoulPoint, "Seoul", 9500000},
    "city.1"
);

System.out.println("type = " + city.getFeatureType().getTypeName());
System.out.println("geometry = " + city.getDefaultGeometry().getClass().getSimpleName());
System.out.println("name = " + city.getAttribute("name"));
System.out.println("crs = " + city.getFeatureType().getCoordinateReferenceSystem().getName());
```

- 코드에서 강조할 포인트:
  - `DataUtilities.createType`은 "어떤 구조의 데이터를 다룰지"를 먼저 고정한다.
  - `SimpleFeatureBuilder`는 실제 한 건의 데이터를 타입에 맞춰 채운다.
  - `srid=4326`을 빼먹으면 나중에 좌표 변환과 렌더링에서 혼란이 커진다.
- 실습 안내:
  1. `cityType`의 `name`, `population` 타입을 일부러 바꿔본다.
  2. `Point`를 다른 좌표로 바꾸고 출력 값을 확인한다.
  3. `geom` 필드 이름을 `location`으로 바꿨을 때 어떤 코드가 영향을 받는지 본다.
- 초급자 체크포인트:
  - Geometry는 위치/형상이고, Feature는 업무 데이터 묶음이라는 구분을 말로 설명할 수 있어야 한다.
  - CRS가 좌표값 그 자체가 아니라 좌표 해석 규칙이라는 점을 확인한다.

### Lesson 2. 개발 환경 구성

- 시간: 15분
- 연결 목표:
  - C-LO-01
  - M1-LO-02
- 왜 이걸 쓰는가:
  - GeoTools는 모듈 단위 라이브러리라서 "필요한 모듈만 정확히 추가"하는 감각이 중요하다.
  - 환경 구성이 흔들리면 이후 레슨에서 API 문제가 아니라 빌드 문제에 시간을 다 쓰게 된다.
- 핵심 개념:
  - BOM은 Gradle의 `platform(...)` 선언으로 GeoTools 모듈 버전 호환을 맞춘다.
  - `gt-epsg-hsql`은 EPSG 코드 기반 CRS 해석에 필요하다.
  - `gt-shapefile`, `gt-geojson`, `gt-swing`, `gt-wms`, `gt-wfs-ng`는 레슨별 기능 모듈이다.
- 코드 예제:

```groovy
plugins {
    id 'application'
}

ext {
    gtVersion = '34.0'
}

repositories {
    mavenCentral()
    maven {
        url = uri('https://repo.osgeo.org/repository/release/')
    }
}

dependencies {
    implementation platform("org.geotools:gt-bom:${gtVersion}")
    implementation 'org.geotools:gt-shapefile'
    implementation 'org.geotools:gt-geojson'
    implementation 'org.geotools:gt-epsg-hsql'
}

application {
    mainClass = 'org.example.geotoolscourse.EnvironmentCheck'
}
```

```java
public class EnvironmentCheck {
    public static void main(String[] args) throws Exception {
        CoordinateReferenceSystem crs = CRS.decode("EPSG:4326");
        System.out.println("GeoTools environment ready: " + crs.getName());
    }
}
```

- 코드에서 강조할 포인트:
  - 초급자는 의존성을 한 번에 많이 넣기보다, 레슨에서 실제로 쓰는 모듈을 보고 추가하는 방식이 이해에 좋다.
  - `CRS.decode("EPSG:4326")`가 실행되면 빌드와 EPSG 플러그인 연결이 모두 살아 있다는 뜻이다.
- 실습 안내:
  1. Gradle 프로젝트를 생성한다.
  2. 위 의존성을 추가한다.
  3. `gradlew.bat run` 또는 IDE 실행으로 `EnvironmentCheck`를 실행한다.
  4. `EPSG:4326`이 출력되면 성공으로 본다.
- 초급자 체크포인트:
  - GeoTools 버전은 개별 dependency마다 쓰지 않고 `platform()` 기반 BOM에서 관리한다.
  - `gt-epsg-hsql`이 없으면 CRS 관련 실습에서 오류가 날 수 있다는 점을 말로 설명하게 한다.

### Lesson 3. 첫 실행 실습

- 시간: 15분
- 연결 목표:
  - C-LO-01
  - M1-LO-03
- 왜 이걸 쓰는가:
  - "첫 번째 성공 경험"이 있어야 이후 모듈의 파일 I/O, 좌표 변환, 렌더링을 모두 같은 흐름으로 받아들일 수 있다.
  - 이 레슨에서는 데이터를 메모리로 전부 올리는 것이 아니라, GeoTools가 파일 소스를 핸들처럼 다루는 방식을 보여준다.
- 핵심 개념:
  - `FileDataStoreFinder`는 파일 타입에 맞는 데이터스토어 구현을 찾아준다.
  - `SimpleFeatureSource`는 피처 읽기 진입점이다.
  - `ReferencedEnvelope`는 데이터 전체 범위를 빠르게 확인할 때 쓴다.
- 코드 예제:

```java
File file = new File("data/districts.shp");
FileDataStore store = FileDataStoreFinder.getDataStore(file);

try {
    SimpleFeatureSource featureSource = store.getFeatureSource();
    ReferencedEnvelope bounds = featureSource.getBounds();

    System.out.println("typeName = " + featureSource.getSchema().getTypeName());
    System.out.println("geometryField = " +
        featureSource.getSchema().getGeometryDescriptor().getLocalName());
    System.out.println("bounds = " + bounds);

    try (SimpleFeatureIterator iterator = featureSource.getFeatures().features()) {
        if (iterator.hasNext()) {
            SimpleFeature first = iterator.next();
            System.out.println("firstFeatureId = " + first.getID());
            System.out.println("firstGeometry = " + first.getDefaultGeometry());
        }
    }
} finally {
    store.dispose();
}
```

- 코드에서 강조할 포인트:
  - GeoTools는 파일을 바로 전부 읽지 않고 `FeatureSource`를 통해 필요할 때 접근한다.
  - 첫 레슨부터 `iterator.close()`가 자동 보장되는 패턴을 보여줘야 나중에 대용량 데이터에서도 습관이 남는다.
- 실습 안내:
  1. `data/districts.shp`를 프로젝트에 둔다.
  2. 스키마 이름, Geometry 필드 이름, bounds를 출력한다.
  3. 첫 피처 1건만 꺼내서 ID와 Geometry를 확인한다.
  4. shapefile 경로를 틀리게 적었을 때 어떤 예외가 나는지 본다.
- 초급자 체크포인트:
  - `FeatureSource`는 "데이터에 대한 접근 창구"라는 표현으로 설명할 수 있어야 한다.
  - 첫 실행 목표는 지도 그리기가 아니라 "공간 데이터를 코드로 읽는 데 성공했다"는 감각을 주는 것이다.

## Module 2. 공간 데이터 기초

- 모듈 시간: 55분
- 연결 목표:
  - C-LO-02
  - M2-LO-01
  - M2-LO-02
  - M2-LO-03
- 모듈 산출물: Geometry/Feature 모델링 예제
- 모듈 메시지: "GeoTools를 잘 쓰려면 데이터를 먼저 파일로 보지 말고 Geometry와 Feature의 구조로 봐야 한다."

### Lesson 1. Geometry 모델 이해

- 시간: 20분
- 연결 목표:
  - C-LO-02
  - M2-LO-01
- 왜 이걸 쓰는가:
  - 공간 연산이 틀리는 가장 흔한 이유는 데이터 구조를 잘못 선택했기 때문이다.
  - 점으로 표현할지, 선으로 표현할지, 면으로 표현할지를 먼저 결정할 수 있어야 이후 연산이 자연스럽다.
- 핵심 개념:
  - `Point`: 단일 위치
  - `LineString`: 경로 또는 선형 객체
  - `Polygon`: 면적을 가진 영역
  - `Multi*`: 같은 타입 Geometry의 묶음
- 코드 예제:

```java
GeometryFactory geometryFactory = JTSFactoryFinder.getGeometryFactory();

Point station = geometryFactory.createPoint(new Coordinate(127.0276, 37.4979));
LineString road = geometryFactory.createLineString(new Coordinate[] {
    new Coordinate(127.02, 37.49),
    new Coordinate(127.03, 37.50),
    new Coordinate(127.04, 37.51)
});
Polygon district = geometryFactory.createPolygon(new Coordinate[] {
    new Coordinate(127.00, 37.48),
    new Coordinate(127.05, 37.48),
    new Coordinate(127.05, 37.52),
    new Coordinate(127.00, 37.52),
    new Coordinate(127.00, 37.48)
});

for (Geometry geometry : List.of(station, road, district)) {
    System.out.println(geometry.getGeometryType()
        + ", dimension=" + geometry.getDimension()
        + ", numPoints=" + geometry.getNumPoints());
}
```

- 코드에서 강조할 포인트:
  - Geometry 타입을 잘못 잡으면 이후 `buffer`, `intersection`, 스타일링 결과가 기대와 달라진다.
  - `Polygon`은 시작 좌표와 끝 좌표가 닫혀 있어야 한다는 점을 바로 보여준다.
- 실습 안내:
  1. 점, 선, 면을 각각 하나씩 만든다.
  2. `getGeometryType()`, `getDimension()`, `getNumPoints()`를 출력한다.
  3. `Polygon` 닫는 좌표를 제거해 보고 어떤 문제가 생기는지 확인한다.
- 초급자 체크포인트:
  - "지하철역은 Point, 도로는 LineString, 행정구역은 Polygon"처럼 현실 예시와 매핑할 수 있어야 한다.

### Lesson 2. JTS 기초 실습

- 시간: 20분
- 연결 목표:
  - C-LO-02
  - M2-LO-02
- 왜 이걸 쓰는가:
  - GeoTools의 공간 연산은 대부분 JTS Geometry 위에서 이루어진다.
  - 즉 GeoTools를 쓴다는 것은 JTS Geometry를 잘 다룰 수 있다는 뜻과 거의 같다.
- 핵심 개념:
  - `GeometryFactory`: Geometry를 코드로 만드는 기본 도구
  - `WKTReader`: 텍스트 기반 Geometry를 빠르게 읽는 도구
  - `isValid()`: 연산 전에 Geometry가 정상인지 확인하는 최소 습관
- 코드 예제:

```java
GeometryFactory geometryFactory = JTSFactoryFinder.getGeometryFactory();
WKTReader reader = new WKTReader(geometryFactory);

Point cityHall = geometryFactory.createPoint(new Coordinate(126.9784, 37.5663));
LineString route = geometryFactory.createLineString(new Coordinate[] {
    new Coordinate(126.97, 37.56),
    new Coordinate(126.98, 37.565),
    new Coordinate(126.99, 37.57)
});
Polygon area = (Polygon) reader.read(
    "POLYGON ((126.97 37.56, 126.99 37.56, 126.99 37.58, 126.97 37.58, 126.97 37.56))"
);

System.out.println("point valid = " + cityHall.isValid());
System.out.println("route length = " + route.getLength());
System.out.println("area valid = " + area.isValid());
System.out.println("area size = " + area.getArea());
```

- 코드에서 강조할 포인트:
  - WKT는 샘플 Geometry를 설명하거나 테스트할 때 가장 빠른 형식이다.
  - 길이와 면적은 좌표 단위에 따라 의미가 달라진다. 지금 값은 "도" 단위에 가까워 실제 거리/면적과 다르다.
- 실습 안내:
  1. WKT를 복사해 Polygon을 만든다.
  2. `isValid()`를 찍어본다.
  3. 좌표 하나를 이상하게 바꿔 유효성 변화를 본다.
  4. 다음 모듈의 CRS 변환 전에는 면적 계산을 믿으면 안 된다는 점을 기록한다.
- 초급자 체크포인트:
  - "JTS는 GeoTools의 공간 계산 엔진"이라는 표현을 그대로 기억하게 한다.

### Lesson 3. Feature 구조 이해

- 시간: 15분
- 연결 목표:
  - C-LO-02
  - M2-LO-03
- 왜 이걸 쓰는가:
  - 실무 데이터는 Geometry만 있으면 쓸모가 없다. 이름, 코드, 상태, 인구 같은 속성과 묶여야 분석과 출력이 가능하다.
  - 즉 Feature를 다룰 수 있어야 "지도 위 도형"이 아니라 "업무 객체"를 다루는 단계로 넘어간다.
- 핵심 개념:
  - `SimpleFeatureType`: 스키마 정의
  - `SimpleFeature`: 실제 레코드
  - `DefaultFeatureCollection`: 메모리 상의 피처 묶음
- 코드 예제:

```java
SimpleFeatureTypeBuilder typeBuilder = new SimpleFeatureTypeBuilder();
typeBuilder.setName("station");
typeBuilder.setCRS(CRS.decode("EPSG:4326"));
typeBuilder.add("geom", Point.class);
typeBuilder.add("name", String.class);
typeBuilder.add("line", String.class);
typeBuilder.setDefaultGeometry("geom");

SimpleFeatureType stationType = typeBuilder.buildFeatureType();

SimpleFeatureBuilder featureBuilder = new SimpleFeatureBuilder(stationType);
featureBuilder.add(JTSFactoryFinder.getGeometryFactory()
    .createPoint(new Coordinate(127.0276, 37.4979)));
featureBuilder.add("Gangnam");
featureBuilder.add("Line 2");

SimpleFeature gangnam = featureBuilder.buildFeature("station.1");

DefaultFeatureCollection collection = new DefaultFeatureCollection("stations", stationType);
collection.add(gangnam);

System.out.println(collection.getSchema().getTypeName());
System.out.println(gangnam.getAttribute("name"));
```

- 코드에서 강조할 포인트:
  - `setDefaultGeometry("geom")`를 명시하지 않으면 후속 렌더링/필터에서 헷갈릴 수 있다.
  - `DefaultFeatureCollection`은 소규모 학습용/중간 결과용으로 좋지만 대용량 저장 대체재는 아니다.
- 실습 안내:
  1. 역 이름과 노선 속성을 가진 피처를 3개 만든다.
  2. 컬렉션에 넣고 반복문으로 출력한다.
  3. `line` 속성으로 그룹을 나눌 수 있는지 말로 설명한다.
- 초급자 체크포인트:
  - "Geometry는 위치, Feature는 위치+설명"으로 정확히 구분할 수 있어야 한다.

## Module 3. 데이터 읽기 / 쓰기

- 모듈 시간: 70분
- 연결 목표:
  - C-LO-03
  - M3-LO-01
  - M3-LO-02
  - M3-LO-03
- 모듈 산출물: Shapefile-GeoJSON 변환기
- 모듈 메시지: "GeoTools가 실무에서 바로 쓰이는 이유는 데이터 소스가 달라도 접근 패턴이 비슷하기 때문이다."

### Lesson 1. Shapefile 읽기/쓰기

- 시간: 20분
- 연결 목표:
  - C-LO-03
  - M3-LO-01
- 왜 이걸 쓰는가:
  - Shapefile은 여전히 현업에서 자주 마주치는 입문용 포맷이다.
  - 이 레슨의 목표는 파일 포맷 자체를 외우는 것이 아니라, 읽기와 쓰기가 `DataStore` 패턴으로 연결된다는 감각을 잡는 것이다.
- 핵심 개념:
  - `FileDataStoreFinder`: 기존 파일 열기
  - `ShapefileDataStoreFactory`: 새 shapefile 만들기
  - `SimpleFeatureStore`: 피처 쓰기
- 코드 예제:

```java
File inputFile = new File("data/districts.shp");
File outputFile = new File("output/districts-copy.shp");

FileDataStore inputStore = FileDataStoreFinder.getDataStore(inputFile);

try {
    SimpleFeatureSource source = inputStore.getFeatureSource();

    ShapefileDataStoreFactory factory = new ShapefileDataStoreFactory();
    Map<String, Serializable> params = new HashMap<>();
    params.put("url", outputFile.toURI().toURL());
    params.put("create spatial index", Boolean.TRUE);

    ShapefileDataStore outputStore = (ShapefileDataStore) factory.createNewDataStore(params);
    outputStore.createSchema(source.getSchema());

    Transaction tx = new DefaultTransaction("copy");
    try {
        String typeName = outputStore.getTypeNames()[0];
        SimpleFeatureSource targetSource = outputStore.getFeatureSource(typeName);
        SimpleFeatureStore targetStore = (SimpleFeatureStore) targetSource;
        targetStore.setTransaction(tx);
        targetStore.addFeatures(source.getFeatures());
        tx.commit();
    } catch (Exception e) {
        tx.rollback();
        throw e;
    } finally {
        tx.close();
        outputStore.dispose();
    }
} finally {
    inputStore.dispose();
}
```

- 코드에서 강조할 포인트:
  - 읽기와 쓰기 모두 `FeatureSource`/`FeatureStore` 패턴을 공유한다.
  - 트랜잭션을 명시적으로 쓰는 이유는 쓰기 실패 시 파일이 일부만 생성되는 상황을 막기 위해서다.
- 실습 안내:
  1. `districts.shp`를 읽는다.
  2. 복사 파일을 `output/`에 만든다.
  3. 복사 전후 피처 수와 bounds가 같은지 비교한다.
  4. 출력 경로를 잘못 넣었을 때 롤백 흐름을 확인한다.
- 초급자 체크포인트:
  - shapefile 복사는 파일 복사가 아니라 "스키마 생성 + 피처 쓰기"라는 점을 설명할 수 있어야 한다.

### Lesson 2. GeoJSON, KML, WKT 변환

- 시간: 25분
- 연결 목표:
  - C-LO-03
  - M3-LO-02
- 왜 이걸 쓰는가:
  - GeoJSON은 웹과 API 친화적이고, WKT는 디버깅과 테스트에 빠르다.
  - 즉 "저장용 형식"과 "사람이 눈으로 확인하기 좋은 형식"을 상황에 따라 바꾸는 능력이 중요하다.
- 핵심 개념:
  - `FeatureJSON`: Feature/FeatureCollection을 GeoJSON으로 읽고 쓴다.
  - `GeometryJSON`: Geometry 단위 직렬화에 쓴다.
  - `WKTReader`/`WKTWriter`: 사람이 읽기 쉬운 텍스트 표현에 쓴다.
- 코드 예제:

```java
FileDataStore store = FileDataStoreFinder.getDataStore(new File("data/districts.shp"));

try {
    SimpleFeatureSource source = store.getFeatureSource();
    SimpleFeatureCollection features = source.getFeatures();

    FeatureJSON featureJSON = new FeatureJSON();
    featureJSON.setEncodeFeatureCollectionBounds(true);
    featureJSON.setEncodeFeatureCollectionCRS(true);

    try (Writer writer = new FileWriter("output/districts.json")) {
        featureJSON.writeFeatureCollection(features, writer);
    }

    try (SimpleFeatureIterator iterator = features.features()) {
        if (iterator.hasNext()) {
            SimpleFeature first = iterator.next();
            Geometry geometry = (Geometry) first.getDefaultGeometry();
            String wkt = geometry.toText();
            String geojson = new GeometryJSON(6).toString(geometry);

            System.out.println("WKT = " + wkt);
            System.out.println("GeoJSON = " + geojson);
        }
    }
} finally {
    store.dispose();
}
```

- 코드에서 강조할 포인트:
  - 학습자는 `FeatureJSON`과 `GeometryJSON`의 역할 차이를 먼저 이해해야 한다.
  - 디버깅 시에는 Geometry 하나를 `toText()`로 보는 편이 가장 빠를 때가 많다.
  - KML은 과정 범위상 소개만 하고, 변환 확장은 과제로 넘긴다.
- 실습 안내:
  1. Shapefile을 GeoJSON으로 저장한다.
  2. 첫 피처 Geometry를 WKT로 출력한다.
  3. GeoJSON 파일을 에디터로 열어 `type`, `features`, `geometry` 구조를 확인한다.
  4. 확장 과제로 `stations.geojson`을 읽어 역 이름만 로그에 찍게 한다.
- 초급자 체크포인트:
  - GeoJSON은 서비스 교환용, WKT는 빠른 검사용이라는 사용 맥락을 구분하게 한다.

### Lesson 3. DataStore API와 공간 DB 연결

- 시간: 25분
- 연결 목표:
  - C-LO-03
  - M3-LO-03
- 왜 이걸 쓰는가:
  - 실무에서는 파일만 다루지 않는다. DB나 원격 서비스에도 같은 개념으로 붙어야 한다.
  - 이 레슨의 핵심은 "DataStore는 백엔드가 달라도 사용 감각이 비슷하다"는 점을 확인하는 것이다.
- 핵심 개념:
  - `DataStoreFinder`: 연결 파라미터에 맞는 구현체를 자동 선택한다.
  - `getTypeNames()`: 사용 가능한 레이어/테이블 이름을 본다.
  - `getFeatureSource(typeName)`: 실제 조회 시작점
- 코드 예제:

```java
Map<String, Object> params = new HashMap<>();
params.put("dbtype", "postgis");
params.put("host", "localhost");
params.put("port", 5432);
params.put("database", "gis");
params.put("schema", "public");
params.put("user", "postgres");
params.put("passwd", "postgres");

DataStore dataStore = DataStoreFinder.getDataStore(params);

if (dataStore == null) {
    throw new IllegalStateException("PostGIS 연결에 실패했습니다.");
}

try {
    for (String typeName : dataStore.getTypeNames()) {
        System.out.println("typeName = " + typeName);
    }

    String targetType = dataStore.getTypeNames()[0];
    SimpleFeatureSource source = dataStore.getFeatureSource(targetType);
    System.out.println("schema = " + source.getSchema());
    System.out.println("count = " + source.getCount(Query.ALL));
} finally {
    dataStore.dispose();
}
```

- 코드에서 강조할 포인트:
  - 파일이든 DB든 결국 `DataStore -> FeatureSource -> FeatureCollection` 흐름으로 읽는다.
  - 초급자는 "DB 연결 자체"보다 "데이터 접근 패턴의 일관성"을 먼저 이해해야 한다.
- 실습 안내:
  1. 사전 준비된 PostGIS에 붙는다.
  2. 테이블 목록을 출력한다.
  3. 첫 테이블의 스키마와 건수를 확인한다.
  4. shapefile을 읽을 때와 어떤 코드가 같은지 비교한다.
- 초급자 체크포인트:
  - GeoTools는 파일 처리 도구가 아니라 "공간 데이터 접근 API"라는 말이 이해되어야 한다.

## Module 4. 좌표계(CRS)와 좌표 변환

- 모듈 시간: 60분
- 연결 목표:
  - C-LO-04
  - M4-LO-01
  - M4-LO-02
  - M4-LO-03
- 모듈 산출물: 재투영 유틸리티 예제
- 모듈 메시지: "좌표 변환은 GIS의 부가 기능이 아니라, 거리/면적/정렬이 맞게 나오기 위한 전제 조건이다."

### Lesson 1. CRS와 EPSG 코드 이해

- 시간: 20분
- 연결 목표:
  - C-LO-04
  - M4-LO-01
- 왜 이걸 쓰는가:
  - 좌표 숫자만 보고는 위치를 알 수 없다. CRS가 없으면 같은 숫자도 전혀 다른 의미가 된다.
  - 초급자에게는 "좌표가 틀린 게 아니라, 좌표 해석 규칙이 다른 것"이라는 감각을 먼저 심어야 한다.
- 핵심 개념:
  - `EPSG:4326`: WGS84, 경위도
  - `EPSG:5186`: 한국에서 자주 쓰는 투영 좌표계 예시
  - 지리좌표계와 투영좌표계는 연산 목적이 다르다
- 코드 예제:

```java
CoordinateReferenceSystem wgs84 = CRS.decode("EPSG:4326");
CoordinateReferenceSystem korea2000 = CRS.decode("EPSG:5186");

System.out.println("wgs84 = " + wgs84.getName());
System.out.println("wgs84 axis count = " + wgs84.getCoordinateSystem().getDimension());
System.out.println("korea2000 = " + korea2000.getName());
System.out.println("korea2000 axis count = " + korea2000.getCoordinateSystem().getDimension());
```

- 코드에서 강조할 포인트:
  - `CRS.decode()`는 좌표계 이름을 외우는 게 아니라, 표준 코드로 안전하게 정의를 가져오는 방식이다.
  - 면적/거리 계산에 경위도를 그대로 쓰면 왜곡될 수 있다는 문제를 미리 심어둔다.
- 실습 안내:
  1. `EPSG:4326`, `EPSG:5186`을 각각 decode한다.
  2. 이름과 축 수를 출력한다.
  3. 어떤 경우에 4326을 저장/교환용으로, 5186을 분석용으로 쓰는지 토론한다.
- 초급자 체크포인트:
  - CRS는 좌표계 이름표가 아니라 해석 규칙이라는 설명을 할 수 있어야 한다.

### Lesson 2. CRS 정의와 파싱

- 시간: 20분
- 연결 목표:
  - C-LO-04
  - M4-LO-02
- 왜 이걸 쓰는가:
  - 실무에서는 EPSG 코드만 오지 않고 WKT로 정의된 좌표계가 같이 들어오는 경우도 많다.
  - 따라서 `decode`와 `parseWKT`를 모두 알아야 "정의된 CRS를 이해하고 변환 준비를 한다"는 흐름이 완성된다.
- 핵심 개념:
  - `CRS.decode`: 표준 코드 기반 해석
  - `CRS.parseWKT`: 문자 정의 기반 해석
  - `MathTransform`: 실제 좌표 변환기
- 코드 예제:

```java
String wkt = """
PROJCS["Korea 2000 / Central Belt 2010",
  GEOGCS["Korea 2000",
    DATUM["Geocentric datum of Korea",
      SPHEROID["GRS 1980",6378137,298.257222101]],
    PRIMEM["Greenwich",0],
    UNIT["degree",0.0174532925199433]],
  PROJECTION["Transverse_Mercator"],
  PARAMETER["latitude_of_origin",38],
  PARAMETER["central_meridian",127],
  PARAMETER["scale_factor",1],
  PARAMETER["false_easting",200000],
  PARAMETER["false_northing",600000],
  UNIT["metre",1]]
""";

CoordinateReferenceSystem source = CRS.decode("EPSG:4326");
CoordinateReferenceSystem target = CRS.parseWKT(wkt);
MathTransform transform = CRS.findMathTransform(source, target, true);

System.out.println("target = " + target.getName());
System.out.println("isIdentity = " + transform.isIdentity());
```

- 코드에서 강조할 포인트:
  - `MathTransform`은 CRS 설명 그 자체가 아니라 "두 좌표계 사이를 실제로 바꾸는 계산기"다.
  - `true` lenient 옵션은 초급 단계에서 실습 성공률을 높이기 위한 선택이라는 점을 설명한다.
- 실습 안내:
  1. `EPSG:4326`을 source로 둔다.
  2. WKT를 파싱해 target을 만든다.
  3. `MathTransform` 생성까지 성공하면 좌표 변환 준비가 끝났다고 안내한다.
  4. WKT 일부를 깨뜨려 파싱 실패 사례도 보여준다.
- 초급자 체크포인트:
  - `decode`와 `parseWKT`의 차이를 한 문장으로 설명하게 한다.

### Lesson 3. 재투영 실습과 한국 좌표계

- 시간: 20분
- 연결 목표:
  - C-LO-04
  - M4-LO-03
- 왜 이걸 쓰는가:
  - 재투영을 해야만 버퍼 거리, 면적 계산, 지도 겹침이 현실 단위에 가까워진다.
  - 이 레슨은 좌표 변환을 보여주는 게 아니라 "왜 분석 전에 좌표계를 먼저 맞추는지"를 체감하게 하는 레슨이다.
- 핵심 개념:
  - `JTS.transform`: Geometry 단위 재투영
  - `ReprojectingFeatureCollection`: 피처 컬렉션 단위 재투영
  - 한국 사례에서는 `EPSG:5186` 같은 미터 기반 좌표계가 분석에 유리하다
- 코드 예제:

```java
CoordinateReferenceSystem sourceCRS = CRS.decode("EPSG:4326");
CoordinateReferenceSystem targetCRS = CRS.decode("EPSG:5186");
MathTransform transform = CRS.findMathTransform(sourceCRS, targetCRS, true);

GeometryFactory geometryFactory = JTSFactoryFinder.getGeometryFactory();
Point sourcePoint = geometryFactory.createPoint(new Coordinate(126.9784, 37.5667));
Point projectedPoint = (Point) JTS.transform(sourcePoint, transform);

System.out.println("source = " + sourcePoint);
System.out.println("projected = " + projectedPoint);

FileDataStore store = FileDataStoreFinder.getDataStore(new File("data/stations.geojson"));
try {
    SimpleFeatureSource featureSource = store.getFeatureSource();
    SimpleFeatureCollection projectedFeatures =
        new ReprojectingFeatureCollection(featureSource.getFeatures(), targetCRS);

    System.out.println("projected bounds = " + projectedFeatures.getBounds());
} finally {
    store.dispose();
}
```

- 코드에서 강조할 포인트:
  - Geometry 하나를 바꾸는 것과 컬렉션 전체를 바꾸는 방식을 둘 다 보여준다.
  - 이후 공간 연산 레슨에서 버퍼 단위를 미터로 쓰려면 여기서 재투영이 선행되어야 한다.
- 실습 안내:
  1. `Point` 하나를 재투영한다.
  2. 입력 좌표와 출력 좌표 범위가 크게 달라지는 것을 확인한다.
  3. `stations.geojson` 전체를 `EPSG:5186` 기준 컬렉션으로 변환한다.
  4. 변환 전후 bounds를 비교한다.
- 초급자 체크포인트:
  - "버퍼 500"이 500미터가 되려면 먼저 좌표계를 맞춰야 한다는 점을 말로 설명할 수 있어야 한다.

## Module 5. 공간 연산

- 모듈 시간: 75분
- 연결 목표:
  - C-LO-05
  - M5-LO-01
  - M5-LO-02
  - M5-LO-03
- 모듈 산출물: 버퍼/교차 기반 분석 예제
- 모듈 메시지: "공간 연산은 특별한 기능이 아니라, 좌표가 맞는 Geometry에 조건과 계산을 적용하는 일이다."

### Lesson 1. 공간 관계 판별

- 시간: 20분
- 연결 목표:
  - C-LO-05
  - M5-LO-01
- 왜 이걸 쓰는가:
  - 실무 질문은 대부분 "안에 있는가", "겹치는가", "가까운가" 같은 관계 판별로 시작한다.
  - 이 레슨은 복잡한 분석보다 먼저 술어(predicate)를 코드로 읽고 쓰는 감각을 만든다.
- 핵심 개념:
  - `contains`
  - `within`
  - `intersects`
- 코드 예제:

```java
GeometryFactory geometryFactory = JTSFactoryFinder.getGeometryFactory();

Polygon district = geometryFactory.createPolygon(new Coordinate[] {
    new Coordinate(0, 0),
    new Coordinate(10, 0),
    new Coordinate(10, 10),
    new Coordinate(0, 10),
    new Coordinate(0, 0)
});

Point stationInside = geometryFactory.createPoint(new Coordinate(5, 5));
Point stationOutside = geometryFactory.createPoint(new Coordinate(20, 20));

System.out.println("district contains inside = " + district.contains(stationInside));
System.out.println("inside within district = " + stationInside.within(district));
System.out.println("district intersects outside = " + district.intersects(stationOutside));
```

- 코드에서 강조할 포인트:
  - `contains`와 `within`은 방향이 반대인 짝 관계라는 점을 바로 보여준다.
  - 조건문에서 바로 쓸 수 있는 boolean 결과가 나온다는 점 때문에 서비스 필터와 연결하기 쉽다.
- 실습 안내:
  1. 하나의 폴리곤과 두 개의 포인트를 만든다.
  2. `contains`, `within`, `intersects` 결과를 비교한다.
  3. 포인트 좌표를 경계선 위로 옮겨 결과가 어떻게 달라지는지 본다.
- 초급자 체크포인트:
  - 분석 질문을 자연어에서 술어 코드로 바꿔 말할 수 있어야 한다.

### Lesson 2. 공간 연산 실습

- 시간: 25분
- 연결 목표:
  - C-LO-05
  - M5-LO-02
- 왜 이걸 쓰는가:
  - 버퍼, 교차, 합집합, 차집합은 실제 업무 문제를 빠르게 모델링할 때 가장 많이 쓰는 기본기다.
  - 예를 들어 "도로 주변 500m 영향권" 같은 문제는 버퍼 한 번으로 바로 시작할 수 있다.
- 핵심 개념:
  - `buffer`
  - `intersection`
  - `union`
  - `difference`
- 코드 예제:

```java
CoordinateReferenceSystem sourceCRS = CRS.decode("EPSG:4326");
CoordinateReferenceSystem targetCRS = CRS.decode("EPSG:5186");
MathTransform transform = CRS.findMathTransform(sourceCRS, targetCRS, true);

WKTReader reader = new WKTReader();
LineString road = (LineString) JTS.transform(
    reader.read("LINESTRING (126.97 37.56, 126.99 37.57)"),
    transform
);
Polygon site = (Polygon) JTS.transform(
    reader.read("POLYGON ((126.965 37.555, 126.995 37.555, 126.995 37.575, 126.965 37.575, 126.965 37.555))"),
    transform
);

Geometry roadBuffer = road.buffer(500.0);
Geometry overlap = roadBuffer.intersection(site);
Geometry remaining = site.difference(roadBuffer);

System.out.println("buffer area = " + roadBuffer.getArea());
System.out.println("overlap area = " + overlap.getArea());
System.out.println("remaining area = " + remaining.getArea());
```

- 코드에서 강조할 포인트:
  - 버퍼 거리 `500.0`을 미터로 해석하려고 먼저 재투영한다.
  - "왜 이걸 쓰는가"는 연산 이름 암기가 아니라 실제 질문에 대응하는 것이다.
- 실습 안내:
  1. 선형 객체와 관심 영역 폴리곤을 만든다.
  2. 도로 버퍼 500m를 만든다.
  3. 교차 영역과 남은 영역 면적을 각각 계산한다.
  4. 버퍼 값을 100m, 1000m로 바꿔 결과 변화를 비교한다.
- 초급자 체크포인트:
  - 연산 전에 단위 확인, 연산 후에는 결과 Geometry 의미 확인이라는 두 단계 습관을 갖게 한다.

### Lesson 3. 측정과 공간 인덱스

- 시간: 30분
- 연결 목표:
  - C-LO-05
  - M5-LO-03
- 왜 이걸 쓰는가:
  - 데이터가 많아지면 모든 Geometry를 전부 비교하는 방식은 금방 느려진다.
  - 이 레슨은 정확도보다 먼저 "후보를 줄이고 나서 정밀 판별한다"는 실무 흐름을 익히게 한다.
- 핵심 개념:
  - `distance`
  - `getArea`
  - `STRtree`
- 코드 예제:

```java
STRtree index = new STRtree();

FileDataStore store = FileDataStoreFinder.getDataStore(new File("data/stations.geojson"));

try {
    SimpleFeatureSource source = store.getFeatureSource();
    try (SimpleFeatureIterator iterator = source.getFeatures().features()) {
        while (iterator.hasNext()) {
            SimpleFeature feature = iterator.next();
            Geometry geometry = (Geometry) feature.getDefaultGeometry();
            index.insert(geometry.getEnvelopeInternal(), feature);
        }
    }

    GeometryFactory geometryFactory = JTSFactoryFinder.getGeometryFactory();
    Point queryPoint = geometryFactory.createPoint(new Coordinate(127.0276, 37.4979));
    Envelope searchEnvelope = queryPoint.buffer(0.01).getEnvelopeInternal();

    @SuppressWarnings("unchecked")
    List<SimpleFeature> candidates = index.query(searchEnvelope);

    for (SimpleFeature candidate : candidates) {
        Geometry geometry = (Geometry) candidate.getDefaultGeometry();
        double distance = geometry.distance(queryPoint);
        System.out.println(candidate.getID() + " distance = " + distance);
    }
} finally {
    store.dispose();
}
```

- 코드에서 강조할 포인트:
  - `STRtree`는 정답을 바로 주는 것이 아니라 "가능성 있는 후보"를 빨리 좁혀준다.
  - 거리 계산 역시 좌표계에 따라 의미가 달라진다는 점을 다시 연결한다.
- 실습 안내:
  1. 역 피처들을 인덱스에 넣는다.
  2. 검색 지점을 하나 만든다.
  3. 먼저 envelope로 후보를 찾고, 그 뒤 `distance()`로 정렬 가능한 값을 구한다.
  4. 후보 수와 전체 수를 비교해 성능 감각을 설명한다.
- 초급자 체크포인트:
  - 인덱스는 "정확한 판별 전에 후보를 줄이는 단계"라는 표현을 기억하게 한다.

## Module 6. 지도 렌더링과 스타일링

- 모듈 시간: 85분
- 연결 목표:
  - C-LO-06
  - M6-LO-01
  - M6-LO-02
  - M6-LO-03
- 모듈 산출물: 스타일 적용 지도 렌더러
- 모듈 메시지: "분석 결과를 보여주지 못하면 구현은 절반만 끝난 것이다. GeoTools는 결과를 지도 형태로 확인할 수 있게 해준다."

### Lesson 1. MapContent 구조 이해

- 시간: 25분
- 연결 목표:
  - C-LO-06
  - M6-LO-01
- 왜 이걸 쓰는가:
  - 렌더링은 마법처럼 일어나는 것이 아니라 `데이터 + 스타일 + 지도 컨테이너`가 합쳐져서 만들어진다.
  - 이 구조를 알아야 이후 SLD와 이미지 출력이 납득된다.
- 핵심 개념:
  - `MapContent`: 지도 컨테이너
  - `FeatureLayer`: 벡터 레이어
  - `Style`: 화면 표현 규칙
- 코드 예제:

```java
FileDataStore store = FileDataStoreFinder.getDataStore(new File("data/districts.shp"));

try {
    SimpleFeatureSource featureSource = store.getFeatureSource();
    Style style = SLD.createSimpleStyle(featureSource.getSchema());

    MapContent map = new MapContent();
    map.setTitle("District Overview");
    map.addLayer(new FeatureLayer(featureSource, style));

    System.out.println("layer count = " + map.layers().size());
    System.out.println("map title = " + map.getTitle());
} finally {
    store.dispose();
}
```

- 코드에서 강조할 포인트:
  - `MapContent`는 지도 자체라기보다 "지도를 그리기 위한 재료 묶음"이다.
  - `SLD.createSimpleStyle`은 빠른 확인용 기본 스타일이다.
- 실습 안내:
  1. shapefile을 연다.
  2. 기본 스타일을 만든다.
  3. `MapContent`에 레이어를 하나 올린다.
  4. 아직 그리지 않아도 구조가 완성되었는지 로그로 확인한다.
- 초급자 체크포인트:
  - 지도 렌더링은 `FeatureSource -> Layer -> MapContent` 순서로 머릿속에 정리되어야 한다.

### Lesson 2. SLD와 스타일 적용

- 시간: 30분
- 연결 목표:
  - C-LO-06
  - M6-LO-02
- 왜 이걸 쓰는가:
  - 스타일이 없으면 지도를 그려도 의미가 잘 드러나지 않는다.
  - 즉 스타일은 "예쁘게 꾸미기"가 아니라 "무엇이 중요한지 읽히게 만들기" 위한 도구다.
- 핵심 개념:
  - `SLD.createPolygonStyle`
  - `SLD.createLineStyle`
  - Geometry 타입에 따라 스타일 선택이 달라진다
- 코드 예제:

```java
FileDataStore districtStore = FileDataStoreFinder.getDataStore(new File("data/districts.shp"));
FileDataStore roadStore = FileDataStoreFinder.getDataStore(new File("data/roads.shp"));

try {
    SimpleFeatureSource districtSource = districtStore.getFeatureSource();
    SimpleFeatureSource roadSource = roadStore.getFeatureSource();

    Style districtStyle = SLD.createPolygonStyle(
        new Color(50, 80, 80),
        new Color(255, 224, 130),
        0.75f
    );
    Style roadStyle = SLD.createLineStyle(new Color(220, 70, 70), 2.0f);

    MapContent map = new MapContent();
    map.setTitle("Styled Map");
    map.addLayer(new FeatureLayer(districtSource, districtStyle, "districts"));
    map.addLayer(new FeatureLayer(roadSource, roadStyle, "roads"));

    System.out.println("styled layers = " + map.layers().size());
} finally {
    districtStore.dispose();
    roadStore.dispose();
}
```

- 코드에서 강조할 포인트:
  - 폴리곤과 라인은 같은 스타일로 다루지 않는다.
  - 색과 선 두께는 "무엇을 읽게 할 것인가"라는 의도를 담는 최소 장치다.
- 실습 안내:
  1. 행정구역과 도로를 각각 읽는다.
  2. 폴리곤용, 라인용 스타일을 따로 만든다.
  3. 겹쳐 그렸을 때 무엇이 먼저 보여야 하는지 레이어 순서를 바꿔본다.
  4. 색상과 두께를 바꿔 가독성을 비교한다.
- 초급자 체크포인트:
  - 스타일은 데이터 의미 전달을 위한 것이라는 점을 설명할 수 있어야 한다.

### Lesson 3. 지도 출력과 인터랙션

- 시간: 30분
- 연결 목표:
  - C-LO-06
  - M6-LO-03
- 왜 이걸 쓰는가:
  - 결과를 로그만으로 확인하면 공간적 오류를 놓치기 쉽다.
  - PNG 출력은 가장 빠른 결과 확인 방법이고, `JMapFrame`은 초급자가 즉시 확대/이동을 시험해 볼 수 있는 쉬운 UI다.
- 핵심 개념:
  - `StreamingRenderer`: 이미지 렌더링
  - `ImageIO.write`: PNG 저장
  - `JMapFrame.showMap`: 빠른 인터랙티브 확인
- 코드 예제:

```java
FileDataStore store = FileDataStoreFinder.getDataStore(new File("data/districts.shp"));

try {
    SimpleFeatureSource source = store.getFeatureSource();
    Style style = SLD.createSimpleStyle(source.getSchema());

    MapContent map = new MapContent();
    map.setTitle("District Export");
    map.addLayer(new FeatureLayer(source, style));

    ReferencedEnvelope bounds = source.getBounds();
    StreamingRenderer renderer = new StreamingRenderer();
    renderer.setMapContent(map);

    BufferedImage image = new BufferedImage(1000, 800, BufferedImage.TYPE_INT_ARGB);
    Graphics2D graphics = image.createGraphics();
    graphics.setPaint(Color.WHITE);
    graphics.fillRect(0, 0, 1000, 800);
    renderer.paint(graphics, new Rectangle(1000, 800), bounds);
    graphics.dispose();

    ImageIO.write(image, "png", new File("output/districts.png"));

    JMapFrame.showMap(map);
} finally {
    store.dispose();
}
```

- 코드에서 강조할 포인트:
  - PNG는 자동 보고서, 이메일 공유, 중간 결과 검증에 바로 쓸 수 있다.
  - `JMapFrame`은 데스크톱 제품용 UI라기보다 학습용/검증용 확인 창이라는 점을 분명히 한다.
- 실습 안내:
  1. shapefile을 읽어 지도를 구성한다.
  2. PNG 파일을 출력한다.
  3. `output/districts.png`를 열어 렌더링이 맞는지 확인한다.
  4. `JMapFrame`으로 띄워 확대/이동해 본다.
- 초급자 체크포인트:
  - 렌더링 결과 검증은 로그보다 지도로 보는 편이 빠르다는 점을 이해하게 한다.

## Module 7. OGC 웹 서비스 연동

- 모듈 시간: 70분
- 연결 목표:
  - C-LO-07
  - M7-LO-01
  - M7-LO-02
  - M7-LO-03
- 모듈 산출물: WMS/WFS 클라이언트 데모
- 모듈 메시지: "GeoTools는 로컬 파일만 다루는 도구가 아니라, OGC 표준 서비스를 소비하는 클라이언트 역할도 한다."

### Lesson 1. WMS/WFS 연결 개요

- 시간: 20분
- 연결 목표:
  - C-LO-07
  - M7-LO-01
- 왜 이걸 쓰는가:
  - WMS는 지도 이미지를, WFS는 피처 데이터를 준다.
  - 즉 "보기"가 목적이면 WMS, "분석/후처리"가 목적이면 WFS를 우선 검토해야 한다.
- 핵심 개념:
  - `WebMapServer`: WMS 연결
  - Capabilities 문서: 서버가 제공하는 레이어와 요청 가능 기능 목록
  - named layer: 실제 요청 가능한 레이어
- 코드 예제:

```java
URL capabilitiesUrl = new URL(
    "http://localhost:8080/geoserver/wms?service=WMS&request=GetCapabilities"
);

WebMapServer wms = new WebMapServer(capabilitiesUrl);
WMSCapabilities capabilities = wms.getCapabilities();
org.geotools.ows.wms.Layer[] namedLayers = WMSUtils.getNamedLayers(capabilities);

System.out.println("service title = " + capabilities.getService().getTitle());
for (org.geotools.ows.wms.Layer layer : namedLayers) {
    System.out.println("layer = " + layer.getName() + " / " + layer.getTitle());
}
```

- 코드에서 강조할 포인트:
  - WMS 연결의 첫 단계는 지도를 바로 그리는 것이 아니라, 서버가 무엇을 줄 수 있는지 능력 문서를 읽는 것이다.
  - 초급자에게는 WMS를 "지도 이미지 API"라고 설명하면 이해가 빠르다.
- 실습 안내:
  1. 로컬 GeoServer WMS에 연결한다.
  2. 서비스 제목과 레이어 목록을 읽는다.
  3. 어떤 레이어가 실제 요청 가능한 named layer인지 확인한다.
  4. WMS와 WFS의 결과 형태 차이를 표로 정리하게 한다.
- 초급자 체크포인트:
  - WMS는 이미지, WFS는 피처라는 차이를 코드와 결과 관점에서 설명할 수 있어야 한다.

### Lesson 2. Filter Encoding 실습

- 시간: 25분
- 연결 목표:
  - C-LO-07
  - M7-LO-02
- 왜 이걸 쓰는가:
  - 서비스나 파일에서 필요한 것만 가져오려면 필터가 필요하다.
  - 결국 실무에서는 "전체를 다 받고 나서 자르기"보다 "조건을 보내서 필요한 것만 받기"가 더 중요하다.
- 핵심 개념:
  - `ECQL.toFilter`
  - `Query`
  - BBOX와 속성 조건 조합
- 코드 예제:

```java
FileDataStore store = FileDataStoreFinder.getDataStore(new File("data/stations.geojson"));

try {
    SimpleFeatureSource source = store.getFeatureSource();
    String typeName = source.getSchema().getTypeName();
    String geomField = source.getSchema().getGeometryDescriptor().getLocalName();

    Filter filter = ECQL.toFilter(
        "BBOX(" + geomField + ", 126.8, 37.4, 127.2, 37.7) AND line = 'Line 2'"
    );
    Query query = new Query(typeName, filter);
    SimpleFeatureCollection filtered = source.getFeatures(query);

    System.out.println("filtered bounds = " + filtered.getBounds());
    try (SimpleFeatureIterator iterator = filtered.features()) {
        while (iterator.hasNext()) {
            SimpleFeature feature = iterator.next();
            System.out.println(feature.getAttribute("name"));
        }
    }
} finally {
    store.dispose();
}
```

- 코드에서 강조할 포인트:
  - geometry 필드명을 하드코딩하지 않고 스키마에서 읽는 습관을 들인다.
  - 이 필터 패턴은 로컬 파일뿐 아니라 WFS 요청에서도 같은 생각으로 이어진다.
- 실습 안내:
  1. `stations.geojson`에서 geometry 필드명을 읽는다.
  2. BBOX 조건과 속성 조건을 함께 건다.
  3. 결과 피처 이름만 로그에 출력한다.
  4. BBOX 범위를 줄이거나 `line` 값을 바꿔 결과 수가 어떻게 달라지는지 확인한다.
- 초급자 체크포인트:
  - 필터는 조회 후 가공이 아니라 조회 자체를 더 똑똑하게 만드는 도구라고 이해하게 한다.

### Lesson 3. GeoServer 연동 가이드 실습

- 시간: 25분
- 연결 목표:
  - C-LO-07
  - M7-LO-03
- 왜 이걸 쓰는가:
  - 서비스 연동의 목표는 "붙었다"가 아니라 "필요한 레이어와 피처를 프로그램 안에서 소비할 수 있다"는 데 있다.
  - 이 레슨에서는 GeoServer를 외부 시스템이라기보다 GeoTools가 표준 방식으로 다룰 수 있는 데이터 공급자로 보게 한다.
- 핵심 개념:
  - `WFSDataStoreFactory.createGetCapabilitiesRequest`
  - `WFSDataAccessFactory.URL`
  - `DataStoreFinder`로 WFS 연결
- 코드 예제:

```java
URL baseUrl = new URL("http://localhost:8080/geoserver/wfs");
URL capabilitiesUrl = WFSDataStoreFactory.createGetCapabilitiesRequest(baseUrl);

Map<String, Object> params = new HashMap<>();
params.put(WFSDataAccessFactory.URL.key, capabilitiesUrl);

DataStore wfsStore = DataStoreFinder.getDataStore(params);

if (wfsStore == null) {
    throw new IllegalStateException("WFS 연결에 실패했습니다.");
}

try {
    String typeName = wfsStore.getTypeNames()[0];
    SimpleFeatureSource source = wfsStore.getFeatureSource(typeName);
    String geomField = source.getSchema().getGeometryDescriptor().getLocalName();

    Filter filter = ECQL.toFilter(
        "BBOX(" + geomField + ", 126.8, 37.4, 127.2, 37.7)"
    );
    SimpleFeatureCollection features = source.getFeatures(filter);

    System.out.println("remote type = " + typeName);
    try (SimpleFeatureIterator iterator = features.features()) {
        while (iterator.hasNext()) {
            SimpleFeature feature = iterator.next();
            System.out.println(feature.getID());
        }
    }
} finally {
    wfsStore.dispose();
}
```

- 확장 코드 예제:

```java
URL wmsUrl = new URL(
    "http://localhost:8080/geoserver/wms?service=WMS&request=GetCapabilities"
);
WebMapServer wms = new WebMapServer(wmsUrl);
org.geotools.ows.wms.Layer layerInfo = WMSUtils.getNamedLayers(wms.getCapabilities())[0];

MapContent map = new MapContent();
map.setTitle("GeoServer WMS Preview");
map.addLayer(new WMSLayer(wms, layerInfo));
JMapFrame.showMap(map);
```

- 코드에서 강조할 포인트:
  - WFS는 피처를 받아와 후속 분석으로 연결할 수 있다.
  - WMS는 빠르게 화면에 보여주는 데 유리하다.
  - 같은 GeoServer여도 목적에 따라 WMS와 WFS를 다르게 쓴다는 점이 핵심이다.
- 실습 안내:
  1. GeoServer WFS Capabilities URL을 만든다.
  2. `DataStoreFinder`로 연결한다.
  3. 첫 타입을 선택해 BBOX 필터 조회를 수행한다.
  4. 확장 단계에서 같은 서버를 WMS로 연결해 지도 미리보기를 띄운다.
- 초급자 체크포인트:
  - "보여주기=WMS, 가공하기=WFS"라는 규칙을 실제 코드와 함께 설명할 수 있어야 한다.

## 5. 모듈별 운영 포인트

- Module 1:
  - 용어 정의를 너무 길게 하지 않는다.
  - 첫 성공 로그를 빠르게 보여준다.
- Module 2:
  - Geometry와 Feature를 분리해 설명한다.
  - 면적/길이 값이 아직 실제 단위가 아니라는 점을 반복한다.
- Module 3:
  - 파일 포맷 차이보다 DataStore 패턴의 공통점을 먼저 강조한다.
  - 읽기와 쓰기 모두 자원 해제 패턴을 습관화한다.
- Module 4:
  - CRS를 암기 과목처럼 다루지 않는다.
  - 분석 전에 좌표계를 맞춰야 한다는 맥락을 계속 연결한다.
- Module 5:
  - 연산 이름보다 해결하는 질문을 먼저 말한다.
  - 미터 단위 버퍼를 쓰기 전에 재투영 여부를 반드시 확인하게 한다.
- Module 6:
  - 스타일은 꾸미기보다 읽기 쉽게 만드는 장치라고 설명한다.
  - 이미지 출력과 인터랙티브 확인을 둘 다 보여준다.
- Module 7:
  - 서버 구축보다 소비자 코드에 집중한다.
  - 외부 서비스 오류가 나도 GeoTools 코드 구조는 유지된다는 점을 설명한다.

## 6. 자체 리뷰

### 6.1 목표 커버리지 점검

| 학습목표 ID | 커버하는 레슨 | 점검 결과 |
| --- | --- | --- |
| C-LO-01 | M1-L1, M1-L2, M1-L3 | 충족 |
| C-LO-02 | M2-L1, M2-L2, M2-L3 | 충족 |
| C-LO-03 | M3-L1, M3-L2, M3-L3 | 충족 |
| C-LO-04 | M4-L1, M4-L2, M4-L3 | 충족 |
| C-LO-05 | M5-L1, M5-L2, M5-L3 | 충족 |
| C-LO-06 | M6-L1, M6-L2, M6-L3 | 충족 |
| C-LO-07 | M7-L1, M7-L2, M7-L3 | 충족 |

### 6.2 요구사항 충족 점검

- 교안 모드 기준 문서인 [_workspace/01_curriculum.md](C:\Users\jinif\Documents\workspace\harness-100\codex\geotools-online-course\_workspace\01_curriculum.md)의 모듈/레슨 순서를 유지했다.
- 모든 레슨에 아래 4가지를 포함했다.
  - 학습 목표 연결
  - 핵심 개념 설명
  - GeoTools 실제 사용 코드 예제
  - 실습 안내
- 설명보다 "왜 이걸 쓰는지"를 먼저 제시하도록 모든 레슨을 같은 패턴으로 통일했다.
- 초급자가 막히기 쉬운 지점을 각 레슨의 체크포인트로 분리해 두었다.

### 6.3 강점

- Geometry -> Feature -> 파일 I/O -> CRS -> 공간 연산 -> 렌더링 -> 서비스 연동으로 난이도 상승이 자연스럽다.
- GeoTools 코드가 단편 예제가 아니라 다음 모듈에서 재사용되는 흐름으로 연결된다.
- Module 4와 Module 5 사이에 좌표계/단위 문제를 강하게 연결해 초급자 오개념 가능성을 낮췄다.
- Module 7에서 WMS와 WFS의 역할 차이를 코드 기준으로 분리해 설명했다.

### 6.4 남은 리스크

- Module 7 실습은 GeoServer/WFS/WMS 엔드포인트 준비 여부에 따라 성공률이 크게 달라진다.
- Module 3 PostGIS 실습도 사전 DB 준비가 없으면 강의 중 지연이 생길 수 있다.
- 예제 데이터의 실제 geometry 필드명과 속성명이 문서 예시와 다르면 일부 코드는 현장 조정이 필요하다.

### 6.5 최종 판정

- 판정: 사용 가능
- 치명적 누락: 없음
- 운영 전 확인 필요:
  - `data/` 예제 파일명과 필드명 확정
  - PostGIS 연결 정보 확정
  - GeoServer WMS/WFS 엔드포인트 사전 점검
