## 4장 주석

\- 잘 달린 주석은 그 어떤 정보보다 유용하다.

\- 그러나 경솔하고 근거 없는 주석은 코드를 이해하기 어렵게 만든다.

\- 주석은 ‘순수하게 선하지’ 못하다. 주석은 기껏해야 필요악이다.

\- 우리는 코드로 의도를 표현하지 못해서, 실패를 만회하기 위해 주석을 사용한다.

\- 주석은 언제나 “실패”를 의미한다.

\- 주석없이는 표현할 방법을 찾지 못해서 할 수 없이 주석을 사용한다.

\- 주석이 필요한 상황이 온다면? → 곰곰히 고민해 본다. 상황을 역전해 코드로 의도를 표현할 방법은 없을까?

\- 주석을 무시하는 이유 → 거짓말을 한다. 주석은 오래 될 수록 코드에서 멀어진다.

\- 그 이유는 프로그래머들이 주석을 유지하고 보수하기란 현실적으로 불가능 하기 때문이다.

\- 코드는 변화하고 진화한다. 불행하게도 주석이 언제나 코드를 따라가지 않는다.

\- 주석이 코드에서 분리되어 점점 더 부정확한 고아로 변하는 사례가 흔하다.

```java
MockRequest request;

private final String HTTP_DATE_REGEXP =
	"[SMTWF][a-z]{Z}\\,\\s[0-9]{2}\\s[JFMASOND][a-z]{2}\\s"+
	"[0-9]{4}\\s[0-9]{2}\\:[0-9]{2}\\:[0-9]{2}\\sGMT";

private Response response;
private FitNesseContext context;
private FileResponder responder;
private Locale saveLocale;
// Example: "Tue, 02 Apr 2003 22:18:49 GMT"
```

\- 짐작컨데, HTTP_DATE_REGEXP 상수와 주석 사이에 다른 인스턴스 변수를 추가했을 가능성이 농후하다.

\- 코드를 깔끔하게 정리하고 표현력을 강화하는 방향으로, 애초에 주석이 필요없는 방향으로 에너지를 쏟자.

\- 부정확한 주석은 아예 없는 주석보다 훨씬 더 나쁘다. 부정확한 주석은 결코 이뤄지지 않을 기대를 심어준다.

\- 진실은 코드 한곳에만 존재한다. 코드만이 자기가 하는 일을 진실되게 말하고 정확한 정보를 제공하는 유일한 출처다.

## 주석은 나쁜 코드를 보완하지 못한다.

\- 코드에 주석을 추가하는 일반적인 이유는 코드 품질이 나쁘기 때문이다.

\- 표현력이 풍부하고 깔끔하며 주석이 거의 없는 코드가, 복잡하고 어수선하며 주석이 많이 달린 코드가 훨씬 좋다.

## 코드로 의도를 표현하라

\- 확실히 코드만으로는 의도를 설명하기 어려운 경우가 존재한다.

\- 불행히도 많은 개발자가 이를 코드는 훌륭한 수단이 아니라는 의미로 해석한다. 이는 분명히 잘못된 생각이다

```java
if((employee.flags & HOURLY_FLAG) && (employee.age > 65))
```

\- 두개의 코드를 비교해보자.

```java
if (employee.isEligibleForFullBenefits())
```

\- 몇 초만 더 생각하면 코드로 대다수 의도를 표현할 수 있다. 많은 경우 주석으로 달려는 설명을 함수로 만들어 표현해도 충분하다.

## 좋은 주석

\- 어떤 주석은 필요하거나 유익하다. 지금부터 글자 값 한다고 생각하는 주석 몇가지를 소개한다.

\- 하지만 명심해야 할 점은 좋은 주석은 주석을 달지 않는 방법을 찾아낸 주석이라는 것이다.

### 법적인 주석

\- 회사가 정립한 구현 표준에 맞춰 법적인 이유로 특정 주석을 넣으라고 명시하는 경우.

\- 예를 들어, 각 소스 파일 첫 머리에 주석으로 들어가는 저작권 정보와 소유권 정보는 필요하고 타당하다.

\- 다음은 FitNess에서 모든 소스 파일 첫머리에 추가한 표준 주석 헤더다.

```java
// Copyright (C) 2003,2004,2005 by Object Mentor, Inc. All rights reserved.
// GNU General Public License 버전 2 이상을 따르는 조건으로 배포한다.
```

\- 소스파일 첫 머리에 들어가는 주석이 반드시 계약 조건이나 법적인 정보일 필요는 없다. 모든 조항과 조건을 열거하는 대신에 가능하다면, 표준 라이선스나 외부 문서를 참조해도 된다.

### 정보를 제공하는 주석

\- 때로는 기본적인 정보를 주석으로 제공하면 편리하다.

\- 예를 들어, 다음 주석은 추상 메소드가 반환할 값을 설명한다.

```java
// 테스트 중인 Responder 인스턴스를 반환한다.
protected abstract Responder responderInstance();
```

\- 때로 위와 같은 주석이 유용할지라도, 가능하다면 함수 이름에 정보를 담는 편이 더 좋다.

\- 위 코드는 함수이름을 responderBeingTested로 바꾸면 주석이 필요 없어질 것이다.

```java
// kk:mm:ss EEE, MMM dd, yyyy 형식이다.
Pattern timeMatcher = Pattern. compile(
"\Id*: \Id*:\1d* Ilw*, Iw* Ild*, \Id*");
```

\- 이는 좀더 나은 예시이다. 위에 제시한 주석은 사용한 정규표현식이 시각과 날짜를 뜻한다고 설명한다.

\- 구체적으로는 주어진 형식 문자열을 사용해 SimpleDateFormat.format 함수가 반환하는 시각과 날짜를 뜻한다.

\- 이왕이면 시각과 날짜를 변환하는 클래스를 만들어 코드를 옮겨주면 더 좋고 깔끔하다. 그러면 주석이 필요 없어지기 때문이다.

### 의도를 설명하는 주석

\- 주석은 구현을 이해하게 도와주는 선을 넘어 결정에 깔린 의도까지 설명한다.

\- 다음은 주석으로 흥미로운 결정을 기록한 예제다.

```java
public int compareTo(Objcet o)
{
	if(o instanceof WikiPagePath)
	{
		WikiPagePath p = (WikiPagePath) o;
		String compressedName = StringUtil.join(names, "");
		String compressedArgumentName = StringUtil.join(p.names, "");
		return compressedName.compareTo(compressedArgumentName);
	}
	return 1; // 옳은 유형이므로 정렬 순위가 더 높다.
}
```

\- 조금 더 나은 예제다 저자의 문제 해결 방식에 동의하지 않을지라도 저자의 의도는 분명히 드러난다.

```java
public void testConcurrentAddWidgets() throws Exception {
	WidgetBuilder widgetBuilder =
		new WidgetBuilder (new Class[]{BoldWidget.class});
	String text = "'''bold text'''";
	ParentWidget parent =
		new Boldwidget (new MockwidgetRoot(), "''bold text''");
	AtomicBoolean failFlag = new AtomicBoolean();

	failFlag. set (false);
// 스레드를 대량 생성하는 방법으로 어떻게든 경쟁 조건을 만들려 시도한다.

	for (int i=0; i<25000; i++) {
		WidgetBuilderThread widgetBuilderThread = new WidgetBuilderThread (widgetBuilder, text, parent, failFlag);
		Thread thread = new Thread(widgetBuilderThread);
		thread.start();
		}
		assertEquals (false, failflag.get())
	}
```

### 의미를 명료하게 밝히는 주석

\- 때때로 모호한 인수나 반환값은 그 의미를 읽기 좋게 표현하면 이해하기 쉬워진다.

\- 일반적으로는 인수나 반환값 자체를 명확하게 만들면 더 좋겠지만, 인수나 반환값이 표준 라이브러리나 변경하지 못하는 코드에 속한다면 의도를 명료하게 밝히는 주석이 유용하다.

```java
public void testCompareTo( ) throws Exception
{
	WikiPagePath a = PathParser.parse("PageA");
	WikiPagePath ab = PathParser.parse("PageA. PageB"');

```

### 결과를 경고하는 주석

\- 때로 다른 프로그래머에게 결과를 경고 할 목적으로 주석을 사용한다.

```java
//이유 시간이 충분하지 않다면 실행하지 마십시오.
public void _testwithReallyBigFile()
{
	writeLinesToFile(10000000);
	response.setBody(testfile);
	response.readyToSend (this);
	String responseString = output.toString);
	assertsubstring("Content-Length: 1000000000", responseString);
	assertTrue(bytesSent > 1000000000);
}
```

\- 물론 요즘에는 @ignore 속성을 이용해 테스트 케이스를 꺼버린다.

\- 구체적인 실명은 @ignore 속성에 문자열로 넣어준다.

\- 예시) @ignore(’실행이 너무 오래 걸린다.’)

\- 하지만 JUnit4가 나오기 전에는 메서드 이름 앞에 \_기호를 붙이는 방법이 일반적인 관례였다.

\- 위에서 제시한 주석은 (다소 경박하지만) 매우 적절한 지적이다.

\- 다음은 주석이 아주 적절한 예제다.

```java
public static SimpleDateFormat makeStandardHttpDateFormat()
{
	// SimpleDateFormat은 스레드에 안전하지 못하다.
	// 따라서 각 인스턴스를 독립적으로 생성해야 한다.
	SimpleDateFormat df = new SimpleDateFormat("EEE, dd MMM HH:mm:ss z");
	df.setTimeZone(TimeZone.getTimeZone('GMT'));
	return df;
}
```

\- 더 나은 해결책이 있을 수 있지만, 프로그램의 효율을 높이기 위해 정적 초기화 함수를 사용하려던 열성적인 프로그래머가 주석 때문에 실수를 면한다.

### TODO 주석

\- 때로는 ‘앞으로 할 일’을 //TODO 주석으로 남겨두면 편하다.

```java
// TODO- MdM 현재 필요하지 않다.
// 체크아웃 모델을 도입하면 함수가 필요 없다.
protected VersionInfo makeVersion() throws Exception
{
	return null;
}
```

\- TODO 주석은 프로그래머가 필요하다 여기지만 당장 구현하기 어려운 업무를 기술한다.

\- 하지만 어떤 용도로 사용하더라도 시스템에 나쁜 코드를 남겨 놓는 핑계가 되서는 안된다.

\- 요즘 나오는 대다수의 IDE는 TODO주석 전부를 찾아 보여줌으로 잊을 염려는 없다.

\- 그래도 주기적으로 TODO 주석을 점검해 없애도 괜찮은 주석은 없애라 권고한다.

### 중요성을 강조하는 주석

\- 자칫 대수롭지 않다고 여겨질 뭔가의 중요성을 강조하기 위해서도 주석을 사용한다.

```java
String listItemContent = match.group(3).trim() ;
// 여기서 trim은 정말 중요하다. trim 함수는 문자열에서 시작 공백을제거한다.
// 문자열에 시작 공백이 있으면 다른 문자열로 인식되기 때문이다.
new ListitemWidget(this, listItemContent, this.level + 1);
return buildList(text.substring(match.end()));
```

### 공개 API에서 Javadocs

\- 설명이 잘 된 공개 API는 참으로 유용하고 만족스럽다.

\- 표준 자바 라이브러리에서 사용한 Javadocs가 좋은 예시다.

\- 공개 API를 구현한다면 반드시 훌륭한 Javadocs를 작성한다. 하지만 이 장에서 제시하는 나머지 충고도 명심하자. 여느 주석과 마찬가지로 Javadocs역시 그릇된 정보를 전달할 가능성이 존재한다.

## 나쁜주석

\- 대다수의 주석이 이 범주에 속한다. 일반적으로 대다수 주석은 허술한 코드를 지탱하거나, 엉성한 코드를 변명하거나, 미숙한 결정을 합리화 하는 등 프로그래머가 주절거리는 독백이다.

### 주절거리는 주석

\- 특별한 이유 없이 의무감으로 혹은 프로세스에서 하라고 하니까 마지못해 주석을 단다면 전적으로 시간낭비다.

\- 주석을 달기로 결정했다면 충반한 시간을 들여 최고의 주석을 달도록 노력한다.

```java
public void loadProperties ()
{
	try
	{
		String propertiesPath = propertiesLocation + "/" + PROPERTIESFILE;
		FileInputStream propertiesStream = new FileInputStream(propertiesPath);
		loadedProperties.load(propertiesStream);
	}
	catch (IOException e)
	{
		// 속성 파일이 없다면 기본값을 모두 메모리로 읽어 들였다는 의미다.
	}
}
```

\- 이는 FItNess에서 발견한 코드로 주석을 제대로 달았다면 상당히 유용했을 코드다.

\- 하지만 저자가 서둘렀거나 부주의했고 그냥 주절거려 놓았기에 판독이 불가능하다.

\- catch 블록의 주석은 저자에게 의미가 있겠지만 그 의미가 다른 사람들에게는 전해지지 않는다. IOException이 발생하면 속성 파일이 없다는 뜻이고, 그러면 모든 기본값을 메모리로 읽어드린 상태라고 한다.

\- 하지만 누가 모든 기본값을 읽어 들이는가?

\- 답을 알아내려면 다른 코드를 뒤져보는 수 밖에 없다. 이해가 안되어 다른 모듈까지 뒤져야 하는 주석은 독자와 제대로 소통하지 못하는 주석이다.

### 같은 이야기를 중복하는 주석

\- 다음 코드는 헤더에 달른 주석이 같은 코드 내용을 그대로 중복한다. 자칫하면 코드보다 주석을 읽는 시간이 더 오래 걸린다.

```java
// 목록 4-1
// this.closed가 true일 때 반환되는 유틸리티 메서드다.
// 타임아웃에 도달하면 예외를 던진다.
public synchronized void waitforClose(final long timeoutMillis)
throws Exception
{
	if(!closed)
	{
		wait (timeoutMillis);
		if(!closed)
			throw new Exception("MockResponseSender could not be closed");
	}
}
```

\- 위와 같은 주석은 코드보다 더 많은 정보를 제공하지 못한다.

\- 코드를 정당화하지도, 의도나 근거를 설명하지도 않고, 코드보다 읽기가 쉽지도 않다.

\- 다음 코드는 톰캣에서 가져온 코드다. 쓸모없고 중복된 Javadocs가 매우 많다. 아래 주석은 코드만 지저분 하고 정신없게 만든다.

\- 기록이라는 목적에 전혀 기여하지 못한다.

```java
public abstract class ContainerBase
	implements Container, Lifecycle, Pipeline,
	MBeanRegistration, Serializable {
	/**
		* 이 컴포넌트의 프로세서 지연값
		*/
			protected int backgroundProcessorDelay = -1;

	/**
		* 이 컴포넌트를 지원하기 위한 생명주기 이벤트
		*/
			protected LifecycleSupport lifecycle =
			new LifecycleSupport(this);
	/**
		* 이 컴포넌트를 위한 컨테이너 이벤트 Listener
		*/
			protected ArrayList listeners = new ArrayList() ;
	/**
		* 컨테이너와 관련된 Loader 구현
		*/
			protected Loader loader = null;
	/**
		* 컨테이너와 관련된 Logger 구현
		*/
			protected Log logger= null;
	/**
		* 관련된 logger 이름
		*/
			protected String logName= null;
	/**
		* 컨테이너와 관련된 Manager 구현
		*/
			protected Manager manager = null;
	/**
		* 컨테이너와 관련된 Cluster
		*/
			protected Cluster cluster = null;
	/**
		* 사람이 읽을 수 있는 컨테이너 이름
		*/
			protected String name = null;
	/**
		* 컨테이너의 부모 컨테이너
		*/
		protected Container parent = null;
	/**
		* Loader를 설치할 때 구성이 끝나야 할 어버이 클래스 로더
		*/
		protected ClassLoader parentClassLoader = null;
	/**
		* 컨테이너와 관련된 Pipeline 객체
		*/
		protected Pipeline pipeline = new StandardPipeline(this);
	/**
		* 컨테이너와 관련된 Realm
		*/
		protected Realm realm= null;
	/**
		* 컨테이너와 관련된 DirContect 객체
		*/
		protected Dircontext resources = null;

```

### 오해할 여지가 있는 주석

\- 때로는 의도는 좋지만 프로그래머가 딱 맞을 정도로 엄밀하게 주석을 달지 못하기도 한다.

\- 목록 4-1의 주석을 보면 중복이 많으면서도 오해의 여지가 있다.

\- this.closed가 ture여야 메서드는 반환된다. 아니면 무조건 타임아웃을 기다렸다 this.closed가 그래도 true가 아니면 예외를 던진다.

\- 주석에 담긴 ‘살짝 잘못된 정보’로 인해 this.closed가 true로 변하는 순간에 함수가 반환되리라 생각으로 어느 프로그래머가 경솔하게 함수를 호출 할 지도 모른다.

### 의무적으로 다는 주석

\- 모든 함수에 Javadocs를 달거나 모든 변수에 주석을 달아야 한다는 규칙은 어리석기 그지없다. 이런 주석은 코드를 복잡하게 만들고, 혼동과 무질서를 초래한다.

```java
/*
* 목록 4-3
* @param title CD 제목
* @param author CD 저자
* @param tracks CD 트랙 숫자
* @param durationInMinutes CD 길이 (단위: 분)
*/
public void addCD(String title, String author,
									int tracks, int durationInMinutes) {
CD cd= new CD();
cd.title = title;
cd.author= author;
cd.tracks = tracks;
cd.duration = durationInMinutes;
cdList.add(cd);
}
```

\- 목록 4-3은 모든 함수에 Javadocs를 넣으라는 규칙이 낳은 괴물이다.

\- 아래와 같은 주석은 아무런 가치가 없고, 오히려 코드만 헷갈리게 만든다.

### 이력을 기록하는 주석

\- 때때로 사람들은 모듈을 편집할 때마다 모듈 첫머리에 주석을 추가한다.

\- 그리하여 모듈 첫머리 주석은 지금까지 모듈에 가한 변경을 모두 기록하는 일지 혹은 로그가 된다. 첫머리 주석만 십여쪽을 넘어서는 모듈도 있다.

```java
* 변경이력 (11-0ct-2001부터)
* -------------------------
* 11-0ct-2001 : 클래스를 다시 정리하고 새로운 패키지인
*               com,irefinery.date로 옮겼다 (DG);
* 05-Nov-2001 : getDescription() 메서드를추가했으며
*               NotableDate class를 제거했다 (DG);
* 12-Nov-2001 : IBD>| setDescription() 메서드를 요구한다. NotableDate
*               클래스를 없앴다. (DG); getPreviousDayOtweek(),
*               getFollowingDayOtWeekl(), getivearestDayOfweek()를 변경해
*               버그를 수정했다 (DG);
* 05-Dec-2001 : SpreadsheetDate 클래스에 존재하는 버그를 수정했다 (DG);
* 29-MAY-2002 : month 상수를 독자적인 인터페이스로 옮겼다.
*               (MonthConstants) (DG);
* 27-Aug-2002 : adoMonths() 메서드에 있는 버그를 수정했다. N???levka Petr 덕분이다 (DG);
* 03-0ct-2002 : Checkstyleol이 보고한 오류를 수정했다(DG);
* 13-Mar-2003 : Serializable을 구현했다(DG);
* 29-Vay-2003 : adaMonths 메서드에 있는 버그를 수정했다(DG);
* 04-Sep-2003 : Comparable을 구현했다. isInRange Javadocs를 갱신했다(DG);
* 05- Jan-2005 : addYears() 메서드에 있는 버그를 수정했다 (1096282) (DG);
```

\- 예전에는 모든 모듈 첫머리에 변경이력을 기록하고 관리하는 관례가 바람직했다.

\- 당시에는 소스코드관리 시스템이 없었기 때문이다. 하지만 이제는 혼란만 가중하기 때문에 완전히 제거하는 편이 좋다.

### 있으나 마나 한 주석

\- 때때로 있으나 마나 한 주석을 접한다. 쉽게 말해, 너무 당연한 사실을 언급하며 새로운 정보를 제공하지 못하는 주석이다.

```java
/**
* 기본 생성자
*/
protected AnnualDateRule() {
}

그렇단 말이지? 다음은 어떤가?

/** 월중일자*/

private int day0fMonth;

이번에는 전형적인 중복을 보여준다.

/**
* 월 중 일자를 반환한다.
* @return 월 중 일자
*/
public int getDay0fMonth() {
	return day0fMonth;
}
```

\- 위와 같은 주석은 지나친 참견이라 개발자가 주석을 무시하는 습관에 빠진다.

\- 코드를 읽으며 자동으로 주석을 건너띈다. 결국은 코드가 바뀌면서 주석은 거짓말로 변한다.

```java
// 목록 4-4
private void startSending()
{
	try
	{
		doSending();
	}
	catch(SocketException e)
	{
		// 정상. 누군가 요청을 멈췄다.
	}
	catch(Exception e)
	{
		try
		{
			response.add(ErrorResponder.makeExceptionString(e));
			response.closeAll();
		}
		catch (Exception e1)
		{
			// 이게 뭐야!
		}
	}
}
```

\- 목록 4-4에서 첫 번쨰 주석은 적절해 보인다. catch 블록은 무시해도 괜찮은 이유를 설명하는 주석이다. 하지만 두번째 주석은 전혀 쓸모가 없다.

\- 있으나 마나한 주석으로 분풀이를 하는 대신 프로그래머가 코드 구조를 개선했다면 짜증낼 필요가 없었을 것이다.

\- 목록 4-5를 보면 마지막 try/catch 블록을 독자적인 함수로 만드는데 노력을 쏟았어야 함을 알 수 있다.

```java
private void startSending(){
{
	try
	{
		doSending();
	}
	catch(SocketException e)
	{
		// 정상. 누군가가 요청을 멈췄다.
	}
	catch(Exception e)
	{
		addExceptionAndCloseResponse(e);
	}
}
	private void addExceptionAndCloseResponse(Exception e)
	{
		try
		{
			response.add(ErrorResponder.makeExceptionString(e));
			response.closeAll();
		}
		catch(Exception el)
		{
		}
}
```

\- 있으나 마나 한 주석을 달려는 유혹에서 벗어나 코드를 정리하자.

\- 더 낫고, 행복한 프로그래머가 되는 지름길이다.

### 무서운 잡음

\- 때로는 Javadocs도 잡음이다. 다음은 잘 알려진 오픈 소스 라이브러리에서 가져온 코드다. 아래 나오는 Javadocs는 어떤 목적을 수행할까?

\- 답은 없다. → 단지 문서를 제공해야 한다는 잘못된 욕심으로 탄생한 잡음이다.

```java
/** The name. */
private String name;

/** The version. */
private String version;

/** The licenceName. */
private String licenceName;

/** The version. */
private String info;
```

\- 위 주석을 한 번 더 주의 깊게 읽어보면 잘라서 붙여넣기 오류가 보인다. 주석을 작성한 저자가 주의를 기울이지 않는다면 독자가 얻을 이익이 없다.

### 함수나 변수로 표현할 수 있다면 주석을 달지 마라

\- 다음 코드를 살펴보자

```java
// 전역 목록 <smoduLe>에 속하는 모듈이 우리가 속한 하위 시스템에 의존하는가?
if (smodule.getDependSubsystems().contains(subSysMod.getSubSystem()))
```

\- 이 코드에서 주석을 없애고 다시 표현하면 다음과 같다.

```java
ArrayList moduleDependees = smodule. getDependSubsystems( );
String ourSubSystem = subSysMod. getSubSystem);
if (moduleDependees.contains(ourSubSystem))
```

\- 코드를 작성한 저자는 (가능성이 희박하지만) 주석을 먼저 달고 주석에 맞춰 코드를 작성했을수도 있다.
\- 하지만 위와 같이 주석이 필요하지 않도록 코드를 개선하는 편이 더 좋다.

### 위치를 표시하는 주석

\- 때때로 프로그래머는 소스파일에서 특정 위치를 표시하려 주석을 사용한다.

\- 예를 들어 다음과 같은 행이 있다.

```java
// Actions ///////////////////////////
```

\- 극히 드물지만 위와 같은 배너 아래 특정 기능을 모아놓으면 유용한 경우도 있긴 있다.

\- 하지만 일반적으로 위와 같은 주석은 가독성만 낮추므로 제거해야 마땅하다.

\- (특히 뒷부분 슬래시)

\- 너무 자주 사용하지 않는다면 배너는 눈에 띄며 주의를 환기한다. 그로므로 반드시 필요할 때만, 아주 드믈게 사용하는 편이 좋다. 남용하면 잡음이 된다.

### 닫는 괄호에 다는 주석

\- 때로는 프로그래머들이 닫는 괄호에 특수한 주석을 달아 놓는다.

```java
public class wc {
public static void main(String[] args) {
	BufferedReader in = new BufferedReader(new InputStreamReaderSystem.in));
	String line;
	int lineCount = 0;
	int charCount = 0;
	int wordCount = 0;
	try {
		while ((line = in.readLine()) =! null) {
			lineCount++;
			charCount += line.length();
			String words[] = line.split("\\W");
			wordCount =+ words.length;
		} //while
	System.out.println("wordCount = " + wordCount);
	System. out.println("lineCount = " + lineCount);
	System. out.printIn("charCount = " + charCount);
		} //try
	catch (IOException e) {
	System.err.println("Error:" + e.getMessage()); }
	} //catch
} //main
}
```

\- 중첩이 심하고 장황한 함수라면 의미가 있을지 모르지만, (우리가 선호하는) 작고 캡슐화된 함수에서는 잡음일 뿐이다. \- 그러므로 닫는 괄호에 주석을 달아야 겠다는 생각이 든다면, 함수를 줄이려 시도하자.

### 공로를 돌리거나 저자를 표시하는 주

```java
/* 카리나가 추가함 */
```

\- 소스 코드 관리 시스템은 누가 언제 무엇을 추가했는지 귀신처럼 기억한다. 그럼으로 저자 이름으로 코드를 오염시킬 필요가 없다.

### 주석으로 처리한 코드

\- 주석으로 처리한 코드만큼 밉살스러운 관행도 드물다. 다음처럼 코드를 작성하지 마라.

```java
InputStreamResponse response = new InputStreamResponse();
response.setBody(formatter.getResultStream(),formatter.getByteCount());
//InputStream resultsStream = formatter.getResultStream();
//StreamReader reader = new StreamReader(resultsStream);
//response.setContent(reader.read(formatter.getByteCount())) ;
```

\- 주석으로 처리된 코드는 다른 사람들이 지우기를 주저한다. -> 이유가 있어서 남겨 놓았을거라 생각한다.

\- 그래서 질 나쁜 와인병 바닥에 앙금이 쌓이듯 쓸모 없는 코드가 점차 쌓여간다.

\- 다음은 아파치 commons에서 가져온 코드다.

```java
this.bytePos = writeBytes(pngIdBytes, 0);
//hdrPos = bytePos;
writerHeader();
writeResolution();
//dataPos = bytePos;
if (writelmageData()) {
    writeEnd();
    this.pngbytes = resizeByteArray(this.pngBytes, this.maxPos);
}
else {
    this.pngBytes = null;
}
return this.pngBytes;
```

\- 두행을 주석으로 처리한 이유는 무엇일까?
\- 1960년대 즈음에는 주석으로 처리한 코드가 유용했다. 하지만 우수한 소스코드관리 시스템을 사용하면서 우리를 대신해 코드를 기억해준다.
\- 이제는 주석으로 처리할 필요가 없다. 그냥 코드를 삭제해라.

### HTML 주석

\- 소스코드에서 HTML주석은 혐오 그 자체다.
\- HTML 주석은 (주석을 읽기 쉬워야 하는) 편집기/IDE에서 조차 읽기가 어렵다.
\- (Javadocs와 같은) 도구로 주석을 뽑아 웹 페이지에 올릴 작정이라면 주석에 HTML태그를 삽입해야 하는 책임은 프로그래머가 아니라 도구가 져야 한다.

```java
/**
* 적합성테스트를수행하기위한과업
* 이 과업은 적합성 테스트를 수행해 결과들 출력한다.
* <p/>
* <pre>
* 용법:
* &lt; taskdef name=&quot; execute-fitnesse-tests&quot;
*       classname=&quot; fitnesse.ant.ExecuteFitnesseTestsTask&quot;
*       classpathref=&quot; classpath&quot; /&gt;
* 또는
* &lt;taskdef classpathref=&quot; classpath&quot;
*           resource=&quot; tasks.properties&quot; /&gt;
*<p/>
* &lt;execute-fitnesse-tests
*   suitepage=&quot; FitNesse.SuiteAcceptanceTests&quot;
*   fitnesseport=&quot;8082&quot;
*   resultsdir=&quot;${results.dir}&quot;
*   resultshtmlpage=&quot;fit-results.html&quot;
*   classpathref=Squot; classpath&quot; /&gt;
* </pre>
*/
```

### 전역정보

\- 주석을 달아야 한다면 근처에 있는 코드만 기술하라.
\- 코드 일부에 주석을 달면서 시스템 전반적인 정보를 기술하지 마라.

```java
/**
* 적합성 테스트가 동작하는 포트: 기본값은 <b>8082</b>.
*
* @param fitnessePort
*/
public void setFitnessePort(int fitnessePort)
{
this.fitnessePort = fitnessePort;
}
```

\- 주석은 기본 포트 정보를 기술하지만 함수 자체는 포트 기본값을 전혀 통제하지 못한다.
\- 주석은 바로 아래 함수가 아닌 시스템 어딘가에 있는 다른 함수를 설명한다는 이야기다.
\- 즉, 포트 기본값을 설정하는 코드가 변해도 아래 주석이 변한다는 보장이 없다.

### 너무 많은 정보

\- 주석에 흥미로운 역사나 관련 없는 정보를 장황하게 늘어놓지 마라.
\- 다음은 base64를 인코딩/디코딩 하는 함수를 테스트하는 모듈에서 가져온 주석이다.

```java
/*
RFC 2045 - Multipurpose Internet Mail Extensions (MIME)
1부: 인터넷 메시지 본체형식
6.8절, Base64 내용 전송 인코딩(Content- Transfer-Encoding)
인코딩 과정은 입력 비트 중 24비트 그룹을 인코딩된 4글자로구성된
출력 문자열로 표현한다. 왼쪽에서 오른쪽으로 진행해가며, 3개를 묶어 비트 입력
그룹을 형성한다. 이렇게 만들어진 24비트는 4개를 묶어 6비트 그룹으로 취급하며,
각각은 base64 알파벳에서 단일 자릿수로 해석된다.
base64 인코딩으로 비트스트림을 인코딩할때, 비트스트림은
MSB(Most Significant Bit) 우선으로 정렬되어 있다고 가정한다. 따라서,스트림에서
첫번째 비트는 첫 8비트 바이트에서 최상위 비트가되며, 여덟번째 비트는 첫 8비트
바이트에서 최하위 비트가 된다.
*/
```

### 모호한 관계

\- 주석과 주석이 설명하는 코드도 둘 사이 관계가 명백해야 한다.
\- 이왕 공들여 주석을 달았다면 적어도 독자가 주석과 코드를 읽어보고 무슨 소리인지 알아야 한다.

```java
/*
* 모든 픽셀을 담을만큼 충분한 배열로 시작한다. (여기에 필터 바이트를 더한다).
* 그리고 헤더정보를 위해 200바이트를 더한다.
*/
this.pngBytes = new byte[((this.width + 1) * this.height * 3) + 200];
```

\- 여기서 필터 바이트란 무엇일까? 주석을 다는 목적은 코드만으로 설명이 부족해서다.
\- 주석자체가 다시 설명을 요구하는것은 안타까운 경우다.

### 함수 헤더

\- 짧은 함수는 긴 설명이 필요 없다. 짧고 한 가지만 수행하며 이름을 잘 붙인 함수가 주석으로 헤더를 추가한 함수보다 좋다

### 비공개 코드에서 Javadocs

\- 공개 API는 Javadocs가 유용하지만 공개하지 않을 코드라면 쓸모 없다.

\- 시스템 내부에 속한 클래스와 함수에 Javadocs를 생성할 필요 없다.

\- 오히려 코드만 보기 싫고 산만해진다.

### 예제

\- 4-7 예제는 나쁜 코드와 주석을 보여주는 코드다.

\- 아래 모듈은 상당수가 이런 코드를 보면 '주석을 잘 달았다'고 생각하는 시절이 있기에 매력적이다.

```java
/**
* 4-7
* 이 클래스는 사용자가 지정한 최대값까지 소수를 생성한다. 사용된 알고리즘은
* 에라스토테네스의 체다.
* <p>
* 에라스토테네스: 기원전 276년에 리비아키레네에서 출생, 기원전 194년에 사망
* 지구 둘레를 최초로 계산한 사람이자 달력에 윤년을 도입한 사람.
* 알렉산드리아 도서관장을 역임.
* <p>
* 알고리즘은 상당히 단순하다. 2에서 시작하는 정수배열을 대상으로
* 2의 배수를 모두 제거한다. 다음으로 남은 정수를 찾아 이 정수의 배수를 모두 지운다.
* 최대값의 제곱근이 될때까지 이를 반복한다.
* @author Alphonse
* @version 13 Feb 2002 a t p
*/

import java.util.*;
public class GeneratePrimes
{
/**
* @param maxValue는 소수를 찾아낼 최대값
*/
public static int[] generatePrimes (int maxValue)
{
    if (maxValue >= 2) // 유일하게 유효한 경우
    {
        //선언
        int s = maxValue + 1, // 배열 크기
        boolean[] f = new boolean[s];
        int i;

        //배열을 참으로 초기화
        for (i = 0; i < s; i ++)
            f[i] = true;

        // 소수가 아닌 알려진 숫자를 제거
        f[0] = f[1] = false

        // 체

        int j;
        for (i = 2; i < Math.sqrt(s) + 1; i++){
            if (f[i]) // i가 남아 있는 숫자라면 이 숫자의 배수를 구한다.
            {
                for(j = 2 * i; j < s; j += i)
                    f[j] = false //배수는 소수가 아니다.
            }
        }

        // 소수 개수는?
        int count = 0;
        for (i = 0; i < s; i++)
        {
            if(f[i])
                count++; // 카운트 증가
        }

        int[] primes = new int[count];

        //소수를 결과 배열로 이동한다.
        for (i = 0; j = 0; j < s; i++)
        {
            if(f[i])
                primes[j++] = i;
        }
        return primes; // 소수를 반환한다.
    }
    else // maxValue < 2
        return new int[0]; //입력이 잘못되면 비어있는 배열을 반환한다.
}
}
```

\- 아래 코드는 4-7을 리팩터링한 결과다.

\- 전체 주석양이 상당히 줄었다.

```java
/*
* 4-8
* 이 클래스는 사용자가 지정한 최대값까지 소수를 구한다.
* 알고리즘은 에라스토테네스의 체다.
* 2에서 시작하는 정수배열을 대상으로 작업한다.
* 처음으로 남아있는 정수를 찾아 배수를 모두 제거한다.
* 배열에 더이상 배수가 없을때까지 반복한다.
*/
public class PrimeGenerator{
    private static boolean[ ] crossedOut;
    private static int [] result;

    public static int [] generatePrimes(int maxValue)
    {
        if (maxValue < 2)
        return new int [0];

    else
    {
        uncrossIntegersUpTo(maxValue);
        crossOutMultiples() ;
        putUncrossedIntegersIntoResult();
        return result;
    }
}
    private static void uncrossIntegersUpTo(int mavalue)
    {
        crossedOut = new boolean[maxvalue + 1];
        for (int 1 = 2; 1 < crossedOut.length; 1++)
            rossedOut[i] = false;
    }
    private static void crossOutMultiples()
    {
        int limit = determineIterationLimit();
        for (int 1 =2; 1 < limit; 1++)
            if (notCrossed(i))
                crossoutMultiplesUf(i);
    }
    private static int determineIterationLimit()
    {
        // 배열에 있는 모든 배수는 배열 크기의 제곱근 보다 작은 소수의 인수다.
        // 따라서 이 제곱근보다 더 큰 숫자의 배수는 제거 할 필요가 없다.
        double iterationLimit = Math.sqrt(crossedOut.length);
        return (int) iterationLimit;
    }
    private static void crossOutMultiples0f(int i)
    {
        for (int multiple = 2*1;
        multiple < crossedOut.length;
        multiple =+ i;
        crossedOut [multiple] = true;
    }
    private static boolean notCrossed(int i)
    {
        return crossedOut[i] == false;
    }

    private static void putUncrossedintegersIntoResult()
    {
         result = new int[numberOfUncrossedIntegers()];
        for (int j = 0, i = 2; i < crossedOut.length; i++)
            if (notCrossed(i))
                result[j++] = i;
    }
    private static int numberOfUncrossedIntegers()
    {
        int count = 0;
        for (int i = 2; i < crossedOut.length; i++)
            if (notCrossed(i))
                count++;
        return count;
    }
}
```

\- 첫번째 주석은 중복이라 하기 쉽다. 하지만 해당 주석을 통해 알고리즘의 이해가 쉬워진다 생각해서 남겼다.

\- 두번째 주석은 거의 확실히 필요하다. 루프 한계값으로 제곱근을 사용한 이유를 설명하기 때문이다.

\- 저자는 변수 이름을 바꾸거나 코드 구조를 조정해 이유를 명확하게 설명할 방법을 찾지 못했다.
