## 2장 의미 있는 이름

이름을 잘 지으면 여러모로 편하다!

2장에서는 이름을 잘 짓는 간단한 규칙을 몇 가지 소개한다.

### 의도를 분명히 밝혀라

**의도가 분명하게 이름을 지어라!**

→ 당연한 말이지만 정말로 중요하다. 좋은 이름을 지으려면 시간이 걸리지만 좋은 이름으로 절약하는 시간이 훨씬 더 많다. (만약 이름을 짓고 넘어가도 더 좋은 이름이 떠오르면 바꿔라)

```typescript
let d:number; // 경과 시간 (단위: 날짜)
```

이런 이름 d는 아무 의미도 없다. 측정하려는 값과 단위를 표현하는 이름이 필요하다.

```typescript
let daysSinceCreation: number;
let fileAgeInDays: number;
```

이런식으로 의도가 드러나는 이름은 코드 이해와 변경이 쉬워진다.

```typescript
function getThem(): number[] {
	const list1: number[] = [];

  for (let i = 0; i < theList.length; i++) {
    if (theList[i][0] === 4) {
      list1.push(theList[i]);
    }
  }

	return list1;
}
```

코드가 하는 일을 짐작하기 어렵다. 왜일까? 문제는 코드의 함축성이다.

코드 맥락이 코드 자체에 명시적이지 않으므로 다음과 같은 정보를 알고 있어야 한다.

1. theList에 무엇이 들었는가
2. theList에서 0번째 값이 어째서 중요한가?
3. 값 4는 무슨 의미인가?
4. 함수가 반환하는 리스트 list1를 어떻게 사용하는가

→ 위 코드에서는 이 정보들이 드러나지 않는다.

아래처럼 각 개념에 이름만 붙여도 코드가 상당히 나아진다.

```typescript
function getFlaggedCells(): number[] {
	const flaggedCells: number[] = [];

  for (let i = 0; i < gameBoard.length; i++) {
		const cell = gameBoard[i];

    if (cell[STATUS_VALUE] === FLAGGED) {
      flaggedCells.push(gameBoard[i]);
    }
  }

	return flaggedCells;
}
```

코드의 구조는 똑같지만 이름을 바꾼것만으로 더욱 명확해졌다.

또한 int형이 아닌 간단한 클래스로 리스트를 만들어도 된다.

```typescript
function getFlaggedCells(): number[] {
	const flaggedCells = new CellList();

  for (let i = 0; i < gameBoard.length; i++) {
		const cell = gameBoard[i];

    if (cell.isFlagged()) {
      flaggedCells.add(gameBoard[i]);
    }
  }

	return flaggedCells;
}
```

### 그릇된 정보를 피하라

프로그래머는 코드에 그릇된 단서를 남겨서는 안 된다.

- 널리 쓰이는 의미가 있는 단어를 다른 의미로 사용하면 안된다.

  ex) 빗변(hypotenuse)를 표현하기 위해 hp라고 사용할수도 있지만, hp는 유닉스 플랫폼을 가리키는 이름이므로 적절하지 않다.

- list 처럼 프로그래머에게 특수한 의미를 지니는 단어는 함부로 사용하면 안된다.

  ex) 계정을 그룹으로 묶을때 accountList라고 명명했을 때 list(배열) 자료형이 아닐 경우 잘못된 정보를 제공하는 것이다. → accountGroup 또는 단순히 Accounts라 명명한다.

  ```typescript
  class accountList; //-> 이름은 list(배열)이지만 배열이 아니다.
  ```

- 유사한 개념은 유사한 표기법을 사용한다.
  → 요즘 개발환경은 자동완성 기능을 제공한다. 만약 이름 몇 자만 입력한 후 뜨는 가능한 후보 목록에 유사한 개념이 나오고, 각 개념 차이가 명백히 드러난다면 자동완성을 굉장히 유용하게 사용할 수 있다.
- 서로 다른 모듈에서 흡사한 이름을 사용하지 않도록 주의한다.

  ex) 한 모듈에서 XYZControllerForEfficientHandlingOfStrings라는 이름을 사용하고 다른 모듈에서 XYZControllerForEfficientStorageOfStrings라는 이름을 사용할 때 두 단어는 많이 비슷하다.
  → 다른 모듈이지만 이름이 비슷해 헷갈릴 수 있다.

- 구별하기 어려울 정도로 비슷해 보이는 문자를 이름으로 사용하지 않도록 주의한다.

  ex) 숫자 1과 영어 소문자 L, 대문자 O와 숫자 0

  ```typescript
  let a = 1;
  if (O === l) a = O1;
  else l = 01;
  ```
  
  → 실제 이런코드를 만나면 답이 없다.

### 의미 있게 구분하라

- 컴파일러나 인터프리터만 통과하려는 생각으로 코드를 구현하는 프로그래머는 스스로 문제를 일으킨다.

  ex) 동일한 범위 안에서 다른 두 개념에 같은 이름을 사용하지 못한다. 그래서 한쪽의 이름을 마음대로 바꾸는데, 철자를 살짝 바꿨다가 나중에 철자 오류를 고치는 순간 컴파일이 불가능한 상황에 빠진다.
  → class라는 이름을 사용했다고 비슷한 발음인 klass사용 (ㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋㅋ)

- 연속된 숫자를 덧붙이는 것도 안좋다.

  ex) a1,a2..aN같은 이름은 저자 의도가 전혀 드러나지 않는다.

  ```typescript
  function copyChars(a1: string[], a2: string[]): void {
    for (let i = 0; i < a1.length; i++) {
      a2[i] = a1[i];
    }
  }
  ```

- 또한 불용어를 추가한 이름 역시 아무런 정보도 제공 못한다.

  ex) Product라는 클래스와 ProductInfo, ProductData라는 클래스가 있다고 생각하자. 이들은 개념을 구분하지 않은 채 이름만 달리한 경우다. Info나 Data는 a,an, the와 마찬가지로 의미가 불분명한 불용어다.
  (단 이런 a, the와 같은 접두어를 사용하지 말라는 소리가 아니다. 예전에는 전역변수와 지역변수 구분이 힘들때 지역변수에는 A, 전역변수에는 The를 붙여서 구분하는 방법도 사용했다. 요즘에는 IDE가 발전해서 이렇게 안써도 된다.)

  ex) 코드를 읽다가 Customer라는 클래스와 CustomerObject라는 클래스를 발견했을 때 차이를 알 수 있는가? → 이름만보고는 유추할 수 없다.

  ```typescript
  getActiveAccount();
  getActiveAccounts();
  getActiveAccoutInfo();
  ```

  이런 코드가 있을 경우 각 함수가 무슨 역할을 하는지 알수가 없다.
  → 읽는 사람이 차이를 알도록 이름을 지어라.

### 발음하기 쉬운 이름을 사용하라.

- 발음하기 쉬운 이름을 사용해라! 어려운 이름은 토론하기 어렵다
  → 결국 프로그래밍은 사회 활동이기 때문에 원활한 의사소통을 위해선 발음하기 쉬운 이름을 사용하는게 좋다.

```typescript
class DtaRcrd102 {
  #genymdhms: Date;
  #modymdhms: Date;
}

class Customer {
  #generationTimestamp: Date;
  #modificationTimestamp: Date;
}
```

둘중에 어느코드가 더 알아보기 쉽나? → 첫번째는 본인들만 아는 규칙으로 작성했기 때문에 알아보기 어렵다. 두번째는 이름을 보고 무슨 변수인지 알아낼 수 있다.

### 검색하기 쉬운 이름을 사용하라

- 문자 하나를 사용하는 이름과 상수는 텍스트 코드에서 쉽게 눈에 띄지 않는다.
- 이름 길이는 범위 크기에 비례해야 한다.
- 변수나 상수를 코드 여러 곳에서 사용한다면 검색하기 쉬운 이름이 바람직하다.

```typescript
const realDaysPerIdealDay = 4;
const WORK_DAYS_PER_WEEK = 5;
let sum = 0;

for (let j = 0; j < WORK_DAYS_PER_WEEK; j++){
  const realTaskDays = taskEstimate[j] * realDaysPerIdealDay;
  const realTaskWeeks = (realTaskDays / WORK_DAYS_PER_WEEK);
  sum += realTaskWeeks;
}
```

위 코드에서는 이름을 의미있게 지어서 길어지지만 검색 시 쉽고 빠르게 찾을 수 있다.

→ 이름은 짧을 수록 좋으나, 더이상 압축할 수 없을 만큼만 짧아야 한다.

### 인코딩을 피하라

- 문제 해결에 집중하는 개발자에게 인코딩은 불필요한 정신적 부담이다.
- 인코딩한 이름은 거의가 발음하기 어려우며 오타가 생기기도 쉽다.

**헝가리식 표기법**

: 이름의 앞에 데이터 타입을 명시하는 것.

- 이름 길이가 제한된 옛날에는 이 규칙을 위반할수밖에 없었다.

  ex) 포트란은 첫 글자로 유형을 표현, 초창기 베이식은 글자 하나에 숫자 하나만 허용.

- 요즘의 ide는 컴파일 하지 않고도 타입 오류를 감지할 정도로 발전했다.
  → 이제는 헝가리식 표기법이나 기타 인코딩 방식이 방해가 된다.

**멤버 변수 접두어**

- 이제는 멤버 변수에 m\_이라는 접두어를 붙일 필요도 없다. 클래스와 함수는 접두어가 필요 없을 정도로 작아야 한다.
- 멤버 변수를 다른색상으로 표시하거나 눈에 띄게 보여주는 ide를 사용해야 한다.

```typescript
class Part {
  #m_dsc: string = ''; // 설명 문자열, m_은 멤버 변수를 뜻함.

  setName(name: string): void {
    this.#m_dsc = name;
  }
} // 예전에는 이렇게 썼음
```

```typescript
class Part {
  #description: string = ''; // 설명 문자열, m_은 멤버 변수를 뜻함.

  setName(description: string): void {
    this.#description = description;
  }
} // 이런식으로 쓰는 것을 권장
```

⇒ 접두어는 옛날에 작성한 코드라는 징표가 되어버림.

**인터페이스 클래스와 구현 클래스**

- 때로는 인코딩이 필요한 경우도 있다.

  ex) 도형을 생성하는 ABSTRACT FACTORY를 구현한다고 가정하자. 이것은 인터페이스 클래스 이므로 구현은 구체 클래스(concrete class)에서 한다.
  → 여기서 두 클래스 이름을 어떻게 지어야 좋을까?
  ⇒ 우선 인터페이스는 접두어 안붙히는 편이 좋다. 그러므로 인터페이스 클래스의 이름을 ShapeFactory, 구현 클래스의 이름을 ShapeFactoryImp, CShapeFactory등으로 짓는 것이 좋다.

### 자신의 기억력을 자랑하지 마라

- 자신만 아는 이름으로 변수이름을 짓지 말아라!
- 또한 문자 하나만 사용하는 변수 이름은 문제가 있다. (루프에서 반복 횟수를 세는 경우 등을 빼고)

⇒ 프로그램은 **명료함이 최고이다**

### 클래스 이름

- 클래스 이름과 객체 이름은 명사나 명사구가 적합하다

  ex) Customer, WikiPage, Acount 등

- Manager, Processor, Data 등의 단어는 피하고 동사는 사용하지지 않는다.

### 메서드 이름

- 메서드 이름은 동사나 동사구가 적합하다.

  ex) postPayment, deletePage 등

- 점근자(Accessor), 변경자(Mutator), 조건자(Predicate)는 표준에 따라 값 앞에 get, set,is를 붙인다.
- 생상자를 중복정의할 때는 정적 팩토리 메서드를사용한다.

```typescript
const fulcrumPoint = Complex.FromRealNumber(23.0);
const fulcrumPoint = new Complex(23.0);
//아래보다 위 코드가 더 좋다.
```

### 기발한 이름은 피하라

- 이름이 너무 기발하면 그 함수가 무엇을 하는지 모를 수 있다.
  재미난 이름보다 명료한 이름을 선택해라

  ex)”수류탄투척”이라는 이름은 재밌지만, 무슨 일을 하는 함수인지 모를 것이다. 그냥 DeleteItems이란 이름을 쓰자.

### 한 개념에 한 단어를 사용하라

- 추상적인 개념 하나에 단어 하나를 선택해야 한다

  ex) 예를 들어 똑같은 일을 하는 메서드를 클래스마다 fetch, retrieve,get 등 제각각으로 부르면 혼란스럽다.

- 메서드 이름은 독자적이고 일관적이어야 한다.
  → 요즘 ide는 클래스에 있는 메서드까지는 보여주지만 주석이나, 그 메서드가 무슨 일을 하는지 까지는 잘 보여주지 않는다. 그래서 메서드의 이름이 일관적이어야지 무슨 일을 하는지 알 수 있다.

### 말장난을 하지 마라

- 한 단어를 두 가지 목적으로 사용하지 마라. 다른 개념에 같은 단어를 사용한다면 그것은 말장난에 불과하다.
  - 지금까지 두개의 인자를 더하는 함수를 add로 사용해 왔는데, 리스트에 값을 집어 넣는것도 add로 사용해도 될까?
    → 안된다. 기존 메서드와 맥락이 다르므로 insert나 append 라는 이름이 적당한다.

### 해법 영역에서 가져온 이름을 사용하라

- 코드를 읽을 사람도 프로그래머라는 사실을 명심해라.
  - 전산 용어, 알고리즘 이름, 패턴 이름, 수학 용어 등은 사용해도 괜찮다.
  - 프로그래머에게 익숙한 기술과 개념은 사용해도 좋다

### 문제 영역에서 가져온 이름을 사용하라 ?

- 적절한 “프로그래머 용어”가 없다면 문제 영역에서 이름을 가져온다.
  - **현재 해결하는 문제에 대한 전문적인 용어**를 가지고 온다.(예를 들어 환자 관리 시스템을 만든다면 의료 언어)
    → 그렇게 된다면 그 분야의 전문가에게 의미를 물어 파악할 수 있다.

### 의미 있는 맥락을 추가하라

- 대다수의 이름은 의미가 불분명하다

  - 클래스, 함수, 이름 공간(namespace)에 넣어 맥락을 부여한다.
    모든 방법이 힘들면 마지막으로 접두어를 붙인다. → 최대한 지양

    ex) state라는 변수 하나만 사용했을때 이것이 주소의 일부라는 사실을 바로 알 수 있을까?
    → 어렵다. 그러므고 addr이라는 접두어를 추가해 addrState라 쓰면 좀 더 쉽게 알 수 있다.

```typescript
function printGuessStatistics(candidate: string, count: number): void {
  let number: string;
  let verb: string;
  let pluralModifier: string;

  if (count === 0) {
    number = 'no';
    verb = 'are';
    pluralModifier = 's';
  } else if (count === 1) {
    number = '1';
    verb = 'is';
    pluralModifier = '';
  } else {
    number = String(count);
    verb = 'are';
    pluralModifier = 's';
  }

  const guessMessage = `There ${verb} ${number} ${candidate} ${pluralModifier}`;
  console.log(guessMessage);
}
```

- 다음 코드를 보면 함수가 길고, 세 변수를 함수 전반에서 사용한다. (맥락이 불분명하다)
  그래서 클래스를 만든 후 세변수를 클래스에 넣어서 사용하고, 함수를 작은 조각으로 쪼개면 이 함수가 무엇을 하는지 더 분명해진다.

```typescript
class GuessStatisticsMessage {
  #number: string = '';
  #verb: string = '';
  #pluralModifier: string = '';

  make(candidate: string, count: number): string {
    this.createPluralDependentMessageParts(count);
    const tmp = `There ${this.#verb} ${this.#number} ${candidate} ${
      this.#pluralModifier
    }`;
    return tmp;
  }

  createPluralDependentMessageParts(count: number): void {
    if (count === 0) {
      this.thereAreNoLetters();
    } else if (count === 1) {
      this.thereIsOneLetter();
    } else {
      this.thereAreManyLetters(count);
    }
  }

  thereAreManyLetters(count: number): void {
    this.#number = String(count);
    this.#verb = 'are';
    this.#pluralModifier = 's';
  }

  thereIsOneLetter() {
    this.#number = '1';
    this.#verb = 'is';
    this.#pluralModifier = '';
  }

  thereAreNoLetters() {
    this.#number = 'no';
    this.#verb = 'are';
    this.#pluralModifier = 's';
  }
}
```

### 불필요한 맥락을 없애라

- 예를 들어 “고급 휘발유 충전소”(Gas Station Deluxe)라는 앱을 짤 때 모든 클래스 이름을 GSD로 지으면 멍청한 짓이다.
  → IDE에서 G를 입력하는 순간 모든 클래스의 이름이 열거될게 뻔하기 때문이다.
- 일반적으로는 짧은 이름이 긴 이름보다 좋다, 단 의미가 분명한 경우에 한해서다(더 줄일 수 없을 때까지만 줄이자). 이름에 불필요한 말을 추가하지 말자

### 마치면서

좋은 이름을 선택하려면

- 설명 능력이 뛰어나야 한다.
- 문화적인 배경이 같아야 한다

<aside>
	💡 기술, 비즈니스, 관리 문제가 아닌 교육 문제다!
</aside>
<br>
<aside>
	💡 사람들이 이름을 바꾸지 않으려는 이유 하나는 다른 개발자가 반대할까 두려워서다.

	→ 하지만 오히려 좋은 이름으로 바꿔주면 반갑고 고마워 한다.
</aside>

<aside>
	💡 우리들 대다수는 자신이 짠 클래스 이름과 메서드 이름을 모두 암기하지 못한다.

    → 그러므로 암기는 요즘 나오는 도구에게 맡기고, 문장이나 문단처럼 읽히는 코드를 짜는 데 집중해야한다.

</aside>
