# 16장. `SerialDate` 리팩터링

## 첫째, 돌려보자

단위 테스트가 최대한 많은 코드를 돌려 보아야 한다. (=테스트 커버리지를 높여야 한다.)

## 둘째, 고쳐보자

### 문서

- 소스 코드 제어 도구를 사용하는 현대에는, 변경 이력을 코드에 남길 필요는 없어 보인다.
- 한 소스 코드에 많은 언어를 사용하는 것은 바람직하지 못하다.
- 네이밍을 할 때 적합한 표현을 사용해야 하고, 이름으로 추상 클래스 여부 등을 헷갈릴 여지가 없어야 한다.
  - ex) `SerialDate`는 '1899년 12월 30일을 기준으로 경과한 날짜 수'라는 일종의 '일련번호(serial number)'를 사용하기 때문에 이렇게 네이밍했지만, 특성상 제품 식별 번호와 같은 어감을 주는 'serial'보다는 'relative offset' 등의 표현이 더 적합해 보인다.
  - ex) `SerialDate`는 무언가 구현된 것 같은 느낌을 주지만, 사실 추상 클래스이다.
  - ex) `date.addDays(7)`는 7일만큼 날짜를 뒤로 미룬 새로운 객체를 생성하지만, 기존 객체를 변경한다고 오해하기 쉽다.
- 불필요한 주석은 잘못된 정보가 쌓이기 좋은 곳이다.
- 긴 코드는 별도의 소스 파일로 옮기자.

### 객체지향

- 서로 관련된 것들끼리 배치하자.
  - ex) 상수 `EARLIEST_DATE_ORDINAL`가 `SpreadsheetDate` 클래스에서만 사용하고 있으면, `DayDate` 클래스에 배치하지 말고 `SpreadsheetDate` 클래스에 배치하자.
- 기반 클래스(base class)는 파생 클래스(derivative)를 몰라야 바람직하다.

  - ex) 추상 클래스인 `DayDate`가 `SpreadsheetDate`의 구현 정보를 알아야 하는 딜레마가 있다. 이 경우 abstract factory 패턴으로 다음과 같이 리팩토링하자.

    ```java
    public abstract class DayDateFactory {
      private static DayDateFactory factory = new SpreadsheetDateFactory();
      public static void setInstance(DayDateFactory factory) {
        DayDateFactory.factory = factory;
      }

      protected abstract DayDate _makeDate(int ordinal);
      protected abstract DayDate _makeDate(int day, DayDate.Month month, int year);
      protected abstract DayDate _makeDate(int day, int month, int year);
      protected abstract DayDate _makeDate(java.util.Date date);
      protected abstract int _getMinimumYear();
      protected abstract int _getMaximumYear();

      // ...
    }
    ```

    ```java
    public class SpreadsheetDateFactory extends DayDateFactory {
      public DayDate _makeDate(int ordinal) {
        return new SpreadsheetDate(ordinal);
      }

      public DayDate _makeDate(int day, DayDate.Month month, int year) {
        return new SpreadsheetDate(day, month, year);
      }

      public DayDate _makeDate(int day, int month, int year) {
        return new SpreadsheetDate(day, month, year);
      }

      public DayDate _makeDate(Date date) {
        final GregorianCalendar calendar = new GregorianCalendar();
        calendar.setTime(date);
        return new SpreadsheetDate(
          calendar.get(Calendar.DATE),
          DayDate.Month.make(calendar.get(Calendar.MONTH) + 1),
          calendar.get(Calendar.YEAR)
        );
      }

      protected int _getMinimumYear() {
        return SpreadsheetDate.MINIMUM_YEAR_SUPPORTED;
      }

      protected int _getMaximumYear() {
        return SpreadsheetDate.MAXIMUM_YEAR_SUPPORTED;
      }
    }
    ```

### 디버그

- 명확히 정해진 상수의 모음은 `enum`으로 정의하자.
  - 그러면 `isValidMonthCode`와 같이 입력을 검증하는 메서드를 쓰지 않아도 된다.
- 원인과 결과가 분명한 오류를 컨트롤하는 것이 낫다. 원인 또는 결과가 불분명한 오류가 날 수 있는 상황이라면, 이를 방지하고 대신 원인과 결과가 분명한 오류를 감수하는 방향으로 바꾸어 보자.
  - ex) `serialVersionUID`는 직접 선언하면 문제가 없지만, 선언하지 않으면 컴파일러가 자동으로 생성해 의도치 않은 변경이 일어나 추적하기 어려운 문제가 발생한다.  
    -> 차라리 `serialVersionUID`를 자동 제어할 수 있도록 변경해서, 잘 알려진 오류인 `InvalidClassException`을 디버깅할 수 있도록 하자.
- 메서드 인수로 플래그를 넘기는 것은 일반적으로 바람직하지 않다.
- 무언가가 구현에 논리적으로 의존한다면 물리적으로도 의존해야 한다.
  - ex) `getDayOfWeek` 메서드는 `SpreadsheetDate` 구현에 의존하지 않는 것처럼 보이지만, 알고리즘을 보면 0번째 날짜의 요일에 의존하므로 상위 `DayDate`로 옮길 수 없다.
