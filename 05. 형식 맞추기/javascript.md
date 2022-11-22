## 5장. 형식 맞추기
프로그래머는 형식을 깔끔하게 맞춰 코드를 작성해야 한다.    
코드 형식을 맞추기 위해 **간단한 규칙**을 정하고 모두가 그 규칙을 따라야 한다.    

### ✅ 형식을 맞추는 목적
코드 형식은 **중요**하다.    
**코드 형식**은 **의사소통**의 일환이며, 의사소통은 **전문 개발자의 일차적인 의무**다.    
맨 처음 잡아놓은 구현 스타일과 가독성 수준은 **유지보수 용이성과 확장성**에 계속 영향을 미친다.    

### ✅ 적절한 행 길이를 유지하라
자바의 파일 크기 : 클래스 크기와 밀접.
<img width="427" src="https://user-images.githubusercontent.com/66112716/202843818-40e3122e-4572-4eba-8eef-65014fc8218b.png">
- 상자를 관통하는 선 : 각 프로젝트에서의 최대 파일 길이와 최소 파일 길이    
→ 500줄이 넘지 않고 대부분 200줄 정도인 파일으로 커다란 시스템을 구축할 수 있음을 명시
- 일반적으로 **큰 파일보다 작은 파일이 이해하기 쉬움**

#### ▶️ 신문 기사처럼 작성하라
독자는 위 → 아래로 기사를 읽는다.
- **최상단** : 기사를 몇 마디로 요약하는 표제
   - 독자 : 표제를 보고 기사를 읽을지 말지 결정
- **첫 문단** : 전체 기사 내용의 요약
   - 세세한 사실은 숨기고 커다란 그림을 보여줌
   - 읽으며 내려갈수록 날짜, 이름, 발언, 주장, 기타 세부사항이 나옴
   
소스파일 역시 신문 기사와 비슷하게 작성한다.
- **이름** : 간단하면서도 설명이 가능하게 지을 것
   - 이름만 보고도 올바른 모듈을 살펴보고 있는지 아닌지 판단할 정도로 신경써서 지을 것
- **소스파일 첫 부분** : 고차원 개념과 알고리즘 설명
   - 아래로 내려갈수록 의도를 세세하게 묘사할 것
- **마지막** : 가장 저차원 함수 & 세부 내역을 적을 것

>
💡 신문은 다양한 기사로 이뤄지며, 대다수 기사가 아주 짧다.    
  어떤 기사는 조금 길지만, 한 면을 채우는 기사는 거의 없다.    
  신문이 읽을 만한 이유는 여기에 있다.    

#### ▶️ 개념은 빈 행으로 분리하라
대부분의 코드 : `왼쪽` → `오른쪽`, `위` → `아래` 방향으로 읽힌다.    
- **각 행** : 수식 or 절
- **일련의 행 묶음** : 완결된 생각 하나를 표현
   - 생각 사이는 빈 행을 넣어 분리
```java
package fitnesse.wikitext.widgets;

import java.util.regex.*;

public class BoldWidget extends ParentWidget {
 // pass
};

public BoldWidget(Parent parent, String text) throws Exception {
 // pass
};

public String render() throws Exception {
 // pass
}
```
- 위 코드의 문제점
   - 패키지 선언부, import문, 각 함수 사이에 빈 행이 들어감
   - 간단한 규칙이나 코드의 세로 레이아웃에 심오한 영향을 미침
   - ✓ **빈 행** : 새로운 개념을 시작한다는 시각적 단서
   
```java
package fitnesse.wikitext.widgets;
import java.util.regex.*;
public class BoldWidget extends ParentWidget {
 // pass
};
public BoldWidget(Parent parent, String text) throws Exception {
 // pass
};
public String render() throws Exception {
 // pass
}
```
빈 행을 뺄 경우 코드의 가독성이 떨어져 암호처럼 보인다.    
빈 행의 삽입 여부에 따라 **행 묶음의 분리**효과를 얻을 수 있다.    
빈 행을 삽입하지 않을 경우, **코드 전체가 한 덩어리**로 보인다.    

#### ▶️ 세로 밀집도
- 줄바꿈 : 개념 분리 의미
- 세로 밀집도 : 연관성 의미    
   → 세로 밀집도 : 서로 밀접한 코드 행은 세로로 가까이 놓아야 한다는 뜻
   <br />
- 의미 없는 주석으로 두 인스턴스 변수를 떨어뜨려 놓은 예시 (5-3)
```js
class ReporterConfig {
  /**
  * 리포터 리스너의 클래스 이름
  */
  constructor(m_className, m_properties) {
    this.m_className = m_className;
    this.m_properties = [];
  }
  
  /**
  * 리포터 리스너의 속성
  */
  addProperty(property) {
    m_properties.append(property);
  }
}
```
- 5-3 예시 리팩토링 코드
   - 코드가 **한눈**에 들어옴
   - 변수 2개에 메서드가 1개인 클래스라는 사실이 드러남
```js
class ReporterConfig {
  constructor(m_className, m_properties) {
    this.m_className = m_className;
    this.m_properties = [];
  }
  
  addProperty(property) {
    m_properties.append(property);
  }
}
```

#### ▶️ 수직 거리
시스템이 무엇을 하는지 이해하고 싶은데,     
이 조각 저 조각이 어디에 있는지 찾고 기억하는데 시간과 노력이 소모된다.

**서로 밀접한 개념은 세로로 가까이 둬야 한다.**    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; → 두 개념이 서로 다른 파일에 속할 경우 규칙이 통하지 않는다.    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; → 타당한 근거가 없다면 서로 밀접한 개념은 한 파일에 속해야 마땅하다.    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; → 위 이유가 java의 `protected` 변수를 피해야 하는 이유 중 하나

- 같은 파일에 속할 정도로 밀접한 두 개념은 **세로 거리의 연관성**을 표현
   - **연관성** : 한 개념을 이해하는 데 다른 개념이 중요한 정도
- 연관성이 깊은 두 개념이 멀리 떨어져 있을 경우
   - 코드를 읽는 사람이 소스파일과 클래스를 여기저기 뒤져야 함
<br />

- **변수 선언**
   - 변수는 사용하는 위치에 **최대한 가까이** 선언
   - 아래 예제의 함수는 매우 짧으므로 **지역 변수는 각 함수 맨 처음에 선언**

```js
function readPreferences() {
  let is = null;
  try {
    //pass
  }
  catch (e) {
    try {
      if (is != null) is.close();
    }
    catch(e1) {
    }
  }
}
```

- **루프를 제어하는 변수** : 루프 문 내부에 선언
```js
function countTestCases() {
  let count = 0;
  forEach(each => count += each.countTestCases());
  return count;
}
```

- 긴 함수에서 블록 상단이나 루프 직전에 변수를 선언하는 사례
```java
for (XmlTest test : m_suite.getTests()) {
	TestRunner tr = m_runnerFactory.newTestRunner(this.test);
	tr.addListener(m_textReporter);
	m_testRunners.add(tr);

	invoker = tr.getInvoker();
    
    for (ITestNGMethod m : tr.getBeforeSuiteMethods()) {
    	beforeSuiteMethods.put(m.getMethod(), m);
    }
    
    for (ITestNGMethod m : tr.getAfterSuiteMethods()) {
    	afterSuiteMethods.put(m.getMethod(), m);
    }
}
// ...
```

- **인스턴스 변수**
   - 클래스 맨 처음에 선언
   - 변수간 세로로 거리를 두지 않음
   - 잘 설계한 클래스는 클래스의 많은 메서드가 인스턴스 변수를 사용하기 떄문

   - 인스턴스 변수를 선언하는 위치에 대한 논쟁 ↑
   - `C++` : 모든 인스턴스 변수를 클래스 마지막에 선언하는 **가위 규칙** 적용
   - `Java` : 보통 클래스 맨 처음에 인스턴스 선언    
   → 중요점 : 잘 알려진 위치에 인스턴스 변수를 모은다는 사실    
   → 변수 선언을 어디서 찾을지 모두가 알고 있어야 함    
   <br />

- 코드를 읽다가 우연히 변수를 발견하게 되는 상황
```js
class TestSuite implements Test {
  constructor(theClass, name) {
  	this.theClass = theClass;
    this.name = name;
  }
  
  createTest(theClass, name) {
    // ...
  }
  
  warning(message) {
    // ...
  }
  
  exceptionToString(t) {
   // ...
  }
  
  const fName;
  const fTests;
  
  TestSuite() {
  }
  
  TestSuite(theClass) {
    // ...
  }
  
  TestSuite(theclass, name) {
    // ...
  }
}
```

- **종속 함수*
   - 한 함수가 다른 함수를 호출할 경우 두 함수는 **세로로 가까이 배치**
   - 가능할 경우, **함수를 호출되는 함수보다 먼저 배치**    
   → 효과 : 자연스럽게 읽히는 프로그램
   - 일관적인 규칙을 적용할 경우 독자는 **방금 호출한 함수가 잠시 후에 정의되리라는 사실을 예측**할 수 있음
   <br />

- 아래 예제
   - 첫째 함수에서 가장 먼저 호출하는 함수가 바로 아래 정의됨
   - 다음으로 호출하는 함수는 그 아래에 정의됨
   - 호출되는 함수를 찾기 쉬워짐
   - 모듈 전체의 가독성이 높아짐
    
```js
clss WikiPageResponder implements SecureResponder {
  constructor(page, pageData, pageTitle, request, crawler) {
   	this.page = page;
   	this.pageData = pageData;
   	this.pageTitle = pageTitle;
   	this.request = request;
   	this.crawler = crawler;
  }
  
  makeResponse(context, request)
 	throw exception {
      const pageName = getPageNameOrDefault(request, "FrontPage");
      loadPage(pageName, context);
      if (page == null)
        return notFountResponse(context, request);
      else
        return makePageResponse(context);
  }
   
  getPageNameOrDefault(request, defaultPageName) {
    const pageName = request.getResource();
    if (pageName == null)
      pageName = defaultPageName;

    return pageName;
   }

  loadPage(resuorce, context)
  throw exception {
    const path = PathParser.paser(resource);
    this.crawler = context.root.getPageCrawler();
    this.page = crawler.getPage(context.root, path);
    if (page != null)
      this.pageData = this.page.getData();
  }

  notFoundResponse(context, request) 
  throw exception {
    return new NotFoundResponder().makeResponse(context, request);
  }

  makePageResponse(context)
  throw exception {
    this.pageTitle = PathParser.render(this.crawler.getFullPath(this.page));
    const html = makeHtml(context);

    const response = new SimpleResponse();
    response.setMaxAge(0);
    response.setContent(html);
    return response;
  }
}
```
- 위 코드는 상수를 적절한 수준에 두는 좋은 예제이다.
- `getPageNameOrDefault()` 내부에서 `"FrontPage"` 상수를 사용하는 방법도 가능
   - 하지만, 잘 알려진 상수가 적절하지 않은 저차원 함수에 묻히게 됨    
   → 상수를 알아야 마땅한 함수에서 실제로 사용하는 함수로 상수를 넘겨주는 방법이 더욱 좋음    

- **개념적 유사성**
   - 어떤 코드는 서로 끌어당김 → **개념적 친화도가 높기 떄문**
   - 친화도가 높을수록 코드를 가까이에 배치할 것

   - 친화도가 높은 요인
   1. 한 함수가 다른 함수를 호출해 생기는 직접적인 종속성
   1. 변수와 그 변수를 사용하는 함수
   1. 비슷한 동작을 수행하는 일군의 함수

- 개념적 유사성의 좋은 예시 코드
```js
class Assert {
    assertTrue(message, condition) {
        if (!condition) fail(message);
    }

    assertTrue(condition) {
        assertTrue(null, condition);
    }

    asertFalse(message, condition) {
        assertTrue(message, !condition);
    }

    assetFalse(condition) {
        assertFalse(null, condition);
    }
}
```
- 위 예제는 개념적인 친화도가 매우 높음
   - 명명법이 똑같으며 기본 기능이 유사하고 간단함
   - 서로가 서로를 호출하는 관계는 부차적 요인
   - 종속적인 관계가 없어도 가까이 배치할 함수들임

#### ▶️ 세로 순서
- 일반적으로 함수 호출 종속성은 **아래 방향**으로 유지
- 함수를 호출하는 함수보다 나중에 배치
   - 소스 코드 모듈이 `고차원` → `저차원`으로 자연스럽게 내려감
- 신문 기사와 마찬가지로 **가장 중요한 개념을 가장 먼저 표현**함
   - 표현 시 : **세세한 사항을 최대한 배제할 것**
   - 세세한 사항 : 가장 마지막에 표현    
   → 독자가 소스 파일에서 첫 함수 몇개만 읽어도 개념 파악이 용이해짐 

#### ▶️ 가로 형식 맞추기
<img width="466" alt="스크린샷 2022-11-21 오후 5 25 06" src="https://user-images.githubusercontent.com/66112716/203001189-eca76a2d-18fc-491e-94c4-6c94a82d1c1c.png">

- 프로젝트 7개에서 조사한 행 길이 분포 그래프    
→ 프로그래머는 명백하게 **짧은 행을 선호**함
- 따라서 **짧은 행이 바람직**함

- 예전 : 오른쪽으로 스크롤할 필요가 없도록 프로그래밍
- 최근 : 매우 커진 모니터    
   → 글꼴 크기를 줄여 200자까지도 한 화면에 들어감       
   → 추천하지 않는 방식     
- 120자 정도의 행 길이 제한

#### ▶️ 가로 공백과 밀집도
가로로는 **공백**을 사용해 **밀접한 개념과 느슨한 개념을 표현**함    
```js
function measureLine(line) {
    lineCount++;
    const lineSize = line.length();
    const totalChars += lineSize;
    lineWidthHistogram.addLine(lineSize, lineCount);
    recordWidestLine(lineSize);
}
```
- 할당 연산자의 강조를 위해 앞뒤에 공백을 준 예시 코드
   <br/>
- 할당문 : 왼쪽 요소와 오른쪽 요소가 분명히 나뉨
   - 공백을 넣을 시 **두가지 주요 요소가 확실히 나뉨**이 더욱 분명해짐
   <br/>
- 함수 이름과 이어지는 괄호 사이에는 공백을 넣지 않음
   - 함수와 인수는 서로 밀접하기 때문
- 공백을 넣으면 한 개념이 아니라 별개로 보임
   - 함수 호출 코드에서 괄호 안 **인수** : 공백으로 분리
   - 쉼표를 강조해 **인수가 별개**라는 사실을 보여주기 위함
   <br/>
- **연산자 우선순위 강조**를 위해서도 공백 사용
```js
class Quadatic {
    root1(a, b, c) {
        let determinant = determinant(a, b, c);
        return (-b + Math.sqrt(determinant)) / (2*a);
    }

    root2(a, b, c) {
        let determinant = determinant(a, b, c);
        return (-b + Math.sqrt(determinant)) / (2*a);
    }

    determinant(a, b, c) {
        return b*b - 4*a*c;
    }
}
```
- 수식을 읽기에 아주 편한 예제
   - 승수 사이 공백 X
   - 곱셈 : 우선순위 가장 높음
   - 항 사이 : 공백 들어감
   - 덧셈 & 뺄셈 : 우선순위가 곱셉보다 낮음
   <br/>
- 코드 형식을 자동으로 맞춰주는 도구 : 대다수가 **연산자 우선순위를 고려하지 못함**
   - 수식에 똑같은 간격 적용
   - 위와 같은 공백을 넣어줘도 도구에서 없애는 경우가 흔함

#### ▶️ 가로 정렬
```java
public class FitNesseExpediter implements ResponseSender {
    private   Socket          socket;
    private   InputStream     input;
    private   OutputStream    output;
    private   Request         request;
    private   Response        response;
    private   FitNesseContext context;
    protected long            requestParsingTimeLimit;
    private   long            requestProgress;
    private   long            requestParsingDeadline; 
    private   boolean         hasError;

    public FitNesseExpeditor(Socket s,
                             FitNesseContext context) throws Exception
    {
      this.context =            context;
      socket =                  s;
      input =                   s.getInputStream();
      output =                  s.getOutputStream();
      requestParsingTileLimit = 10000;
    }
}
```
- 위 코드 : 유용하지 않음
- 코드가 엉뚱한 부분을 강조해 진짜 의도가 가려짐
   - 변수 유형은 무시하고 변수 이름부터 읽게 됨
   - 할당 연산자보다 오른쪽 피 연산자에 눈이 감
   - 코드 형식을 자동으로 맞춰주는 도구는 대다수가 **위와 같은 정렬을 무시함**
   <br/>
- 정렬하지 않을 경우 오히려 중대한 결함을 찾기 쉬움
- 정렬이 필요할 정도로 목록이 길 경우,
   - 문제는 **목록의 길이**이지, 정렬 부족이 아님
   <br/>
- 아래 코드와 같이 선언부가 길 경우 클래스를 쪼개야 함
```java
public class FitNesseExpediter implements ResponseSender {
    private Socket socket;
    private InputStream input;
    private OutputStream output;
    private Request request;
    private Response response;
    private FitNesseContext context;
    protected long requestParsingTimeLimit;
    private long requestProgress;
    private long requestParsingDeadline; 
    private boolean hasError;

    public FitNesseExpeditor(Socket s, FitNesseContext context) throws Exception
    {
      this.context = context;
      socket = s;
      input = s.getInputStream();
      output = s.getOutputStream();
      requestParsingTileLimit = 10000;
    }
}
```

#### ▶️ 들여쓰기
소스파일은 윤곽도와 계층적으로 유사    
- **파일 전체에 적용되는 정보** & **파일 내 개별 클래스에 적용되는 정보**    
- 클래스 내 **각 메서드에 적용되는 정보** & **블록 내 블록에 재귀적으로 적용되는 정보**
- 계층에서의 각 수준 : **이름을 선언하는 범위이자 선언문과 실행문을 해석하는 범위**       
💡 범위로 이뤄진 계층의 표현을 위해 **코드 들여쓰기**를 사용   

- **들여쓰는 정도** : 계층에서 코드가 자리잡은 수준에 비례    
- **클래스 정의와 같은 파일 수준인 문장**은 들여쓰기 적용 X     
- **클래스 내 메서드**는 클래스보다 한 수준 들여쓰기   
- **메서드 코드**는 메서드 선언보다 한 수준 들여쓰기     
- **블록 코드**는 블록을 포함하는 코드보다 한 수준 들여쓰기      

- 프로그래머는 이런 **들여쓰기 체계에 크게 의존함**    
- **왼쪽으로 코드를 맞춰** 코드가 속하는 **범위**를 시각적으로 표현    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; → 이 범위에서 저 범위로 재빨리 이동하기 쉬워짐         
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; → 현재 상황과 무관한 `if문`/`while문` 코드를 일일이 살펴볼 필요가 없음        
소스파일 왼쪽을 훑으며 `새 메서드`, `새 변수`, `새 클래스`를 찾음     

```js
class FitNesseServer implements SocketServer { constructor(context) { this.context = context; } serve(s) { serve(s, 10000); } serve(s, requestTimeout) { try { const sender = new FitNesseExpediter(s, context); sender.setRequestParsingTimeLimit(requestTimeout); sender.start();} catch(e) {e.printStackTrace(); } } }
```
vs    
```js
class FitNesseServer implements SocketServer {
    constructor(context) { 
        this.context = context; 
    } 
    serve(s) { 
        serve(s, 10000);
    } 
    serve(s, requestTimeout) { 
        try { 
            const sender = new FitNesseExpediter(s, context); 
            sender.setRequestParsingTimeLimit(requestTimeout); 
            sender.start();
        } 
        catch(e) { e.printStackTrace(); }
        } 
}
```
- 들여쓰기를 한 파일의 구조는 **한 눈에 들어옴**
- 변수, 생성자 함수, 접근자 합수, 메서드가 금방 보임
- 들여쓰기를 한 코드 : 쉽게 분석 및 이해 가능
- 들여쓰기를 하지 않은 코드 : 열심히 분석하지 않는 한 빠른 이해가 어려움

- **들여쓰기 무시하기**
   - 간단한 `if문`, `짧은 while문`, `짧은 함수`에서의 들여쓰기 규칙을 무시하고자 하는 유혹이 생김
   - 한 행에 범위를 뭉뚱그린 코드를 피할 것
```js
class CommentWidget extends TextWidget {
    constructor(REGEXP) { this.REGEXP = "^#[^\r\n]*(?:(?:\r\n)|\n|\r)?"; }
    CommentWidget(parent, text) { super(parent, text); }
    render() { throw ""; }
}
```
- 들여쓰기로 범위를 제대로 표현한 코드 (위 코드 리팩터링 버전)
```js
class CommentWidget extends TextWidget {
    constructor(REGEXP) { 
        this.REGEXP = "^#[^\r\n]*(?:(?:\r\n)|\n|\r)?"; 
    }

    CommentWidget(parent, text) { 
        super(parent, text); 
    }

    render() { 
        throw ""; 
    }
}
```

#### ▶️ 가짜 범위
빈 블록을 올바로 들여쓰고 괄호로 감쌀 것     
```js
// 좋지 않은 코드
while(dis.read(buf, 0, readBufferSize) != -1)
;
```

- **팀 규칙**
   - 팀은 한가지 규칙에 합의해야 한다.
   - 모든 팀원은 그 규칙을 따라야 한다.
<br/>

- 팀 규칙에 고려할 부분    
   - 어디에 괄호를 넣을 것인가?    
   - 들여쓰기는 몇 자로 할 것인가?    
   - 클래스와 변수와 메서드 이름은 어떻게 지을 것인가?    
   - etc.    

좋은 소프트웨어 시스템은 읽기 쉬운 문서로 이루어짐       
스타일은 일관적으로 매끄려워야 함           
**💡 한 소스 파일에서 봤던 형식이 다른 소스 파일에도 쓰이리라는 신뢰감을 독자에게 줘야 함**      
온갖 스타일을 뒤섞어 소스코드를 필요 이상으로 복잡하게 만드는 실수는 피할 것     

- **밥 아저씨의 형식 규칙**
   - 코드 자체가 최고의 구현 표준 문서가 되는 예시
```js
class CodeAnalyzer implements JavaFileAnalysis {
    constructor(lineCount, maxLineWidth, widestLineNumber, lineWidthHistogram, totalChars) {
        this.lineCount = lineCount;
        this.maxLineWidth = maxLineWidth;
        this.widestLineNumber = widestLineNumber;
        this.lineWidthHistogram = lineWidthHistogram;
        this.totalChars = totalChars;
    }

    CodeAnalyzer() {
        this.lineWidthHistogram = new LineWidthHistogram();
    }

    findJavaFiles(parentDirectory) {
        const files = [];
        findJavailes(parentDirectory, files);
        return files;
    }

    findJavaFiles(parentDirectory, files) {
        for (file in parentDirectory.listFiles()) {
            if (file.getName().endsWith(".java"))
                files.add(file);
            else if (file.isDirectory())
                findJavaFiles(file, files);
        }
    }

    analyzeFile(javaFile) {
        const br = new BufferReader(new FileReader(javaFiles));
        const line;
        while((line =  br.readLine()) != null)
            neasureLine(line);
    }

    measureLine(line) {
        this.lineCount++;
        const lineSize = line.length();
        this.totalChars += lineSize;
        this.lineWidthHistogram.addLine(lineSize, this.lineCount);
        recordWidestLine(lineSize);
    }

    recordWidestLine(lineSize) {
        if (lineSize > maxLineWidth) {
            this.maxLineWidth = lineSize;
            this.widestLineNumber = this.lineCount;
        }
    }

    getLineCount() {
        return this.lineCount;
    }

    getMatLineWidth() {
        return this.maxLineWidth;
    }

    getWidestLineNumber() {
        return this.getWidestLineNumber;
    }

    getLineWidthHistogram() {
        return this.lineWidthHistogram;
    }

    getMeanLineWidth() {
        return this.totalChars / this.lineCount;
    }

    getMedianLineWidth() {
        const sortedWidths = getSortedWidths();
        const cumulativeLineCount = 0;
        for (let width in sortedWidths) {
            cumulativeLineCount += lineCountForWidth(width);
            if (cumulatuveLineCount > this.lineCount/2)
                return width;
        }
        throw new Error("Cannot get here");
    }

    lineCountForWidth(width) {
        return this.lineWidthHistogram.getLineforWidth(width).size();
    }

    getSortedWidths() {
        const widths = this.lineWidthHistogram.getWidths();
        const sortedWidths = Array.from(width);
        sortedWidths.sort();
        return sortedWidths;
    }
}
```
