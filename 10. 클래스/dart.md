# 10장 클래스

### 클래스 체계

(자바의 경우)

1. 가장 먼저 변수 목록을 작성
   - 정적(static), 공개(public) 상수가 있다면 맨 처음에 나온다.
   - 다음으로 정적 비공개(private)변수가 나오며, 비공개 인스턴스 변수가 나온다.
2. 변수 목록 다음에는 함수가 나온다.
   - 공개 함수를 먼저 쓴다.
   - 비공개 함수는 자신을 호출하는 공개 함수 직후에 넣는다.

⇒ 추상화 단계가 순차적으로 내려간다.

(dart의 경우)

1. 클래스의 경우 파스칼 케이스(ExampleClassName)로 작성한다.
2. 함수,변수의 경우 캐멀 케이스(exampleVariableName)로 작성한다.
3. 파일 이름의 경우 스네이크 케이스(example_file_name.dart)로 작성한다.
4. 비공개(public) 함수,변수는 _(언더바)를 앞에 붙이고 작성한다.

```dart
class Student{
    String name = "";
    int age = 0;
    int _id = 123123123;
    void show() {
        print('$name\n$age\n$id');
    }
```

### 캡슐화

- 변수와 유틸리티 함수는 가능한 공개하지 않는 편이 좋다. (반드시는 아니다)
  - 때로는 protected로 선언해 테스트 코드에 접근을 허용하기도 한다.

→ 하지만 캡슐화를 풀어주는 결정은 언제나 최후의 수단이다.

### 클래스는 작아야한다


- 클래스를 만들 때 제일 중요한 것은 **“크기가 작아야 한다”** 이다.
  → 여기서 작아야하는 것은 물리적인 행뿐만 아니라 **“책임”** 도 포함된다.

```dart
//슈퍼클래스?(어마어마하게 큰 만능 클래스)

class SuperDashboard extends JFrame implements MetaDataUser {
	String getCustomizerLanguagePath();
	void setSystemConfigPath(String systemConfigPath);
	String getSystemConfigDocument();
	void setSystemConfigDocument(String systemConfigDocument);
	bool getGuruState();
	bool getNoviceState();
	bool getOpenSourceState();
	void showObject(MetaObject object);
	void showProgress(String s);
	bool isMetadataDirty();
	void setIsMetadataDirty(bool isMetadataDirty);
	Component getLastFocusedComponent();
	void setLastFocused(Component lastFocused);
	void setMouseSelectState(bool isMouseSelected);
	bool isMouseSelected();
	LanguageManager getLanguageManager();
	Project getProject();
	Project getFirstProject();
	Project getLastProject();
	String getNewProjectName();
	void setComponentSizes(Dimension dim);
    String getCurrentDir();
	void setCurrentDir(String newDir);
	void updateStatus(int dotPos, int markPos);
	Class[] getDataBaseClasses(); //이건 뭐지
	MetadataFeeder getMetadataFeeder();
	void addProject(Project project);
	bool setCurrentProject(Project project);
	bool removeProject(Project project);
	MetaProjectHeader getProgramMetadata();
	 void resetDashboard();
	 Project loadProject(String fileName, String projectName);
	 void setCanSaveMetadata(boolean canSave);
	 MetaObject getSelectedObject();
	void deselectObjects();
	void setProject(Project project);
	void editorAction(String actionName, ActionEvent event);
	void setMode(int mode);
	FileManager getFileManager();
	void setFileManager(FileManager fileManager);
	ConfigManager getConfigManager();
	void setConfigManager(ConfigManager configManager);
	ClassLoader getClassLoader();
	void setClassLoader(ClassLoader classLoader);
	Properties getProps();
	String getUserHome();
	String getBaseDir();
	int getMajorVersionNumber();
	int getMinorVersionNumber();
	int getBuildNumber();
	MetaObject pasting(MetaObject target, MetaObject pasted, MetaProject project);
	void processMenuItems(MetaObject metaObject);
	void processMenuSeparators(MetaObject metaObject);
	void processTabPages(MetaObject metaObject);
	void processPlacement(MetaObject object);
	void processCreateLayout(MetaObject object);
	void updateDisplayLayer(MetaObject object, int layerIndex);
	void propertyEditedRepaint(MetaObject object);
	void processDeleteObject(MetaObject object);
	bool getAttachedToDesigner();
	void processProjectChangedState(bool hasProjectChanged);
	void processObjectNameChanged(MetaObject object);
	void runProject();
	void setAllowDragging(bool allowDragging);
	bool allowDragging();
	bool isCustomizing();
	void setTitle(String title);
	IdeMenuBar getIdeMenuBar();
	void showHelper(MetaObject metaObject, String propertyName);

	// ... many non-public methods follow ...
}
```

- 위의 클래스는 공개 메서드가 약 70개 정도 되는 정말 큰 클래스다.

- 이 클래스를 아래와 같이 메서드 몇 개만으로 만든다면?

```dart
class SuperDashboard extends JFrame implements MetaDataUser {
	Component getLastFocusedComponent();
	void setLastFocused(Component lastFocused);
	int getMajorVersionNumber();
	int getMinorVersionNumber();
	int getBuildNumber();
}
```

- 이 클래스는 크기가 작은가?
  → 물리적인 크기는 작지만 ** “책임” **이 너무 많다. 이래서는 안된다.
- 클래스 이름은 해당 클래스의 책임(역할)을 기술해야 한다.
  - 클래스 이름이 떠오르지 않는다면 책임이 너무 많거나 크기가 커서 그런것이다.
  - 클래스 설명은 if, and, but, or을 사용하지 않고 25단어 내외로 가능해야 한다.

### 단일 책임 원칙

- 단일 책임 원칙(Single Responsibility Principle,SRP) : 클래스를 변경하는 이유는 단 하나뿐이어야 한다. (클래스의 책임은 하나뿐이어야 한다)
- 위의 8줄짜리 SuperDashboard 클래스를 변경해야 하는 이유는 두가지다.
  1. SuperDashboard는 소프트웨어 버전 정보를 추적한다. 근데 버전 정보는 소프트웨어를 출시할 때마다 달라진다.
  2. SuperDashboard는 스윙 컴포넌트를 관리하며 코드를 변경할 때마다 버전 번호가 달라진다.
- 위의 클래스에서 버전관리 메서드 3개 만을 따로하는 Version이라는 클래스를 만들어준다.

```dart
class Version {
	int getMajorVersionNumber();
	int getMinorVersionNumber();
	int getBuildNumber();
}
```

→ 이렇게 만든 Version의 경우 버전이 바뀌는 것이 클래스 변경의 이유가 되며, 다른 곳에서 재사용 하기도 쉬운 구조가 된다.

- SRP는 중요한 개념이고, 이해하고 지키기 수원한 개념이지만 가장 많이 무시되는 규칙이기도 하다.

  - 개발자가 “깨끗하고 체계적인 소프트웨어”보다 “돌아가는 소프트웨어”에 초점을 맞추기 때문이다.

    → 프로그램이 돌아가기만 하면 된다라는 마음으로 SRP를 지키지 않는다.

  - 또한 자잘한 단일 책임 클래스가 많아지면 큰 그림을 이해하기 어려워진다고 우려한다.

    → 하지만 이는 사실이 아니다. 작은 클래스가 많은 시스템이든 큰 클래스가 몇 개뿐인 시스템이든 돌아가는 부품은 수가 비슷하다.

    → 예를 들어 “도구 상자를 관리하는데 작은 서랍을 많이 두고 기능과 이름을 명확하게 나눠놓느냐, 아니면 큰 서랍 몇개에 전부 던져놓고 싶은가?”의 차이이다.

### 응집도

- 클래스는 인스턴스 변수 수가 작아야 한다.
- 또한 각 클래스 메서드는 클래스 인스턴스 변수를 하나 이상 사용해야 한다.

→ 일반적으로 메서드가 변수를 더 많이 사용할수록 응집도가 높아진다.

- 우리는 응집도가 높은 클래스를 선호한다.

  → 응집도가 높다는 소리는 클래스에 속한 메서드와 변수로 서로 의존하며 논리적인 단위로 묶인다는 의미기 때문이다.

```dart
class Stack {
	int topOfStack = 0;
	List<int> elements;
    int size() {
		return topOfStack;
	}

	void push(int element) {
		topOfStack++;
		elements.add(element);
	}

	int pop() {
        try {
            if (topOfStack == 0) {
                throw PoppedWhenEmpty();
            }
		    int element = elements.indexWhere(--topOfStack);
		    elements.removeAt(topOfStack);
		    return element;
        } catch (e,s) {
            log(e, stackTrace: s);
        }
		
	}
}
```

- 위 Stack 클래스는 응집도가 아주 높다. size()를 제외한 다른 두 메서드는 두 변수를 모두 사용한다.

- “함수를 작게, 매개변수 목록을 짧게”라는 전략을 따르다 보면 때때로 몇몇 메서드만이 사용하는 인스턴스 변수가 아주 많아진다.
  → 이는 새로운 클래스로 쪼개야 한다는 신호다. 응집도가 높아지도록 변수와 메서드를 적절히 분리해 새로은 클래스를 만든다.

### 응집도를 유지하면 작은 클래스 여럿이 나온다

- 큰 함수를 작은 함수 여럿으로 나누기만 해도 클래스 수가 많아진다

  - 예를들어 변수가 아주 많은 큰 함수 하나가 있다. 큰 함수 일부를 작은 함수 하나로 빼내고 싶은데, 빼내려는 코드가 큰 함수에 정의된 변수 넷을 사용한다.

    → 네 변수를 클래스 인스턴스 변수로 승격한다면 새 함수는 인수가 필요없다.

  ```dart
  class AAA{
  	void fourOperations(){
  		int a=1,b=2,c,3,d=4;
        print('${a+b+c+d}');
  		... //많은 양의 코드들
  	}
  }
  ```

  ```dart
  class AAA{
  	int a=1,b=2,c=3,d=4; //fourOperations 안에 있는 a,b,c,d를 클래스 인스턴스 변수로 만들어줌
  	void sum(){ //인수 없이 사용가능.
        print('${a+b+c+d}');
  	}
  	void fourOperations(){

  	}
  }
  ```

- 하지만 이렇게 되면 클래스가 응집력을 잃는다. 몇몇 함수만 사용하는 인스턴스 변수가 점점 늘어나기 때문.

  → 이렇게 응집력을 잃는다면 클래스를 쪼개라!

  → 큰 함수를 작은 함수, 큰 클래스를 작은 클래스로 쪼개다 보면 프로그램에 점점 더 체계가 잡히고 구조가 투명해진다.

- 다음의 예시를 보라

  ```dart
  // 유서깊은 예제

  class PrintPrimes {
  	void main() {
  		const int M = 1000;
  		const int RR = 50;
  		const int CC = 4;
  		const int WW = 10;
  		const int ORDMAX = 30;
  		List<int> P = List<int>.filled(M+1, 0, growable: true);
  		late int PAGENUMBER;
  		late int PAGEOFFSET;
  		late int ROWOFFSET;
  		late int C;
  		late int J;
  		late int K;
  		late bool JPRIME;
  		late int ORD;
  		late int SQUARE;
  		late int N;
		List<int> MULT = List<int>.filled(ORDMAX + 1, 0, growable: true);

  		J = 1;
  		K = 1;
  		P[1] = 2;
  		ORD = 2;
  		SQUARE = 9;

  		while (K < M) {
  			do {
  				J = J + 2;
  				if (J == SQUARE) {
  					ORD = ORD + 1;
  					SQUARE = P[ORD] * P[ORD];
  					MULT[ORD - 1] = J;
  				}
  				N = 2;
  				JPRIME = true;
  				while (N < ORD && JPRIME) {
  					while (MULT[N] < J)
  						MULT[N] = MULT[N] + P[N] + P[N];
  					if (MULT[N] == J)
  						JPRIME = false;
  					N = N + 1;
  				}
  			} while (!JPRIME);
  			K = K + 1;
  			P[K] = J;
  		}
  		{
  			PAGENUMBER = 1;
  			PAGEOFFSET = 1;
  			while (PAGEOFFSET <= M) {
				print('The First $M Prime Numbers --- Page $PAGENUMBER\n');
  				for (ROWOFFSET = PAGEOFFSET; ROWOFFSET < PAGEOFFSET + RR; ROWOFFSET++) {
  					for (C = 0; C < CC;C++)
  						if (ROWOFFSET + C * RR <= M)
							print("${P[ROWOFFSET + C * RR]}");
  					print('\n');
  				}
				print('\f');
				PAGENUMBER = PAGENUMBER + 1;
				PAGEOFFSET = PAGEOFFSET + RR * CC;
  			}
  		}
  	}
  }
  ```

  → 이 코드는 함수가 하나로 이루어진대다가 들여쓰기가 심하고, 변수도 정리가 안되어있고, 구조가 빡빡하게 결합되었다.

  → 최소한 여러 함수로는. 나눠야 한다.

- 위의 긴 코드를 작은 함수와 클래스로 나눈 후 함수, 클래스, 변수에 좀 더 의미 있는 이름을 부여한 결과

```dart
class PrimePrinter {
	void main() {
		const int NUMBER_OF_PRIMES = 1000;
		List<int> primes = PrimeGenerator.generate(NUMBER_OF_PRIMES);

		const int ROWS_PER_PAGE = 50;
		const int COLUMNS_PER_PAGE = 4;
		RowColumnPagePrinter tablePrinter = RowColumnPagePrinter(ROWS_PER_PAGE, COLUMNS_PER_PAGE, "The First $NUMBER_OF_PRIMES Prime Numbers");
		tablePrinter.print(primes);
	}
}
```

```dart
class RowColumnPagePrinter {
	final int _rowsPerPage;
	final int _columnsPerPage;
	final int _numbersPerPage;
	final String _pageHeader;

	const RowColumnPagePrinter(int rowsPerPage, int columnsPerPage, String pageHeader) {
		this.rowsPerPage = rowsPerPage;
		this.columnsPerPage = columnsPerPage;
		this.pageHeader = pageHeader;
		numbersPerPage = rowsPerPage * columnsPerPage;
	}

	void print(List<int> data) {
		int pageNumber = 1;
		for (int firstIndexOnPage = 0 ;
			firstIndexOnPage < data.length ;
			firstIndexOnPage += numbersPerPage) {
			int int lastIndexOnPage =  Math.min(firstIndexOnPage + numbersPerPage - 1, data.length - 1);
			_printPageHeader(pageHeader, pageNumber);
			_printPage(firstIndexOnPage, lastIndexOnPage, data);
			print('\f');
			pageNumber++;
		}
	}

	void _printPage(int firstIndexOnPage, int lastIndexOnPage, List<int> data) {
		int firstIndexOfLastRowOnPage =
		firstIndexOnPage + rowsPerPage - 1;
		for (int firstIndexInRow = firstIndexOnPage ;
			firstIndexInRow <= firstIndexOfLastRowOnPage ;
			firstIndexInRow++) {
			_printRow(firstIndexInRow, lastIndexOnPage, data);
			print('\n');
		}
	}

	void _printRow(int firstIndexInRow, int lastIndexOnPage, int[] data) {
		for (int column = 0; column < columnsPerPage; column++) {
			int index = firstIndexInRow + column * rowsPerPage;
			if (index <= lastIndexOnPage)
				printStream.format("%10d", data[index]);
		}
	}

	void _printPageHeader(String pageHeader, int pageNumber) {
		print('$pageHeader --- Page $pageNumber\n\n');
	}
}
```

```dart

class PrimeGenerator {
	static List<int> _primes;
	static List<int> _multiplesOfPrimeFactors;

	List<int> generate(int n) {
		_primes = List<int>.filled(n,0,growable: true);
		_multiplesOfPrimeFactors = List<int>();
		_set2AsFirstPrime();
		_checkOddNumbersForSubsequentPrimes();
		return _primes;
	}

	void _set2AsFirstPrime() {
		_primes[0] = 2;
		_multiplesOfPrimeFactors.add(2);
	}

	void _checkOddNumbersForSubsequentPrimes() {
		int primeIndex = 1;
		for (int candidate = 3 ; primeIndex < _primes.length ; candidate += 2) {
			if (_isPrime(candidate))
				_primes[primeIndex++] = candidate;
		}
	}

	bool _isPrime(int candidate) {
		if (_isLeastRelevantMultipleOfNextLargerPrimeFactor(candidate)) {
			_multiplesOfPrimeFactors.add(candidate);
			return false;
		}
		return _isNotMultipleOfAnyPreviousPrimeFactor(candidate);
	}

	bool _isLeastRelevantMultipleOfNextLargerPrimeFactor(int candidate) {
		int nextLargerPrimeFactor = _primes[_multiplesOfPrimeFactors.size()];
		int leastRelevantMultiple = nextLargerPrimeFactor * nextLargerPrimeFactor;
		return candidate == leastRelevantMultiple;
	}

	bool _isNotMultipleOfAnyPreviousPrimeFactor(int candidate) {
		for (int n = 1; n < _multiplesOfPrimeFactors.size(); n++) {
			if (isMultipleOfNthPrimeFactor(candidate, n))
				return false;
		}
		return true;
	}

	bool _isMultipleOfNthPrimeFactor(int candidate, int n) {
		return candidate == _smallestOddNthMultipleNotLessThanCandidate(candidate, n);
	}

	int _smallestOddNthMultipleNotLessThanCandidate(int candidate, int n) {
		int multiple = _multiplesOfPrimeFactors[n];
		while (multiple < candidate) {
			multiple += 2 * primes[n];
		}			
		_multiplesOfPrimeFactors[n] = multiple;
		return multiple;
	}
}
```

- 이렇게 리펙토링 한 결과
  - 길이가 늘어났다
    1. 서술적인 변수 이름 사용
    2. 코드에 주석을 추가하여 가독성 높임
    3. 공백을 추가하여 가독성 높임
       ⇒ 길이가 늘어났지만 보기가 훨씬더 좋아졌다.
  - 원래 코드의 세가지 책임을 각각 분산했다.
    1. PrimePrinter 클래스는 main 함수만을 포함하며 실행 환경을 책임짐.
    2. RowCollumnPagePrinter 클래스는 숫자 목록을 주어진 행과 열에 맞춰 출력해줌.
    3. PrimeGenerator 클래스는 소수 목록을 생성하는 방법을 계산.
  - 원래 코드와 바뀐 코드의 알고리즘과 동작 원리는 동일하다.

### 변경하기 쉬운 클래스

- 대다수의 시스템은 지속적인 변경이 가해진다. 또한 변경할 때마다 시스템이 의도대로 동작하지 않을 위험이 따른다.

  → 깨끗한 시스템은 클래스를 체계적으로 정리해 변경에 수반하는 위험을 낮춘다.

- 다음은 SQL 문자열을 만드는 sql 클래스다.
  ```dart
  class Sql {
	final String table;
	final List<Column> columns;
  	Sql({required this.table, required this.columns});

  	String create();
  	String insert(List<Object> fields);
  	String selectAll();
  	String findByKey(String keyColumn, String keyValue);
	String select(Column column, String pattern);
  	String select(Criteria criteria);
  	String preparedInsert();
  	String _columnList(List<Column> columns);
  	String _valuesList(List<Object> fields, List<Column>columns);
	String _selectWithCriteria(String criteria);
  	String _placeholderList(List<Column> columns);
  }
  ```
  - 두가지 이유로 변경을 해야하므로 SRP 위반
    1. 새로운 SQL문을 지원할때 sql 클래스를 변경해야함
    2. 기본 sql문을 수정할때도 sql 클래스를 변경해야함
- 위에서 공개 인터페이스를 각각 sql 클래스에서 파생하는 클래스로 만들었다.

  ```dart
  class Sql {
  	final String table;
	final List<Column> columns;
  	Sql({required this.table, required this.columns});
  	abstract String generate();
  }
  class CreateSql extends Sql {
	final String table;
	final List<Column> columns;
  	CreateSql({required this.table, required this.columns});
  	String generate();
  }

  class SelectSql extends Sql {
	final String table;
	final List<Column> columns;
  	SelectSql({required this.table, required this.columns});
  	String generate();
  }

  class InsertSql extends Sql {
	final String table;
	final List<Column> columns;
	final List<Object> fields;
  	InsertSql({required this.table, required this.columns, required this.fields});
  	String generate();
  	String _valuesList(List<Object> fields, List<Column> columns);
  }

  class SelectWithCriteriaSql extends Sql {
	final String table;
	final List<Column> columns;
	final Criteria criteria;
  	SelectWithCriteriaSql({required this.table, required this.columns, required this.criteria});
  	String generate();
  }

  class SelectWithMatchSql extends Sql {
	final String table;
	final List<Column> columns;
	final String pattern;
  	SelectWithMatchSql({required this.table, required this.columns, required this.pattern});
  	String generate();
  }

  class FindByKeySql extends Sql {
	final String table;
	final List<Column> columns;
	final String keyValue;
  	FindByKeySql({required this.table, required this.columns, required this.keyValue});
  	String generate();
  }

  class PreparedInsertSql extends Sql {
	final String table;
	final List<Column> columns;
  	PreparedInsertSql({required this.table, required this.columns});
  	String generate();

  	String _placeholderList(List<Column> columns);
  }

  class Where {
	final String criteria;
  	Where(required this.criteria);
  	String generate();
  }

  class ColumnList {
	final List<Column> columns;
  	ColumnList(required this.columns);
  	String generate();
  }
  ```

  - 이렇게 고친다면 각 클래스는 극도로 단순해지고 코드를 순식간에 이해할 수 있다.
  - 또한 함수 하나를 수정했다고 다른 함수가 망가질 위험도 사실상 사라진다.
  - 새로운 sql문을 추가하여도 다른 코드가 망가지지 않는다.
  - 객체 지향 설계인 OCP(Open-Closed Principle)도 지원한다.
    - OCP란 클래스는 확장에 개방적이고 수정에 폐쇄적이어야 한다는 원칙
      → 위의 sql 클래스는 파생 클래스를 생성하는 방식으로 새 기능에 개방적인 동시에, 다른 클래스를 닫아 놓는 방식으로 수정에 폐쇄적이다.

### 변경으로부터 격리

- 코드는 변한다.
- 클래스는 추상 클래스와 클라이언트 클래스로 나누는데, 상세한 구현에 의존하는 클라이언트 클래스는 구현이 바뀌면 위험에 빠진다.
  → 그래서 추상클래스를 사용해 구현에 미치는 영향을 격리한다.
- 상세한 구현에 의존하는 코드는 테스트가 어렵다
  - 예를 들어, PortFolio 클래스를 만든다고 가정하자. 그런데 이 클래스는 TokyoStockExchange라는 외부 api를 사용해 값을 계산한다.
    → 5분마다 값이 달라지는 api이므로 테스트 코드를 짜기란 쉽지 않다.
- 그래서 직접 api를 호출하는게 아닌 exchange라는 추상클래스 생성 후 메서드를 하나 선언

```dart
class StockExchange {
	abstract Money currentPrice(String Symbol);
}
```

- 다음으로 StockExchange 추상클래스를 구현하는 TokyoStockExchange 클래스를 구현.
  또한 PortFolio 생성자를 수정해 StockExchange 참조를 인수로 받는다.

```dart
class Portfolio {
	StockExchange exchange;
	Portfolio();
}
```

- 이제 TokyoStockExchange 클래스를 흉내내는 테스트용 클래스를 만들 수 있다.
  - 테스트용 클래스는 StockExchagne 추상클래스를 사용하여 고정된 주가를 반환.
  - 고정된 값을 테스트에 이용.

```dart
// 테스트용 클래스는 StockExchange 인터페이스를 구현하며 고정된 주가를 반환한다.

class PortfolioTest {
	FixedStockExchangeStub exchange;
	Portfolio portfolio;

	void setUp() {
		try {
			exchange = FixedStockExchangeStub();
			exchange.fix("MSFT", 100);
			portfolio = Portfolio(exchange);
		} catch (e,s) {
			log(e, stackTrace: s);
		}
		
	}

	void GivenFiveMSFTTotalShouldBe500() {
		try {
			portfolio.add(5, "MSFT");
			Assert.assertEquals(500, portfolio.value());
		} catch(e,s) {
			log(e, stackTrace: s);
		}
		
	}
}
```

- 위에서 개선한 Portfolio 클래스는 상세 구현 클래스가 아닌 StockExchange라는 인터페이스에 의존하므로,실제로 주가를 얻어오는 출처나 얻어오는 방식 등과 같은 구체적인 사실을 모두 숨길 수 있다.
