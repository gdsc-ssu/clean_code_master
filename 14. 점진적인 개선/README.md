# 14장 점진적인 개선

이 장은 점진적인 개선을 보여주는 사례 연구이다. 확장성이 부족한 모듈을 개선, 정리하는 단계를 살펴본다.

<br>

## 1. Args 소개

문자열 배열을 분석하는 유틸리티 “`Args`"

- **사용법)** `Args` 생성자에 “인수 문자열”과 “형식 문자열”을 넘겨 `Args` 인스턴스를 생성한 후, `Args` 인스턴스에다 인수 값을 질의한다.
- **첫 번째 매개변수)** 형식 또는 스키마 ( “`l,p#,d*`" )
  - `-l` : bool 인수
  - `-p` : 정수 인수
  - `-d` : 문자열 인수
- **두 번째 매개변수)** main으로 넘어온 명령행 인수 배열

```java
// 간단한 Args 사용법
public static void main(String[] args) {
  try {
    Args arg = new Args("l,p#,d*", args);
    boolean logging = arg.getBoolean('l');
    int port = arg.getInt('p');
    String directory = arg.getString('d');
    executeApplication(logging, port, directory);
  } catch (ArgsException e) {
    System.out.print("Argument error: %s\\n", e.errorMessage());
  }
}
```

<br>

## 2. Args 구현

아래 코드는 스타일과 구조에 신경을 쓴 괜찮은 `Args` 클래스다. 여기저기 뒤적일 필요 없이 위에서 아래로 코드가 읽힌다는 사실에 주목한다.

```java
// Args.java
package com.objectmentor.utilities.args;

import static com.objectmentor.utilities.args.ArgsException.ErrorCode.*; 
import java.util.*;

public class Args {
  private Map<Character, ArgumentMarshaler> marshalers;
  private Set<Character> argsFound;
  private ListIterator<String> currentArgument;
  
  public Args(String schema, String[] args) throws ArgsException { 
    marshalers = new HashMap<Character, ArgumentMarshaler>(); 
    argsFound = new HashSet<Character>();
    
    parseSchema(schema);
    parseArgumentStrings(Arrays.asList(args)); 
  }
  
  private void parseSchema(String schema) throws ArgsException { 
    for (String element : schema.split(","))
      if (element.length() > 0) 
        parseSchemaElement(element.trim());
  }
  
  private void parseSchemaElement(String element) throws ArgsException { 
    char elementId = element.charAt(0);
    String elementTail = element.substring(1); validateSchemaElementId(elementId);
    if (elementTail.length() == 0)
      marshalers.put(elementId, new BooleanArgumentMarshaler());
    else if (elementTail.equals("*")) 
      marshalers.put(elementId, new StringArgumentMarshaler());
    else if (elementTail.equals("#"))
      marshalers.put(elementId, new IntegerArgumentMarshaler());
    else if (elementTail.equals("##")) 
      marshalers.put(elementId, new DoubleArgumentMarshaler());
    else if (elementTail.equals("[*]"))
      marshalers.put(elementId, new StringArrayArgumentMarshaler());
    else
      throw new ArgsException(INVALID_ARGUMENT_FORMAT, elementId, elementTail);
  }
  
  private void validateSchemaElementId(char elementId) throws ArgsException { 
    if (!Character.isLetter(elementId))
      throw new ArgsException(INVALID_ARGUMENT_NAME, elementId, null); 
  }
  
  private void parseArgumentStrings(List<String> argsList) throws ArgsException {
    for (currentArgument = argsList.listIterator(); currentArgument.hasNext();) {
      String argString = currentArgument.next(); 
      if (argString.startsWith("-")) {
        parseArgumentCharacters(argString.substring(1)); 
      } else {
        currentArgument.previous();
        break; 
      }
    } 
  }
  
  private void parseArgumentCharacters(String argChars) throws ArgsException { 
    for (int i = 0; i < argChars.length(); i++)
      parseArgumentCharacter(argChars.charAt(i)); 
  }
  
  private void parseArgumentCharacter(char argChar) throws ArgsException { 
    ArgumentMarshaler m = marshalers.get(argChar);
    if (m == null) {
      throw new ArgsException(UNEXPECTED_ARGUMENT, argChar, null); 
    } else {
      argsFound.add(argChar); 
      try {
        m.set(currentArgument); 
      } catch (ArgsException e) {
        e.setErrorArgumentId(argChar);
        throw e; 
      }
    } 
  }
  
  public boolean has(char arg) { 
    return argsFound.contains(arg);
  }
  
  public int nextArgument() {
    return currentArgument.nextIndex();
  }
  
  public boolean getBoolean(char arg) {
    return BooleanArgumentMarshaler.getValue(marshalers.get(arg));
  }
  
  public String getString(char arg) {
    return StringArgumentMarshaler.getValue(marshalers.get(arg));
  }
  
  public int getInt(char arg) {
    return IntegerArgumentMarshaler.getValue(marshalers.get(arg));
  }
  
  public double getDouble(char arg) {
    return DoubleArgumentMarshaler.getValue(marshalers.get(arg));
  }
  
  public String[] getStringArray(char arg) {
    return StringArrayArgumentMarshaler.getValue(marshalers.get(arg));
  } 
}
```

<br>

한 가지 먼저 읽어볼 코드가 있다면 `ArgumentMarshaler` 정의인데, 아래 코드들은 `ArgumentMarshaler` 인터페이스와 파생 클래스다.

```java
// ArgumentMarshaler.java
public interface ArgumentMarshaler {
  void set(Iterator<String> currentArgument) throws ArgsException;
}

// BooleanArgumentMarshaler.java
public class BooleanArgumentMarshaler implements ArgumentMarshaler { 
  private boolean booleanValue = false;
  
  public void set(Iterator<String> currentArgument) throws ArgsException { 
    booleanValue = true;
  }
  
  public static boolean getValue(ArgumentMarshaler am) {
    if (am != null && am instanceof BooleanArgumentMarshaler)
      return ((BooleanArgumentMarshaler) am).booleanValue; 
    else
      return false; 
  }
}

// StringArgumentMarshaler.java
import static com.objectmentor.utilities.args.ArgsException.ErrorCode.*;

public class StringArgumentMarshaler implements ArgumentMarshaler { 
  private String stringValue = "";
  
  public void set(Iterator<String> currentArgument) throws ArgsException { 
    try {
      stringValue = currentArgument.next(); 
    } catch (NoSuchElementException e) {
      throw new ArgsException(MISSING_STRING); 
    }
  }
  
  public static String getValue(ArgumentMarshaler am) {
    if (am != null && am instanceof StringArgumentMarshaler)
      return ((StringArgumentMarshaler) am).stringValue; 
    else
      return ""; 
  }
}

// IntegerArgumentMarshaler.java
import static com.objectmentor.utilities.args.ArgsException.ErrorCode.*;

public class IntegerArgumentMarshaler implements ArgumentMarshaler { 
  private int intValue = 0;
  
  public void set(Iterator<String> currentArgument) throws ArgsException { 
    String parameter = null;
    try {
      parameter = currentArgument.next();
      intValue = Integer.parseInt(parameter);
    } catch (NoSuchElementException e) {
      throw new ArgsException(MISSING_INTEGER);
    } catch (NumberFormatException e) {
      throw new ArgsException(INVALID_INTEGER, parameter); 
    }
  }
  
  public static int getValue(ArgumentMarshaler am) {
    if (am != null && am instanceof IntegerArgumentMarshaler)
      return ((IntegerArgumentMarshaler) am).intValue; 
    else
    return 0; 
  }
}

// 나머지 DoubleArgumentMarshaler와 StringArrayArgumentMarshaler는 
// 다른 파생 클래스와 똑같은 패턴이므로 코드를 생략한다.
```

<br>

여기서 눈에 거슬릴 수 있는 부분은 **오류 코드 상수를 정의**하는 부분이다.

```java
import static com.objectmentor.utilities.args.ArgsException.ErrorCode.*;

public class ArgsException extends Exception { 
  private char errorArgumentId = '\\0'; 
  private String errorParameter = null; 
  private ErrorCode errorCode = OK;
  
  public ArgsException() {}
  
  public ArgsException(String message) {super(message);}
  
  public ArgsException(ErrorCode errorCode) { 
    this.errorCode = errorCode;
  }
  
  public ArgsException(ErrorCode errorCode, String errorParameter) { 
    this.errorCode = errorCode;
    this.errorParameter = errorParameter;
  }
  
  public ArgsException(ErrorCode errorCode, char errorArgumentId, String errorParameter) {
    this.errorCode = errorCode; 
    this.errorParameter = errorParameter; 
    this.errorArgumentId = errorArgumentId;
  }
  
  public char getErrorArgumentId() { 
    return errorArgumentId;
  }
  
  public void setErrorArgumentId(char errorArgumentId) { 
    this.errorArgumentId = errorArgumentId;
  }
  
  public String getErrorParameter() { 
    return errorParameter;
  }
  
  public void setErrorParameter(String errorParameter) { 
    this.errorParameter = errorParameter;
  }
  
  public ErrorCode getErrorCode() { 
    return errorCode;
  }
  
  public void setErrorCode(ErrorCode errorCode) { 
    this.errorCode = errorCode;
  }
  
  public String errorMessage() { 
    switch (errorCode) {
      case OK:
        return "TILT: Should not get here.";
      case UNEXPECTED_ARGUMENT:
        return String.format("Argument -%c unexpected.", errorArgumentId);
      case MISSING_STRING:
        return String.format("Could not find string parameter for -%c.", errorArgumentId);
      case INVALID_INTEGER:
        return String.format("Argument -%c expects an integer but was '%s'.", errorArgumentId, errorParameter);
      case MISSING_INTEGER:
        return String.format("Could not find integer parameter for -%c.", errorArgumentId);
      case INVALID_DOUBLE:
        return String.format("Argument -%c expects a double but was '%s'.", errorArgumentId, errorParameter);
      case MISSING_DOUBLE:
        return String.format("Could not find double parameter for -%c.", errorArgumentId); 
      case INVALID_ARGUMENT_NAME:
        return String.format("'%c' is not a valid argument name.", errorArgumentId);
      case INVALID_ARGUMENT_FORMAT:
        return String.format("'%s' is not a valid argument format.", errorParameter);
    }
    return ""; 
  }
  
  public enum ErrorCode {
    OK, INVALID_ARGUMENT_FORMAT, UNEXPECTED_ARGUMENT, INVALID_ARGUMENT_NAME, 
    MISSING_STRING, MISSING_INTEGER, INVALID_INTEGER, MISSING_DOUBLE, INVALID_DOUBLE
  }
}
```

<br>

이처럼 단순한 개념을 구현하는데 코드가 많이 필요한 이유는 자바로 구현했기 때문이다. 자바는 정적 타입 언어라서 타입 시스템을 만족하려면 많은 단어가 필요하다.

하지만 위 코드는 이름을 붙인 방법, 함수 크기, 코드 형식 등을 고려한다면 전반적으로 깔끔한 구조에 잘 짜인 프로그램으로 여겨주면 좋겠다.

<br>

처음부터 위처럼 구현하기는 어렵다. 프로그래밍은 과학보다 공예(craft)에 가깝기 때문에 **깨끗한 코드를 짜려면 먼저 지저분한 코드를 짠 뒤에 정리해야 한다.**

대다수의 신참 프로그래머는 무조건 돌아가는 프로그램을 목표로 잡고, 일단 프로그램이 '돌아가면' 다음 업무로 넘어간다. '돌아가는' 프로그램은 그 상태가 어떻든 그대로 버려둔다. 경험이 풍부한 전문 프로그래머라면 이런 행동이 전문가로서 자살 행위라는 사실을 잘 안다.

<br>

## 3. Args: 1차 초안

아래 코드는 맨 처음 짰던 Args 클래스다. 코드는 '돌아가지만' 엉망이다.

```java
// Args.java (1차 초안)
import java.text.ParseException; 
import java.util.*;

public class Args {
  private String schema;
  private String[] args;
  private boolean valid = true;
  private Set<Character> unexpectedArguments = new TreeSet<Character>(); 
  private Map<Character, Boolean> booleanArgs = new HashMap<Character, Boolean>();
  private Map<Character, String> stringArgs = new HashMap<Character, String>(); 
  private Map<Character, Integer> intArgs = new HashMap<Character, Integer>(); 
  private Set<Character> argsFound = new HashSet<Character>();
  private int currentArgument;
  private char errorArgumentId = '\\0';
  private String errorParameter = "TILT";
  private ErrorCode errorCode = ErrorCode.OK;
  
  private enum ErrorCode {
    OK, MISSING_STRING, MISSING_INTEGER, INVALID_INTEGER, UNEXPECTED_ARGUMENT}
    
  public Args(String schema, String[] args) throws ParseException { 
    this.schema = schema;
    this.args = args;
    valid = parse();
  }
  
  private boolean parse() throws ParseException { 
    if (schema.length() == 0 && args.length == 0)
      return true; 
    parseSchema(); 
    try {
      parseArguments();
    } catch (ArgsException e) {
    }
    return valid;
  }
  
  private boolean parseSchema() throws ParseException { 
    for (String element : schema.split(",")) {
      if (element.length() > 0) {
        String trimmedElement = element.trim(); 
        parseSchemaElement(trimmedElement);
      } 
    }
    return true; 
  }
  
  private void parseSchemaElement(String element) throws ParseException { 
    char elementId = element.charAt(0);
    String elementTail = element.substring(1); 
    validateSchemaElementId(elementId);
    if (isBooleanSchemaElement(elementTail)) 
      parseBooleanSchemaElement(elementId);
    else if (isStringSchemaElement(elementTail)) 
      parseStringSchemaElement(elementId);
    else if (isIntegerSchemaElement(elementTail)) 
      parseIntegerSchemaElement(elementId);
    else
      throw new ParseException(String.format("Argument: %c has invalid format: %s.", 
        elementId, elementTail), 0);
    } 
  }
    
  private void validateSchemaElementId(char elementId) throws ParseException { 
    if (!Character.isLetter(elementId)) {
      throw new ParseException("Bad character:" + elementId + "in Args format: " + schema, 0);
    }
  }
  
  private void parseBooleanSchemaElement(char elementId) { 
    booleanArgs.put(elementId, false);
  }
  
  private void parseIntegerSchemaElement(char elementId) { 
    intArgs.put(elementId, 0);
  }
  
  private void parseStringSchemaElement(char elementId) { 
    stringArgs.put(elementId, "");
  }
  
  private boolean isStringSchemaElement(String elementTail) { 
    return elementTail.equals("*");
  }
  
  private boolean isBooleanSchemaElement(String elementTail) { 
    return elementTail.length() == 0;
  }
  
  private boolean isIntegerSchemaElement(String elementTail) { 
    return elementTail.equals("#");
  }
  
  private boolean parseArguments() throws ArgsException {
    for (currentArgument = 0; currentArgument < args.length; currentArgument++) {
      String arg = args[currentArgument];
      parseArgument(arg); 
    }
    return true; 
  }
  
  private void parseArgument(String arg) throws ArgsException { 
    if (arg.startsWith("-"))
      parseElements(arg); 
  }
  
  private void parseElements(String arg) throws ArgsException { 
    for (int i = 1; i < arg.length(); i++)
      parseElement(arg.charAt(i)); 
  }
  
  private void parseElement(char argChar) throws ArgsException { 
    if (setArgument(argChar))
      argsFound.add(argChar); 
    else 
      unexpectedArguments.add(argChar); 
      errorCode = ErrorCode.UNEXPECTED_ARGUMENT; 
      valid = false;
  }
  
  private boolean setArgument(char argChar) throws ArgsException { 
    if (isBooleanArg(argChar))
      setBooleanArg(argChar, true); 
    else if (isStringArg(argChar))
      setStringArg(argChar); 
    else if (isIntArg(argChar))
      setIntArg(argChar); 
    else
      return false;
    
    return true; 
  }
  
  private boolean isIntArg(char argChar) {
    return intArgs.containsKey(argChar);
  }
  
  private void setIntArg(char argChar) throws ArgsException { 
    currentArgument++;
    String parameter = null;
    try {
      parameter = args[currentArgument];
      intArgs.put(argChar, new Integer(parameter)); 
    } catch (ArrayIndexOutOfBoundsException e) {
      valid = false;
      errorArgumentId = argChar;
      errorCode = ErrorCode.MISSING_INTEGER;
      throw new ArgsException();
    } catch (NumberFormatException e) {
      valid = false;
      errorArgumentId = argChar; 
      errorParameter = parameter;
      errorCode = ErrorCode.INVALID_INTEGER; 
      throw new ArgsException();
    } 
  }
  
  private void setStringArg(char argChar) throws ArgsException { 
    currentArgument++;
    try {
      stringArgs.put(argChar, args[currentArgument]); 
    } catch (ArrayIndexOutOfBoundsException e) {
      valid = false;
      errorArgumentId = argChar;
      errorCode = ErrorCode.MISSING_STRING; 
      throw new ArgsException();
    } 
  }
  
  private boolean isStringArg(char argChar) { 
    return stringArgs.containsKey(argChar);
  }
  
  private void setBooleanArg(char argChar, boolean value) { 
    booleanArgs.put(argChar, value);
  }
  
  private boolean isBooleanArg(char argChar) { 
    return booleanArgs.containsKey(argChar);
  }
  
  public int cardinality() { 
    return argsFound.size();
  }
  
  public String usage() { 
    if (schema.length() > 0)
      return "-[" + schema + "]"; 
    else
      return ""; 
  }
  
  public String errorMessage() throws Exception { 
    switch (errorCode) {
      case OK:
        throw new Exception("TILT: Should not get here.");
      case UNEXPECTED_ARGUMENT:
        return unexpectedArgumentMessage();
      case MISSING_STRING:
        return String.format("Could not find string parameter for -%c.", errorArgumentId);
      case INVALID_INTEGER:
        return String.format("Argument -%c expects an integer but was '%s'.", errorArgumentId, errorParameter);
      case MISSING_INTEGER:
        return String.format("Could not find integer parameter for -%c.", errorArgumentId);
    }
    return ""; 
  }
  
  private String unexpectedArgumentMessage() {
    StringBuffer message = new StringBuffer("Argument(s) -"); 
    for (char c : unexpectedArguments) {
      message.append(c); 
    }
    message.append(" unexpected.");
    
    return message.toString(); 
  }
  
  private boolean falseIfNull(Boolean b) { 
    return b != null && b;
  }
  
  private int zeroIfNull(Integer i) { 
    return i == null ? 0 : i;
  }
  
  private String blankIfNull(String s) { 
    return s == null ? "" : s;
  }
  
  public String getString(char arg) { 
    return blankIfNull(stringArgs.get(arg));
  }
  
  public int getInt(char arg) {
    return zeroIfNull(intArgs.get(arg));
  }
  
  public boolean getBoolean(char arg) { 
    return falseIfNull(booleanArgs.get(arg));
  }
  
  public boolean has(char arg) { 
    return argsFound.contains(arg);
  }
  
  public boolean isValid() { 
    return valid;
  }
  
  private class ArgsException extends Exception {
  } 
}
```

위 코드는 명백히 미완성이다. 인스턴스 변수 개수도 압도적으로 많고, ‘TILT’와 같은 희안한 문자열, `HashSets`와 `TreeSets`, `try-catch-catch` 블록 등 모두가 지저분한 코드에 기여하는 요인이다.

골칫거리가 더 커지기 전에 기능을 더 이상 추가하지 않고 리팩터링을 시작해보자.

우선 인수 유형은 다양하지만 모두가 유사한 메서드를 제공하므로 클래스 하나가 적합하다고 판단했다. 그래서 `ArgumentMarshaler`라는 개념이 탄생했다.

<br>

## 4. 점진적으로 개선하기

프로그램을 망치는 가장 좋은 방법 중 하나는 개선이라는 이름 아래 구조를 크게 뒤집는 행위다. 어떤 프로그램은 그저 그런 '개선'에서 결코 회복하지 못한다. '개선' 전과 똑같이 프로그램을 돌리기가 아주 어렵기 때문이다.

그래서 **테스트 주도 개발**(Test-Driven Development, TDD)이라는 기법을 사용했다. TDD는 언제 어느 때라도 시스템이 돌아가야 한다는 원칙을 따른다.

변경 전후 시스템이 똑같이 돌아간다는 사실을 확인하려면 언제든 실행이 가능한 자동화된 테스트 슈트가 필요하다. 
(단위 테스트 슈트, 인수 테스트 사용)

<br>

- 1단계) `ArgumentMarshaler` 클래스의 골격 추가 (기존 코드 끝에)
- 2단계) `Boolean` 인수를 저장하는 `HashMap`에서 `Boolean` 인수 유형을 `ArgumentMarshaler` 유형으로 변경 (코드를 최소로 건드리는 단순한 변경)
- 3단계) `null` 점검 위치 변경 (`boolean` → `ArgumentMarshaler`, 새 인수 유형을 추가하기 위한 사전 작업)
  1. `getBoolean` 함수에서 `falseIfNull` 제거 (`falseIfNull` 함수 자체 제거)
  2. 함수를 두 행으로 쪼갠 후 `ArgumentMarshaler`를 `am`이라는 독자적인 변수에 저장, `null` 점검
- 4단계) 인수 추가 과정(String, Int)
  1. `HashMap` 변경 후 `parse`, `set`, `get` 함수 수정
  2. 인수 기능을 `ArgumentMarshaler`로 옮김
  3. 파생 클래스를 만들어 기능 분산
     1. `setBoolean` 함수를 `BooleanArgumentMarshaler`로 옮김
     2. `get` 메서드를 `BooleanArgumentMarshaler`로 옮김
     3. `String`과 `Integer` 인수 유형도 `set`, `get`을 옮긴 후 사용하지 않는 함수를 제거하고 변수를 옮김
- 5단계) `ArgumentMarshaler`로 맵을 만들어 원래 맵을 교체하고, 관련 메서드를 변경함
- 6단계) `isBooleanArg`, `isIntArg`, `isStringArg`를 `marshalers`를 사용한 방식으로 변경 
  → 직후 바로 `marshers.get`을 호출하는 코드 제거 
  → `isxxxArg` 메서드를 별도로 선언하지 않고 인라인 코드로 변경
- 7단계) `set` 함수에서 기존 `HashMap`을 marshalers `HashMap`으로 교체 (boolean, string, integer)
- 8단계) Boolean, String, Integer 맵을 사용하는 코드를 제거
- 9단계) 거의 사용하지 않는 `parse` 메서드 세 개를 인라인 코드로 변경
- 10단계) `setArgument` 함수에서 일일이 유형을 확인하는 방식에서 `ArgumentMarshaler.set`만 호출하면 충분하도록 변경 
  (`setIntArg`, `setStringArg`, `setBooleanArg`를 해당 Argument Marshaler 파생 클래스로 내림)
- 11단계) `set` 함수를 적절한 파생 클래스로 내림 (Boolean, String, Int 모두 동일)
  1. if-else가 연쇄적으로 이어지는 구문 제거를 위해 if-else 연쇄문에서 오류 코드를 꺼냄
  2. `setBooleanArg` 함수는 모든 책임을 `BooleanArgumentMarshaler`로 전가
  3. `ArgumentMarshaler`에 `set` 함수를 추가 (`setBooleanArg` 함수 제거하고, `set` 함수로 직접 호출)
- 12단계) `ArgumentMarshaler`를 인터페이스로 변환
- 13단계) 모든 예외를 하나로 모아 `ArgsException` 클래스를 만든 후, 독자 모듈로 옮김 
  (이제 `Args` 클래스가 던지는 예외는 `ArgsException`뿐)

<br>

## 5. 최종 코드

위 단계(물론 생략된 과정이 있다)를 통해 구조를 수정한 이후에 **새로운 인수 유형을 추가하기 쉬워졌으며**, 마지막 단계에서 **예외/오류 처리 코드를 완벽하게 분리**함으로서 완성도를 높였다.

**소프트웨어 설계는 분할만 잘해도 품질이 크게 높아진다. 적절한 장소를 만들어 코드만 분리해도 설계가 좋아진다. 관심사를 분리하면 코드를 이해하고 보수하기 훨씬 더 쉬워진다.**

<br>

최종적으로 완성된 코드는 아래와 같다.

```java
// ArgsException.java
public class ArgsException extends Exception { 
  private char errorArgumentId = '\\0'; 
  private String errorParameter = "TILT"; 
  private ErrorCode errorCode = ErrorCode.OK;
  
  public ArgsException() {}
  
  public ArgsException(String message) {super(message);}
  
  public ArgsException(ErrorCode errorCode) { 
    this.errorCode = errorCode;
  }
  
  public ArgsException(ErrorCode errorCode, String errorParameter) { 
    this.errorCode = errorCode;
    this.errorParameter = errorParameter;
  }
  
  public ArgsException(ErrorCode errorCode, char errorArgumentId, String errorParameter) {
    this.errorCode = errorCode; 
    this.errorParameter = errorParameter; 
    this.errorArgumentId = errorArgumentId;
  }
  
  public char getErrorArgumentId() { 
    return errorArgumentId;
  }
  
  public void setErrorArgumentId(char errorArgumentId) { 
    this.errorArgumentId = errorArgumentId;
  }
  
  public String getErrorParameter() { 
    return errorParameter;
  }
  
  public void setErrorParameter(String errorParameter) {  
    this.errorParameter = errorParameter;
  }
  
  public ErrorCode getErrorCode() { 
    return errorCode;
  }
  
  public void setErrorCode(ErrorCode errorCode) { 
    this.errorCode = errorCode;
  }
  
  public String errorMessage() throws Exception { 
    switch (errorCode) {
      case OK:
        throw new Exception("TILT: Should not get here.");
      case UNEXPECTED_ARGUMENT:
        return String.format("Argument -%c unexpected.", errorArgumentId);
      case MISSING_STRING:
        return String.format("Could not find string parameter for -%c.", errorArgumentId);
      case INVALID_INTEGER:
        return String.format("Argument -%c expects an integer but was '%s'.", errorArgumentId, errorParameter);
      case MISSING_INTEGER:
        return String.format("Could not find integer parameter for -%c.", errorArgumentId);
      case INVALID_DOUBLE:
        return String.format("Argument -%c expects a double but was '%s'.", errorArgumentId, errorParameter);
      case MISSING_DOUBLE:
        return String.format("Could not find double parameter for -%c.", errorArgumentId);
    }
    return ""; 
  }
  
  public enum ErrorCode {
    OK, INVALID_FORMAT, UNEXPECTED_ARGUMENT, INVALID_ARGUMENT_NAME, MISSING_STRING,
    MISSING_INTEGER, INVALID_INTEGER,
    MISSING_DOUBLE, INVALID_DOUBLE
  }
}
// Args.java
public class Args {
  private String schema;
  private Map<Character, ArgumentMarshaler> marshalers = new HashMap<Character, ArgumentMarshaler>();
  private Set<Character> argsFound = new HashSet<Character>(); 
  private Iterator<String> currentArgument;
  private List<String> argsList;
  
  public Args(String schema, String[] args) throws ArgsException { 
    this.schema = schema;
    argsList = Arrays.asList(args);
    parse();
  }
  
  private void parse() throws ArgsException { 
    parseSchema();
    parseArguments();
  }
  
  private boolean parseSchema() throws ArgsException {
    for (String element : schema.split(",")) { 
      if (element.length() > 0) {
        parseSchemaElement(element.trim()); 
      }
    }
    return true; 
  }
  
  private void parseSchemaElement(String element) throws ArgsException { 
    char elementId = element.charAt(0);
    String elementTail = element.substring(1); 
    validateSchemaElementId(elementId);
    if (elementTail.length() == 0)
      marshalers.put(elementId, new BooleanArgumentMarshaler());
    else if (elementTail.equals("*")) 
      marshalers.put(elementId, new StringArgumentMarshaler());
    else if (elementTail.equals("#"))
      marshalers.put(elementId, new IntegerArgumentMarshaler());
    else if (elementTail.equals("##")) 
      marshalers.put(elementId, new DoubleArgumentMarshaler());
    else
      throw new ArgsException(ArgsException.ErrorCode.INVALID_FORMAT, elementId, elementTail);
      
  private void validateSchemaElementId(char elementId) throws ArgsException { 
    if (!Character.isLetter(elementId)) {
      throw new ArgsException(ArgsException.ErrorCode.INVALID_ARGUMENT_NAME, elementId, null);
    } 
  }
  
  private void parseArguments() throws ArgsException {
    for (currentArgument = argsList.iterator(); currentArgument.hasNext();) {
      String arg = currentArgument.next();
      parseArgument(arg); 
    }
  }
  
  private void parseArgument(String arg) throws ArgsException { 
    if (arg.startsWith("-"))
      parseElements(arg); 
  }
  
  private void parseElements(String arg) throws ArgsException { 
    for (int i = 1; i < arg.length(); i++)
      parseElement(arg.charAt(i)); 
  }
  
  private void parseElement(char argChar) throws ArgsException { 
    if (setArgument(argChar))
      argsFound.add(argChar); 
    else 
      throw new ArgsException(ArgsException.ErrorCode.UNEXPECTED_ARGUMENT, argChar, null);
  } 
  
  private boolean setArgument(char argChar) throws ArgsException { 
    ArgumentMarshaler m = marshalers.get(argChar);
    if (m == null)
      return false; 
    try {
      m.set(currentArgument);
      return true;
    } catch (ArgsException e) {
      e.setErrorArgumentId(argChar);
      throw e; 
    }
  }
  
  public int cardinality() { 
    return argsFound.size();
  }
  
  public String usage() { 
    if (schema.length() > 0)
      return "-[" + schema + "]"; 
    else
      return ""; 
  }
  
  public boolean getBoolean(char arg) { 
    ArgumentMarshaler am = marshalers.get(arg); 
    boolean b = false;
    try {
      b = am != null && (Boolean) am.get(); 
    } catch (ClassCastException e) {
      b = false; 
    }
    return b; 
  }
  
  public String getString(char arg) { 
    ArgumentMarshaler am = marshalers.get(arg); 
    try {
      return am == null ? "" : (String) am.get(); 
    } catch (ClassCastException e) {
      return ""; 
    }
  }
  
  public int getInt(char arg) { 
    ArgumentMarshaler am = marshalers.get(arg); 
    try {
      return am == null ? 0 : (Integer) am.get(); 
    } catch (Exception e) {
      return 0; 
    }
  }
  
  public double getDouble(char arg) { 
    ArgumentMarshaler am = marshalers.get(arg); 
    try {
      return am == null ? 0 : (Double) am.get(); 
    } catch (Exception e) {
      return 0.0; 
    }
  }
  
  public boolean has(char arg) { 
    return argsFound.contains(arg);
  } 
}
```

<br>

## 6. 결론

**단순히 돌아가는 코드에 만족하는 프로그래머는 전문가 정신이 부족하다.**

나쁜 코드보다 더 오랫동안 더 심각하게 개발 프로젝트에 악영향을 미치는 요인도 없다. 물론 나쁜 코드도 깨끗한 코드로 개선할 수 있다. 하지만 비용이 엄청나게 많이 든다.

그러므로 **코드는 언제나 최대한 깔끔하고 단순하게 정리하자**. 절대로 썩어가게 방치하면 안 된다.