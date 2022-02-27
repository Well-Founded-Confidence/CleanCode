# Chapter 12. 창발성

- [Chapter 12. 창발성](#chapter-12-창발성)
  - [창발적 설계로 깔끔한 코드를 구현하자](#창발적-설계로-깔끔한-코드를-구현하자)
  - [단순한 설계 규칙 1: 모든 테스트를 실행하라](#단순한-설계-규칙-1-모든-테스트를-실행하라)
  - [단순한 설계 규칙 2~3: 리팩토링](#단순한-설계-규칙-23-리팩토링)
  - [중복을 없애라](#중복을-없애라)
  - [표현하라](#표현하라)
  - [클래스와 메서드 수를 최소로 줄여라](#클래스와-메서드-수를-최소로-줄여라)

---

## 창발적 설계로 깔끔한 코드를 구현하자

- 단순한 설계 규칙 네 가지(중요도 순)
  1. 모든 테스트를 실행한다
  2. 중복을 없앤다
  3. 프로그래머 의도를 표현한다
  4. 클래스와 메서드 수를 최소로 줄인다

</br>

## 단순한 설계 규칙 1: 모든 테스트를 실행하라

설계는 의도한 대로 돌아가는 시스템을 내놓아야 한다.

문서로는 시스템을 완벽하게 설계했지만, 시스템이 의도한 대로 돌아가는지 검증할 간단한 방법이 없다면, 문서 작성을 위해 투자한 노력에 대한 가치는 인정받기 힘들다.

- 테스트가 가능한 시스템을 만들려고 애쓰면 설계 품질이 더불어 높아진다.
- 결합도가 높으면 테스트 케이스를 작성하기 어렵다.
- 테스트 케이스를 많이 작성할수록 개발자는 DIP(의존 관계 역전 원칙)와 같은 원칙을 적용하고 의존성 주입(DI), 인터페이스, 추상화 등과 같은 도구를 사용해 결합도를 낮춘다.

</br>

## 단순한 설계 규칙 2~3: 리팩토링

- 테스트 케이스를 모두 작성했다면 이제 코드와 클래스를 정리해도 괜찮다.
- 테스트 케이스가 있으므로 코드를 정리하면서 시스템이 깨질까 걱정할 필요가 없다.
- 리팩토링 단계에서는 소프트웨어 설계 품질을 높이는 기법이라면 무엇이든 적용해도 괜찮다.
  - 응집도를 높이고
  - 결합도를 낮추고
  - 관심사를 분리하고
  - 시스템 관심사를 모듈로 나누고
  - 함수와 클래스 크기를 줄이고
  - 더 나은 이름을 선택하고

</br>

## 중복을 없애라

- 중복은 추가 작업, 추가 위험, 불필요한 복잡도를 뜻한다.

깔끔한 시스템을 만들려면 단 몇 줄이라도 중복을 제거하겠다는 의지가 필요하다.

```java
public void scaleToOneDimension(float desiredDimension, float imageDimension) {
  if (Math.abs(desiredDimension - imageDimension) < errorThreshold)
    return;
  float scalingFactor = desiredDimension / imageDimension;
  scalingFactor = (float)(Math.floor(scalingFactor * 100) * 0.01f);

  RenderedOpnewImage = ImageUtilities.getScaledImage(image, scalingFactor, scalingFactor);
  image.dispose();
  System.gc();
  image = newImage;
}

public synchronized void rotate(int degrees) {
  RenderedOpnewImage = ImageUtilities.getRotatedImage(image, degrees);
  image.dispose();
  System.gc();
  image = newImage;
}
```

- scaleToOneDimension 메서드와 rotate 메서드를 살펴보면 일부 코드가 동일하다. 다음과 같이 코드를 정리해 중복을 제거한다.

```java
public void scaleToOneDimension(float desiredDimension, float imageDimension) {
	if (Math.abs(desiredDimension - imageDimension) < errorThreshold)
	  return;
	float scalingFactor = desiredDimension / imageDimension;
	scalingFactor = (float) Math.floor(scalingFactor * 10) * 0.01f);
	replaceImage(ImageUtilities.getScaledImage(image, scalingFactor, scalingFactor));
}

public synchronized void rotate(int degrees) {
	replaceImage(ImageUtilities.getRotatedImage(image, degrees));
}

private void replaceImage(RenderedOpnewImage) {
	image.dispose();
	System.gc();
	image = newImage;
}
```

**문제점이 발생했다.**

- 공통적인 코드를 새 메서드로 뽑고 보니 클래스가 SRP를 위반한다.
- 그러므로 새로 만든 replaceImage 메서드를 다른 클래스로 옮겨도 좋겠다.
- 그러면 새 메서드의 가시성이 높아지고 다른 팀원이 새 메서드를 좀 더 추상화해 다른 맥락에서 재사용할 기회를 포착할지도 모른다.
- 이런 '소규모 재사용'은 시스템 복잡도를 극적으로 줄여준다.
- 소규모 재사용을 제대로 익혀야 대규모 재사용이 가능하다.



TEMPLATE METHOD 패턴은 고차원 중복을 제거할 목적으로 자주 사용하는 기법이다.

```java
public class VacationPolicy {
  public void accrueUSDDivisionVacation() {
    // 지금까지 근무한 시간을 바탕으로 휴가 일수를 계산하는 코드
    // ...
    // 휴가 일수가 미국 최소 법정 일수를 만족하는지 확인하는 코드
    // ...
    // 휴가 일수를 급여 대장에 적용하는 코드
    // ...
  }

  public void accrueEUDivisionVacation() {
    // 지금까지 근무한 시간을 바탕으로 휴가 일수를 계산하는 코드
    // ...
    // 휴가 일수가 유럽연합 최소 법정 일수를 만족하는지 확인하는 코드
    // ...
    // 휴가 일수를 급여 대장에 적용하는 코드
    // ...
  }
}
```

- 최소 법정 일수를 계산하는 코드만 제외하면 두 메서드는 거의 동일하다.
- 최소 법정 일수를 계산하는 알고리즘은 직원 유형에 따라 살짝 변한다.
- 여기에 TEMPLATE METHOD 패턴을 적용해 눈에 들어오는 중복을 제거한다.

</br>

```java
abstract public class VacationPolicy {
  public void accrueVacation() {
    caculateBaseVacationHours();
    alterForLegalMinimums();
    applyToPayroll();
  }

  private void calculateBaseVacationHours() { /* ... */ };
  abstract protected void alterForLegalMinimums();
  private void applyToPayroll() { /* ... */ };
}

public class USVacationPolicy extends VacationPolicy {
  @Override 
  protected void alterForLegalMinimums() {
    // 미국 최소 법정 일수를 사용한다.
  }
}

public class EUVacationPolicy extends VacationPolicy {
  @Override 
  protected void alterForLegalMinimums() {
    // 유럽연합 최소 법정 일수를 사용한다.
  }
}
```

- 하위 클래스는 중복되지 않는 정보만 제공해 accrueVacation 알고리즘에서 빠진 '구멍'을 메운다.

</br>

## 표현하라

자신이 이해하는 코드를 짜기는 쉽다. 

하지만 **나중에 코드를 유지보수할 사람이 코드를 짜는 사람만큼이나 문제를 깊이 이해할 가능성은 희박하다**.

개발자가 코드를 명백하게 짤수록 다른 사람이 그 코드를 이해하기 쉬워진다. 그래야 결함이 줄어들고 유지보수 비용이 적게 든다.

1. **좋은 이름을 선택한다**
   - 이름과 기능이 완전히 딴판인 클래스나 함수로 유지보수 담당자를 놀라게 해서는 안 된다.
2. **함수와 클래스 크기를 가능한 줄인다**
   - 작은 클래스와 작은 함수는 이름 짓기도 쉽고, 구현하기도 쉽고, 이해하기도 쉽다.
3. **표준 명칭을 사용한다**
   - 예를들어, 디자인 패턴은 의사소통과 표현력 강화가 주요 목적이다.
   - 클래스가 `COMMAND`나 `VISITOR`와 같은 표준 패턴을 사용해 구현된다면 클래스 이름에 패턴 이름을 넣어준다.
   - 그러면 다른 개발자가 클래스 설계 의도를 이해하기 쉬워진다.

4. **단위 테스트 케이스를 꼼꼼히 작성한다**
   - 테스트 케이스는 소위 '예제로 보여주는 문서'다. 다시 말해, 잘 만든 테스트 케이스를 읽어보면 클래스 기능이 한눈에 들어온다.
5. **표현력을 높이는 가장 중요한 방법은 노력이다**
   - 흔히 코드만 돌린 후 다음 문제로 직행하는 사례가 너무도 흔하다.
   - 나중에 읽을 사람을 고려해 조금이라도 읽기 쉽게 만들려는 충분한 고민은 거의 찾기 어렵다.
   - 하지만 나중에 코드를 읽을 사람은 바로 자신일 가능성이 높다는 사실을 명심하자.

자신의 **작품**에 조금만 더 **주의**를 기울이자.

</br>

## 클래스와 메서드 수를 최소로 줄여라

중복을 제거하고, 의도를 표현하고, SRP를 준수한다는 기본적인 개념도 **극단적으로 치달으면 득보다 실이 많아진다.**

- **클래스와 메서드 크기를 줄이자고 조그만 클래스와 메서드를 수없이 만드는 사례도 있다.**
  - 그래서 이 규칙은 함수와 클래스 수를 <u>가능한 줄이라고 제안</u>한다.

- **때로는 무의미하고 독단적인 정책 탓에 클래스 수와 메서드 수가 늘어나기도 한다.**
  - <u>클래스마다 무조건 인터페이스를 생성하라고 요구</u>하는 구현 표준👎🏻
  - 자료 클래스와 동작 클래스는 무조건 분리해야 한다고 주장하는 개발자 👎🏻
  - 가능한 독단적인 견해는 멀리하고 실용적인 방식을 택하자.👍🏻
- **목표는 함수와 클래스 크기를 작게 유지하면서 동시에 시스템 크기도 작게 유지하는 데 있다.**
  - 하지만 이 규칙은 간단한 설계 규칙 네 개 중 우선순위가 가장 낮다.
  - 다시 말해, 클래스와 함수 수를 줄이는 작업도 중요하지만, 테스트 케이스를 만들고 중복을 제거하고 의도를 표현하는 작업이 더 중요하다는 뜻이다.

