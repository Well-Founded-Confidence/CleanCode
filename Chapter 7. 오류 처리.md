# Chapter 7. 오류 처리

- [Chapter 7. 오류 처리](#chapter-7-오류-처리)
  - [Try-Catch-Finally 문부터 작성하라](#try-catch-finally-문부터-작성하라)
  - [Unchecked 예외를 사용하라](#unchecked-예외를-사용하라)
  - [예외에 의미를 제공하라](#예외에-의미를-제공하라)
  - [호출자를 고려해 예외 클래스를 정의하라](#호출자를-고려해-예외-클래스를-정의하라)
  - [정상 흐름을 정의하라](#정상-흐름을-정의하라)
  - [null을 반환하지 마라](#null을-반환하지-마라)
  - [null을 전달하지 마라](#null을-전달하지-마라)
  - [결론](#결론)

---

## Try-Catch-Finally 문부터 작성하라

```java
private static void tryTest() {
    String test="";
    try {
        test = "트라이 들어옴";
        throw new Exception();
    }catch(Exception e) {
        System.out.println("Catch문 들어옴");
    }finally {
        System.out.println(test);
    }
}
```

`test` 변수의 출력 결과는 어떨까? `""`일까 `"트라이 들어옴"` 일까?

`try`는 `트랜잭션`과 다르지만 비슷한 면이 있다.
</br>
</br>

## Unchecked 예외를 사용하라

```java
public void depth1(){
    try{
	    depth2();
    }catch(PermissionDeniedException e){
        e.printStackTrace();
    }
}

private static void depth2() throws PermissionDeniedException{
    depth3();
}

private static void depth3() throws PermissionDeniedException{
    depth4();
}

private static void depth4() throws PermissionDeniedException{
    throw new PermissionDeniedException();
}
```

위 상황에서 `depth4`의 Exception이 바뀐다면 연쇄적으로 상위 Exception도 모두 수정해야 한다.

이 과정에서 확인된 예외로 인해 캡슐화가 깨진다.

UncheckedException은 별도로 예외 처리를 하지 않아도 되므로 사용하는 것을 권장한다.
</br>
</br>

## 예외에 의미를 제공하라

자바는 모든 예외에 호출 스택(`e.printStackTrace()`)을 제공한다.

하지만 실패한 코드의 의도를 파악하려면 **호출 스택만으로 부족**하다.

오류 메세지에 정보를 담아 예외와 함께 던지자.

- 실패한 연산 이름
- 실패 유형

로깅 기능을 사용한다면 catch 블록에서 오류를 기록하도록 충분한 정보를 넘겨주자
</br>
</br>

## 호출자를 고려해 예외 클래스를 정의하라

```java
// 예외를 모두 잡아내는 방법
try{
    ...
} catch(DeviceResponseException e){
    ...
} catch(ATM1212UnlockedException e){
    ...
} catch(GMXError e){
    ...
}

// 예외 클래스를 정의하고 사용하는 방법
try {
    ...
} catch(PortDeviceFailure e){
    ...
}
```

`PortDeviceFailure` 하나로 묶어서 쓰면 코드가 간결해진다.
</br>
</br>

## 정상 흐름을 정의하라

```java
// expenseReportDAO가 항상 객체를 반환하지 않는 경우
try {
    MealExpenses expenses = expenseReportDAO.getMeals(exployee.getID());
    m_total += expenses.getTotal(); // 외식을 한 경우
} catch(MealExpensesNotFound e){
    m_total += getMealPerDiem(); // 구내식당 금액만큼 추가 (4000)
}

// expenseReportDAO가 항상 객체를 반환하는 경우
MealExpenses expenses = expenseReportDAO.getMeals(exployee.getID());
m_total += expenses.getTotal();

public class PerDiemMealExpenses implements MealExpenses {
    ...
    public int getTotal(){
        // 기본값으로 일일 기본 식비를 반환한다.
        if(/* 외식하고 결제를 청구했는가? */){
            return /*외식비*/
        }
        return /* 구내식당 금액 반환 */
    }
}
```

이를 특수 사례 패턴(SPECIAL CASE PATTERN)이라 한다. 클래스를 만들거나 객체를 조작해 특수 사례를 처리하는 방식이다.

이렇게 하면 클라이언트 코드가 예외적인 상황을 처리할 필요가 없어진다.
</br>
</br>

## null을 반환하지 마라

```java
List<Employee> employees = getEmployees();
if(employees != null) {
    for(Employee e : employees) {
        totalPay += e.getPay();
    }
}
```

위에서 `getEmployees`는 null도 반환한다. 하지만, 그럴 필요가 없다.

```java
List<Employee> employees = getEmployees();
for(Employee e : employees) {
    totalPay += e.getPay();
}

...
public List<Employee> getEmployees() {
    if(.. 직원이 없다면 ..)
        return Collections.emptyList();
}
```

이렇게 코드를 변경하면 **코드도 깔끔**해질뿐더러 **NullPointerException이 발생할 가능성도 줄어든다.**
</br>
</br>

## null을 전달하지 마라

정상적인 인수로 null을 기대하는 API가 아니라면 메서드로 null을 전달하는 코드는 최대한 피한다.

```java
public class MetricsCalculator
{
    public double xProjection(Point p1, Point p2){
        return (p2.x - p1.x) * 1.5;
    }
    ...
}
```

누군가의 인수로 null을 전달한다면 **NullPointerException**이 발생한다.



```java
public class MetricsCalculator
{
    public double xProjection(Point p1, Point p2){
        if(p1==null || p2==null){
            throw InvalidArgumentException(
	            "Invalid argument for MetricsCalculator.xProjection");
        }
        return (p2.x - p1.x) * 1.5;
    }
    ...
}
```

위 코드가 첫 번째 코드보단 나을 수 있을지언정 InvalidArgumentException 예외를 잡아내는 처리가 필요하다.



```java
public class MetricsCalculator
{
    public double xProjection(Point p1, Point p2) {
        assert p1 != null : "p1 should not be null";
        assert p2 != null : "p2 should not be null";
        return (p2.x - p1.x) * 1.5;
    }
}
```

위처럼 assert문을 사용하는 방법도 있다.



**애초에 null을 넘기지 못하도록 금지하는 정책이 합리적**이다. 즉, 인수로 null이 넘어오면 코드에 문제가 있다는 말이다.

이런 정책을 따르면 그만큼 부주의한 실수를 저지를 확률도 작아진다.
</br>
</br>

## 결론

깨끗한 코드는 읽기도 좋아야 하지만 안정성도 높아야 한다.

이 둘은 따로 봐선 안되고 하나의 동일한 목표여야 한다.

**오류 처리를 프로그램 논리와 분리**해 독자적인 사안으로 고려하면 튼튼하고 깨끗한 코드를 작성할 수 있다.

오류 처리를 프로그램 논리와 분리하면 독립적인 추론이 가능해지며 코드 유지보수성도 크게 높아진다.

