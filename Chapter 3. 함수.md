- [Chapter3. 함수](#chapter3-함수)
  - [작게 만들어라!](#작게-만들어라)
    - [블록과 들여쓰기](#블록과-들여쓰기)
  - [한 가지만 해라!](#한-가지만-해라)
  - [함수 당 추상화 수준은 하나로!](#함수-당-추상화-수준은-하나로)
    - [위에서 아래로 코드 읽기: 내려가기 규칙](#위에서-아래로-코드-읽기-내려가기-규칙)
  - [Switch문](#switch문)
  - [서술적인 이름을 사용하라!](#서술적인-이름을-사용하라)
  - [함수 인수](#함수-인수)
    - [많이 쓰는 단항 형식](#많이-쓰는-단항-형식)
    - [플래그 인수](#플래그-인수)
    - [이항 함수](#이항-함수)
    - [삼항 함수](#삼항-함수)
    - [인수객체](#인수객체)
    - [인수 목록](#인수-목록)
    - [동사와 키워드](#동사와-키워드)
  - [부수 효과를 일으키지 마라](#부수-효과를-일으키지-마라)
    - [출력 인수](#출력-인수)
  - [명령과 조회를 분리하라!](#명령과-조회를-분리하라)
  - [오류 코드보다 예외를 사용하라!](#오류-코드보다-예외를-사용하라)
    - [Try/Catch 블록 뽑아내기](#trycatch-블록-뽑아내기)
    - [Error.java 의존성 자석](#errorjava-의존성-자석)
  - [반복하지 마라!](#반복하지-마라)
  - [구조적 프로그래밍](#구조적-프로그래밍)
  - [함수를 짜는 과정](#함수를-짜는-과정)
---
# Chapter3. 함수

> 어떤 프로그램이든 가장 기본적인 단위가 `함수`다

- 읽기 쉽고 이해하기 쉬운 함수는 무슨 이유에서일까?
- 의도를 분명히 표현하는 함수를 어떻게 구현할 수 있을까?
- 함수에 어떤 속성을 부여해야 처음 읽는 사람이 프로그램 내부를 직관적으로 파악할 수 있을까?

## 작게 만들어라!

> 작은 함수, 코드가 짧은 함수를 만들어라

### 블록과 들여쓰기

- `if`/`else`/`while`문 등에 들어가는 블록은 한 줄이어야 한다.
  - 대개 이 곳에서 함수를 호출하기 때문이다.
  - 바깥을 감싸는 함수(`enclosing function`)가 작아진다.
  - 호출 함수 이름을 적절히 짓는다면 코드를 이해하기도 쉬워진다.
- 함수에서 들여쓰기는 1-2단을 넘어가면 안된다
  ```java
  // bad
  if (pageData.hsAttribute("Test")) {
    if (includeSuiteSetup) {
      if () {
        if () {
          // ...
        }
      }
    }
    if (setup != null) {
      // ...
    }
    if (teardown != null) {
      // ...
    }
  }
  
  // good
  boolean isTestPage = pageData.hasAttribute("Test");
  if (isTestPage) {
    includeSetupPages(testPage, newPageContent, isSuite);
  }
  
  // better
  // 인스턴스 변수로 만들 것을 판단하고, 함수를 쪼갠다
  boolean isSuite;
  if (isTestPage()) {
    includeSetupAndTeardownPages();
  }
  ```

## 한 가지만 해라!

> 함수는 `한 가지`를 해야 한다. 그 `한 가지`를 잘 해야 한다. 그 `한 가지`만을 해야 한다.

## 함수 당 추상화 수준은 하나로!
- 함수가 확실히 '한 가지' 작업만 하려면 함수 내 모든 문장의 추상화 수준이 동일해야 함
- 추상화 수준 예시
  - getHtml() : 추상화 수준 매우 높음
  - String pagePathName = PathParser.render(pagepath) : 추상화 수준 중간
  - .append("\n") : 추상화 수준 매우 낮음

- `p.392 [G34] 함수는 추상화 수준을 한 단계만 내려가야 한다`
  > - 함수 내 모든 문장은 추상화 수준이 동일해야 함. 
  > - 추상화 수준은 함수 이름이 의미하는 작업보다 한 단계만 낮아야 함
  ```java
  // bad
  // 추상화 수준이 2개가 섞여 있음(수평선에 크기가 있다는 개념 & HR 태그 자체의 문법)
  // 즉, hr 수평선 태그와 태그의 size 속성을 명시하는 태그가 함수 안에서 직접적으로 작성되어 있다는 것
  public String render() throws Exception {
    StringBuffer html = new StringBuffer("<hr");
    if (size > 0) {
    	html.append(" size=\"")
  				.append(size + 1)
        	.append("\"");
    }
    html.append(">");
    
    return html.toString();
  }
  
  // good
  // hr, size 분리
  // 결론적으로 render() 메서드는 hr 태그를 html 형식으로 리턴하는 행위에 집중(생성과 size를 결정하는 로직은 별도 클래스나 메서드로 분리)
  public String render() throws Exception {
    HtmlTag hr = new HtmlTag("hr");  // HtmlTag 클래스로 hr 태그 (문법적) 생성 로직 분리
    if (extraDashes > 0) {
      hr.addAttribute("size", hrSize(extraDashes));  // hrSize 메서드로 사이즈 결정 로직 분리
    }
    
    return hr.html();
  }
  
  private String hrSize(int height) {
    int hrSize = height + 1;
    return String.format("%d", hrSize);
  }
### 위에서 아래로 코드 읽기: 내려가기 규칙

> 코드는 위에서 아래로 이야기처럼 읽혀야 좋다

## Switch문

```java
public Money calculatePay(Employee e) throws InvalidEmployeeType{
    switch (e.type){
        case COMMISSIONED:
            return calculateCommissionedPay(e);
        case HOURLY:
            return calculateHourlyPay(e);
        case SALARIED:
            return calculateSalariedPay(e);
        default:
            throw new InvalidEmployeeType(e.type);
    }
}
```

위 함수에서 몇 가지 문제가 있다.

1. 함수가 길다.

   - 새 직원 유형을 추가하면 더 길어지기 때문

2. `한 가지` 작업만 수행하지 않는다.

3. SRP(Single Responsibility Principle)를 위반한다.

   - 코드를 변경할 이유가 여럿이기 때문

4. OCP(Open Closed Principle)를 위반한다.

   - 새 직원 유형을 추가할 때마다 코드를 변경하기 때문

5. **위 함수와 구조가 동일한 함수가 무한정 존재한다는 사실**이다.

   ```java
   isPayday(Employee e, Date date);
   deliverPay(Employee e, Money pay);
   ...
   ```

   가능성은 무한하며, 모두가 똑같이 유해한 구조다.

```java
// 위 문제를 해결한 코드
public abstract class Employee {
    public abstract boolean isPayDay();
    public abstract Money calculatePay();
    public abstract void deliverPay(Money pay);    
}
//
public interface EmployeeFactory {
    public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType;
}
//
public class EmployeeFactoryImpl implements EmployeeFactory {
    public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
        switch (r.type) {
            case COMMISSIONED:
                return new CommissionedEmployee(r);
            case HOURLY:
                return new HourlyEmployee(r);
            case SALARIED:
                return new SalariedEmployee(r);
            default:
                throw new InvalidEmployeeType(r.type);
        }
    }
}
```

이렇게 상속 관계로 숨긴 후에는 절대로 다른 코드에 노출하지 않는다.

## 서술적인 이름을 사용하라!

> "코드를 읽으면서 짐작했던 기능을 각 루틴이 그대로 수행한다면 깨끗한 코드라 불러도 된다"

- 이름이 길어도 괜찮다.
- 길고 서술적인 이름이, 짧고 어려운 이름보다 좋다.
- 길고 서술적인 이름이, 길고 서술적인 주석보다 좋다.
- 함수 이름을 정할 때는 여러 단어가 쉽게 읽히는 명명법을 사용한다.
- 여러 단어를 사용해 함수 기능을 잘 표현하는 이름을 선택한다.

- 개발자 머릿속에서도 설계가 뚜렷해지므로 코드를 개선하기 쉬워진다

- 이름을 붙일 때는 일관성이 있어야 한다.

  - 모듈 내에서 함수 이름은 **같은 문구, 명사, 동사**를 사용한다.
    - **include**Setup**And**Teardown**Pages**
    - **include**Suite**Setup**Page
    - **include**Setup**Page**

  - 문체가 비슷하면 이야기를 순차적으로 풀어가기도 쉬워진다

## 함수 인수

> 이상적인 인수 개수는 0개(무항)다. 4개 이상(다항)은 가능한 피하는 편이 좋다.

`includeSetupPageInto(new PageContent)`보다 `includeSetupPage()`가 이해하기 더 쉽다.

만약, 인수가 여러개이고 테스트 케이스를 작성한다고 가정하자. 각 인수의 조합으로 함수를 검증해야 하므로 굉장히 번거로워진다.

### 많이 쓰는 단항 형식

- 인수에 질문을 던지는 경우
- 인수를 뭔가로 변환해 결과를 반환하는 경우

```java
fileExists("MyFile"); // 첫번째 경우
InputStream fileOpen("MyFile"); // 두번째 경우, String 형의 파일 이름을 InputStream으로 변환
```

### 플래그 인수

### 이항 함수

x,y 좌표 같은 경우 이항 함수가 적절할 수 있다.

```java
Point p = new Point(1,2);
```

반면 아래의 경우 이항 함수보다 단항 함수가 더 깔끔하다.

```java
writeField(name);
writeField(outputStream, name);
```



### 삼항 함수

### 인수객체

> 인수가 2-3개 필요하다면 클래스 변수로 선언할 것을 고려한다

```java
Circle makeCircle(double x, double y, double radius);
Circle makeCircle(Point center, double radius);
```

### 인수 목록

**인수 개수가 가변적인 함수**도 필요하다.

```java
String.format("%s worked %.2f" hours.", name, hours");
```

가변 인수 전부를 동등하게 취급하면 <u>List 형 인수 하나로 취급할 수 있다</u>. 

즉, String.format은 2개의 인수를 받는 `이항 함수`이다.

```java
public String format(String format, Object... args)
```

가변 인수를 취하는 모든 함수에 같은 원리가 적용된다.

### 동사와 키워드

```java
write(name);
writeField(name); // 이름(name)이 필드(field)라는 사실이 분명히 드러남
```

단항 함수는 함수와 인수가 동사/명사 쌍을 이뤄야 한다.



```java
assertEquals(expected, actual);
assertExpectedEqualsActual(expected, actual); // 함수 이름에 인수 이름이 있기에 인수 순서를 기억할 필요가 없어진다
```

함수 이름에 키워드를 추가하는 형식

## 부수 효과를 일으키지 마라

```java
// UserValidator.java
public class UserValidator {
    private Cryptographer cryptographer;
    
    public boolean checkPassword(String userName, String password) {
        User user = UserGateway.findByName(userName);
        if (user != User.NULL) {
            String codedPhrase = user.getPhraseEncodedByPassword();
            String phrase = cryptographer.decrypt(codedPhrase, password);
            if ("Valid Password".equals(phrase)) {
                Session.initialize();
                return true;
            }
        }
        return false;
    }
}
```

`userName`과 `password`를 확인하고 두 인수가 올바르면 true를 아니면 false를 반환하는 함수다.

하지만, 이 함수는`Session.initialize()` 호출을 하므로 부수 효과를 일으킨다.

**함수 이름만 봐서는 세션을 초기화한다는 사실이 드러나지 않는다**.

함수 이름만 보고 함수를 호출하는 사용자는 **사용자를 인증하면서 기존 세션 정보를 지워버릴 위험에 처한다**.

```java
public boolean checkPasswordAndInitializeSession(String userName, String password);
```

따라서 위와 같이 바꾸는 것이 좋다.

### 출력 인수

> 일반적으로 출력 인수는 피해야한다. 함수에서 상태를 변경해야 한다면 **함수가 속한 객체 상태를 변경하는 방식**을 택한다.

```java
// bad
public void appendFooter(StringBuffer report, String s) {}

// good
report.appendFooter(s);
```

위 함수를 보고 2가지를 유추할 수 있다.

1. 무언가에 `s`를 바닥글로 첨부한다
2. `s`에 바닥글을 첨부한다



```java
public void appendFooter(StringBuffer report);
```

인수 `s`가 출력 인수라는 사실은 분명하지만 함수 선언부를 찾아보고 나서야 알 수 있다.

따라서 다음과 같이 변경하는 것이 좋다.

```java
report.appendFooter();
```

## 명령과 조회를 분리하라!

> 함수는 뭔가를 수행하거나 뭔가에 답하거나 둘 중 하나만 해야 한다.

```java
public boolean set(String attribute, String value)...
```

이 함수는 이름이 attribute인 속성을 찾아 값을 value로 설정하고 성공하면 true, 실패하면 false를 반환한다.

```java
if(set("username","imksh"))...
```

하지만 위를 보았을 때는 2가지로 유추할 수 있다.

1. `username`을 `imksh`로 설정되어 있는지 확인하는 코드
2. `username`을 `imksh`로 설정하는 코드

if문 때문에 해석이 모호하게 되는데, `set`이라는 함수명을 다음으로 바꿔도 모호해진다.

```java
public boolean setAndCheckIfExists(String attribute, String value)...
    
if(setAndCheckIfExists("username", "imksh"))... // if 때문에 여전히 이상하다.
```

해결책은 **명령과 조회를 분리**해 혼란을 애초에 뿌리뽑는 방법이다.



```java
if(attributeExists("username")){
    setAttribute("username", "imksh");
    ...
}
```

## 오류 코드보다 예외를 사용하라!

> 정상 동작과 오류 처리 동작을 분리하면 코드를 이해하고 수정하기 쉬워진다.

```java
if (deletePage(page) == E_OK)... // 에러코드를 반환하고 이를 처리하는 방법
    
try{	// try-catch로 예외를 사용하는 방법
    deletePage(page);
}catch(Exception e){
    looger.log(e.getMessage());
}
```

### Try/Catch 블록 뽑아내기

> try/catch 블록을 별도 함수로 뽑아내자

```java
public void delete(Page page) { // 모든 오류를 처리하는 함수
    try {
        deletePageAndAllReferences(page);
    }catch (Exception e){
        logError(e);
    }
}

private void deletePageAndAllReferences(Page page) throws Exception { // 기능을 수행하는 함수
    deletePage(page);
	registry.deleteReference(page.name);
    configKeys.deleteKey(page.name.makeKey());
}
```

### Error.java 의존성 자석

> Error 대신 Exception을 사용하자

## 반복하지 마라!

> AOP(Aspect Oriented Programming), COP(Component Oriented Programming)처럼 중복 제거 전략을 사용하자

## 구조적 프로그래밍

- <u>함수가 크다면</u> 루프 안에서 break나 continue를 사용해선 안 된다.
- goto 문은 큰 함수에서만 의미가 있으므로 피하자

## 함수를 짜는 과정

- 처음에는 길고 복잡하다
- 단위 테스트 케이스를 만든다
- 다음을 수행한다
  - 코드를 다듬는다
  - 함수를 만든다
  - 이름을 바꾼다
  - 중복을 제거한다
  - 메서드를 줄이고 순서를 바꾼다
  - 때로는 전체 클래스를 쪼개기도 한다
- 위 단계를 거치는 와중에도 항상 단위 테스트를 통과한다