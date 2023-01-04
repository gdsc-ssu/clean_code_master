# 9장 단위 테스트

애자일과 TDD 덕택에 단위 테스트를 자동화하는 프로그래머들이 많아졌지만, **제대로 된 테스트 케이스를 작성**해야 한다는 중요한 사실을 놓치고 있다.

<br>

## TDD 법칙 세 가지

> TDD 기본 규칙) 실제 코드를 작성하기 전에 단위 테스트부터 작성한다.
>
- **첫째 법칙**: 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.
- **둘째 법칙**: 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
- **셋째 법칙**: 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.

​	→ 이렇게 일하면 실제 코드를 사실상 전부 테스트하는 테스트 케이스가 나오게 되는데, 방대한 테스트 코드는 심각한 관리 문제를 유발하기도 한다.

<br>

## 깨끗한 테스트 코드 유지하기

실제 코드가 진화하면 테스트 코드도 변화해야 한다. 그런데 테스트 코드가 지저분하고, 복잡할수록 실제 코드를 작성하는 시간보다 테스트 케이스를 추가하고 변경하는 시간이 더 걸릴 수 있다.

→ **테스트 코드는 실제 코드 못지 않게 중요하므로 깨끗하게 작성해야 한다.**

<br>

### 테스트는 유연성, 유지보수성, 재사용성을 제공한다

테스트 케이스가 있으면 **변경**이 쉬워지기 때문이다. 
테스트 커버리지가 높아질수록 변경에 대한 공포는 줄어든다.

→ 테스트 코드가 지저분하면 코드를 변경하는 능력과 코드 구조를 개선하는 능력이 떨어진다. 결국 테스트 코드를 잃어버리고, 실제 코드도 망가진다.

<br>

## 깨끗한 테스트 코드

- **가독성은 실제 코드보다 테스트 코드에 더욱 중요하다.**

- 가독성을 높이기 위해 명료성, 단순성, 풍부한 표현력이 필요하다.

- 테스트 코드는 최소의 표현으로 많은 것을 나타내야 한다.

<br>

9-1 테스트 케이스는 addPage와 assertSubString을 부르느라 중복되는 코드가 매우 많으며, 자질구레한 사항이 너무 많아 테스트 코드의 표현력이 떨어져 읽는 사람을 고려하지 않는다.

```js
// 9-1
// jest 라이브러리
describe('SerializedPageResponderTest', () => {
  test('testGetPageHieratchyAsXml', () => {
    crawler.addPage(root, PathParser.parse("PageOne"));
    crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
    crawler.addPage(root, PathParser.parse("PageTwo"));
    request.setResource("root");
    request.addInput("type", "pages");
    const responsder = new SerializedPageResponcder();
    const response = responder.makeResponse(
      new FitNesseContext(root), request
    );
    const xml = response.getContent();
    expect(response.getContentType()).toBe("text/xml");
    expect(xml).toContain("<name>PageOne</name>");
    expect(xml).toContain("<name>PageTwo</name>");
    expect(xml).toContain("<name>ChildOne</name>");
  });
  test('testGetPageHieratchyAsXmlDoesntContainSymbolicLinks', () => {
    const pageOne = crawler.addPage(root, PathParser.parse("PageOne"));
    crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
    crawler.addPage(root, PathParser.parse("PageTwo"));
    const data = pageOne.getData();
    const properties = data.getProperties();
    const symLinks = properties.set(SymbolicPage.PROPERTY_NAME);
    symLinks.set("SymPage", "PageTwo");
    pageOne.commit(data);
    request.setResource("root");
    request.addInput("type", "pages");
    const responsder = new SerializedPageResponcder();
    const response = responder.makeResponse(
      new FitNesseContext(root), request
    );
    const xml = response.getContent();
    expect(response.getContentType()).toBe("text/xml");
    expect(xml).toContain("<name>PageOne</name>");
    expect(xml).toContain("<name>PageTwo</name>");
    expect(xml).toContain("<name>ChildOne</name>");
    expect(xml).not.toContain("SymPage");
  });
  test('testGetDataAsHtml', () => {
    crawler.addPage(root, PathParser.parse("PageOne"), "test page");
    request.setResource("TestPageOne");
    request.addInput("type", "data");
    const responsder = new SerializedPageResponcder();
    const response = responder.makeResponse(
      new FitNesseContext(root), request
    );
    const xml = response.getContent();
    expect(response.getContentType()).toBe("text/xml");
    expect(xml).toContain("test page");
    expect(xml).toContain("<Test");
  });
})
```

테스트와 무관하여 테스트 코드의 의도를 흐리는 코드도 보인다.

- PathParser는 문자열을 pagePath 인스턴스로 변환하고, 이는 웹 로봇(crawler)이 사용하는 객체이므로 필요 없다.
- responder 객체를 생성하는 코드와 response를 수집해 변환하는 코드 또한 필요 없다.

<br>

9-2는 개선한 코드로, 좀 더 깨끗하고 이해하기 쉽다.

```js
// 9-2
describe('SerializedPageResponderTest', () => {
  test('testGetPageHieratchyAsXml', () => {
    makePages("PageOne", "PageOne.ChildOne", "PageTwo");
    submitRequest("root", "type:pages");
    assertResponseIsXML();
    assertResponseContains(
    	"<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
    );
  });
  test('testGetPageHieratchyAsXmlDoesntContainSymbolicLinks', () => {
    const page = makePage("PageOne");
    makePages("PageOne.ChildOne", "PageTwo");
    
    addLinkTo("PageOne.ChildOne", "PageTwo");
    submitRequest("root", "type:pages");
    assertResponseIsXML();
    assertResponseContains(
    	"<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
    );
    assertResponseDoesNotContain("SymPage");
  });
  test('testGetDataAsHtml', () => {
    makePageWithContent("TestPageOne", "test page");
    
    submitRequest("TestPageOne", "type:data");
    
    assertResponseIsXML();
    assertResponseContains("test page", "<Test");
  });
})
```

BUILD-OPERATE-CHECK 패턴이 위와 같은 테스트 구조에 적합하다.

각 테스트는 명확히 세 부분으로 나눠진다.

1. 테스트 자료를 만든다.
2. 테스트 자료를 조작한다.
3. 조작한 결과가 올바른지 확인한다.

<br>

### 도메인에 특화된 테스트 언어

9-2는 도메인에 특화된 언어(DSL)로 테스트 코드를 구현하는 기법을 보여준다. 흔히 사용하는 시스템 조작 API를 사용하는 대신, API 위에다 함수와 유틸리티를 구현한 후 그 함수와 유틸리티를 사용하므로 테스트 코드를 짜기도 읽기도 쉬워진다.

이렇게 구현한 함수와 유틸리티는 테스트 코드에서 사용하는 특수 API가 되어 테스틀르 구현하는 당사자와 나중에 테스트를 읽어볼 독자를 도와주는 테스트 언어다.

<br>

### 이중 표준

테스트 API 코드에 적용하는 표준은 실제 코드에 적용하는 표준과 확실히 다르다.

단순하고, 간결하고, 표현력이 풍부해야 하지만, **실제 코드만큼 효율적일 필요는 없다.**

<br>

9-3 코드는 세세한 사항이 아주 많아 테스트 코드를 읽기 어렵다.

```js
// 9-3
test('turnOnLoTempAlarmAtThreashold', () => {
  hw.setTemp(WAY_TOO_COLD);
  expect(hw.heaterState()).toBeTruthy();
  expect(hw.blowerState()).toBeTruthy();
  expect(hw.coolerState()).toBeFalsy();
  expect(hw.hiTempAlarm()).toBeFalsy();
  expect(hw.loTempAlarm()).toBeTruthy();
})
```

<br>

아래는 불필요한 코드를 숨겨 가독성을 크게 높인 코드다.

```js
// 9-4
test('turnOnLoTempAlarmAtThreashold', () => {
  wayTooCold();
  expect(hw.getState()).toBe("HBchL");
});
```
<br>
물론 때때로 "그릇된 정보를 피하라"라는 규칙의 위반에 가까운 테스트 코드를 작성하여, 테스트 코드를 이해하기 쉽게 만들어줄 수 있다.

```js
// 9-5
describe('EnvironmentControllerTest', () => {
  test('turnOnCoolerAndBlowerIfTooHot', () => {
    tooHot();
    expect(hw.getState()).toBe("hBChl");
  })
  test('turnOnHeaterAndBlowerIfTooCold', () => {
    tooCold();
    expect(hw.getState()).toBe("HBchl");
  })
  test('turnOnHiTempAlarmAtThreshold', () => {
    wayTooHot();
    expect(hw.getState()).toBe("hBCHl");
  })
  test('turnOnLoTempAlarmAtThreshold', () => {
    wayTooCold();
    expect(hw.getState()).toBe("HBchL");
  })
})
```
<br>

실제 환경에서는 절대로 안되지만, 테스트 환경에서는 전혀 문제 없는 방식이 있다. 대개 메모리나 CPU 효율과 관련이 있는 경우다. 코드의 깨끗함과는 **철저히** 무관하다.
<br>

## 테스트 당 assert 하나
assert 문이 단 하나인 함수는 결론이 하나라서 코드를 이해하기 쉽고 빠르다.
하지만 9-2코드에서 "출력이 XML이다"와 "특정 문자열을 포함한다"는 assert 문 두 개를 하나로 병합하는 방식은 불합리해 보인다. 하지만 9-7처럼 테스트를 두 개로 쪼개 각자가 assert를 수행하면 된다.

```js
// 9-7
// jest에서는 beforeEach 메서드를 이용해 아래 중복 코드 문제를 추가적으로 개선할 수 있다.
describe('testGetPageHieratchy', () => {
  test('testGetPageHieratchyAsXml', () => {
    makePages("PageOne", "PageOne.ChildOne", "PageTwo");
    whenRequestIsIssued("root", "type:pages");
    thenResponseShouldBeXML();
  });
  test('testGetPageHierarcyHasRightTags', () => {
    makePages("PageOne", "PageOne.ChildOne", "PageTwo");
    whenRequestIsIssued("root", "type:pages");
    thenResponseShouldContain(
      "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
    );
  });
})
```
하지만 위처럼 테스트를 분리하면 중복되는 코드가 많아진다.
<br>

물론 중복을 제거하는 방법이 여러가지가 있겠지만, 이것저것 감안해보면 9-2처럼 여러 assert 문을 넣어주는게 좋기도 하다. 단지 assert 문 개수는 최대한 줄이는게 좋다는 생각이다.
<br>

### 테스트 당 개념 하나
**"테스트 함수마다 한 개념만 테스트하라"**는 규칙이 더 좋다.
여러 개념을 한 함수로 몰아 넣으면 독자가 각 절이 거기에 존재하는 이유와 각 절이 테스트하는 개념을 모두 이해해야 한다.
<br>

9-8은 독자적인 개념 세 개를 테스트하므로 독자적인 테스트 세 개로 쪼개야 마땅하다.
```js
// 9-8
describe('testAddMonths', () => {
  let d1;
  beforeEach(() => {
    d1 = SerialDate.createInstance(31, 5, 2004);
  });
  
  test('add 1 month & finish with 30', () => {
    const d2 = SerialDate.addMonths(1, d1);
    expect(d2.getDayOfMonth()).toBe(30);
	expect(d2.getMonth()).toBe(6);
    expect(d2.getYYYY()).toBe(2004);
  });
  
  test('add 2 months & finish with 31', () => {
    const d3 = SerialDate.addMonths(2, d1);
    expect(d3.getDayOfMonth()).toBe(31);
	expect(d3.getMonth()).toBe(7);
    expect(d3.getYYYY()).toBe(2004);
  });
  
  test('add 1 months & finish with 30', () => {
    const d4= SerialDate.addMonths(2, SerialDate.addMonths(1, d1));
    expect(d4.getDayOfMonth()).toBe(30);
	expect(d4.getMonth()).toBe(7);
    expect(d4.getYYYY()).toBe(2004);
  });
})
```
목록 9-8은 각 절에 assert 문이 여럿이라는 사실이 문제가 아니다. 한 테스트 함수에서 여러 개념을 테스트한다는 사실이 문제다.
<br>

### 규칙
- **개념 당 assert 문 수를 최소로 줄여라**
- **테스트 함수 하나는 개념 하나만 테스트하라**
<br>

## F.I.R.S.T
깨끗한 테스트는 다음 다섯 가지 규칙을 따른다.
- **Fast**: 테스트는 자주 돌려야하므로 빨라야 한다.
- **Independent**: 각 테스트는 독립적으로 그리고 어떤 순서로 실행해도 괜찮아야 한다.
- **Repeatable**: 테스트는 어떤 환경에서도 반복 가능해야 한다.
- **Self-Validating**: 테스트는 bool 값으로 결과를 내야 한다. (성공 or 실패)
- **Timely**: 단위 테스트는 실제 코드를 구현하기 직전에 구현한다.
<br>

## 결론
테스트 코드는 실제 코드의 유연성, 유지보수성, 재사용성을 보존하고 강화하기 때문에 실제 코드만큼 중요하다.
**그러므로 테스트 코드를 지속적으로 깨끗하게 관리해야 한다.**
