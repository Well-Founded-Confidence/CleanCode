## Chapter 1. 깨끗한 코드


### 코드란?

- 기계가 실행할 정도로 요구사항을 상세히 표현하는 수단

- 요구사항에 더욱 가까운 언어 & 요구사항에서 정형 구조를 뽑아내는 도구

  

### 나쁜 코드

- 제품 출시 주기 지연

- 이전 버전 버그 개선 X

- 프로그램 시동 시간 & 죽는 횟수 증가

  ⇒ 기능을 추가 할수록 코드 엉망



#### 르블랑의 법칙

> 나중은 결코 오지 않는다

- "나중에 손 봐야지"라고 생각 하는 것

- 안 돌아가는 것보단 돌아가는게 좋지~

⇒ **시간이 걸리더라도 초반에 클린한 코드를 짜자!**



### 나쁜 코드로 치르는 대가

- 개발 속도 저하
- 팀 생산성 저하

`깨끗한 코드를 만드는 노력이 비용을 절감할 뿐만 아니라 전문가로서 살아남는 길`

`기한을 맞추는 방법 = 빨리 가는 방법 = 언제나 코드를 최대한 깨끗하게 유지하는 습관`



### 깨끗한 코드

- 비야네 스트롭스트룹 (Biarne Stroustrup)

1. 논리가 간단해야 버그가 숨어들지 못한다
2. 의존성을 최대한 줄여야 유지보수가 쉬워진다
3. 오류는 명백한 전략에 의거해 철저히 정리한다 
4. 성능을 최적으로 유지해야 한다

  

- 그래디 부치 (Grady Booch)

1. 단순하고 직접적이다
2. 깨끗한 설계자의 의도를 숨기지 않는다
3. 명쾌한 추상화와 단순한 제어문으로 가득하다

  

- 큰 데이브 토마스 (Dave Thomas)

1. 작성자가 아닌 사람도 읽기 쉽고 고치기 쉽다
2. 단위 테스트 케이스와 인수 테스트 케이스가 존재한다
3. 의미 있는 이름이 붙는다
4. 특정 목적을 달성하는 방법은 여러 가지가 아닌 하나만 제공한다
5. 의존성은 최소이며 각 의존성을 명확히 정의한다



- 마이클 페더스 (Michael Feathers)

1. 언제나 누군가 주의 깊게 짰다는 느낌을 준다
2. 고치려고 살펴봐도 딱히 손 댈 곳이 없다



- 론 제프리스 (Ron Jeffries)

1. 모든 테스트를 통과한다
2. 중복이 없다
3. 시스템 내 모든 설계 아이디어를 표현한다
4. 클래스, 메서드, 함수 등을 최대한 줄인다



- 워드 커닝햄 (Ward Cunningham)

1. 코드를 읽으면서 짐작했던 기능을 각 루틴이 그대로 수행한다
2. 코드가 그 문제를 풀기 위한 언어처럼 보인다



`주변 코드가 읽기 쉬우면 새 코드를 짜기도 쉽다. = 읽기 쉬운 코드를 짜자!`



### 보이스카우트 규칙

> 캠프장은 처음 왔을 때보다 더 깨끗하게 해놓고 떠나라

- 잘 짠 코드가 전부는 아니다.
- 시간이 지나도 언제나 깨끗하게 유지해야 한다
- 한 번에 많은 시간과 노력을 투자하지 말고, **지속적 개선**을 하자