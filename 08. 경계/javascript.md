## 8. 경계
시스템에 들어가는 모든 소프트웨어를 직접 개발하는 경우는 드물다.
어떤 식이로든 이 외부 코드를 우리 코드에 깔끔하게 통합해야만 한다.
이 장에서는 소프트웨어 경계를 깔끔하게 처리하는 기법과 기교를 살펴본다.

### **외부 코드 사용하기**

\- **패키지 제공자나 프레임워크 제공자**는 **적용성을 최대한 넓히**려고 하지만, **사용자**는 **자신의 요구에 집중하는 인터페이스를 만듦**

\- 이러한 긴장으로 인해 **시스템 경계에서 문제**가 생길 소지가 많다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcuTbJi%2FbtrUvWb9ChS%2Fj6LKANGtDVkTcaRrxtaqA0%2Fimg.png)

\- 위 이미지의 Map(java.util.Map)은 굉장히 다양한 인터페이스로 수많은 기능을 제공한다.

\- Map은 굉장히 다양한 인터페이스로, 수많은 기능을 제공하기 때문에 Map이 **제공하는 기능성과 유연성은 유용하지만 위험도 크다.**

\- 예를 들어, Map 사용자라면 누구나 clear()로 Map 내용을 지울 권한이 있고, Map의 객체 유형을 제한하지 않음으로써 사용자는 어떤 객체 유형도 추가할 수 있게 된다.

```js
// Sensor라는 객체를 담는 Map을 만드려면 다음과 같이 Map 생성
const sensors = new HashMap();

// Sensor 객체가 필요한 코드는 다음과 같이 Sensor 객체를 가져옴
const s = sensors.get(sensorId);
```

\- 위와 같은 코드에서 **의도를 분명하게 표현하기 위해 제네릭스(Generics)를 사용**하게 된다. ~타입스크립트로 표현하기 어렵네...~

```js
const sensors = new HashMap();
...
const s = sensors.get(sensorId);
```

\- 하지만 위 방법도 "Map<String, Sensor>가 사용자에게 필요하지 않은 기능까지 제공한다"는 문제는 해결하지 못한다.

\- 프로그램에서 Map<String, Sensor> 인스턴스를 여기저기로 넘긴다면, Map 인터페이스가 변할 경우 수정할 코드가 많아진다.

\- 아래는 Map을 좀 더 깔끔하게 사용한 코드이다. **제네릭스의 사용 여부를 Sensors 안에서 결정**하기 때문에 Sensors 사용자는 제네릭스가 사용되었는지 여부에 신경 쓸 필요가 없다.

```js
class Sensors() {
  constructor() {	
		this.sensors = new HashMap();
	}
  
  getById(id) {
    return this.sensors.get(id);
  }
}
```

\- **경계 인터페이스인 Map을 Sensors 안으로 숨김으로써** Map 인터페이스가 변하더라도 Sensors 클래스 안에서 객체 유형을 관리하고 변환하기 때문에 **나머지 프로그램에는 영향을 미치지 않는다.**

\- Sensors 클래스는 **프로그램에 필요한 인터페이스만 제공하기 때문에 쉽게 오용할 수 있다.**

\- Sensors 클래스는 **나머지 프로그램이 설계 규칙과 비즈니스 규칙을 따르도록 강제할 수 있다.**

\- Map과 같은 **경계 인터페이스를 이용할 때는 이를 이용하는 클래스나 클래스 계열 밖으로 노출되지 않도록 주의**한다.

\- Map **인스턴스를 공개 API의 인수로 넘기거나 반환값으로 사용하지 않는다.**

### **경계 살피고 익히기**

\- 외부 코드를 사용하면 적은 시간에 더 많은 기능을 출시하기 쉬워지지만, **우리 자신을 위해 우리가 사용할 코드를 테스트하는 편이 바람직**

\- **짐 뉴커크가 제시한 학습 테스트**는 우리쪽 코드를 작성해 외부 코드를 호출하는 대신 먼저 **간단한 테스트 케이스를 작성함으로써 외부 코드를 익히도록** 하였다.

\- **학습 테스트**는 **프로그램에서 사용하려는 방식대로 외부 API를 호출함으로써 통제된 환경에서 API를 제대로 이해하는지를 확인**한다.

\- **학습 테스트**는 **API를 사용하려는 목적에 초점**을 맞춘다.

### **log4j 익히기**

\- 로깅 기능을 직접 구현하는 대신 아파치의 log4j 패키지를 사용하려 한다고 가정하자.

\- python의 logging을 사용하였음.

(참고 : [https://www.hanbit.co.kr/channel/category/category\_view.html?cms\_code=CMS4250329609](https://www.hanbit.co.kr/channel/category/category_view.html?cms_code=CMS4250329609))

```java
public class LogTest {
  private Logger logger;
  
  @Before
  public void initialize() {
    logger = Logger.getLogger("logger");
    logger.removeAllAppenders();
    Logger.getRootLogger().removeAllAppenders();
  }
  
  @Test
  public void basicLogger() {
    BasicConfiguurator.configure();
    logger.info("basicLogger");
  }
  
  @Test
  public void addAppenderWithStream() {
    logger.addAppender(
    	new ConsoleAppender(
      	new PatternLayout("%p %t %m%n")
      )
    );
    logger.info("addAppenderWithStream");
  }
 
  @Test
  public void addAppenderWithoutStream() {
    logger.addAppender(
    	new ConsoleAppender(
      	new PatternLayout("%p %t %m%n")
      )
    );
    logger.info("addAppenderWithoutStream");
  }
}
```

### **학습 테스트는 공짜 이상이다**

\- 학습 테스트는 **이해도를 높여주는 정확한 실험**이다.

\- 학습 테스트는 **투자하는 노력보다 얻는 성과가 더 크기** 때문에 공짜 이상이다.

\- 학습 테스트는 **패키지가 예상대로 도는지 검증**한다. **패키지 새 버전이 나온다면 학습 테스트를 돌림으로써 차이가 있는지 확인**한다.

\- 학습 테스트를 이용한 학습이 필요하든 그렇지 않든, **실제 코드와 동일한 방식으로 인터페이스를 사용하는 테스트 케이스가 필요**하다.

\- 이러한 **경계 테스트가 있다면 패키지의 새 버전으로 이전하기 쉬워지고**, 그렇지 않다면 낡은 버전을 오랫동안 사용하게 된다...

### **아직 존재하지 않는 코드를 사용하기**

\- 경계와 관련해 또 다른 유형은 **아는 코드와 모르는 코드를 분리하는 경계**다.

\- 우리 지식이 경계를 너머 미치지 못하는 코드 영역도 있고, 알려도 해도 알 수가 없을 수도 있고, 더 이상 내다보지 않기로 결정하기도 함

\- 작가는 몇 년 전 무선통신 시스템에 들어갈 소프트웨어 개발에 참여하였는데, 프로젝트 지연을 원하지 않았기에 '송신기' 하위 시스템과 아주 먼 부분부터 작업하기 시작했다.

\- '송신기' 모듈에게 원하는 기능은 "지정한 주파수를 이용해 이 스트림에서 들어오는 자료를 아날로그 신호로 전송하라" 였다.

\- **구현을 나중으로 미루고 자체적으로 인터페이스를 정의**하였다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbRt1PW%2FbtrUuIyvO74%2Fo6yxYETsLwPhONtHIGGxL1%2Fimg.png)

\- **우리가 바라는 인터페이스를 구현**하면 우리가 **인터페이스를 전적으로 통제한다**는 장점이 생기고, **코드 가독성**이 높아지고 **코드 의도도 분명**해진다.

\- 위의 그림에서 볼 수 있듯이 (통제하지 못하며 정의되지도 않은) 송신기 API에서 CommunicationsController를 분리했다.

\- 저쪽 팀이 송신기 API를 정의한 후에는 TransmitterAdapter를 구현해 간극을 메웠고, ADAPTER 패턴으로 API 사용을 캡슐화해 API가 바뀔 때 수정할 코드를 한곳으로 모았다.

\- **적절한 FakeTransmitter 클래스를 사용**하면 CommunicationsController를 테스트할 수 있고, Transmitter API 인터페이스가 나온 다음 경계 테스트 케이스를 생성해 우리가 API를 올바로 사용하는지 테스트하는 등, **편하게 설계를 테스트할 수 있다.**

### **깨끗한 경계**

\- **통제하지 못하는 코드를 사용할 때**는 **너무 많은 투자를 하거나 향후 변경 비용이 지나치게 커지지 않도록** 각별히 주의해야 한다.

\- **경계에 위치하는 코드는 깔끔하게 분리**하고, **기대치를 정의하는 테스트 케이스도 작성**해야 한다.

\- 통제가 불가능한 외부 패키지에 의존하는 대신 **통제가 가능한 우리 코드에 의존함으로써 외부 코드에 휘둘리는 것을 방지**할 수 있다.

\- **외부 패키지를 호출하는 코드를 줄여 경계를 관리해야** 한다. 어느 방법이든 **코드 가독성**이 높아지며, **경계 인터페이스를 사용하는 일관성**도 높아지며, **외부 패키지가 변했을 때 변경할 코드도 줄어든다.**
