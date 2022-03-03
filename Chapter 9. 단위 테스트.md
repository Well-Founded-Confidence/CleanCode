# Chapter 9. 단위 테스트

## TDD 법칙 세 가지

1. 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.
2. 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
3. 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.
- 위 법칙을 따르면 테스트 코드와 실제 코드를 거의 동시에 개발할 수 있다.
- 실제 코드를 전부 테스트하는 테스트 케이스를 만들 수 있지만, 방대한 테스트 코드는 심각한 관리 문제를 유발하기도 한다.

```java
// test.java

@Autowired
public Service service;

@MockBean
public Repository repository;

@SpyBean
public Repository repository;

@Test
public void methodTest() {
    Assert.assertTrue(service.getMember().size()==10); // return null
    // False
}

// build -> test -> run
```
## Test Double

> 테스트에서 원본 객체를 대신하는 객체

- Stub
  - 원래의 구현을 최대한 단순한 것으로 대체한다
  - 테스트를 위해 프로그래밍된 항목에만 응답한다
- Spy
  - Stub의 역할을 하면서 호출에 대한 정보를 기록한다
  - 예로, 이메일 서비스에서 메시지가 몇 번 전송되었는지 확인할 때
- Mock
  - 행위를 검증하기 위해 가짜 객체를 만들어 테스트하는 방법
  - 호출에 대한 동작을 프로그래밍할 수 있다
  - Stub은 상태를 검증하고 Mock은 행위를 검증한다


## 깨끗한 테스트 코드 유지하기

- 테스트 코드를 중요하게 생각하지 않는 경우
    - 실제 코드를 테스트만 하면 그만이라고 생각함
    - 일회용 테스트 코드를 짜오다가 자동화된 단위 테스트 슈트를 짜기는 쉽지 않음
- 실제 코드가 진화하면 테스트 코드도 변해야 함
    - 테스트 코드가 복잡할수록 실제 코드를 짜는 시간보다 테스트 케이스를 추가하는 시간이 더 걸림
    - 새 버전을 출시할 때마다 테스트 케이스를 유지보수하는 비용이 늘어남
- 테스트 슈트가 없는 경우
    - 자신이 수정한 코드가 제대로 도는지 확인할 방법이 없음
    - 시스템을 수정해도 안전하다는 사실을 검증하지 못함
    - 결함율이 높아짐

→ 테스트 코드를 **깨끗**하게 짜면 해결됨

→ 테스트 코드는 실제 코드 못지 않게 중요하며 **깨끗**하게 짜야한다!!

### 테스트는 유연성, 유지보수성, 재사용성을 제공한다

코드에 **유연성, 유지보수성, 재사용성**을 제공하는 버팀목은 **단위 테스트**다.

- 테스트 케이스가 있으면 변경이 쉬워진다.
- 테스트 케이스가 없다면 모든 변경이 잠정적인 버그다.
- 자동화된 단위 테스트 슈트는 설계와 아키텍처를 최대한 깨끗하게 보존하는 열쇠다.

테스트 코드가 지저분할수록 코드를 변경하는 능력과 코드 구조를 개선하는 능력이 떨어진다.

## 깨끗한 테스트 코드

깨끗한 테스트 코드를 만들려면 **가독성**이 중요하다.

- 가독성을 높이려면 명료성, 단순성, 풍부한 표현력이 필요하다.
- 테스트 코드는 최소의 표현으로 많은 것을 나타내야 한다.

```java
// 9-1
// SerializedPageResponderTest.java
public void testGetPageHierarchyAsXml() throws Exception {
  crawler.addPage(root, PathParser.parse("PageOne"));
  crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
  crawler.addPage(root, PathParser.parse("PageTwo"));

  request.setResource("root");
  request.addInput("type", "pages");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response =
    (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
  String xml = response.getContent();

  assertEquals("text/xml", response.getContentType());
  assertSubString("<name>PageOne</name>", xml); //중복
  assertSubString("<name>PageTwo</name>", xml);
  assertSubString("<name>ChildOne</name>", xml);
}

public void testGetPageHierarchyAsXmlDoesntContainSymbolicLinks() throws Exception {
  WikiPage pageOne = crawler.addPage(root, PathParser.parse("PageOne"));
  crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
  crawler.addPage(root, PathParser.parse("PageTwo"));

  PageData data = pageOne.getData();
  WikiPageProperties properties = data.getProperties();
  WikiPageProperty symLinks = properties.set(SymbolicPage.PROPERTY_NAME);
  symLinks.set("SymPage", "PageTwo");
  pageOne.commit(data);

  request.setResource("root");
  request.addInput("type", "pages");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response =
    (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
  String xml = response.getContent();

  assertEquals("text/xml", response.getContentType());
  assertSubString("<name>PageOne</name>", xml); //중복
  assertSubString("<name>PageTwo</name>", xml);
  assertSubString("<name>ChildOne</name>", xml);
  assertNotSubString("SymPage", xml);
}

public void testGetDataAsHtml() throws Exception {
  crawler.addPage(root, PathParser.parse("TestPageOne"), "test page");

  request.setResource("TestPageOne"); request.addInput("type", "data");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response =
    (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
  String xml = response.getContent();

  assertEquals("text/xml", response.getContentType());
  assertSubString("test page", xml);
  assertSubString("<Test", xml);
}
```
- BUILD-OPERATE-CHECK 패턴?
    
    BUILD: 테스트 자료를 만든다.
    
    OPERATE: 테스트 자료를 조작한다.
    
    CHECK: 조작한 결과가 올바른지 확인한다.
- addPage와 assertSubString을 부르느라 중복되는 코드가 매우 많다.
- PathParser는 crawler가 사용하는 객체인데, 테스트와 무관하며 테스트 코드의 의도만 흐린다.
- responder 객체를 생성하는 코드와 response를 수집해 변환하는 코드 역시 마찬가지다.
- resource와 인수에서 요청 URL을 만드는 코드도 보인다.
- 독자에 대한 배려가 부족하다. 테스트와 무관한 코드를 이해한 후에야 테스트 케이스를 이해할 수 있다.
```java
// 9-2
// SerializedPageResponderTest.java (Refactored code)
public void testGetPageHierarchyAsXml() throws Exception {
  makePages("PageOne", "PageOne.ChildOne", "PageTwo");

  submitRequest("root", "type:pages");

  assertResponseIsXML();
  assertResponseContains(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
}

public void testSymbolicLinksAreNotInXmlPageHierarchy() throws Exception {
  WikiPage page = makePage("PageOne");
  makePages("PageOne.ChildOne", "PageTwo");

  addLinkTo(page, "PageTwo", "SymPage");

  submitRequest("root", "type:pages");

  assertResponseIsXML();
  assertResponseContains(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
  assertResponseDoesNotContain("SymPage");
}

public void testGetDataAsXml() throws Exception {
  makePageWithContent("TestPageOne", "test page");

  submitRequest("TestPageOne", "type:data");

  assertResponseIsXML();
  assertResponseContains("test page", "<Test");
}
```
- 잡다하고 세세한 코드를 거의 다 없앴다는 것에 주목!
- 테스트 코드는 **진짜 필요한 자료 유형과 함수만 사용**한다.

### 도메인에 특화된 테스트 언어

DSL(Domain-specific language)

- 흔히 쓰는 시스템 조작 API를 사용하는 대신 API 위에다 함수와 유틸리티를 구현한 후 그 함수와 유틸리티를 사용함 → 테스트 코드를 짜기도 읽기도 쉬워짐
- 테스트 코드에서 사용하는 특수 API가 됨 → 테스트 언어
- 숙련된 개발자라면 자기 코드를 좀 더 간결하고 표현력이 풍부한 코드로 리팩터링해야한다.

### 이중 표준

테스트 API 코드에 적용하는 표준은 실제 코드에 적용하는 표준과 다르다.

- 실제 환경이 아니라 테스트 환경에서 돌아가는 코드이기 때문에 실제 코드만큼 효율적일 필요는 없다.
- 실제 환경에서는 절대로 안 되지만 테스트 환경에서는 전혀 문제 없는 방식이 있다.
    - 메모리, CPU 효율과 관련 있는 경우
```java
// 9-3
// EnvironmentControllerTest.java
// 온도가 급격하게 떨어지면 경보, 온풍기, 송풍기가 모두 가동되는지 확인하는 코드
@Test
public void turnOnLoTempAlarmAtThreashold() throws Exception {
  hw.setTemp(WAY_TOO_COLD); 
  controller.tic(); 
  assertTrue(hw.heaterState());   
  assertTrue(hw.blowerState()); 
  assertFalse(hw.coolerState()); 
  assertFalse(hw.hiTempAlarm());       
  assertTrue(hw.loTempAlarm());
}

// good
@Test
public void turnOnCoolerAndBlowerIfTooHot() throws Exception {
  tooHot();
  assertEquals("hBchl", hw.getState());
}

@Test
public void turnOnHeaterAndBlowerIfTooCold() throws Exception {
  tooCold();
  assertEquals("HBchl", hw.getState());
}

@Test
public void turnOnHiTempAlarmAtThreshold() throws Exception {
  wayTooHot();
  assertEquals("hBCHl", hw.getState());
}

@Test
public void turnOnLoTempAlarmAtThreshold() throws Exception {
  wayTooCold();
  assertEquals("HBchL", hw.getState());
}

public String getState() {
  String state = "";
  state += heater ? "H" : "h";
  state += blower ? "B" : "b";
  state += cooler ? "C" : "c";
  state += hiTempAlarm ? "H" : "h";
  state += loTempAlarm ? "L" : "l";
  return state;
}
```
## 테스트당 assert 하나

JUnit으로 테스트 코드를 짤 때는 함수마다 assert 문을 단 하나만 사용해야 한다?

- 장점: assert문이 하나인 함수는 결론이 하나라서 코드를 이해하기 쉽고 빠르다.
- 단점: 테스트를 분리하면 중복되는 코드가 많아진다.

→ ‘단일 assert 문' 규칙은 훌륭한 지침이지만 때로는 함수 하나에 여러 assert 문을 넣기도 한다.

→ assert 문 개수는 최대한 줄이는 게 좋다.

- cf. TEMPLATE METHOD 패턴
    
    [https://yaboong.github.io/design-pattern/2018/09/27/template-method-pattern/](https://yaboong.github.io/design-pattern/2018/09/27/template-method-pattern/)
    

### 테스트당 개념 하나

“테스트 함수마다 한 개념만 테스트하라"는 규칙

- 한 테스트 함수에서 여러 개념을 테스트하지 말자.

### 결론

→ 개념 당 **assert 문 수를 최소**로 줄여라.

→ 테스트 함수 하나는 **개념 하나만 테스트**하라.

## F.I.R.S.T

깨끗한 테스트는 다음 다섯 가지 규칙을 따른다.

- **Fast**(빠르게): 테스트는 빨리 돌아야 한다.
    - 테스트가 느려 자주 돌리지 못하면 초반에 문제를 찾아내 고치지 못한다.
- **Independent**(독립적으로): 각 테스트는 서로 의존하면 안 된다.
    - 각 테스트는 독립적으로, 어떤 순서로 실행해도 괜찮아야 한다.
    - 하나가 실패할 때 나머지도 잇달아 실패하므로 원인을 진단하기 어려워지며 후반 테스트가 찾아내야 할 결함이 숨겨진다.
- **Repeatable**(반복가능하게): 테스트는 어떤 환경에서도 반복 가능해야 한다.
    - 실제 환경, QA 환경, 버스를 타고 집으로 가는 길에 사용하는(네트워크에 연결되지 않은) 노트북 환경에서도 실행할 수 있어야 한다.
- **Self-Validating**(자가 검증하는): 테스트는 bool 값으로 결과를 내야 한다. 성공 아니면 실패.
- **Timely**(적시에): 단위 테스트는 테스트하려는 실제 코드를 구현하기 직전에 구현한다.

## 결론

- 테스트 코드는 실제 코드 만큼이나 프로젝트 건강에 중요하다.
- 테스트 코드는 실제 코드의 유연성, 유지 보수성, 재사용성을 보존하고 강화한다.
- 테스트 코드를 지속적으로 깨끗하게 관리하고 표현력을 높이고 간결하게 정리하자.
- 테스트 API를 구현해 도메인 특화 언어(DSL)를 만들자.
- 테스트 코드가 망가지면 실제 코드도 망가진다. **테스트 코드를 깨끗하게 유지하자.**
