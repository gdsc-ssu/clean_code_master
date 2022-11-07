## 3. 함수

\- 프로그래밍 초창기에는 시스템을 루틴과 하위 루틴으로 나눴으나, 지금은 함수만 살아남았다.

\- 가장 기본적인 단위가 함수.

```python
# 좋지 않은 코드
def testableHtml(pageData:PageData, includeSuiteSetup:bool) -> str:
	try:
    	wikiPage = pageData.getWikiPage()
        buffer = StringBuffer()
        if pageData.hasAttribute("Test"):
        	if includeSuiteSetup:
            	suiteSetup = PageCrawlerImpl.getInheritedPage(SuiteResponder.SUITE_SETUP_NAME, wikiPage)
                if suiteSetup != NULL:
                	pagePath = suiteSetup.getPageCrawler().getFullPath(suiteSetup)
                	pagePathName = PathParser.render(pagePath)
                    buffer.append("!include -setup .")
                    buffer.append(pagePathName)
                    buffer.append("\n")
            setup = PageCralwerImpl.getInheritedPage("Setup", wikiPage)
            if setup != NULL:
            	setupPath = wikiPage.getPageCrawler().getFullPath(setup)
                setupPathName = PathParser.render(setupPath)
                buffer.append("!include -setup .")
                buffer.append(setupPathName)
                buffer.append("\n")
        buffer.append(pageData.getContent())
        if pageData.hasAttribute("Test"):
        	teardown = PageCrawlerImpl.getInheritedPage("TearDown", wikiPage)
            if teardown != NULL:
            	tearDownPath = wikiPage.getPageCralwer().getFullPath(teardown)
                String tearDownPathName = PathParser.render(tearDownPath)
                buffer.append("\n")
                buffer.append("!include -teardown .")
                buffer.append(tearDownPathName)
                buffer.append("\n")
            if includeSuiteSetup:
            	suiteTeardown = PageCrawlerImpl.getInheritedPage(SuiteResponder.SUITE_TEARDOWN_NAME, wikiPage)
            if suiteTeardown != NULL:
            	pagePath = suiteTeardown.getPageCrawler().getFullPath(suiteTeardown)
                pagePathName = PathParser.render(pagePath)
                buffer.append("!include -teardown .")
                buffer.append(pagePathName)
                buffer.append("\n")
                
    pageData.setContent(str(buffer))
    return pageData.getHtml()
```

\- 위 코드는 추상화 수준도 너무 다양하고, 코드도 너무 길다, ...

\- 아래 코드는 메서드 몇 개를 추출하고, 이름 몇 개를 변경하고, 구조를 조금 변경한 형태

```python
# 위 코드 리팩터링 버전
def renderPageWithSetupsAndTeardowns(pageData:PageData, isSuite:bool)->str:
	try:
    	isTestPage = pageData.hasAttribute("Test")
        if isTestPage:
        	testPage = pageData.getWikiPage()
            newPageContent = StringBuffer()
            includeSetupPages(testPage, newPageContent, isSuite)
            newPageContent.append(pageData.getContent())
            includeTeardownpages(testPage, newPageContent, isSuite)
            pageData.setContent(str(newPageContent))
   return pageData.getHTML()
```

### **작게 만들어라!**

\- 함수를 만드는 **첫째 규칙은 '작게!', 둘째 규칙은 '더 작게!'**

\- 증명할만한 근거는 없지만... 지금까지의 경험을 바탕으로 그리고 오랜 시행착오를 바탕으로 필자는 작은 함수가 좋다고 확신.

\- **각 함수가 이야기 하나를 표현할 수 있도록, 명백하게 구성해야 함.**

```python
# 리-리팩토링한 코드
def renderPageWithSetupsAndTeardowns(pageData:PageData, isSuite:bool) -> str:
	try:
    	if isTestPage(pageData):
        	includeSetupAndTeardownPages(pageData, isSuite)
    return pageData.getHtml()
```

#### 블록과 들여쓰기

\- if문, else문, while문 등에 들어가는 **블록은 한 줄이어야** 한다!

\- 바깥을 감싸는 함수(enclosing function)이 작아질 뿐 아니라, 블록 안에서 호출하는 함수 이름을 적절히 짓는다면 코드 이해도 쉬워짐

\- **중첩 구조가 생길만큼 함수가 커져서는 안 된다.** 

### **한 가지만 해라!**

\- **함수는 한 가지를 해야 한다. 그 한 가지를 잘 해야 한다. 그 한 가지만을 해야 한다.**

\- 사실 리-리팩토링한 코드는 세 단계를 하고 있다고 할 수 있는데, 이는 지정된 함수 이름 아래에서 추상화 수준이 하나임.

\- 우리가 함수를 만드는 이유는 **큰 개념을(함수 이름을) 다음 추상화 수준에서 여러 단계로 나눠 수행하기 위해서.**

\- 다른 표현이 아니라 **의미 있는 이름으로 다른 함수를 추출할 수 있다면 그 함수는 여러 작업을 하고 있다**고 할 수 있음.

#### 함수 내 섹션

\- 한 가지 작업만 하는 함수는 자연스럽게 섹션으로 나누기 어렵다.

### **함수 당 추상화 수준은 하나로!**

\- 함수가 '한 가지' 작업만 하려면 **함수 내 모든 문장의 추상화 수준이 동일해**야 한다.

\- **한 함수 내에 추상화 수준을 섞으면 코드를 읽는 사람이 헷갈린다**.. 특정 표현이 근본 개념인지 아니면 세부사항인지 구분하기 어렵기 때문

\- 근본 개념과 세부 사항을 뒤섞기 시작하면 사람들이 함수에 세부사항을 점점 더 추가하게 된다.

#### 위에서 아래로 코드 읽기: **내려가기** 규칙

\- **위에서 아래로 프로그램을 읽으면 함수 추상화 수준이 한 번에 한 단계씩 낮아진다.**

\- **TO(그냥 ~하려면의 의미) 문단을 읽듯이 프로그램이 읽혀야** 하며, 각 TO 문단은 현재 추상화 수준을 설명한다.

\- TO 문단을 읽어내려 가듯이 코드를 구현하면 추상화 수준을 일관되게 유지하기가 쉬워진다.

\- **각 함수는 일정한 추상화 수준을 유지**한다.

### **Switch문**

\- **switch문은 작게 만들기 어렵다.

\- **다형성(polymorphism)**을 이용하여 각 switch문을 저차원 클래스에 숨기고 절대로 반복하지 않는 방법이 있다.

```python
# python에는 switch-case라는 게 딱히 없고... if-elif-else로...
def calculatePay(e:Employee)->Money:
    etype = e.type
    if etype == "COMMISSIONED":
        return calculateCommissionedPay(e)
    elif etype == "HOURLY":
        return calculateHourlyPay(e)
    elif etype == "SALARIED":
        return calculateSalariedPay(e)
    else:
        raise(InvalidEmployeeType(e.type))
```

\- 위 코드의 문제점

  - 함수가 길다

  - **'한 가지' 작업만 수행하지 않는다**

  - **SRP(Single Responsibility Principle)**을 위반한다

  - **OCP(Open Closed Principle)**을 위반한다

  - 위 함수와 **구조가 동일한 함수가 무한정 존재할 수 있음**. (ex. isPayday(e:Employee, date:Date)와 deliverPay(e:Employee, pay:Money)

```python
# 위 코드의 문제점 해결
class Employee():
	def isPayday(self)->bool :
    	pass
    def calculatePay(self)->Money :
		pass
    def deliveryPay(self, pay:Money):
		pass

class EmployeeFactory():
	def makeEmployee(r:EmployeeRecord):
    	pass
        
class EmployeeFactoryImpl(EmployeeFactory):
	def makeEmployee(r:EmployeeRecord):
    	if r.type == "COMMISIONED":
        	return CommisionedEmployee(r)
        elif r.type == "HOURLY":
        	return HourlyEmployee(r)
        elif r.type == "SALARIED":
        	return SalariedEmployee(r)
        else:
        	raise Exception(InvalidEmployeeType(r.type))
```

### **서술적인 이름을 사용하라**

\- 함수가 하는 일을 좀 더 잘 표현하는, **서술적인 이름이 좋은 이름**이라고 할 수 있음.

\- 함수가 작고 단순할수록 서술적인 이름을 고르기가 쉬워짐

\- 때로는 긴 이름이 더 나을 때도 있음. **길고 서술적인 이름이 짧고 어려운 이름보다, 길고 서술적인 주석보다 긴 이름이 좋다**.

\- 함수 이름을 정할 때는 **여러 단어가 쉽게 읽히는 명명법** 사용하고, 이 단어들을 사용해 함수 기능을 잘 표현하는 이름을 선택.

\- **이름을 붙일 때는 일관성**이 있어야 한다.

  - 모듈 내에서 함수 이름은 같은 문구, 명사, 동사를 사용해야 한다. (includeSetupAndTeardownPages, includeSetupPages, ...)

### **함수 인수**

\- **함수에서 이상적인 인수 개수는 순서대로 0개(무항), 1개(단항), 2개(이항)**. 3개 이상은 피하는 것이 좋음

\- **인수는 개념을 이해하기 어렵게 만든다**. ( includeSetupPageInto(PageContent)보다는 includeSetupPage()가 더 이해하기 쉬움 )

\- 테스트 관점에서는 갖가지 인수 조합으로 함수를 검증해야 하기 때문에 더 어렵다.

\- 출력 인수는 입력 인수보다 더 어렵다. 

\- **최선은 입력 인수가 없는 경우이며, 차선은 입력 인수가 1개뿐인 경우**

#### 많이 쓰는 단항 형식

\- **함수에 인수 1개를 넘기는 이유** 두 가지

  - 1. **인수에 질문을 던지는 경우**. boolean fileExists("MyFile")

  - 2. **인수로 뭔가를 변환해 결과를 반환하는 경우**. InputStream fileopen("MyFile")

\- 함수 이름을 지을 때는 **두 경우를 분명히 구분**해야 하고, 언제나 **일관적인 방식으로 두 형식을 사용**해야 한다.

\- **이벤트**는 아주 유용한 단항 함수 형식. 입력 인수만 있고, 출력 인수는 없다.

  - 프로그램은 함수 호출을 이벤트로 해석해 입력 인수로 시스템 상태를 바꾼다. (ex. passwordAttemptFailesNtimes(int attempts))

  - **이벤트라는 사실이 코드에 명확히 드러나야 하기 때문에, 이름과 문맥을 주의해서 선택해야 한다.**

\- 단항 함수는 가급적 피해야 하며, 변환 함수에서 출력 인수 사용하면 안 됨.

\- 입력 인수를 그대로 돌려주는 함수라 할지라도 변환 함수 형식을 따르는 것이 변환 형태를 유지할 수 있기 때문에 좋음.

#### 플래그 인수

\- 플래그 인수는 추하다... (ㅡ\_ㅡ)

\- **함수로 bool값을 넘기는 관례는 함수가 한꺼번에 여러 가지를 처리한다고 대놓고 공표하는 것!이므로 정말 끔찍하다.**

#### 이항 함수

\- 인수가 2개인 함수는 인수가 1개인 함수보다 이해하기 어렵다. (writeField(name)은 writeField(outputStream, name)보다 쉬움)

\- 이항 함수가 적절한 경우도 있긴 하다...! 직교 좌표계 점을 표현할 때...

\- **이항 함수가 무조건 나쁜 건 아니지만 그만큼 위험이 따른다는 사실을 이해하고 가능하면 단항 함수로 바꾸도록 애써야 한다.**

#### 삼항 함수

\- **인수가 3개인 함수는 인수가 2개인 함수보다 훨씬 더 이해하기 어렵다** ~(당연한 거 아님?)~

\- assertEquals(1.0, amount, .001)은 그다지 음험하지 않은 삼항 함수. 삼항 함수로 사용할 가치가 충분하다.

#### 인수 객체

\- **인수가 2~3개 필요하다면 일부를 독자적인 클래스 변수로 선언할 가능성을 짚어봐야 한다.**

```python
def makeCircle(x:float, y:float, radius:float) -> Circle:
	pass
def makeCircle(center:Point, radius:float) -> Circle:
	pass
```

\- 위 코드에서 x와 y를 묶었듯이 변수를 묶어 넘기려면 이름을 붙여야 하므로 결국은 개념을 표현하게 된다.

#### 인수 목록

\- 때로는 인수 개수가 가변적인 함수도 필요하다.

\- **가변 인수를 취하는 모든 함수에 같은 원리가 적용**된다.

\- 가변 인수를 취하는 함수는 단항, 이항, 삼항 함수로 취급할 수 있지만, 이를 넘어서는 인수를 사용할 경우에는 문제가 있다.

#### 동사와 키워드

\- **함수의 의도나 인수의 순서와 의도를 제대로 표현하려면 좋은 함수 이름이 필수**다.

\- 단항 함수는 함수와 인수가 동사/명사 쌍을 이뤄야 한다. (write(name), 더 나은 이름은 writeField(name))

\- 함수 이름에 인수 이름을 넣는, 키워드를 추가하는 형식. (assertEquals보다 assertExceptedEqualsActual(expected, actual)이 좋음) -> 인수 순서를 기억할 필요가 없어진다.

### **부수 효과를 일으키지 마라**

\- 부수 효과는 거짓말이다.

\- **함수에서 한가지를 하겠다고 약속하고선 클래스 변수를 수정하거나, 함수로 넘어온 인수나 시스템 전역 변수를 수정**한다.

\- 많은 경우 **시간적인 결합(temporal coupling)**이나 **순서 종속성(order dependency)을 초래**한다.

```python
# UserValidator.py
class UserValidator:
	cryptographer = ""	# Cryptographer type
    
    def checkPassword(userName:str, password:str)->bool:
    	user = UserGateway.findByName(userName)
        if user != User.NULL:
        	codedPhrase = user.getPhraseEncodedByPassword()
            phrase = cryptographer.decrypt(codedPhrase, password)
            if "Valid Password" == phrase:
            	Session.initialize()
                return True
        return False
```

\- 위 코드에서 함수가 일으키는 부수 효과는 Session.initialize() 호출이다. checkPassword함수는 암호를 확인할 뿐.

\- 함수 이름만 보고 함수를 호출하는 사용자는 사용자를 인증하면서 기본 세션 정보를 지워버릴 위험에 처하게 됨.

\- 이런 부수 효과가 시간적인 결합을 초래한다.

\- checkPassword 함수는 특정 상황에서만, 세션을 초기화해도 괜찮은 경우에만 호출이 가능하다. 

\- 만약 **시간적인 결합이 필요하다면 함수 이름에 분명히 명시해야** 한다.

#### 출력 인수

\- 우리는 일반적으로 인수를 함수 입력으로 해석.

\- 선언부 appendFooter(report:str)를 봐야 호출부 appendFooter(s)에서의 s가 출력 인수라는 것을 알 수 있었음

\- **함수 선언부를 찾아보는 행위는 인지적으로 거슬린다는 뜻이므로 피해야 한다**.

\- 일반적으로 출력 인수는 피해야 한다. **함수에서 상태를 변경해야 한다면 함수가 속한 객체 상태를 변경하는 방식을 택해야**. 

### **명령과 조회를 분리하라!**

\- 함수는 뭔가를 수행하거나, 뭔가에 답하거나 둘 중 하나만 해야 한다. **객체 상태를 변경하거나, 객체 정보를 반환하거나 둘 중 하나.**

```python
def set(attribute:str, value:str) -> bool:
	pass
    
# ------------------------------------------

if set("username", "unclebob")) ...
```

\- 위의 함수는 이름이 attribute인 속성을 찾아 값을 value로 설정한 후 성공하면 true, 실패하면 false 반환.

\- "set"이라는 단어가 동사인지 형용사인지 분간하기 어려워 함수를 호출하는 코드만 봐서는 의미가 모호함

\- 함수를 구현한 개발자는 "set"을 동사로 의도했지만, if문 안에 넣고 보면 형용사로 느껴진다.

\- 진짜 해결책은 **명령과 조회를 분리**해 혼란을 애초에 뿌리뽑는 방법이다.

```python
if attributeExists("username"):
	setAttribute("username", "unclebob")
```

### **오류 코드보다 예외를 사용하라!**

\- **명령 함수에서 오류 코드를 반환하는 방식은 명령/조회 분리 규칙을 미묘하게 위반**한다.

```python
if deletePage(page) == E_OK:
	pass
```

\- 위 코드는 동사/형용사 혼란을 일으키지 않는 대신 여러 단계로 중첩되는 코드를 야기한다.

\- 오류 코드를 반환하면 호출자는 오류 코드를 곧바로 처리해야 한다는 문제에 부딪힌다.

```python
if deletePage(page) == E_OK:
	if registry.deleteReference(page.name) == E_OK:
    	if configKeys.deleteKey(page.name.makeKey()) == E_OK:
        	logging.info("page deleted")
        else:
        	logging.info("configKey not deleted")
    else:
    	logging.info("deleteReference from registry failed")
else:
	logging.info("delete failed")
    return E_ERROR
```

\- **오류 코드 대신 예외를 사용하면 오류 처리 코드가 원래 코드에서 분리되므로 코드가 깔끔해진다.**

```python
try:
	deletePage(page)
    registry.deleteReference(page.name)
    configKeys.deleteKey(page.name.makeKey())
catch (Exception as e):
	logging.info(e.getMessage())
```

#### Try/Catch 블록 뽑아내기

\- try/catch 블록은 원래 추하다... 코드 구조에 혼란을 일으키며 정상 동작과 오류 처리 동작을 뒤섞는다

\- 그러므로 **try/catch 블록을 별도 함수로 뽑아내는 편이 좋다.**

```python
def delete(page:Page):
	try:
    	deletePageAndAllReferences(page)
    catch (Exception as e):
    	logError(e)

def deletePageAndAllReferences(page:Page):
	deletePage(page)
    registry.deleteReference(page.name)
    configKeys.deleteKey(page.name.makeKey())
    
def logError(e:Exception):
	logging.info(e.getMessage())
```

\- 위에서 delete 함수는 모든 오류를 처리하기 때문에 코드를 이해하기 쉽다.

\- **정상 동작과 오류 처리 동작을 분리하면 코드를 이해하고 수정하기 쉬워진다.**

#### 오류 처리도 한 가지 작업이다

\- **오류를 처리하는 함수는 오류만 처리해야** 마땅하다.

\- 함수에 키워드 try가 있다면 함수는 try문으로 시작해 catch/finally 문으로 끝나야 한다.

#### Error.java 의존성 자석

\- 오류 코드를 반환한다는 이야기는, 클래스든 열거형 변수든, 어디선가 오류 코드를 정의한다는 뜻이다.

```java
public enum Error{
	OK,
    INVALID,
    NO_SUCH,
    LOCKED,
    OUT_OF_RESOURCES,
    WAITING_FOR_EVENT;
}
```

\- 위와 같은 클래스는 의존성 자석(magnet)이다.

\- Error enum이 변한다면 이를 사용하는 클래스 전부를 다시 컴파일하고 다시 배치해야 하기 때문에 클래스 변경이 어려워진다.

\- 그래서 새 오류 코드를 추가하는 대신 기존 오류 코드를 재사용하여 예외를 사용하게 된다.

\- **오류 코드 대신 예외를 사용**하면 새 예외는 Exception 클래스에서 파생되므로 **재컴파일/재배치 없이도 새 예외 클래스 추가 가능**

### **반복하지 마라!**

\- 중복은 문제다. 코드 길이가 늘어날 뿐 아니라 알고리즘이 변하면 여러 곳을 다 손봐야 하고, 어느 한 곳이라도 빠뜨릴 경우 오류 발생 가능

\- **중복을 없애면 모듈 가독성이 크게 높아짐**

\- 구조적 프로그래밍, AOP(Aspect Oriented Programming), COP(Component Oriented Programming) 모두 어떤 면에서 중복 제거 전략.

### **구조적 프로그래밍**

\- Dijkstra의 구조적 프로그래밍 원칙 : 모든 함수와 함수 내 모든 블록에 입구와 출구가 하나만 존재해야 한다. 함수는 return 문이 하나여야 하고, 루프 안에서 break, continue, goto 사용해선 안됨.

\- 함수를 작게 만든다면 간혹 return, break, continue를 여러 차례 사용해도 단일 입/출구 규칙보다 의도를 표현하기 쉬워져서 괜찮다. 

\- goto문은 큰 함수에서만 의미가 있으므로, 작은 함수에서는 피해야만 한다.

### **함수를 어떻게 짜죠?**

\- 처음에는 길고 복잡하고, 들여쓰기 단계도 많고 중복된 루프도 많지만 코드를 다듬고, 이름을 바꾸고, 함수를 만드는 과정 등을 거침으로써 좋은 함수를 얻을 수 있게 된다.

\- **처음부터 딱 나오는 건 아니기 때문에... 괜찮다! 수정하면 된다!**

### **결론**

\- 모든 시스템은 특정 응용 분야 시스템을 기술할 목적으로 프로그래머가 설계한 도메인 특화 언어로 만들어진다.

\- 함수는 그 언어에서 동사며, 클래스는 명사다.

\- master 프로그래머는 시스템을 구현할 프로그램이 아니라 풀어갈 이야기로 여긴다. 이것이 진짜 목표!

\- **작성하는 함수가 분명하고 정확한 언어로 깔끔하게 같이 맞아떨어져야 이야기를 풀어가기가 쉬워진다는 사실을 기억하길 바란다!**

```python
# 위에 import 부분 생략
class SetUpTeardownIncluder:
	def __init__(self):
    	self.pageData = 0	# PageData type
        self.isSuite = 0		# bool type
        self.testPage = 0	# WikiPage type
        self.newPageContent = ""
        self.pageCralwer = 0	# PageCrawler type
        
	def render(self, pageData:PageData)->str:
    	return self.render(pageData, False)
        
    def render(self, pageData:PageData, isSuite:boolean)->str:
    	return self.SetupTeardownIncluder(pageData).render(isSuite)
        
    def setUpTearDownIncluder(self, pageData:PageData):
    	self.pageData = pageData
        self.testPage = pageData.getWikiPage()
        self.pageCrawler = testPage.getPageCrawler()
        self.newPageContent = ""
        
    def render(self, isSuite:bool)->str:
    	self.isSuite = isSuite
        if self.isTestPage():
        	self.includeSetupAndTeardownPages()
        return self.pageData.getHtml()
        
    def isTestPage(self)->bool:
    	return self.pageData.hasAttribute("Test")
        
    def includeSetupAndTeardownPages(self):
    	self.includeSetupPages()
        self.includePageContent()
        self.includeTeardownPages()
        self.updatPageContent()
        
    def includeSetupPages(self):
    	if self.isSuite:
	        self.includeSuiteSetupPage()
        self.includeSetupPage()
        
    def includeSuiteSetupPage(self):
    	self.include(SuiteResponder.SUITE_SETUP_NAME, "-setup")
        
    def includeSetupPage(self):
    	self.include("SetUp", "-setup")
        
    def includePageContent(self):
    	self.newPageContent.append(pageData.getContent())
        
    def includeTeardownPages(self):
    	self.includeTearDownPage():
        if self.isSuite:
        	self.includeSuiteTeardownPage()
            
    def includeTeardownPage(self):
    	self.include("TearDown", "-teardown")
        
    def includeSuiteTeardownPage(self):
    	self.include(SuiteResponder.SUITE_TEARDOWN_NAME, "-teardown")
        
    def updatePageContent(self):
    	self.pageData.setContent(str(newPageContent))
        
    def include(self, pageName:str, arg:str):
    	self.inheritedPage = self.findInheritedPage(pageName)
        if self.inheritedPage != NULL:
        	self.pagePathName = self.getPathNameForPage(self.inheritedPage)
            self.buildIncludeDirective(self.pagePathName, arg)
            
    def findInheritedPage(self, pageName:str):
    	return self.PageCrawlerImpl.getInheritedPage(pageName, self.testPage)
        
    def getPathNameForPage(self, page:WikiPage):
    	self.pagePath = self.pageCrawler.getFullPath(page)
        return self.PathParser.render(self.pagePath)
        
    def buildIncludeDirective(self, pagePathName:str, arg:str):
    	self.newPageContent.append("\n!include ")
        self.newPageContent.append(arg)
        self.newPageContent.append(" .")
        self.newPageContent.append(pagePathName)
        self.newPageContent.append("\n")
```
