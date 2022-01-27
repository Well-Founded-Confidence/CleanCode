- [주석](#--)
  * [주석은 나쁜 코드를 보완하지 못한다](#-------------------)
  * [코드로 의도를 표현하라!](#-------------)
  * [좋은 주석](#-----)
    + [법적인 주석](#------)
    + [정보를 제공하는 주석](#-----------)
    + [의도를 설명하는 주석](#-----------)
    + [의미를 명료하게 밝히는 주석](#---------------)
    + [결과를 경고하는 주석](#-----------)
    + [TODO 주석](#todo---)
    + [중요성을 강조하는 주석](#------------)
    + [공개 API에서 Javadocs](#---api---javadocs)
  * [나쁜 주석](#-----)
    + [주절거리는 주석](#--------)
    + [같은 이야기를 중복하는 주석](#---------------)
    + [오해할 여지가 있는 주석](#-------------)
    + [의무적으로 다는 주석](#-----------)
    + [이력을 기록하는 주석](#-----------)
    + [있으나 마나 한 주석](#-----------)
    + [함수나 변수로 표현할 수 있다면 주석을 달지 마라](#---------------------------)
    + [위치를 표시하는 주석](#-----------)
    + [닫는 괄호에 다는 주석](#------------)
    + [공로를 돌리거나 저자를 표시하는 주석](#--------------------)
    + [주석으로 처리한 코드](#-----------)
    + [전역 정보](#-----)
    + [모호한 관계](#------)
---
# 주석

> 주석은 필요악이다. 코드로 의도를 표현할 방법을 생각하라

## 주석은 나쁜 코드를 보완하지 못한다

> 표현력이 풍부하고 깔끔하며 주석이 거의 없는 코드가, 복잡하고 어수선하며 주석이 많이 달린 코드보다 훨씬 좋다

## 코드로 의도를 표현하라!

```java
// 직원에게 복지 혜택을 받을 자격이 있는지 검사한다.
if ((employee.flags & HOURLY_FLAG) && (employee.age > 65))...
```

```java
if (employee.isEligibleForFullBenefits())...
```

주석 없이도 코드로 대다수 의도를 표현할 수 있다. 조금 더 고민해보자.

## 좋은 주석

> 어떤 주석은 필요하거나 유익하다

### 법적인 주석

```java
// Copyright (C) ~~
```

표준 주석 헤더다. 오픈소스 프로젝트에서 자주 볼 수 있다.

### 정보를 제공하는 주석

```java
// 테스트 중인 Responder 인스턴스를 반환한다.
protected abstract Responder responderInstance();
```

해당 주석은 **추상 메서드가 반환할 값을 설명**한다.

이런 주석이 유용하다 할지라도, 가능하다면 아래와 같이 함수 이름에 정보를 담고 주석을 제거하는1 편이 더 좋다.

```java
protected abstract Responder responderBeingTested();
```



다음은 코드에서 사용한 정규표현식이 시각과 날짜를 뜻한다고 설명하는 주석이다.

```java
// kk:mm:ss EEE, MMM dd, yyyy 형식이다.
Pattern timeMatcher = Pattern.compile("\\d*:\\d*:\\d* \\w*, \\w* \\d*, \\d*");
```

구체적으로는 주어진 형식 문자열을 사용해 SimpleDateFormat.format 함수가 반환하는 시각과 날짜를 뜻한다.

이왕이면 **시각과 날짜를 변환하는 클래스를 만들어 코드를 옮겨**주면 더 깔끔하다.

### 의도를 설명하는 주석

```java
public int compareTo(Object o)
{
    if(o instanceof WikiPagePath)
    {
        WikiPagePath p = (WikiPagePath) o;
        String compressedName = StringUtil.join(names, "");
        String compressedArgumentName = StringUtil.join(p.names, "");
        return compressedName.compareTo(compressedArgumentName);
    }
    return 1; // 오른쪽 유형이므로 정렬 순위가 더 높다.
}
```

```java
public void testConcurrentAddWidgets() throws Exception {
    WidgetBuilder widgetBuilder = new WidgetBuilder(new Class[]{BoldWidget.class});
    String text = "'''bold text'''";
    ParentWidget parent = new BoldWidget(new MockWidgetRoot(), "'''bold text'''");
    AtomicBoolean failFlag = new AtomicBoolean();
    failFlag.set(false);
    
    // 스레드를 대량 생성하는 방법으로 어떻게든 Race condition을 만들려 시도한다
    for (int i = 0; i< 25000; i++){
        WidgetBuilderThread widgetBuilderThread = new WidgetBuilderThread(widgetBuilder, text, parent, failFlag);
        Thread thread = new Thread(widgetBuilderThread);
        thread.start();
    }
    assertEquals(false, failFlag.get());
}
```

### 의미를 명료하게 밝히는 주석

> 모호한 인수나 반환값은 그 의미를 읽기 좋게 표현하면 이해하기 쉬워진다.

`의도를 설명하는 주석`의 예제를 보면 알겠지만, 주석이 올바른지 검증하기 쉽지 않다.

그러므로 주석을 달 때는 더 나는 방법이 없는지 고민하고 정확히 달도록 각별히 주의한다.

### 결과를 경고하는 주석

다음은 특정 테스트 케이스를 꺼야하는 이유를 설명하는 주석이다.

```java
// 여유 시간이 충분하지 않다면 실행하지 마십시오.
public void _testWithReallyBigFile()
{
    writeLinesToFile(10000000);
    ...
}
```

요즘은 `@Ignore` 속성을 이용해 테스트 케이스를 꺼버리고 구체적인 설명은 `@Ignore` 속성에 문자열로 넣어준다.

```java
@Ignore("실행이 너무 오래 걸린다")
```



다음은 주석이 아주 적절한 예다.

```java
public static SimpleDateFormat makeStandardHttpDateFormat()
{
    // SimpleDateFormat은 스레드에 안전하지 못하다.
    // 따라서 각 인스턴스를 독립적으로 생성해야 한다.
    SimpleDateFormat df = new SimpleDateFormat("EEE, dd MMM yyyy HH:mm:ss z");
    df.setTimeZone(TimeZone.getTimeZone("GMT"));
    return df;
}
```

위 주석을 통해 정적 초기화 함수를 사용하려던 개발자가 주석 덕분에 실수를 면할 수 있다.

### TODO 주석

> 앞으로 할 일은 //TODO 주석으로 남겨두면 편하다.

```java
// TODO-MdM 현재 필요하지 않다.
// 체크아웃 모델을 도입하면 함수가 필요 없다.
protected VersionInfo makeVersion() throws Exception
{
    return null;
}
```

IDE에서는 TODO 주석만 모아볼 수도 있고 Intellij에서는 TODO 주석의 경우 타 주석과 글자색도 들어간다.

### 중요성을 강조하는 주석

```java
String listItemContent = matchgroup(3).trim();
// 여기서 trim은 정말 중요하다. trim 함수는 문자열에서 시작 공백을 제거한다.
// 문자열에 시작 공백이 있으면 다른 문자열로 인식되기 때문이다.
new ListItemWidget(this, listItemContent, this.level + 1);
return buildList(text.substring(match.end());
```

### 공개 API에서 Javadocs

공개 API를 구현한다면 반드시 훌륭한 Javadocs를 작성한다. 하지만 여느 주석과 마찬가지로 독자를 오도하거나, 잘못 위치하거나, 그릇된 정보를 전달할 가능성이 존재한다.

## 나쁜 주석

> 대다수 주석이 이 범주에 속한다.

### 주절거리는 주석

```java
public void loadProperties()
{
    try
    {
        String propertiesPath = propertiesLocation + "/" + PROPERTIES_FILE;
        FileIOnputStream propertiesStream = new FileInputStream(propertiesPath);
        loadedProperties.load(propertiesStream);
	}
    catch(IOException e)
    {
        // 속성 파일이 없다면 기본값을 모두 메모리로 읽어 들였다는 의미다.
	}
}
```

해당 주석은 저자에게야 의미가 있겠지만 다른 사람들에게는 전해지지 않는다.

답을 알아내기 위해선 다른 코드를 뒤져야하며, 이 주석은 **독자와 제대로 소통하지 못하는 주석**이다.

### 같은 이야기를 중복하는 주석

```java
// this.closed가 true일 때 반환되는 유틸리티 메서드다.
// 타임아웃에 도달하면 예외를 던진다.
public synchronized void waitForClose(final long timeoutMillis) throws Exception {
    if(!closded)
    {
        wait(timeoutMillis);
        if(!closed)
            throw new Exception("MockResponseSender could not be closed");
    }
}
```

이 주석은 주석이 코드보다 더 많은 정보를 제공하지 못한다.

### 오해할 여지가 있는 주석

`같은 이야기를 중복하는 주석`의 코드는 중복이 많으면서도 오해할 여지가 있다.

`this.closed`가 `true`로 변하는 순간에 **메서드는 반환되지 않는다**.

### 의무적으로 다는 주석

모든 함수에 Javadocs를 달거나 모든 변수에 주석을 달아야 한다는 규칙은 어리석다.

오히려 코드만 헷갈리게 만들 수 있다.

### 이력을 기록하는 주석

옛날엔 소스 코드 관리 시스템이 없었기에 코드에 이력(Changelog)을 표시했다.

하지만 이젠 혼란만 가중할 뿐이므로 완전히 제거하자.

### 있으나 마나 한 주석

너무나 당연한 사실을 언급하고 새로운 정보를 제공하지 못하는 의미없는 주석이다.

```java
/**
 * 기본 생성자
 */
protected AnnualDateRule() {    
}
```

### 함수나 변수로 표현할 수 있다면 주석을 달지 마라

```java
// 전역 목록 <smodule>에 속하는 모듈이 우리가 속한 하위 시스템에 의존하는가?
if (smodule.getDependSubsystems().contains(subSysMod.getSubSystem()))...
```

주석을 없애고 아래처럼 표현했다.

```java
ArrayList moduleDependees = smodule.getDependSubsystems();
String ourSubSystem = subSysMod.getSubSystem();
if (moduleDependees.contains(ourSubSystem))...
```

### 위치를 표시하는 주석

```java
// Actions //////////////////////////////
```

이 기능은 필요할 때만 드물게 사용하는 편이 좋다.

### 닫는 괄호에 다는 주석

```java
try {
     while (true) {
         
     }	//while
} // trye
```

중첩이 심하고 장황한 함수라면 의미가 있을지도 모르지만 작고 캡슐화된 함수에는 불필요하다.

### 공로를 돌리거나 저자를 표시하는 주석

```java
/* 강승현이 추가함 */
```

소스 코드 관리 시스템은 누가 언제 무엇을 추가했는지 기억한다.

따라서 불필요한 정보다.

### 주석으로 처리한 코드

```java
InputStreamResponse reponse = new inputStreamREsponse();
...
// InputStream resultsStream = formatter.getResultStream();
```

주석으로 처리된 코드는 다른 사람들이 지우기를 주저한다. **이유가 있어서 남겨놓았으리라고 생각하기 때문**이다.

이젠, 모든 코드 기록은 형상 관리에서 처리하기 때문에 과감하게 지우는 것이 좋다.

### 전역 정보

> 시스템의 전반적인 정보를 기술하지마라

```java
/**
 * 적합성 테스트가 동작하는 포트: 기본값은 <b>8082</b>
 * 
 * @param fitnessPort
 */
public void setFitnessePort(int fitnessePort)
{
    this.fitnessePort = fitnessPort;
}
```

즉, 바로 아래에 있는 코드를 설명하는 것이 아니라 시스템 어딘가에 있는 다른 함수를 설명한다는 말이다.

### 모호한 관계

> 주석과 주석이 설명하는 코드는 둘 사이 관계가 명백해야 한다.

```java
/*
 * 모든 픽셀을 담을 만큼 충분한 배열로 시작한다(여기에 필터 바이트를 더한다).
 * 그리고 헤더 정보를 위해 200바이트를 더한다.
 */
this.pngBytes = new byte[((this.width + 1) * this.height * 3) + 200];
```

- 여기서 `필터 바이트`란 무엇일까?

- 200을 추가하는 이유는?

주석을 다는 목적은 코드만으로 설명이 부족해서다. **주석 자체가 다시 설명을 요구해선 안된다**.