# 6장 객체와 자료 구조
우리가 변수를 설정 할 때, 변수를 변경하지 못하도록 private하게 설정한다. 하지만 그것을 조회하거나, 설정하는 함수는 왜 공개할까...?

## 자료 추상화
```dart
//6-1 코드 (구체적인 Point 클래스)
class Point {
    double _x;
    double _y;
}
```

```dart
//6-2코드 (추상적인 Point 클래스)
abstract class Point {
    double getX();
    double getY();
    void setCartesian(double x, double y);
    double getR();
    double getTheta();
    void setPolar(double r, double theta);
}
```
위의 6-1 코드와 6-2 코드의 차이를 보면, 두 클래스 모두 2차원 점을 표현한다. 

하지만 하나는 구현을 외부로 노출시키고, 다른 클래스는 구현을 완전히 숨긴다!

### **6-2의 코드를 보고...**

6-2는 자료 구조의 이상적인 모습이다. 클래스 메서드가 접근 정책을 강제하고, 좌표를 읽을 때 각 값을 개별적으로 읽는다. 

하지만 좌표를 설정할 때는 두 값을 한꺼번에 설정해야 한다!

### **여기서 6-1을 보고 비교한다면...?**
6-1은 직교 좌표계를 사용하는 것을 알 수 있고, 개별적으로 읽고 설정할 수 있으며, ***구현을 노출*** 시킨다!

변수를 private으로 선언하더라도 각 값마다 조회, 설정을 하는 함수를 제공하면 외부로 노출시키는 것과 다를 것이 없다.

### **여기서 알 수 있는 것!**
변수 사이에 함수를 넣는다고 구현이 감춰지지 않고, 구현을 감추려면 추상화가 필수적이다! 

최소한 추상 인터페이스를 제공하여 사용자가 구현을 모른채 자료의 핵심을 조작할 수 있어야하는 것이 클래스이다.

```dart
//6-3 코드 (구체적인 Vehicle 클래스)
abstract class Vehicle {
    double getFuelTankCapacityInGallons();
    double getGallonsOfGasoline();
}
```

```dart
//6-4 코드 (추상적인 Vehicle 클래스)
abstract Vehicle {
    double getPercentFuelRemaining();
}
```
6-1과 6-2를 비교하면 6-2가, 6-3과 6-4를 비교하면 6-4가 더 좋다!

### 그것의 이유는...?
자세하게 공개하는 것보다는 추상적인 개념으로 표현하는 것이 더 좋다. 단순히 인터페이스나 set/get 함수들을 통해서만 추상화가 일어나는 것이 아니기 때문이다!

*무지성으로 getter/setter 함수를 추가하는 것이 가장 별로다.*

## 자료/객체 비대칭

### 객체
추상화 뒤로 자료를 숨긴 채 자료를 다루는 함수만 공개
### 자료 구조
자료를 그대로 공개하고 별다른 함수는 제공하지 않는다.

```dart
//6-5 코드 (절차적인 도형)
class Square {
    Point topLeft;
    double side;
}

class Rectangle {
    Point topLeft;
    double height;
    double width;
}

class Circle {
    Point center;
    double radius;
}

class Geometry {
    final double PI = 3.141592653589793;

    double area(Object shape) {
        if(instanceof(shape, Square)) {
            Square s = shape as Square;
            return s.side * s.side;
        } else if (instanceof(shape, Rectangle)) {
            Rectangle r = shape as Rectangle;
            return r.height * r.width;
        } else if (instanceof(shape, Circle)) {
            Circle c = shape as Circle;
            return PI * c.radius * c.radius;
        } else {
            throw NoSuchShapeException();
        }
    }
}
```
객체 지향 프로그래머가 위 6-5 코드를 보면 경악을 할지도 모르겠다. 클래스가 절차적이라고 비판하면 맞는 말이지만, 이것 비판이 100% 옳다고 할 수 있을까?

만약 Geometry 클래스에 둘레 길이를 구한 perimeter()함수를 추가한다면 도형 클래스는 아무 영향도 받지 않는다!

하지만 새로운 도형을 추가하고 싶다면 Geometry 클래스에 속한 함수를 모두 고쳐야 한다!

```dart
//6-6 코드 (다형적인 도형)
class Square implements Shape {
    Point _topLeft;
    double _side;

    double area() {
        return _side * _side;
    }
}
class Rectangle implements Shape {
    Point _topLeft;
    double _height;
    double _width;

    double area() {
        return _height * _width;
    }
}

class Circle implements Shape {
    Point _center;
    double _radius;
    final double PI = 3.141592653589793;

    double area() {
        return PI * _radius * _radius;
    }
}
```
코드 6-6을 보면 객체 지향적인 도형 클래스이고, area는 다형 메서드임으로 Geometry 클래스는 필요 없다. 

따라서 새 도형을 추가해도 기존 함수에 아무런 영향을 미치지 않는다. 

하지만 새 함수를 추가하려면 도형 클래스 전부를 고쳐야한다.
## 절차적인 코드
* 기존 자료 구조를 변경하지 않으면서 새 함수를 추가하기 쉽다. 
* 새로운 자료 구조를 추가하려면 모든 함수를 고쳐야한다.
## 객체 지향 코드
* 기존 함수를 변경하지 않으면서 새 클래스를 추가하기 쉽다.
* 새로운 함수를 추가하려면 모든 클래스를 고쳐야한다.

적절하게 섞어서 사용하는 것이 프로그래머의 역량!
## [디미터 법칙](http://wikipedia.org/wiki/Law_of_Demeter "디미터 법칙")
> 디미터 법칙은 잘 알려진 **휴리스틱**으로, 모듈은 자신이 조작하는 객체의 내부는 몰라야 한다는 법칙!

따라서 객체는 조회 함수로 내부 구조를 공개하면 안된다. 즉 **"클래스 C의 메서드 f는 다음과 같은 객체의 메서드만 호출해야 한다"** 고 주장한다.
* 클래스 C
* f가 생성한 객체
* f 인수가 넘어온 객체
* C 인스턴스 변수에 저장된 객체

![디미터 법칙 예시](https://velog.velcdn.com/images/wansook0316/post/fb3a8083-6548-4b85-bf6c-e25cf7a18b4b/image.png)

하지만 객체에서 허용된 메서드가 반환되는 객체의 메소드는 호출하면 안된다.

 즉, 낯선 사람은 경계하고 친구만 놀라는 의미로 해석할 수 있다!

```dart
//6-7 코드
final String outputDir = ctxt.getOptions().getStrachDir().getAbsolutePath();
```
6-7과 같은 코드는 getOptions()함수가 반환하는 객체의 getScratchDir() 함수를 호출한 후 getScratchDir() 함수가 반환하는 객체의 getAbsolutePath() 함수를 호출하기 때문에 디미터 법칙을 어긴다.
## 기차 충돌
6-7 코드 같은 경우에는 **기차 충돌**이라 하는데, 여러 객차가 한 줄로 이어진 기차처럼 보이기 때문이고, 일반적으로 조잡하다 보이기 때문에 피하는게 좋다.
```dart
//6-7 코드를 아래와 같이 리팩터링 하는 것이 좋다.
Options opts = ctxt.getOptions();
File scratchDir = opts.getScratchDir();
final String outputDir = scratchDir.getAbsolutePath();
```
## 잡종 구조
위와 같은 특징들 때문에 절반은 객체, 절반은 자료 구조인 잡종 구조가 나온다...

논리적으로는 객체지향적인 프로그램이 절차적인 프로그램 자료 구조 접근처럼 바뀔 수 있지만, 대부분 양쪽의 단점을 가져오는 경우가 대부분이다.

## 구조체 감추기
만약 위의 ctxt, options, scratchDir이 진짜 객체라면 위와 같이 줄줄이 묶어놓으면 안된다. *객체는 내부 구조를 감춰야 함으로.* 그렇다면 임시 디렉터리의 절대 경로는 어떻게 찾을 수 있을까?
```dart
//1번
ctxt.getAbsolutePathOfScratchDirectoryOption();
//2번
ctx.getScratchDirectoryOption().getAbsolutePath();
```
1. 첫 번째는 ctxt 객체에서 공개해야 하는 메서드가 많다.
2. 두 번째는 getScratchDirectoryOption()이 객체가 아니라 자료 구조를 반환한다고 가정한다.

*따라서 둘다 별로이다...*
해답은 ctxt 객체에게 임시 파일을 생성하라고 시키면 된다!
```dart
//올바른 예시
BufferedOutputStream bos = ctxt.createScratchFileStream(classFileName);
```
위의 예시는 ctxt 내부 구조를 드러내지 않고, 모듈에서 해당 함수는 자신이 **몰라야 하는** 여러 객체를 탐색하지 않음으로 디미터 법칙을 위반하지 않는다.
## 자료 전달 객체
자료 구조체는 전형적으로 공개 변수만 있고, 함수가 없는 클래스이다. 이를 **자료 전달 객체(Data Transfer Object, DTO)** 라고 한다.

DTO는 데이터베이스에서 저장된 가공되지 않은 정보를 어플리케이션 코드에서 사용할 객체로 변환하는 과정에서 가장 처음으로 사용하는 구조체이다.

```dart
//주소를 담는 Address DTO 예시
class Address {
    String _street;
    String _streetExtra;
    String _city;
    String _state;
    String _zip;

    Address(String street, String streetExtra, String city, String state, String zip) {
        this._street = street;
        this._streetExtra = streetExtra;
        this._city = city;
        this._state = state;
        this._zip = zip;
    }

    String getStreet() {
        return _street;
    }

    String getStreetExtra() {
        return _streetExtra;
    }

    String getCity() {
        return _city;
    }

    String getState() {
        return _state;
    }
    
    String getZip() {
        return _zip;
    }
}
```

## 활성 레코드
DTO와 마찬가지로 public 변수가 있거나 private 변수에 getter/setter 함수가 있는 자료구지만, 대부분 save나 find와 같은 탐색 함수도 제공한다.

여기서 활성 레코드에 비즈니스 규칙 메서드를 추가해서 자료 구조를 객체로 취급하는 경우가 발생한다. 이는 잡종 구조를 발생시키기 때문이다.

해결 방법은 활성 레코드는 자료 구조로 취급하고, 내부 자료(활성 레코드의 인스턴스)를 숨기는 객체는 따로 생성한다.
## 그래서 결론은?

### **객체**

*동작을 공개하고 자료를 숨긴다.*

따라서 기존 동작을 변경하지 않으면서 새 객체 타입을 추가하기 쉽지만, 기존 객체에 새 동작을 추가하는 것은 어렵다.
### **자료 구조**
*동작 없이 자료를 노출한다.*

기존 자료 구조에 새 동작 추가는 쉽지만, 기존 함수에 새 자료 구조를 추가하기는 어렵다.

- 시스템을 구현할 때, 새로운 자료 타입을 추가하는 유연성이 필요하면 객체가 적합하다.
- 새로운 동작을 추가하는 유연성을 필요하면 자료 구조 + 절차적인 코드가 더 적합하다.
### 이것을 잘 조정하는 사람이 좋은 프로그래머이다!