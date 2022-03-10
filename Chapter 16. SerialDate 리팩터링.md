# Chapter 16 - SerialDate 리팩터링

16장에서는 http://www.jfree.org/jcommon/ 에서 제공하는 JCommon 라이브러리 중 org.jfree.date라는 패키지 속에 위치한 SerialDate라는 클래스를 살펴본다. SerialDate는 날짜를 표현하는 자바 클래스이다.


## 첫째, 돌려보자

(책 뒤쪽의 코드 참고)
SerialDateTests라는 클래스는 단위 테스트 케이스를 몇 개 포함하는데, 돌려보면 실패하는 케이스는 없지만 테스트 케이스를 살펴보면 모든 경우를 점검하지 않는다는 사실이 드러난다. Clover라는 코드 커버리지 분석 도구로 조사한 결과 실행 가능한 문장 중 테스트가 실제로 실행하는 문장은 50%에 불과했다.</br>

그래서 저자는 독자적으로 단위 테스트 케이스를 구현해서 코드 커버리지를 92% 까지 올리고, equals를 equalsIgnoreCase로 바꾸거나 stringTomonthCode() 메서드를 수정하는 등의 작업을 거쳐 결국 모든 테스트 케이스를 통과하도록 만들었다.


## 둘째, 고쳐보자

여기서는 SerialDate 코드를 처음부터(라이선스 정보, 저작권, 작성자, 변경 이력이 나온다.) 하나씩 고쳐나간다..코드가 예제로 첨부하기에는 아주 길고 세세하므로 책을 읽자

(p. 448 코드 참고)

1. 61행에서 시작하는 import 문은 java.text.* 와 java.util.* 로 줄인다.

2. 67행부터 나오는 Javadoc 주석은 HTML 태그를 사용하는데, 한 소스코드에 여러 언어를 사용하므로 모양새를 제대로 맞추기 어려우므로 차라리 주석 전부를 `<pre>` 로 감싸는 편이 좋다.

3. SerialDate라는 클래스명을 DayDate로 변경했다.

4. MonthConstants를 enum으로 정의했다.

5. MonthConstants를 enum으로 변경하면서 int로 달을 받던 메서드는 이제 Month로 받고, isValidMonthCode 메서드와 monthCodeToQuarter와 같은 오류 검사 코드는 없앴다.

6. 93행 주석은 불필요하다. 불필요한 주석은 잘못된 정보가 쌓이기 좋으므로 제거했다.</br>
.</br>
.</br>
.</br>


### 책에 나와있는 리팩터링 작업 정리

첫째, 처음에 나오는 주석은 너무 오래되었다. 그래서 간단하게 고치고 개선했다.

둘째, enum을 모두 독자적인 소스 파일로 옮겼다.

셋째, 정적 변수(dateFormatSymbols)와 정적 메서드(getMonthNames,isLeapYear, lastDayOfMonth)를 DateUtil이라는 새 클래스로 옮겼다.

넷째, 일부 추상 메서드를 DayDate 클래스로 끌어올렸다.

다섯째, Month.make를 Month.fromInt로 변경했다. 다른 enum도 똑같이 변경했다. 또한 모든 enum에 toInt() 접근자를 생성하고 index 필드를 private로 정의했다.

여섯째, plusYears와 plusMonths에 중복이 있었다. correctLastDayOfMonth라는 새 메서드를 통해 중복을 없애고 셋 모두 좀 더 명확하게 했다.

일곱째, 팔방미인으로 사용하던 숫자 1을 없앴다. 모두 Month.JANUARY.toInt() 혹은 Day.SUNDAY.toInt()로 적절히 변경했다. SpreadsheetDate 코드를 살펴보고 알고리즘을 조금 손봤다.

작업 결과, DayDate 코드 커버리지는 84.9%로 감소했다. 테스트 코드가 줄어서가 아니라 클래스 크기가 작아지는 바람에 테스트하지 않는 코드의 비중이 커졌기 때문이다. 하지만 이 테스트하지 않는 코드는 너무나도 사소해 테스트할 필요도 없다.
