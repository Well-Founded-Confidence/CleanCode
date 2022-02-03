## Chapter 6. 객체와 자료 구조

- 변수를 비공개(private)로 정의하는 이유?
  - 남들이 변수에 의존하지 않게 만들고 싶어서
  - 변수 타입이나 구현을 맘대로 바꾸고 싶어서



### 자료 추상화

- 구현을 감추려면 **추상화**가 필요하다.

- 추상 인터페이스를 제공해 사용자가 구현을 모른 채 자료의 핵심을 조작할 수 있어야 진정한 의미의 클래스다.



```java
public class Point {
    public double x;
    public double y;
}
```

- 구현을 외부로 노출



```java
public interface Point {
    double getX();
    double getY();
    void setCartesian(double x, double y);
    double getR();
    double getTheta();
    void setPolar(double r, double theta);
}
```

- 구현을 완전히 숨김



```java
public interface Vehicle {
    double getFuelTankCapacityInGallons(); //자동차 연료 상태를 구체적인 숫자 값으로 알려줌
    double getGallonsOfGasoline();
}

//good
public interface Vehicle {
    double getPercentFuelRemaining(); // 자동차 연료 상태를 백분율이라는 추상적인 개념으로 알려줌
}
```

- 자료를 세세하게 공개하기보다는 추상적인 개념으로 표현하는 편이 좋다.
- 개발자는 객체가 포함하는 자료를 표현할 가장 좋은 방법을 고민해야한다.



### 자료/객체 비대칭

- 객체는 추상화 뒤로 자료를 숨긴 채 자료를 다루는 함수만 공개
- 자료 구조는 자료를 그대로 공개하며 별다른 함수는 제공하지 않는다.



[절차적인 도형]

- 각각의 도형 클래스는 간단한 자료 구조로 아무 메서드도 제공하지 않는다.
- 동작 방식은 Geometry 클래스에서 구현

```java
public class Square {
    public Point topLeft;
    public double side;
}

public class Rectangle {
    public Point topLeft;
    public double height;
    public double width;
}

public class Circle {
    public Point center;
    public double radius;
}

public class Geometry {
    public final double PI = 3.141592653589793;
    
    public double area(Object shape) throws NoSuchShapeException {
        if(shape instanceof Square){
            Square s = (Square)shape;
            return s.side * s.side;
        }
        else if(shape instanceof Rectangle){
            Rectangle r = (Rectangle)shape;
            return r.height * r.width;
        }
        else if(shape instanceof Circle){
            Circle c = (Circle)shape;
            return PI * c.radius * c.radius;
        }
        throw new NoSuchShapeExcetpion();
    }
}
```

- Geometry 클래스에 새로운 함수를 추가하고 싶다면?
  - 도형 클래스는 아무 영향을 받지 않는다.
- 새 도형을 추가하고 싶다면?
  - Geometry 클래스에 속한 함수를 모두 고쳐야 한다.



[객체 지향적인 도형]

```java
public class Square implements Shape {
    private Point topLeft;
    private double side;
    
    public double area(){
        return side * side;
    }
}

public class Rectangle implements Shape {
    private Point topLeft;
    private double height;
    private double width;
    
    public double area(){
        return height * widht;
    }
}

public class Circle implements Shape {
    private Point center;
    private double radius;
    public final double PI = 3.141592653589793;
    
    public double area(){
        return PI * radius * radius;
    }
}
```

- area()는 다형 메서드이므로 Geometry 클래스는 필요 없다.
- 새 도형을 추가하고 싶다면?
  - 기존 함수에 아무런 영향을 미치지 않는다.
- 새 함수를 추가하고 싶다면?
  - 도형 클래스 전부를 고쳐야한다.



[절차지향 코드 vs 객체지향 코드]

> 객체 지향 코드에서 어려운 변경은 절차적인 코드에서 쉬우며, 절차적인 코드에서 어려운 변경은 객체 지향 코드에서 쉽다!

- 절차지향 코드
  - 기존 자료 구조를 변경하지 않으면서 새 함수를 추가하기 쉽다.
  - 새로운 자료 구조를 추가하기 어렵다. 만약 추가하려면 모든 함수를 고쳐야 한다.

- 객체지향 코드
  - 기존 함수를 변경하지 않으면서 새 클래스를 추가하기 쉽다.
  - 새로운 함수를 추가하기 어렵다. 만약 추가하려면 모든 클래스를 고쳐야 한다.



### 디미터 법칙

> 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다.

- Demeter라는 프로젝트를 진행하던 개발자들이 어떤 객체가 다른 객체에 대해 지나치게 많이 알다보니, **결합도**가 높아지고 좋지 못한 설계를 야기한다는 것을 발견했다.
- 이를 개선하고자 **객체에게 자료를 숨기는 대신 함수를 공개**하도록 했다.



[예시]

- 서울에 살고 있는 사용자에게 알림을 보내주는 함수를 구현한다고 가정

```java
@Getter 
public class User { 
    private String email; 
    private String name; 
    private Address address; 
} 

@Getter 
public class Address { 
    private String region; 
    private String details; 
}

@Service
public class NotificationService {
    public void sendMessageForSeoulUser(final User user){
        if("서울".equals(user.getAddress().getRegion())){
            sendNotification(user);
        }
    }
}
```

- 위와 같은 코드는 디미터의 법칙을 위반한 코드이며 객체지향스럽지 못하다.
  - 객체에게 메세지를 보내는 것이 아니라 객체가 가지는 자료를 확인하고 있다.
  - 또한, 다른 객체가 어떠한 자료를 갖고 있는지 지나치게 잘 알고 있다.
  - Getter 메소드를 통해 user객체가 email, name, address를 가지고 있음을 알고 있다.





##### [수정한 코드]

```java
public class Address {
    private String region;
    private String details;
    
    public boolean isSeoulRegion(){
        return "서울".equals(region);
    }
}

public class User {
    private String email;
    private String name;
    private Address address;
    
    public boolean isSeoulUser() {
        return address.isSeoulRegion();
    }
}

@Service
public class NotificationService {
    public void sendMessageForSeoulUser(final User user){
        if(user.isSeoulUser()){
            sendNotification(user);
        }
    }
}
```

- 객체에게 보내는 메세지를 구현함으로써 불필요한 @Getter를 지울 수 있다.
- User 객체와 Address 객체가 어떠한 데이터를 지니고 있는지 모른 채 메세지를 보낼 수 있다.

- 알림을 보내는 로직에서 기존의 user.getAddress().getRegion() 처럼 여러 개의 .(도트)를 사용하여 참조하지 않기 때문에 디미터의 법칙을 잘 준수하고 있다.

[참고]

https://mangkyu.tistory.com/147



##### 기차 충돌

- 여러 객체가 한 줄로 이어진 기차처럼 보이는 코드



```java
final Stirng outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();

//위의 코드를 개선
Options opts = ctxt.getOptions();
File scratchDir = opts.getScratchDir();
final String outputDir = scratchDir.getAbsolutePath();
```

- 위의 코드를 보면 ctxt 객체가 Options를 포함하며, Options가 ScratchDir를, ScratchDir가 AbsolutePath를 포함한다는 사실을 알 수 있다.



```java
final String outputDir = ctxt.options.scratchDir.absolutePath;
```

- 이렇게 구현했을 경우 디미터 법칙을 거론한 필요가 없어진다.
  - 자료구조라면 내부 구조를 노출하므로 디미터 법칙이 적용되지 않는다.



##### 잡종 구조

- 절반은 객체, 절반은 자료 구조인 잡종 구조가 나온다.
- 잡종 구조는 중요한 기능을 수행하는 함수도 있고, 공개 변수나 공개 조회/설정 함수도 있다.
- 잡종 구조는 새로운 함수는 물론이고 새로운 자료 구조도 추가하기 어렵다.
  - 양쪽 단점만 모아놓은 구조



### 자료 전달 객체

> 공개 변수만 있고 함수가 없는 클래스
>
> 데이터베이스와 통신하거나 소켓에서 받은 메시지의 구문을 분석할 때 유용



##### 활성 레코드(=Getter/Setter가 있는 DTO)
- class 내에 함수가 있으면 = 객체다
- 단, DTO에 get/set = 자료구조다.
```java
// DTO(활성 레코드) 내부
public void setAge(int age){
    if(age > 0)  // 잡종 구조
    {
        this.age = age;
    }
}
```
- DTO의 특수한 형태
- 공개 변수가 있거나 비공개 변수에 조회/설정 함수가 잇는 자료구조지만, save나 find와 같은 탐색 함수도 제공
- 활성 레코드에 비즈니스 규칙 메서드를 추가해 자료구조를 객체로 취급하면 자료구조도 아니고 객체도 아닌 잡종 구조가 나오기 때문에 바람직하지 못하다.
  - 그럼 어떻게 해야할까?
    - 활성 레코드는 자료 구조로 취급
    - 비즈니스 규칙을 담으면서 내부 자료를 숨기는 객체는 따로 생성한다.

### 결론

- 객체는 동작을 공개하고 자료를 숨긴다.
  - 기존 동작을 변경하지 않으면서 새 객체 타입을 추가하기는 쉽지만, 기존 객체에 새 동작을 추가하기는 어렵다.
- 자료구조는 별다른 동작 없이 자료를 노출한다.
  - 기존 자료 구조에 새 동작을 추가하기는 쉽지만, 새 자료 구조를 추가하기는 어렵다.
- 새로운 자료 타입을 추가하는 유연성이 필요하다면?
  - 객체가 더 적합
- 새로운 동작을 추가하는 유연성이 필요하다면?
  - 자료 구조와 절차적인 코드가 더 적합
