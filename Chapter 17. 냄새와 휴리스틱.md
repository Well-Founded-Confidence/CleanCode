# 냄새와 휴리스틱

>[C-주석](#C-주석)
>
>[E-환경](#E-환경)
>
>[F-함수](#F-함수)
>
>[G-일반](#G-일반)
>
>[J-자바](#J-자바)
>
>[N-이름](#N-이름)
>
>[T-테스트](#T-테스트)

<br>

## C-주석

##### C1: 부적절한 정보

- 다른 시스템(e.g. 소스 코드 관리 시스템, 버그 추적 시스템, 이슈 추적 시스템, 기타 기록 관리 시스템)에 저장될 정보는 주석으로 적절하지 못함
- 예를 들어, 변경 이력은 장황한 날짜와 내용으로 소스 코드만 번잡하게 만듦
- 작성자, 최종 수정일, SPR(Software Problem Report) 번호 등과 같은 메타 정보만 주석으로 넣음
- 주석은 코드와 설계에 기술적인 설명을 부연하는 수단

<br>

##### C2: 쓸모 없는 주석

- 오래된 주석, 엉뚱한 주석, 잘못된 주석은 쓸모 없음
- 쓸모 없어진 주석은 아예 달지 않거나 빨리 삭제해야 함
- 쓸모 없는 주석은 실제 코드와 멀어지고, 코드와 무관하게 혼자 따로 놀며 코드를 그릇된 방향으로 이끈다

<br>

##### C3: 중복된 주석

```java
i++  // i 증가
  
/**
 * @param sellRequest
 * @return
 * @throws ManagedComponentException
 */
public SellResponse beginSellItem(SellRequest sellRequest) throws ManagedComponentException {}
```

- 코드만으로 충분한데 구구절절 설명하는 주석이 중복된 주석
- 함수 서명(signature)만 달랑 기술하는 Javadoc

<br>

##### C4: 성의 없는 주석

- 작성할 가치가 있는 주석은 잘 작성할 가치도 있다!
- 단어를 신중하게 선택하고, 문법과 구두점을 올바로 사용하고 주절대지 않아야 함. 당연한 소리를 반복하지 않고 간결하고 명료하게 작성해야 함

<br>

##### C5: 주석 처리된 코드

- 주석 처리된 코드는 흉물
- 주석 처리된 코드는 매일 낡아가고 더 이상 존재하지 않는 함수를 호출함. 자신이 포함된 모듈을 오염시키고 읽는 사람을 헷갈리게 만듦
- 주석으로 처리된 코드는 즉각 삭제하고, 소스 코드 관리 시스템을 참조하라

<br>

## E-환경

##### E1: 여러 단계로 빌드한다

```bash
svn get mySystem
cd mySystem
ant all
```

- 빌드는 간단히 한 단계로 끝나야 함
- 소스 코드 관리 시스템에서 이것저것 따로 체크아웃 할 필요가 없어야 함
- 불가해한 명령이나 스크립트를 잇달아 실행해 각 요소를 따로 빌드할 필요가 없어야 함
- 온갖 JAR 파일, XML 파일, 기타 시스템에 필요한 파일을 찾느라 여기저기 뒤적일 필요가 없어야 함

<br>

##### E2: 여러 단계로 테스트한다

- 모든 단위 테스트는 한 명령으로 돌려야 함(IDE에서 버튼 하나로 모든 테스트를 실행하는 것이 가장 이상적)

<br>

## F-함수

##### F1: 너무 많은 인수

- 함수에서 인수 개수는 적을수록 좋음
- 인수 0개 -> 1개 -> 2개 -> 3개 까지는 괜찮고, 넷 이상은 최대한 피하라

<br>

##### F2: 출력 인수

- 출력 인수는 직관을 정면으로 위배한다
- 독자는 인수를 (출력이 아니라) 입력으로 간주한다
- 함수에서 뭔가의 상태를 변경해야 한다면 (출력 인수를 쓰지 말고) 함수가 속한 객체의 상태를 변경한다

<br>

##### F3: 플래그 인수

- boolean 인수는 함수가 여러 기능을 수행한다는 명백한 증거이므로 피해야 함

<br>

##### F4: 죽은 함수

- 아무도 호출하지 않는 함수는 삭제하라(죽은 코드는 낭비다!)
- 소스 코드 관리 시스템이 모두 기억하므로 걱정할 필요가 없음

<br>

## G-일반

##### G1: 한 소스 파일에 여러 언어를 사용한다

- 한 소스 파일 내에서 다양한 언어(XML, HTML, YAML, Javadoc, JavaScript 등)를 포함하는데, 이는 혼란스럽고 조잡하다
- 소스 파일 하나에 언어 하나만 사용하는 방식이 좋음
- 현실적으로 여러 언어가 불가피하나, 소스 파일에서 언어 수와 범위를 최대한 줄이도록 애써야 함

<br>

##### G2: 당연한 동작을 구현하지 않는다

- The Principle of Least Surprise(최소 놀람의 원칙)에 의거해 함수나 클래스는 다른 프로그래머가 당연하게 여길 만한 동작과 기능을 제공해야 함

- ```java
  Day day = DayDate.StringToDay(String dayName)
  ```

  - 위 코드에서 'Monday'를 Day.MONDAY로 변환하리라, 일반적으로 쓰는 요일 약어도 올바로 변환하리라, 대소문자는 당연히 구분하지 않으리라 기대함

- 당연한 동작을 구현하지 않으면 코드를 읽거나 사용하는 사람이 더 이상 함수 이름만으로 함수 기능을 직관적으로 예상하기 어려움

- 당연한 동작을 구현하지 않으면 저자를 신뢰하지 못하므로 코드를 일일이 살펴야 함

<br>

##### G3: 경계를 올바로 처리하지 않는다

- 직관에 의존하려 하지 마라
- 모든 경계 조건을 테스트하는 테스트 케이스를 작성하라

<br>

##### G4: 안전 절차 무시

- 컴파일러 경고 일부를 꺼버리면 빌드가 쉬워질지 모르나 자칫하면 끝없는 디버깅에 시달리게 됨
- 실패하는 테스트 케이스를 일단 제껴두고 나중으로 미루는 태도는 위험하다

<br>

##### G5: 중복

- DRY(Don't Repeat Yourself) - 데이비드 토머스, 앤디 헌트
- 익스트림 프로그래밍 '한 번, 단 한 번만(Once, and only once)' - 켄트 백
- 코드에서 중복을 발견할 때마다 추상화할 기회로 간주하라
- 중복된 코드를 하위 루틴이나 다른 클래스로 분리하라
- 추상화 수준을 높이면 구현이 빨라지고 오류가 적어진다
- 여러 모듈에서 일련의 switch/case나 if/else 문으로 똑같은 조건을 거듭 확인하는 중복을 피하라
  - 다형성(polymorphism)으로 대체하라
- 알고리즘이 유사하나 코드가 서로 다른 중복을 피하라
  - template method pattern, strategy pattern 으로 중복을 제거하라

<br>

##### G6: 추상화 수준이 올바르지 못하다

- 추상화는 저차원 상세 개념에서 고차원 일반 개념을 분리한다

- 세부 구현과 관련된 상수, 변수, 유틸리티 함수는 기초 클래스에 넣으면 안된다(기초 클래스는 구현 정보에 무지해야 마땅하다)

- 예시

  ```java
  // bad
  public interface Stack {
      Object pop() throws EmptyException;
      void push(Object o) throws FullException;
      double percentFull();
      class EmptyException extends Exception {}
      class FullException extends Exception {}
  }
  
  // good
  public interface Stack {
      Object pop() throws EmptyException;
      void push(Object o) throws FullException;
      class EmptyException extends Exception {}
      class FullException extends Exception {}
  }
  
  public interface BoundedStack implements Stack {
      double percentFull();
  }
  ```

  - percentFull() 메서드는 추상화 수준이 올바르지 않는데, 파생 인터페이스에 구체적인 구현을 넣는 방법을 고려해야 함

<br>

##### G7: 기초 클래스가 파생 클래스에 의존한다

- 기초 클래스와 파생 클래스로 나누는 가장 흔한 이유는
  - 고차원 기초 클래스 개념을 저차원 파생 클래스 개념으로 분리해 독립성을 보장하기 위함
- 따라서 기초 클래스가 파생 클래스를 사용한다면 문제가 있는 것이고, 기초 클래스는 파생 클래스를 아예 몰라야 한다
- 일부 예외가 있기는 하지만, 변경이 시스템에 미치는 영향을 적게 하여 유지보수를 수월하게 하라

<br>

##### G8: 과도한 정보

- 잘 정의된 모듈은 인터페이스가 아주 작다(하지만 작은 인터페이스로도 많은 동작이 가능하다!)
- 잘 정의된 인터페이스는 많은 함수를 제공하지 않아 결합도(coupling)가 낮음
- 클래스나 모듈 인터페이스에 노출할 함수를 제한할 줄 알아야 함
  - 클래스가 제공하는 메서드 수는 적을수록 좋음
  - 메서드가 아는 변수 수도 적을수록 좋음
  - 클래스에 들어있는 인스턴스 변수 수도 적을수록 좋음

- 자료, 유틸리티 함수, 상수, 임시 변수를 숨겨라
- 하위 클래스에서 필요하다는 이유로 protected 변수나 함수를 마구 생성하지 마라

<br>

##### G9: 죽은 코드

- 죽은 코드란 실행되지 않는 코드
- 예시
  - 불가능한 조건을 확인하는 if문
  - throw 문이 없는 try문의 catch 블록
  - 아무도 호출하지 않는 유틸리티 함수
  - switch/case 문에서 불가능한 case 조건
- 죽은 코드는 악취를 풍기는데, 이는 설계가 변해도 제대로 수정되지 않기 때문
- 컴파일은 되지만 새로운 규칙이나 표기법을 따르지 않음
- 적절한 장례식을 치뤄주어라

<br>

##### G10: 수직 분리

- 변수와 함수는 사용되는 위치에 가깝게 정의하라
- 예시
  - 지역 변수는 처음으로 사용하기 직전에 선언하며 수직으로 가까운 곳에 위치시켜라
  - 비공개 함수는 처음으로 호출한 직후에 정의

<br>

##### G11: 일관성 부족

- 어떤 개념을 특정 방식으로 구현했다면 유사한 개념도 같은 방식으로 구현하라
- 예시
  - 한 함수에서 response라는 변수에 HttpServletResponse 인스턴스를 저장했다면 (HttpServletResponse 객체를 사용하는) 다른 함수에서도 일관성 있게 동일한 변수 이름을 사용하라
  - 한 메서드를 processVerificationRequest라 명명했다면 (유사한 요청을 처리하는) 다른 메서드도 (processDeletionRequest처럼) 유사한 이름을 사용하라

<br>

##### G12: 잡동사니

- 비어 있는 기본 생성자, 사용하지 않는 변수/함수, 정보 제공하지 못하는 주석 등을 피하라

<br>

##### G13: 인위적 결합

- 서로 무관한 개념을 인위적으로 결합하지 마라
- 예시
  - 일반적인 enum은 특정 클래스에 속할 이유가 없음(enum이 클래스에 속한다면 enum을 사용하는 코드가 특정 클래스를 알아야만 함)
  - 범용 static 함수도 특정 클래스에 속할 이유가 없음
- 인위적인 결합은 직접적인 상호작용이 없는 두 모듈 사이에서 일어남
  - 뚜렷한 목적 없이 변수, 상수, 함수를 당장 편한 위치에 넣어버린 결과임
  - 이는 게으르고 부주의한 행동

<br>

##### G14: 기능 욕심

- 클래스 메서드는 자기 클래스의 변수와 함수에 관심을 가져야지 다른 클래스의 변수와 함수에 관심을 가져서는 안된다
- 메서드가 다른 객체의 참조자(accessor)와 변경자(mutator)를 사용해 그 객체 내용을 조작한다면 메서드가 그 객체 클래스의 범위를 욕심내는 탓

- 예시

  ```java
  public class HourlyPayCalculator {
      public Money calculateWeeklyPay(HourlyEmployee e) {
          int tenthRate = e.getTenthRate().getPennies();
          int tenthsWorked = e.getTenthsWorked();
          int straightTime = Math.min(400, tenthsWorked);
          int overTime = Math.max(0, tenthsWorked - straightTime);
          int straightPay = straightTime * tenthRate;
          int overtimePay = (int)Math.round(overTime * tenthRate * 1.5);
          
          return new Money/(straightPay + overtimePay);
      }
  }
  ```

  - calculateWeeklyPay() 메서드는 HourlyEmployee 객체에서 온갖 정보를 가져옴
  - 즉, calculateWeeklyPay() 메서드는 HourlyEmployee 클래스의 범위를 욕심내는 것
  - 기능 욕심은 한 클래스의 속사정을 다른 클래스에 노출하므로, 별다른 문제가 없다면 제거하는 편이 좋음(예외적인 케이스가 아래처럼 있기도 함)

  ```java
  public class HourlyEmployeeReport {
      private HourlyEmployee employee;
  
      public HourlyEmployeeReport(HourlyEmployee e) {
          this.employee = e;
      }
  
      String reportHours() {
          return String.format(
              "Name: %s\tHours:%d.%1d\n",
              employee.getName(),
              employee.getTenthWorked() / 10,
              employee.getTenthWorked() % 10
          );
      }
  }
  ```

  - reportHours 메서드는 HourlyEmployee 클래스를 욕심내지만, HourlyEmployee 클래스가 보고서 형식을 알 필요는 없음
  - 함수를 HourlyEmployee 클래스로 옮기면 객체 지향 설계의 여러 원칙을 위배함(SRP, OCP, CCP(Common Closure Principle))
  - 즉, HourlyEmployee가 보고서 형식과 결합되므로 보고서 형식이 바뀌면 클래스도 바뀌게 됨

<br>

##### G15: 선택자 인수

```java
// bad
public int calculateWeeklyPay(boolean overtime) {
    int tenthRate = getTenthRate();
    int tenthsWorked = getTenthsWorked();
    int straightTime = Math.min(400, tenthsWorked);
    int overTime = Math.max(0, tenthsWorked - straightTime);
    int straightPay = straightTime * tenthRate;
    double overtimeRate = overtime ? 1.5 : 1.0 * tenthRate;
    int overtimePay = (int)Math.round(overTime * overtimeRate);
    
    return straightPay + overtimePay;
}

// good
public int straightPay() {
    return getTenthsWorked() * getTenthRate();
}

public int overTimePay() {
    int overTimeTenths = Math.max(0, getTenthsWorked() - 400);
    int overTimePay = overTimeBonus(overTimeTenths);
    
    return straightPay() + overTimePay;
}

private int overTimeBonus(int overTimeTenths) {
    double bonus = 0.5 * getTenthRate() * overTimeTenths;
    
    return (int) Math.round(bonus);
}
```

- 초과근무 수당을 1.5배로 지급하면 true 아니면 false 인데, 메서드를 분리하여 좋은 코드로 리팩터링 가능
- bool 인수 뿐만이 아니라 enum, int 등 함수 동작을 제어하려는 인수는 바람직하지 않음
- 일반적으로 인수를 넘겨 동작을 선택하는 대신 새로운 함수를 만드는 편이 좋음

<br>

##### G16: 모호한 의도

```java
// bad
public int m_otCalc() {
    return iThsWkd * iThsRte +
        (int) Math.round(0.5 * iThsRte *
            Math.max(0, iThswKd - 400)
        );
}

// good -> G15 good case code 참조
```

- 행을 바꾸지 않고 표현한 수식, 헝가리식 표기법, 매직 번호는 저자의 의도를 흐림
- 독자에게 의도를 분명히 표현하도록 해야 함

<br>

##### G17: 잘못 지운 책임??????????????????

- 코드를 배치하는 위치를 잘 정해야 함
- 예시
  - PI 상수는 삼각함수를 선언한 클래스에 선언
  - OVERTIME_RATE 상수는 HourlyPayCalculator 클래스에 선언

- 때로는 개발자가 편한 함수에 배치하기도 함

  ```java
  class Report {
    public int getTotalHours() {}
  }
  
  class Employee {
    
    TimeCard timeCard;
    
    Employee() {
      timeCard = new TimeCard();
    }
  }
  
  class TimeCard {
      int runningTotalHours;
      // 근무시간, 휴식시간 등등..
      
      public void computeRunningTotalOfHours() {
        // 결과값 = calculate();
        this.runningTotalHours = '결과값';
      }
    }
  ```

  - 보고서를 출력하는 함수에서 총계 계산 vs 근무 시간을 입력 받는 코드에서 총계를 계산하여 보관

<br>

##### G:18: 부적절한 static 함수

- 좋은 static 함수

  ```java
  public class Math {
    public static double max(double a, double) {}
  }
  ```

  - 특정 인스턴스와 관련된 기능이 아님
  - 메서드를 소유하는 객체에서 가져오는 정보도 아님
  - Math.max 메서드를 재정의할 가능성이 전혀 없음

- 나쁜 static 함수

  ```java
  // bad
  public class HourlyPayCalculator {
    public static void calculatePay(Employee employee, double overtimeRate) {}
  }
  
  // good
  public class Employee {
    public void calculatePay() {}
  }
  ```

  - 특정 객체와 관련이 없으면서 모든 정보를 인수에서 가져오므로 static으로 만들어도 될 것처럼 보이지만

  - 다음의 cases가 있을 수 있음

    - 함수를 재정의할 가능성이 존재하거나 수당 계산 알고리즘이 여러개일수도 있음

    - 예를 들어 OvertimeHourlyPayCalculator와 StraightTimeHourlyPayCalculator를 분리하고 싶을 수도 있음

  - 따라서 Employee 클래스에 속하는 인스턴스 함수여야 함

<br>

##### G19: 서술적 변수

```java
Matcher match = headerPattern.matcher(line);
if (match.find())
{
  // 서술적 변수 key & value
  String key = match.group(1);
  String value = match.group(2);
  headers.put(key.toLowerCase(), value);
}
```

- 켄트백의 저서에 따르면 프로그램 가독성을 높이는 가장 효과적 방법 중 하나는 계산을 여러 단계로 나누고 중간 값으로 서술적인 변수 이름을 사용하는 방법
- 서술적 변수 이름은 일반적으로 더 많을수록 좋아서, 좋은 변수 이름만 붙여도 해독하기 어려운 모듈이 읽기 쉬운 모듈로 탈바꿈함

<br>

##### G20: 이름과 기능이 일치하는 함수

```java
// bad
Date newDate = date.add(5);

// good
addDaysTo(5);
increaseByDays(5);
daysLater(5);
daysSince(5);
```

- date에 5일/5주/5시간 뭘 더해야 할지 알 수 없음

- 이름만으로 기능을 분명히 표현해야 함

<br>

##### G21: 알고리즘을 이해하라

- 문제점
  - 대다수 괴상한 코드는 알고리즘을 충분히 이해하지 않은 채 코드를 구현해서, 여기저기 if문과 flag를 넣어보며 코드를 돌리는 짓을 함
- 테스트케이스를 모두 통과하는 '돌아가는 코드'가 틀렸다는 것이 아니라, 함수가 돌아가는 방식을 확실히 이해하는지 확인하는 것이 중요
- 테스트케이스를 모두 통과한다는 사실만으로는 부족하고, 작성자가 알고리즘이 올바르다는 사실을 알아야 함(그렇지 않은건 게으름의 소치)

<br>

##### G22: 논리적 의존성은 물리적으로 드러내라

- 한 모듈이 다른 모듈에 의존한다면 물리적 의존성도 있어야 함(논리적 의존성만으로는 부족)

- 의존하는 모듈이 상대 모듈에 대해 뭔가를 가정하면(즉, 논리적으로 의존하면) 안되고, 의존하는 정보를 명시적으로 요청하는 편이 좋음

- 예시

  ```java
  // bad
  public class HourlyReporter {
      private HourlyReportFormatter formatter;
      private List<LineItem> page;
      private final int PAGE_SIZE = 55;
  
      public HourlyReporter(HourlyReportFormatter formatter) {
          this.formatter = formatter;
          page = new ArrayList<LineItem>();
      }
  
      public void generateReport(List<hourlyEmployee> employees) {
          for (HourlyEmployee e : employees) {
              addLineItemToPage(e);
              if (page.size() == PAGE_SIZE) {
                  printAndClearItemList();
              }
          }
  
          if (page.size() > 0) {
              printAndClearItemList();
          }
      }
  
      private void printAndClearItemList() {
          formatter.format(page);
          page.clear();
      }
  
      private void addLineItemToPage(HourlyEmployee e) {
          LineItem item = new LineItem();
          item.name = e.getName();
          item.hours = e.getTenthsWorked() / 10;
          item.tenths = e.getTenthsWorked() % 10;
          page.add(item);
      }
      
      public class LineItem {
          public String name;
          public int hours;
          public int tenths
      }
  }
  ```

  - 위 코드는 PAGE_SIZE 상수로 인해 논리적 의존성이 존재함 >> 잘못지운 책임(G17)
  - HourlyReporter 클래스는 HourlyReportFormatter가 페이지 크기를 알 것이라고 가정하는데, 이것이 바로 논리적 의존성
  - 즉, HourlyReportFormatter가 MAX_PAGE_SIZE를 가지고 있어서, HourlyReporter는 formatter.getMaxPageSize()를 호출하게 해야 함

<br>

##### G23: if/else 혹은 switch/case 문보다 다형성을 사용하라

- 문제점
  - switch문을 선택하는 이유는 그 상황에서 올바른 선택이라기 보다는 가장 손쉬운 선택이기 때문
  - 유형보다 함수가 더 쉽게 변하는 경우는 극히 드물기 때문에 모든 switch문을 의심해야 함
- 'switch 문 하나' 규칙
  - 선택 유형 하나에 switch 문을 한 번만 사용
  - 같은 선택을 수행하는 다른 코드에서는 다형성 객체를 생성해 switch문을 대신함

<br>

##### G24: 표준 표기법을 따르라

- 팀은 업계 표준에 기반한 구현 표준을 따라야 함
- 구현 표준
  - 인스턴스 변수 이름 선언 위치
  - 클래스/메서드/변수 네이밍
  - 괄호를 넣는 위치 등
- 코드가 구현 표준이 되게 해야 한다(문서를 만들 필요도 없게)
- 팀원들이 코드로 표준을 이해할 정도로 성숙해야 함

<br>

##### G25: 매직 숫자는 명명된 상수로 교체하라

- 일반적으로 코드에서 숫자를 사용하지 말라는 규칙(숫자는 명명된 상수 뒤로 숨겨라)

  ```java
  private final int SECONDS_PER_DAY = 86_400;
  private final int LINES_PER_PAGE = 55;
  ```

- 매직 숫자라는 용어가 단지 숫자만 의미하지 않고, 의미가 분명하지 않은 토큰을 모두 가리킴

  ```java
  // 매직숫자: 7777, "John Doe"
  assertEquals(7777, Employee.find("John Doe").employeeNumber());
  
  // 매직숫자를 상수로 교체
  int HOURLY_EMPLOYEE_ID = 7777;
  String HOURLY_EMPLOYEE_NAME = "John Doe";
  assertEquals(HOURLY_EMPLOYEE_ID, Employee.find(HOURLY_EMPLOYEE_NAME).employeeNumber());
  ```

- 너무 당연한 것은 안 써도 됨

  ```java
  // bad
  int TWO = 2;
  double circumference = radius * Math.PI * TWO;
  ```

<br>

##### G26: 정확하라

- 문제점

  - 검색 결과 중 첫 번째 결과만 유일한 결과로 간주하는 행동은 순진한 것
  - 부동소수점으로 통화를 표현하는 행동은 거의 범죄에 가까움(오차가 생김)

  - 갱신할 가능성이 희박하다고 Lock, transaction 관리를 건너뛰는 행동은 게으른 것
  - List로 선언할 변수를 ArrayList로 선언하는 것은 지나친 제약
  - 모든 변수를 protected로 선언한 코드는 무절제함

- 개선

  - 결정을 내리는 이유와 예외를 처리하는 방법을 대충 결정하지 않고 분명히 알아야 함
  - 예시
    - 호출 함수가 null을 반환할지도 모르면 반드시 null을 점검
    - 조회 결과가 하나뿐이라 짐작한다면 하나인지 확실히 확인
    - 통화를 다뤄야 한다면 정수를 사용하고 반올림을 올바로 처리
    - concurrent 특성으로 인해 동시 갱신 가능성이 있다면 적절한 트랜잭션 처리해야 함

<br>

##### G27: 관례보다 구조를 사용하라

- 설계 시에 명명 규칙을 세운다면 관례도 좋지만 구조 자체로 강제하는 편이 더 좋음

- 예시

  - 추상 메서드가 있는 기초 클래스가 enum 변수를 잘 지은 switch 문보다 좋음

    (switch문을 매번 똑같이 구현하게 강제할 수는 없어도, 파생 클래스는 추상 메서드를 모두 구현하지 않으면 안되기 때문)

<br>

##### G28: 조건을 캡슐화하라

```java
// bad
if (shouldBeDeleted(timer)) {}

// good
if (timer.hasExpired() && !timer.isRecurrent()) {}
```

- 조건의 의도를 분명히 밝히는 함수로 표현하라

<br>

##### G29: 부정 조건은 피하라

```java
// bad
if (!buffer.shouldNotCompact()) {}

// good
if (buffer.shouldCompact()) {}
```

- 가능하면 긍정 조건으로 표현하라

<br>

##### G30: 함수는 한 가지만 해야 한다

```java
// bad
public void pay() {
    for (Employee e : employees) {
        if (e.isPayday()) {
            Money pay = e.calculatePay();
            e.deliverPay(pay);
        }
    }
}

// good
public void pay() {
    for (Employee e : employees) {
        payIfNecessary(e);
    }
}

private void payIfNecessary(Employee e) {
    if (e.isPayday()) {
        calculateAndDeliverPay(e);
    }
}

private void calculateAndDeliverPay(Employee e) {
    Money pay = e.calculatePay();
    e.deliverPay(pay);
}
```

- 직원 목록 루프, 각 직원 월급일 확인, 해당 직원에게 월급 지급 3가지 일을 분리해야 함

<br>

##### G31: 숨겨진 시간적인 결합

- 때로는 시간적인 결합이 필요하지만, 시간적인 결합을 숨겨서는 안됨

- 함수를 짤 때는 함수 인수를 적절히 배치해 함수가 호출되는 순서를 명백히 드러내야 함

- 예시

  ```java
  public class MoogDiver {
      Gradient gradient;
      List<Spline> splines;
  
      public void dive(String reason) {
          saturateGradient();
          reticulateSplines();
          diveForMoog(reason);
      }
      ...
  }
  
  public class MoogDiver {
      Gradient gradient;
      List<Spline> splines;
  
      public void dive(String reason) {
          Gradient gradient = saturateGradient();
          List<Spline> splines = reticulateSplines(gradient);
          diveForMoog(splines, reason);
      }
  }
  ```

  - 세 함수가 실행되는 순서가 중요한 상황
  - 시간적 순서에 따라 인자가 필요하게 만들어 시간적 결합을 명백히 드러내게 함

<br>

##### G32: 일관성을 유지하라

- 코드 구조에 일관성이 없다면 남들이 맘대로 바꿔도 괜찮다고 생각할 수 있음
- 시스템 전반에 걸쳐 구조가 일관성이 있다면 남들도 일관성을 따르고 보존함

<br>

##### G33: 경계 조건을 캡슐화하라

```java
// bad
if (level + 1 < tags.length) 
{
    parts = new Parse(body, tags, level + 1, offset + endTag);
    body = null;
}

// good
int nextLevel = level + 1;
if (nextLevel < tags.length)
{
    parts = new Parse(body, tags, nextLevel, offset + endTag);
    body = null;
}
```

- 경계 조건은 코드 여기저기서 처리하지 않고 한 곳에서 별도로 처리해야 함

<br>

##### G34: 함수는 추상화 수준을 한 단계만 내려가야 한다

- 함수 내 모든 문장은 추상화 수준이 동일해야 하고, 추상화 수준은 함수 이름이 의미하는 작업보다 한 단계만 낮아야 한다!

- 예시

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
  ```

<br>

##### G35: 설정 정보는 최상위 단계에 둬라

- 기본값 상수나 설정 관련 상수는 추상화 최상위 함수에 명시하라

  (그리고 고차원 함수에서 저차원 함수를 호출할 때 인수로 넘겨라)

- 예시

  ```java
  public static void main(String[] args) throws Exception {
      Arguments arguments = parseCommandLine(args);
  }
  
  public class Arguments {
      public static final String DEFAULT_PATH = ".";
      public static final String DEFAULT_ROOT = "FitNesseRoot";
      public static final int DEFAULT_PORT = 80;
      public static final int DEFAULT_VERSION_DAYS = 14;
      // ...
  }
  ```

<br>

##### G36: 추이적 탐색을 피하라

- 일반적으로 한 모듈은 주변 모듈을 모를수록 좋다(디미터 법칙, Law of Demeter)
  - A가 B를 사용하고 B가 C를 사용한다 하더라도 A가 C를 알아야 할 필요는 없다
  - `a.getB().getC().doSomething();`은 바람직하지 않음
  - 6장 객체와 자료구조 디미터 법칙 참조

<br>

## J-자바

##### J1: 긴 import 목록을 피하고 와일드카드를 사용하라

```java
import package.*;
```

- 패키지에서 클래스를 둘 이상 사용한다면 와일드카드를 사용해 패키지 전체를 가져오라

- 와일드카드를 사용하면 모듈 간에 결합성이 낮아진다

<br>

##### J2: 상수는 상속하지 않는다

```java
// bad
public class HourlyEmployee extends Employee {
    private int tenthsWorked;
    private double hourlyRate;
    
    public Money calculatePay() {
        int straightTime = Math.min(tenthsWorked, TENTHS_PER_WEEK);
        int overTime = tenthsWokred - straightTime;
        return new Money(hourlyRate * (tenthsWorked + OVERTIME_RATE * overTime));
    }
}

// good
import static PayrollConstants.*;
```

- TENTHS_PER_WEEK, OVERTIME_RATE 상수는 Employee 클래스에서 상속한 것인데, 이는 매우 나쁘고 언어의 범위 규칙을 속이는 행위다
- 대신 static import를 사용하라

<br>

##### J3: 상수 vs Enum

- 상수 대신 Enum을 많이 활용하라

- enum은 메서드와 필드도 사용할 수 있어 유연하고 서술적인 강력한 도구다

  ```java
  public enum HourlyPayGrade {
      APPRENTICE {
          public double rate() {
              return 1.0;
          }
      },
      LIEUTENANT_JOURNEYMAN {
          public double rate() {
              return 1.2;
          }
      },
      JOURNEYMAN {
          public double rate() {
              return 1.5;
          }
      },
      MASTER {
          public double rate() {
              return 2.0;
          }
      };
  
      public abstract double rate();
  }
  ```

<br>

## N-이름

##### N1: 서술적인 이름을 사용하라

- 소프트웨어 가독성의 90%는 이름이 결정한다. 시간을 들여 좋은 이름을 선택하고 유효한 상태로 유지하라
- 의미 없는 변수를 사용하지 말고 서술적인 변수를 사용하라(p.400 코드 참조)
- 즉, 이름을 보고 해당 코드가 하는 일을 짐작할 수 있게 하라

<br>

##### N2: 적절한 추상화 수준에서 이름을 선택하라

- 작업 대상 클래스나 함수가 위치하는 추상화 수준을 반영하는 이름을 선택하라

  (구현을 드러내는 이름은 피하라)

- 예시

  ```java
  // bad
  public interface Modem {
      boolean dial(String phoneNumber);
      boolean disconnect();
      boolean send(char c);
      char recv();
      String getConnectedPhoneNumber();
  }
  
  // good
  public interface Modem {
      boolean connect(String connectionLocator);
      boolean disconnect();
      boolean send(char c);
      char recv();
      String getConnectedLocator();
  }
  ```

  - phoneNumber가 너무 구체적이어서 connectionLocator를 사용하는 등 좋은 네이밍을 통해 구현하면, 추후 전화번호, 포트 번호 등 여러 종류가 들어갈 수 있음

<br>

##### N3: 가능하다면 표준 명명법을 사용하라

- 기존 명명법을 사용하는 이름은 이해하기 더 쉬움
- 예시
  - DECORATOR 패턴을 활용한다면 클래스 이름에 Decorator 단어 사용
  - 자바에서 객체를 문자열 반환 시에 toString() 많이 쓰는데 관례 따르는 편이 좋음
  - 프로젝트에 유효한 의미가 담긴 이름을 사용(ubiquitous language)

<br>

##### N4: 명확한 이름

- 함수나 변수의 목적을 명확히 밝히는 이름을 선택하라

- 예시

  ```java
  // bad
  private String doRename() throws Exception {
      if (refactorReferences) {
          renameReferences();
      }
      renamePage();
  
      pathToRename.removeNameFromEnd();
      pathToRename.addNameToEnd(newName);
      
      return PathParser.render(pathToRename);
  }
  
  // good
  private String renamePageAndOptionallyAllReferences() {}
  ```

  - 이름만 봐서는 함수가 하는 일이 분명치 않고 모호함
  - 길지만 서술적인 네이밍을 하라

<br>

##### N5: 긴 범위는 긴 이름을 사용하라

- 이름 길이는 범위 길이에 비례해야 함

- 예시

  ```java
  private void rollMany(int n, int pins) {
      for (int i = 0; i < n; i++) {
          g.roll(pins);
      }
  }
  ```

  - 범위가 5줄 안팎이라면 i,j 변수 이름도 괜찮음
  - 오히려 i를 rollCount 라고 했다면 더 헷갈림

<br>

##### N6: 인코딩을 피하라

- 이름에 유형 정보나 범위 정보를 넣어서는 안됨
  - m_, f, I 접두어 등 불필요
- 헝가리안 표기법을 피하라

<br>

##### N7: 이름으로 부수 효과를 설명하라

- 함수, 변수, 클래스가 하는 일을 모두 기술하는 이름을 사용하라

- 여러 작업을 수행하는 함수에 동사 하나만 사용하지 말고, 모두 기술하라

  ```java
  // bad
  public ObjectOutputStream getOos() throws IOException {
      if (m_oos == null) {
          m_oos = new ObjectOutputStream((m_socket.getOutputStream()));
      }
      return m_oos;
  }
  
  // good
  public ObjectOutputStream createOrReturnOos() {}
  ```

<br>

## T-테스트

##### T1: 불충분한 테스트

- 테스트케이스는 잠재적으로 오류가 날만한 부분은 모두 테스트해야 함
- 테스트케이스가 확인하지 않는 조건이나 검증하지 않는 계산이 있다면 그 테스트는 불완전
- 테스트 케이스는 몇 개나 만들어야 충분할까? -> 갯수가 중요한게 아님

<br>

##### T2: 커버리지 도구를 사용하라!

- IDE에서 시각적 효과를 제공하는 커버리지 도구 등을 사용하라

- 테스트되는 행은 녹색으로, 아닌 것은 붉은색으로 표시되는 등 불충분한 모듈, 클래스, 함수를 찾기 쉬워짐

<br>

##### T3: 사소한 테스트를 건너뛰지 마라

- 사소한 테스트는 짜기 쉬운데 가성비 갓

<br>

##### T4: 무시한 테스트는 모호함을 뜻한다

- 불분명한 요구사항은 테스트 케이스를 주석으로 처리하거나 테스트케이스에 @Ignore를 붙여 표현
- 주석처리나 @Ignore 하는 기준은 모호함이 존재하는 테스트 케이스가 컴파일이 가능한지에 따라 다름

<br>

##### T5: 경계 조건을 테스트하라

- 경계 조건은 각별히 신경 써서 테스트하라
- 알고리즘 중앙 조건은 잘 짜고 경계 조건에서 실수하는 경우가 흔하다

<br>

##### T6: 버그 주변은 철저히 테스트하라

- 버그는 서로 모이는 경향이 있다
- 한 함수에서 버그를 발견했다면 그 함수를 철저히 테스트하는 편이 좋다

<br>

##### T7: 실패 패턴을 살펴라

- 때로는 테스트 케이스가 실패하는 패턴으로 문제를 진단할 수 있다
- 합리적 순서로 정렬된 꼼꼼한 테스트 케이스는 실패 패턴을 드러낸다
- 예시
  - 입력이 자를 넘기는 케이스가 모두 실패하거나, 함수 둘째 인수로 음수를 넘기는 케이스가 실패하면 실패 테스트케이스를 통해서 패턴 파악이 가능함

<br>

##### T8: 테스트 커버리지 패턴을 살펴라

- 통과하는 테스트가 실행하거나 실행하지 않는 코드를 살펴보면 실패하는 테스트케이스의 실패 원인이 드러남

<br>

##### T9: 테스트는 빨라야 한다

- 느린 테스트케이스는 실행하고 싶지 않게 되고, 코드가 망가지게 됨

