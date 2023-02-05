# 11장 시스템

### introuduction

**도시를 세운다면?**

→ 도시를 한 사람의 힘으로 직접 관리하기란 무리다.
그럼에도 도시는 일상적으로 잘 돌아가는데 수도 관리팀, 전력 관리팀, 교통 관리 팀 등 각 분야를 관리하는 팀이 있기 때문이다. 도시는 적절한 추상화와 모듈화로 돌아간다.
큰 그림을 이해하지 못해도 개인과 개인이 관리하는 ‘구성요소’는 효율적으로 돌아간다.

흔히 소프트웨어 팀도 도시처럼 구성한다.
그런데 막상 팀이 제작하는 시스템은 비슷한 수준으로 관심사를 분리하거나 추상화를 이뤄내지 못하는데,
깨끗한 코드를 구현하면 낮은 추상화 수준에서 관심사를 분리하기 쉬워진다.

### 시스템 제작과 시스템 사용을 분리하라

제작(Construction)과 사용(use)은 아주 다르다.

→ 소프트웨어 시스템은 (애플리케이션 객체를 제작하고 의존성을 서로 ‘연결’하는 준비 과정과 (준비 과정 이후에 이어지는) 런타임 로직을 분리해야 한다.

시작 단계는 모든 애플리케이션이 풀어야 할 **관심사**다.

**관심사 분리**는 우리 분야에서 가장 오래되고 가장 중요한 설계 기법중 하나다.
불행히도 대다수 애플리케이션은 시작 단계라는 관심사를 분리하지 않는다.
준비 과정 코드를 주먹구구식으로 구현할 뿐 아니라 런타임 로직과 마구 뒤섞인다.

```typescript
function getService(): MyService {
  if(service == null) {
     service = new MyServiceImpl(...);
  }
  return service;
}
```

이것이 초기화 지연 (Lazy initialization) 혹은 계산 지연(Lazy Evaluation)이라는 기법이다. 장점은 여러가지다.

1. 실제로 필요할 때까지 객체를 생성하지 않으므로 불필요한 부하가 걸리지 않는다. 따라서 애플리케이션 시작시간이 빨라진다.

2. 어떤 경우에도 null 포인터를 반환하지 않는다.

하지만 getService 메서드가 MyServiceImpl과 (위에서는 생략한) 생성자 인수에 명시적으로 의존한다.

런타임 로직에 MyServiceImpl 객체를 전혀 사용하지 않더라도 의존성을 해결하지 않으면 컴파일이 안된다.

테스트도 문제가 된다. MyServiceImpl이 무거운 객체라면 단위 테스트에서 getService 메서드를 호출하기 전에 적절한 테스트 전용 객체를 service 필드에 할당해야 한다.

또한 일반 런타임 로직에다 객체 생성 로직을 섞은 탓에 모든 실행 경로로도 테스트 해야 한다.

즉, 책임이 둘 이라는 말은 메서드가 작업을 두 가지 이상 수행한다는 의미다.

즉, 작게나마 단일 책임 원칙(Single Responsibility Principle, SRP)을 깬다.

무엇보다 MyServiceImpl이 모든 상황에서 적합한 객체인지 모른다. 현실적으로 한 객체 유형이 모든 문맥에 적합할 가능성이 있을까?

초기화 지연기법을 한 번 정도 사용한다면 별로 심각한 문제가 아니다.

하지만 많은 애플리케이션이 이처럼 좀스런 설정 기법을 수시로 사용한다.

체계적이고 탄탄한 시스템을 만들고 싶다면 흔히 쓰는 좀스럽고 손쉬운 기법으로 모듈성을 깨서는 절대로 안된다. 객체를 생성하거나 의존성을 연결할때도 마찬가지다.

설정 논리는 일반 실행 논리와 분리해야 모듈성이 높아진다. 또한 주요 의존성을 해소하기 위한 방식, 즉 전반적이며 일괄적인 방식도 필요하다.

### Main 분리

시스템 생성과 시스템 사용을 분리하는 한 가지 방법으로, 생성과 관련한 코드는 모두 main이나 main이 호출하는 모듈로 옮기고, 나머지 시스템은 모든 객체가 생성되고 모든 의존성이 연결되었다고 가정한다. (그림 참조)

제어 흐름은 쉽다. main 함수에서 시스템에 필요한 객체를 생성한 후 이를 애플리케이션에 넘긴다. 애플리케이션은 그저 객체를 사용할 뿐이다.

main과 애플리케이션 사이에 표시된 의존성 화살표의 방향에 주목하자. 모든 화살표가 main에서 애플리케이션 쪽을 향한다. 즉, 애플리케이션은 main이나 객체가 생성되는 과정을 전혀 모른다는 뜻이다. 단지 모든 객체가 적절히 생성되었다고 가정한다.

### 팩토리

물론 떄로 객체의 생성 시점을 애플리케이션이 결정할 필요도 생긴다.

예를 들어, 주문 처리 시스템에서 애플리케이션은 LineItem 인스턴스를 생성해 Order에 추가한다. 이때 ABSTRACT FACTORY 패턴을 사용한다 그러면 LineItem을 생성하는 시점은 애플리케이션이 결정하지만 LineItem을 생성하는 코드는 애플리케이션이 모른다.

여기서도 마찬가지로 모든 의존성이 main에서 OrderProcessing 애플리케이션으로 향한다. 즉, OrderProcessing 애플리케이션은 LineItem이 생성되는 구체적인 방법을 모른다.

그 방법은 main 쪽에 있는 LineItemFactoryImplementation이 안다. 그럼에도 OrderProcessing 애플리케이션은 LineItem 인스턴스가 생성되는 시점을 완벽하게 통제하며, 필요시 OrderProcessing 애플리케이션에서만 사용하는 생성자 인수를 넘길 수 있다.

### 의존성 주입

사용과 제작을 분리하는 강력한 메커니즘 하나가 의존성 주입(Dependency Injection, DI)이다.

의존성 주입은 제어 역전 기법을 의존성 관리에 적용한 메커니즘이다. 제어 역전에서는 한 객체가 맡은 보조 책임을 새로운 객체에게 전적으로 떠 넘긴다. 새로운 객체는 넘겨받은 책임만 맡으므로 단일 책임 원칙(SRP)을 지키게 된다.

의존성 관리 맥락에서 객체는 의존성 자체를 인스턴스로 만드는 책임은 지지 않는다. 대신에 이런 책임을 다른 ‘전담’ 메커니즘에 넘겨야 한다. 그렇게 함으로서 ‘제어’를 역전한다.

초기 설정은 시스템 전체에서 필요함으로 대게 ‘책임질’ 메커니즘으로 ‘main’ 루틴이나 특수 컨테이너를 사용한다.

JNDI 검색은 의존성 주입을 ‘부분적으로’ 구현한 기능이다. 객체는 디렉터리 서버에 이름을 제공하고 그 이름에 일치하는 서비스를 요청한다.

```java
MyService myService = (MyService)(jndiContext.lookup("NameOfMyService"));
```

호출하는 객체(반환되는 객체가 적절한 인터페이스를 구현하는 한)는 실제로 반환되는 객체의 유형을 제어하지 않는다. 대신 호출하는 객체는 의존성을 능동적으로 해결한다.

진정한 의존성 주입은 클래스가 의존성을 해결하려 시도하지 않는다. 대신 의존성을 주입하는 방법으로 설정자(Setter) 메서드나 생성자 인수를 혹은 둘다 제공한다.

DI 컨테이너는 (대게 요청이 들어올 때마다) 필요한 객체의 인스턴스를 만든 후 생성자 인수나 설정자 메서드를 사용해 의존성을 설정한다.

그렇다면 초기화 지연으로 얻는 장점은 포기해야 하나?

→ 이 기법은 DI를 사용하더라도 때로는 유용하다. 대다수의 DI 컨테이너가 필요할 때까지 객체를 생성하지 않고, 대부분 계산 지연이나 비슷한 최적화에 쓸 수 있도록 팩토리를 호출학거나 프록시를 생성하는 방법을 제공하기 때문이다.

### 확장

‘처음부터 올바르게’ 시스템을 만들 수 있다는 믿음은 미신이다. 우리는 오늘 주어진 사용자 스토리에 맞춰 시스템을 구현해야 한다. 내일은 새로운 스토리에 맞춰 시스템을 조정하고 확장하면 된다. 이것이 반복적이고 점진적인 에자일 방식의 핵심이다.

테스트 주도 개발, 리팩터링, 깨끗한 코드는 코드 수준에서 시스템을 조정하고 확장하기 쉽게 만든다.

하지만 시스템 수준에서는 다르다. 단순한 아키텍처를 복잡한 아키텍처로 조금씩 키울 수 없다는 현실은 정확하다.

```java
스프트웨어 시스템은 물리적인 시스템과 다르다. 관심사를 적절히 분리해 관리한다면 소프트웨어 아키텍처는 점진적으로 발전할 수 있다.
```

소프트웨어 시스템은 ‘수명이 짧다’는 본질로 인해 아키텍처의 점진적인 발전이 가능하다. 먼저 관심사를 적절히 분리하지 못하는 아키텍처의 예시를 소개한다.

```java
// 목록 11-1
package com.example.banking;
import java.util.Collections;
import java.ejb.*;

public interface BankLocal extends java.ejb.EJBLocalObject {
	String getSteetAddr1() throws EJBException;
	String getSteetAddr2() throws EJBException;
	String getCity() throws EJBException;
	String getState() throws EJBException;
	String getZipCode() throws EJBException;
	void setStreetAddr1(String street1) throws EJBException;
	void setStreetAddr2(String street2) throws EJBException;
	void setState(String state) throws EJBException;
	void setZipCode(String zip) throws EJBException;
	Collection getAccounts() throws EJBException;
	void setAccounts(Collection accounts) throws EJBException;
	void addAccounts(AccountDTO accountDTO) throws EJBException;
}
```

해당 코드에서 열거하는 속성은 Bank 주소, 은행이 소유하는 계좌다. 각 계좌 정보는 Account EJB로 처리한다. 목록 11-2는 목록 11-1 인터페이스를 구현한 Bank 빈에 대한 구현 클래스다.

```java
// 목록 11-2
package com.example.banking;
import java.util.Collections;
import java.ejb.*;

public abstract class Bank implements java.ejb.EJBLocalObject {
	// 비즈니스 논리..
	public abstract String getSteetAddr1();
	public abstract String getSteetAddr2();
	public abstract String getCity();
	public abstract String getState();
	public abstract String getZipCode();
	public abstract void setStreetAddr1(String street1);
	public abstract void setStreetAddr2(String street2);
	public abstract void setState(String state);
	public abstract void setZipCode(String zip);
	public abstract Collection getAccounts();
	public abstract void setAccounts(Collection accounts);
	void addAccounts(AccountDTO accountDTO){
		InitialContext context = new InitialContext();
		AccountHomeLocal accountHome = context.lookup("AccountHomeLoacl");
		AccountLocal account = accountHome.create(accountDTO);
		Collection accounts = getAccounts();
		acounts.add(account);
		}
	// EJB 컨테이너 논리

	public abstract void setId(Integer id);
	public abstract Integer getId();
	public Integer ejbCreate(Integer id) {...}
	public void ejbPostCreate(Integer id) {...}
	// 나머지도 구현해야 하지만 일반적으로 비어 있다.
	public void setEntityContext(EntityContext ctx){}
	public void unsetEntityContext(){}
	public void ejbActivate() {}
	public void ejbPassivate() {}
	public void ejbLoad(){}
	public void ejbStore(){}
	public void ejbRemove(){}
}
```

비즈니스 논리로 EJB2 애플리케이션 ‘컨테이너’에 강하게 결합된다. 클래스를 생성할 때 컨테이너에서 파생해야 하고, 컨테이너가 요구하는 다양한 생명주기 메스드도 제공해야 한다.

이렇듯 비즈니스 논리가 덩치 큰 컨테이너와 밀접하게 결합된 탓에 독자적인 단위 테스트가 어렵다. 그래서 EJB2 코드는 프레임워크 밖에서 재사용하기란 사실상 불가능하다.

결국 객체지향프로그래밍이라는 개념조차 뿌리가 흔들린다. 빈은 다른 빈을 상속받지 못한다. 새로운 계정을 추가하기 위한 논리에 주목하자. 일반적으로 EJB2빈은 DTO를 정의한다. DTO에는 메서드가 없고 사실상 구조체다. 즉 동일정보를 저장하는 자료유형이 2개 라는 의미이고, 한 객체에서 다른 객체로 자료를 복사하는 반복적인 규격코드가 필요하다.

### 횡단(cross-cutting)관심사

EJB2 아키텍처는 일부 영역에서 관심사를 거의 완벽하게 분리한다.

영속성과 같은 관심사는 애플리케이션의 자연스러운 객체 경계를 넘나드는 경향이 있다. 모든 객체가 전반적으로 동일한 방식을 이용하게 만들어야 한다.

원론적으로는 모듈화되고 캡슐화된 방식으로 영속성 방식을 구상할 수 있다. 하지만 현실적으로는 영속성 방식을 구현한 코드가 온갖 객체로 흩어진다. 여기서 횡단 관심사 라는 용어가 나온다. 영속성 프레임워크 또한 모듈화 할 수 있다. 도메인 논리도 모듈화 할 수 있다. 문제는 이 두 영역이 세밀한 단위로 곂친다는 것이다.

사실 EJB 아키텍처가 영속성, 보안, 트랜잭션을 처리하는 방식은 관점 지향 프로그래밍을 예견했다고 본다. AOP는 횡단 관심사에 대처해 모듈성을 확보하는 일반적인 방법론이다.

AOP에서 **관점**이라는 모듈 구성 개념은 “특정 관심사를 지원하려면 시스템에서 특정 지점들이 동작하는 방식을 일관성 있게 바꿔야 한다”라고 명시한다. 명시는 간결한 선언이나 프로그래밍 케머니즘으로 수행한다.

영속성을 예로들면 프로그래머는 영속적으로 저장할 객체와 속성을 선언한 후 영속성 책임을 영속성 프레임워크에 위임한다. 그러면 AOP 프레임워크는 대상 코드에 영향을 미치지 않는 상태로 동작 방식을 변경한다.

### 자바 프록시

자바 프록시 단순한 상황에 적합하다. 개별 객체나 클래스에서 메서드 호출을 감싸는 경우가 좋은 예다. 하지만 JDK에서 제공하는 동적 프록시는 인터페이스만 지원한다.

클래스 프록시를 사용하려면 GGLIB, ASM, Javassist 등과 같은 바이트 코드 처리 라이브러리가 필요하다.

목록 11-3은 Bank 애플리케이션에서 JDK 프록시를 사용해 영속성을 지원하는 예제다.

```typescript
// Bank.js (패키지 이름을 감춘다)
interface Bank {
  getAccounts(): Collection<Account>;
  setAccounts(accounts: Collection<Account>): void;
}
```

```typescript
// BankImpl.js

// 추상화를 위한 POJO ("Plain Old Java Object") 구현

class BankImpl implements Bank {
	private accounts: List<Account>

  public getAccounts(): Collection<Account> {
  	return this.accounts;
  }
  
	public setAccounts(accounts: Collection<Account>): void {
      this.accounts = new ArrayList<Account>;
      accounts.forEach((account) => {
        this.accounts.push(account);
      })
  }
}

// BankProxyHandler.js

// 프록시 API가 필요한 "InvocationHandler"
public class BankProxyHandler implements InvocationHandler {
	private bank: Bank;

	public BankProxyHandler(bank: Bank) {
		this.bank = bank;
	}

	// InvocationHandler에 정의된 메서드
	public Object invoke(proxy: Object, method: Method, args: Object[])
			throws Throwable {
		const methodName = method.getName() as string;
		if (methodName.equals("getAccounts")) {
			bank.setAccounts(getAccountsFromDatabase());
			return bank.getAccounts();
		} else if (methodName.equals ("setAccounts")) {
			bank.setAccounts((Collection<Account>) args[0]);
			setAccountsToDatabase(bank.getAccounts));
			return null;
		} else {
			...
		}
	}
  
	// 세부사항은 여기에 이어진다.
	protected getAccountsFromDatabase(): Collection<Account> {...}
	protected setAccountsToDatabase(accounts: Collection<Account>): void {...}
}

// 다른 곳에 위치하는 코드
const bank = Proxy.newProxyInstance(
	Bank.class.getClassLoader(),
	new Class[] { Bank.class },
	new BankProxylandler(new Bankimpt()));
```

위에서는 프록시로 감쌀 인터페이스 Bank와 비즈니스 논리를 구현하는 POJO을 정의했다.

프록시 API에는 InvocationHandler 넘겨 줘야 한다. 님긴 InvocationHandler 는 프록시에 호출되는 Bank 메서드를 구현하는 데 사용된다. BankProxyHandler는 자바 리플렉션 API를 사용해 제네릭스 메서드를 상응하는 BankImpl 메서드로 매핑한다.

### 순수 자바 AOP 프레임워크

다행스럽게 대부분의 프록시 코드는 판박이라 도구로 자동화 가능하다.

순수 자바관점을 구현하는 스프링 AOP, JBoss AOP 등과 같은 여러 자바 프레임워크는 내부적으로 프록시를 사용

스프링은 비즈니스 논리를 POJO로 구현. POJ는 순수하게 도메인에 초점을 맞춘다.

프로그래머는 설정 파일이나 API를 사용해 필수적인 애플리케이션 기반 구조를 구현한다. 이때 프레임워크는 사용자가 모르게 프록시나 바이트코드 라이브러리를 사용해 이를 구현한다.

```java
<beans>
...
<bean id="appDataSource"
class="org.apache.commons.dbcp.BasicDataSource"
destroy-method="close"
p:driverClassName="com.mysql.jdbc.Driver"
p:url="jdbc:mysql://localhost:3306/mydb"
p:username="'me"/>

<bean id="bankDataAccessObiect"
class="com.example.banking.persistence.BankataAccessObject"
p:dataSource-ref="appDataSource"/>

‹bean id="bank"
class="com.example.banking.model.Bank"
p:dataAccessObject-ref="bankDataAccessObject"/>
...
</beans>
```

각 ‘빈’은 중첩된 ‘러시아 인형’의 일부분과 같다. Bank 도메인 객체는 자료 접근자 객체(DAO)로 프록시 되었으며 자료 접근자 객체는 JDBC 드라이버 자료 소스로 프록시 되었다.

클라이언트 Bank 객체에서 getAccounts()를 호출한다고 믿지만 실제로는 Bank POJO의 기본 동작을 확장한 중첩 DECORATOR 객체 집합의 가장 외곽과 통신한다.

애플리케이션에서 DI 컨테이너에게 (XML 파일에 명시된) 시스템 내 최상위 객체를 요청하려면 다음 코드가 필요하다.

```typescript
const bf =
	new XmlBeanFactory(new ClassPathResource("app.xml", getClass())) as XmlBeanFactory;
const bank = bf.getBean("bank") as Bank;
```

스프링 관련 자바 코드가 거의 필요없으므로 애플리케이션은 사실상 스프링과 독립적이다. 즉, EJB2 시스템이 지녔던 강한 결합이라는 문제가 모두 사라진다.

```java
package com.example.banking.model;
import javax.persistence.*;
import java.util.ArrayList;
import java.util.Collection;

@Entity
@Table(name = "BANKS")
public class Bank implements java.io.Serializable {
	@Id @GeneratedValue(strategy=GenerationType.AUTO)
private int id;

@Embeddable // Bank의 데이터베이스행에 '인라인으로 포함된 객체
public class Address {
	protected String streetAddr1;
	protected String streetAddr2;
	protected String city;
	protected String state;
	protected String zipCode;
}

@Embedded
private Address address;

@OneToMany(cascade = CascadeType.ALL, fetch = FetchType.EAGER,
		mappedBy="bank")
private Collection<Account> accounts = new ArrayList<Account>();

public int getId() {
	return id;
}
public void setId(int id) {
	this.id= id;
}
public void addAccount (Account account) {
	account.setBank(this);
	accounts.add(account);
}
public CollectionAccount> getAccounts(){
	return accounts;
}
public void setAccounts(Collection<Account> accounts) {
	this.accounts = accounts;
	}
}
```

원래 EJB2 코드보다 위 코드가 훨씬 더 깨끗하다. 일부 상세한 엔티티 정보는 애너테이션에 포함되어 그대로 남아있지만, 모든 정보가 애너테이션 속에 있으므로 코드 자체는 깔끔하고 깨끗하다.

애너테이션에 들어있는 영속성 정보는 일부든, 전부든 필요시 XML 배치 기술자로 옮겨도 괜찮다.

### AspectJ 관점

마지막으로, 관심사를 관점으로 분리하는 가장 강력한 도구는 AspectJ 언어다.

AspectJ는 관점을 분리하는 강력하고 풍부한 도구 집합을 제공하긴 하지만, 새 도구를 사용하고 새 언어 문법과 사용법을 익혀야 한다는 단점이 있다.

최근에 나온 AspectJ ‘애너테이션 폼’은 새로운 도구와 새로운 언어라는 부담을 어느 정도 완화한다. 또한 스프링 프레임워크는 AspectJ에 미숙한 팀들이 애너테이션 기반 관점을 쉽게 사용하도록 다양한 기능을 제공한다.

### 테스트 주도 시스템 아키텍처 구축

관점으로 관심사를 분리하는 방식은 그 위력이 막강하다. 애플리케이션 도메인 논리를 POJO로 작성할 수 있다면, 즉 코드 수준에서 아키텍처 관심사를 분리할 수 있다면, 진정한 테스트 주도 아키텍처 구축이 가능하다.

### 의사 결정을 최적화하라

가장 적합한 사람에게 책임을 맡기면 가장 좋다.

우리는 때때로 가능한 마지막 순간까지 결정을 미루는 방법이 최선이라는 사실을 까먹곤 한다.

### 명백한 가치가 있을 때 표준을 현명하게 사용하라

표준을 사용하면 아이디어와 컴포넌트 재사용하기 쉽고, 적절한 경험을 가진 사람을 구하기 쉬우며, 좋은 아이디어를 캡슐화하기 쉽고, 컴포넌트를 엮기 쉽다. 하지만 때로는 표준을 만드는 시간이 너무 오래 걸려 업계가 기다리지 못한다. 어떤 표준은 원래 표준을 제정한 목적을 잊어버리기도 한다.

### 시스템은 도메인 특화 언어가 필요하다.

소프트웨어 분야에서도 최근 들어 DSL이 새롭게 조명 받기 시작했다. 효과적으로 사용한다면 DSL은 추상화 수준을 코드 관용구나 디자인 패턴 이상으로 끌어올린다.

### 결론

시스템 역시 깨끗해야 한다. 모든 추상화 단계에서 의도는 명확히 표현해야 한다. 그러려면 POJO를 작성하고 관점 혹은 관점과 유사한 메커니즘을 사용해 각 구현 관심사를 분리해야 한다.

시스템을 설계하든 개별 모듈을 설계하든, 실제로 돌아가는 가장 단순한 수단을 사용해야 한다는 사실을 명심하라.
