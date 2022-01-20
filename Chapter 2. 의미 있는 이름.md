## Chapter 2. 의미 있는 이름

---

### 의도를 분명히 밝혀라

- 좋은 이름을 지으려면 시간이 걸리지만 좋은 이름으로 절약하는 시간이 훨씬 더 많다.

- 따로 주석이 필요하다면 의도를 분명히 드러내지 못했다는 말이다.

  

```java
public List<int []> getThem() {
	List<int []> list1 = new ArrayList<int []>();
	for (int[] x : theList)
		if (x[0] ==4)
			list1.add(x);
		return list1;
}
```

- 코드 맥락이 코드 자체에 명시적으로 드러나지 않는다.



```java
public List<int []> getFlaggedCells() {
	List<int []> flaggedCells = new ArrayList<int []>();
	for (int [] cell : gameBoard)
		if(cell[STATUS_VALUE] == FLAGGED)
			flaggedCells.add(cell);
	return flaggedCells;
}
```

- 각 개념에 이름만 붙여도 코드가 상당히 나아진다.



```java
public List<Cell> getFlaggedCells() {
	List<Cell> flaggedCells = new ArrayList<Cell>();
	for (Cell cell : gameBoard)
		if(cell.isFlagged())
			flaggedCells.add(cell);
	return flaggedCells;
}
```

- (int 배열 → 클래스 사용) 단순히 이름만 고쳤는데도 함수가 하는 일을 이해하기 쉬워졌다.



### 그릇된 정보를 피하라

- 널리 쓰이는 의미가 있는 단어를 다른 의미로 사용해도 안된다.
  - ex) hp, aix, sco
- 서로 흡사한 이름을 사용하지 않도록 주의한다.
- 숫자처럼 보이는 소문자 L이나 대문자 O 변수로 사용하는데 적합하지 않다.



### 의미 있게 구분하라

- 연속된 숫자를 덧붙이거나 불용어(의미가 없는 단어)를 추가하는 방식은 적절하지 못하다.

  - 그릇된 정보를 제공X 즉, 아무런 정보를 제공하지도 못하는 이름

  

```java
public static void copyChars(char a1[], char a2[]) {
	for (int i = 0; i< a1.lenght; i++) {
		a2[i] = a1[i];
}
```

- a1과 a2가 무엇을 의미하는지 알 수 없다.

  - 함수 인수 이름을 source와 destination으로 변경한다면 훨씬 더 코드 읽기가 쉬울 것이다.

    

**`읽는 사람이 차이를 알도록 이름을 지어야한다.`**



### 클래스 이름

- `명사`나 `명사구`가 적합

  - Customer
  - WikiPage
  - Account
  - AddressParser

- Manager, Processor, Data, Info 등과 같은 단어는 피하고, 동사는 사용하지 않는다.

  

### 메서드 이름

- `동사`나 `동사구`가 적합

  - postPayment
  - deletePage
  - save

- `접근자, 변경자, 조건자`는 javabean 표준에 따라 값 앞에 `get, set, is`를 붙인다.

  

### 한 개념에 한 단어를 사용하라

- 똑같은 메서드를 클래스마다 fetch, retrieve, get으로 제각각 부르면 혼란스럽다.
  - 어느 클래스에서 어느 이름을 썼는지 기억하기 어려워 과거 코드를 살피느라 엄청난 시간을 소모할 가능성이 있다.

**`메서드 이름은 독자적이고 일관적이어야 한다.`**



### 의미 있는 맥락을 추가하라

- firstName, lastName, street, houseNumber, city, state, zipcode

  - 변수명을 보면 주소라는 사실을 알 수 있다.

- 만약 state 변수 하나만 사용한다면?

  - state가 주소 일부라는 사실을 알아차리기 어렵다.

  - 그럼 어떻게 해야할까?

    - 주소라는 것을 알아차리기 쉽게 접두어를 추가한다.

    - ex : addrFirstName, addrLastName, addrState

      

### 마치면서

- 코드를 개선하려는 노력을 중단해서는 안된다.
- 다른 사람이 짠 코드를 손본다면 리팩터링 도구를 사용해 문제 해결 목적으로 이름을 개선하라.