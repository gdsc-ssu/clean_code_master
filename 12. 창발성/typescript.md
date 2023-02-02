# 창발성
## 착실하게 따르기만 해도 우수한 설계가 나오는 4가지 규칙!
- 모든 테스트를 실행
- 중복을 없앤다
- 프로그래머의 의도를 표현한다.
- 클래스와 메서드 수를 최소한으로 줄인다.

## 설계 규칙 1. 모든 단위 테스트를 실행하라
제일 먼저, 설계는 의도한 대로 돌아가는 시스템을 만들어야한다.
SRP를 준수하는 클래스는 테스트가 훨씬 더 쉽다!
테스트 케이스를 많이 작성할 수록 개발자는 [DIP](https://shinsunyoung.tistory.com/82)같은 원칙을 적용하고 의존성 주입, 인터페이스, 추상화 같은 도구를 사용해서 결합도를 낮추고, 설계 품질을 올린다.

## 설계 규칙 2~4. 리팩터링
코드 몇 줄을 추가할 때마다 잠시 멈추고 감상해보자... 새로 추가한 코드가 품질을 낮추는가? 그렇다면 정리해서 품질을 올려보자!

코드를 정리하는데 깨질 걱정은 하지 마라! 어차피 테스트 케이스가 있다!

리팩터링 단계에서는 소프트웨어 설계 품질을 높이는 기법을 마구마구 사용해보자.

1. 응집도를 높인다.
2. 결합도를 낮춘다.
3. 관심사를 분리한다.
4. 시스템 관심사를 모듈로 나눈다.
5. 함수와 클래스 크기를 줄인다.
6. 더 나은 이름을 선택한다.

위와 같은 기법들을 사용해보자!

## 설계 규칙 2. 중복을 없애라
중복은 추가 작업, 추가 위험, 불필요한 복잡도를 야기하기 때문에 절대로 배제해야한다.

똑같은 코드는 당연히 중복이고, 비슷한 코드는 더 비슷하게 고쳐보면 리팩터링이 쉽다.

```typescript
size(): number {}
isEmpty(): boolean {}
```
위와 같은 메서드가 존재한다고 생각해보자. 이것들을 각각 구현할 수도 있지만, isEmpty 메서드에서 size 메서드를 이용해서 추가적으로 구현할 필요가 없다!

```typescript
isEmpty() {
	return 0 == size();
}
```

깔끔한 시스템을 만드려면 ***모든 중복을 제거하겠다는 의지***가 있어야 한다!

```typescript
function scaleToOneDimension(desiredDimension: number, imageDimension: number) {
    if(Math.abs(desiredDimension - imageDimension) < errorThreshold) {
        return;
    }
    let scalingFactor = desireDimension / imageDimension;
    scalingFactor = Math.floor(scalingFactor * 100) * 0.01;

    const newImage = ImageUtilities.getScaledImage(image, scalingFactor, scalingFactor) as RenderOp;
    image.dispose();
    image = newImage;
}

function rotate(degrees: number) {
    const newImage = ImageUtilities.getRotatedImage(image, degrees) as RenderedOp; 
    image.dispose();
    image = newImage;
}
```

scaleToOneDimension 메서드와 rotate 메서드를 살펴보면 일부 코드가 동일하다! 중복을 제거해보자.

```typescript
function scaleToOneDimension(desiredDimension: number, imageDimension: double) {
    if(Math.abs(desiredDimension - imageDimension) < errorThreshold) {
        return;
    }
    let scalingFactor = desireDimension / imageDimension;
    scalingFactor = Math.floor(scalingFactor * 100) * 0.01;
    
    _replaceImage(ImageUtilities.getScaledImage(image, scalingFactor, scalingFactor));
}

function rotate(degrees: number) {
    _replaceImage(ImageUtilities.getRotatedImage(image, degrees));
}

//이 함수를 추가함으로 중복을 피할 수 있다!
function _replaceImage(newImage: RenderedOp) {
    image.dispose();
    image = newImage;
}
```

작은 부분이지만, 공통 부분을 뽑고 나니 클래스가 SRP을 위반해버린다! 따라서 새로 만든 replaceImage 메서드를 다른 클래스로 옮겨도 좋겠다. 그렇다면 새 메서드의 가시성이 높아진다.

이러한 '소규모 재사용'은 시스템 복잡도를 극적으로 줄여주고, 소규모 재사용을 제대로 익혀야 대규모 재사용이 가능하다.

TEMPLATE METHOD 패턴은 고차원 중복을 제거하기 위한 목적으로 자주 사용한다.
```typescript
class VacationPolicy {
    accrueUSDivisionVacation() {
        // 지금까지 근무한 시간을 바탕으로 휴가 일수를 계산하는 코드
        //...
        // 휴가 일수가 미국 최소 법정 일수를 만족하는지 확인하는 코드
        // ...
        // 휴가 일수를 급여 대장에 적용하는 코드
        // ...
    }

    accrueEUDivisionVacation() {
        // 지금까지 근무한 시간을 바탕으로 휴가 일수를 계산하는 코드
        //...
        // 휴가 일수가 미국 최소 법정 일수를 만족하는지 확인하는 코드
        // ...
        // 휴가 일수를 급여 대장에 적용하는 코드
        // ...
    }
}
```
위의 법정 일수가 계산하는 코드만 제외하면 두 메서드는 거의 동일하다! 하지만 알고리즘은 직원 유형에 따라서 변경되는 것이다.

TEMPLATE METHOD 패턴을 적용하여 눈에 들어오는 중복을 제거해보자.
```typescript
abstract class VacationPolicy {
    accrueVacation() {
        calculateBaseVacationHours();
        alterForLegalMinimums();
        applyToPayroll();
    }

    _calculateBaseVacationHours() { /* ... */}
    abstract alterForLegalMinimums();
    _applyToPayroll() { /* ... */ }
}

class USVacationPolicy extends VacationPolicy {
    @override
    alterForLegalMinimums() {
        // 미국 최소 법정 일수 사용
    }
}

class EUVacationPolicy extends VacationPolicy {
    @override
    alterForLegalMinimums() {
        // 유럽연합 최소 법정 일수 사용
    }
}
```
하위 클래스에 중복되지 않는 정보만을 제공하여 accrueVacation 알고리즘에서 빠진 부분을 채워주자!

## 설계 규칙 3. 의도를 표현해라
아마 우리 대다수는 엉망인 코드를 접해본 경험이 있을 것이다.

아마 우리 대다수는 스스로 엉망인 코드를 내놓은 경험도 있을 것이다.

**자신이** 이해하는 코드를 짜는 것은 쉽다. 자신이 문제를 잘 파악하고 코드를 구석구석 이해하니까! 하지만 나중에 유지보수하는 사람이 그정도로 깊이 이해할 가능성은 적다.

- 좋은 이름을 선택한다.
- 함수 클래스 크기를 가능한 줄인다.
- 표준 명칭을 사용한다.
- 단위 테스트 케이스를 꼼꼼히 작성한다.

표현력을 높이는 방법은 노력이다! 코드만 돌리고 다음으로 넘어가지 말고, 나중에 읽을 사람을 고려하자. *나중에 코드를 읽을 사람이 자신일 가능성이 높다.*

따라서 코드를 작성하는데 더 시간을 투자하고, 더 나은 이름을 선택하고, 함수를 쪼개자! 주의는 대단한 재능이다.

## 설계 규칙 4. 클래스와 메서드 수를 최소로 줄여라
중복을 제거하고, 의도를 표현하고, SRP를 준수하는 기본적인 개념도 극단으로 가면 득보다 실이 많다.

클래스와 메서드의 크기를 줄이자도 조그만 클래스와 메서드를 수없이 만드는 사례도 있다. 이때는 함수와 클래스를 가능한 줄이도록 하는 것이 좋다.

이것의 예가 클래스마다 무조건 인터페이스를 작성하는 예이다.

또한 자료 클래스와 동작 클래스는 무조건 분리시키는 경우도 예이다.

목표는 함수와 클래스 크기를 작게 유지하면서 동시에 시스템 크기도 작게 유지하는 것에 있다. 하지만 위에 말했던 우선순위가 가장 낮다.

따라서 클래스와 함수의 수를 줄이는 것도 필요는 하지만, 테스트 케이스 만들고 중복을 제거하고 의도를 표현하는 것이 더 중요하다는 의미이다.