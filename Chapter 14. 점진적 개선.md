# 점진적 개선

> 점진적 개선 챕터의 코드 변경 history는 아래 repository의 commit 내역 참조
>
> (pull requests-closed-Files changed를 참조하면 모든 코드를 볼 수 있음)
>
> [cleancode-succesiveRefinement](https://github.com/sungyujeon/cleancode-succesiveRefinement)

<br>

### Args 구현

```java
public static void main(String[] args) {
  try {
    Args arg = new Args("l,p#,d*", args);
    boolean logging = arg.getBoolean('l');
    int port = arg.getInt('p');
    String directory = arg.getString('d');
    executeApplication(logging, port, directory);
  } catch (ArgsException e) {
    System.out.print("Argument error: %s\n", e.errorMessage());
  }
}
```

- Args 생성자에 (입력으로 들어온) 인수 문자열과 형식 문자열을 넘겨 Args 인스턴스를 생성한 후 Args 인스턴스에 인수 값을 질의하는 프로그램

<br>

### 점진적 개선

- 초기 Boolean arg만 짜여저 있는 코드에서 String arg를 추가하는 코드 추가

  [Boolean, String arg 추가 코드](https://github.com/sungyujeon/cleancode-succesiveRefine>ment/pull/1)

  - 문제점 : 새로운 arg가 추가되면 고쳐야 할 메서드가 많음

  - 해결방안 : ArgumentMarshaler interface를 만들어 유사한 메서드를 제공하는 각 인수 유형의 변환을 처리함

    > marshaler : marshalling은 한 객체의 메모리에서 표현방식을 저장 또는 전송에 적합한 다른 데이터 형식으로 변환하는 과정. 직렬화(serialization)와 유사

- Marshaler interface(현재까지는 abstract class) 구현

  [marshaler](https://github.com/sungyujeon/cleancode-succesiveRefinement/pull/2)

  - 문제점
    - 여전히 Args class에서 명시적으로 변수가 그대로 남아 있고
    - setArgument() 메서드에서는 유형을 일일이 확인함
    - 각 set{TYPE}Arg() 메서드에서 구체적인 로직을 실행하여 흉함
    - 오류 처리 코드도 메서드 안에서 처리하고 있음
  - 개선
    - setArg에서 Args class 인스턴스 변수인 args, currentArgument를 2개 사용하고 있기 때문에 각 구체 클래스에 책임을 넘기기 어려우므로 인수로 넘겨서 해결(이 때도 둘 다 인수로 넘기는 것이 아니라 iterator로 변환하여 넘김)
    - setArgument() 메서드에서 일일이 개별 인스턴스에 set 하는 것이 아니라, ArgumentMarshaler.set()만 호출하여 한 번에 처리 가능(즉, 개별 구체 인스턴스에 각기 setArg에 대한 책임을 넘김)
    - 오류 처리 코드도 별도 분리 가능

- Marshaler 구조 리팩터링

  [Refactoring Marshaler ](https://github.com/sungyujeon/cleancode-succesiveRefinement/pull/3)

  - Args 클래스를 구현하고, Marshaler interface를 통해 새로운 arg가 추가되어도 새로운 구체 클래스만 만들도록 구조 개선, 그리고 오류 처리 코드를 별도로 생성하여 책임 분리