---
layout: post
title: 자바 8 함수형 인터페이스 - Consumer<T>
comments: true
categories: Java
---

함수형 인터페이스들은 `java.util.function` 패키지에 정의되어 있다. 제공하는 인터페이스의 종류는 [공식문서](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html)에서 확인하자.

이번 포스팅에서는 `Consumer<T>` 에 대하여 알아보자.

---

# `Consumer<T>`

`Consumer` 는 함수형 인터페이스이다.<br/>
> *함수형 인터페이스에 대한 설명은 [자바의 람다 표현식 - 정의와 사용방법](https://7772.github.io/2021-05-27-lambda-in-java/) 에서 자세히 다루었다.*

<br/>

~~~~ java
@FunctionalInterface
public interface Consumer<T>
~~~~

`Consumer` 의 이름을 보면, 무언가 소비하는 역할을 수행할 것이라고 예상할 수 있다.

다른 함수형 인터페이스들이 각자의 역할이 비교적 명확히 정해져있는 것에 비해 `Consumer` 는 수행할 역할에 대해서 규정하지 않는다.

공식 문서의 정의를 살펴보자.

> `Consumer` 는 하나의 입력 인자를 받고 아무 결과도 반환하지 않는 동작을 표현한다.
> 다른 대부분의 함수형 인터페이스들과 달리, `Consumer` 는 부작용(*side-effects*)을 일으켜 동작하는 것을 기대한다.

`Consumer` 는 Generic 으로 전달되는 `T` 타입을 인자로 전달받는 함수를 실행하고, 그 함수는 `void` 를 반환한다.

해당 함수 내에서 어떠한 역할을 수행하던지 관여하지 않는다.

~~~~ java
@FunctionalInterface
public interface Consumer<T> {

    void accept(T t);

    default Consumer<T> andThen(Consumer<? super T> after) {
        Objects.requireNonNull(after);
        return (T t) -> { accept(t); after.accept(t); };
    }
}
~~~~

*`Consumer` 의 `accept` 함수를 실행하여 원하는 동작을 구현할 수 있다.*

---

## 예제

예제를 통하여 `Consumer` 를 직접 사용해보자.

예제는 [자바 8 함수형 인터페이스 - Predicate](https://7772.github.io/2021-05-29-lambda-functional-interface) 에서 사용했던 `Vehicle` 클래스를 그대로 사용하겠다.

> 사용된 코드는 [Github](https://github.com/7772/study/tree/master/lambdastudy) 에서 확인할 수 있다.

다음과 같은 요구조건을 구현해보자.

1. `Vehicle` 의 상세정보를 확인할 수 있다.
2. 필요에 따라 `Vehicle` 의 상세 정보를 확인 한 후 기타 정보를 추가적으로 확인할 수 있다.

일부러 2번의 요구사항을 추가해보았다. 핵심은 정해진 상세정보 외에 추가적인 행동을 취할 가능성이 열려있다는 것이다.

### `VehicleInformation` - 차량 정보 제공 클래스

먼저 `Vehicle` 의 상세 정보를 확인하는 역할을 수행하는 클래스를 만들어보자.

~~~~ java
public class VehicleInformation {
    private Vehicle vehicle;

    public VehicleInformation(Vehicle vehicle) {
        this.vehicle = vehicle;
    }

    public void print() {
        System.out.println(vehicle.getType());
        System.out.println(vehicle.getVendor());
        System.out.println(vehicle.getModelName());
        System.out.println(vehicle.getColor());
    }
}
~~~~

`VehicleInformation` 은 `Vehicle` 인스턴스를 가지고, `print` 메소드를 통해 `vehicle` 인스턴스의 상세정보를 출력한다.

1번 요구사항은 손쉽게 구현하였다. 이제 2번 요구사항을 구현해야 하는데, `VehicleInformation` 을 변경하지 않으면서 새로운 요구사항에 어떻게 대응할 수 있을까?

> [Open Closed Principle](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle) 에 따르면, 소프트웨어는 확장에 개방되어야 하고 수정에는 패쇄되어야 한다. 즉, 새로운 요구사항이 생겨도 `VehicleInformation` 은 수정되지 않는 것이 바람직한 설계방향이다.

~~~~ java
    // ...
    
    public void print(Consumer<Vehicle> vehicleConsumer) {
        print();
        
        vehicleConsumer.accept(vehicle);
    }
}
~~~~

`Consumer<Vehicle>` 타입의 인스턴스를 인자로 받는 `print` 메소드를 하나 더 추가했다. 이 함수는 `print()` 메소드를 호출한 후에 인자로 받은 `vehicleConsumer` 의 `accept` 메소드를 실행한다.

이렇게 구현해두면 `VehicleInformation` 은 추가될 요구사항에도 전혀 손대지 않아도 된다. 추가되는 요구사항은 `Consumer<Vehicle>` 타입으로 구현한 구현체에서 수행하면 되기 때문에 `VehicleInformation` 자신이 아닌 외부로 그 책임을 넘긴다.

### 차량 정보 사용 부분 예제

구현한 `VehicleInformation` 을 사용해보자.

~~~~ java
public class VehicleConsumerService {
    private Vehicle vehicle;
    private VehicleInformation vehicleInformation;

    public VehicleConsumerService() {
        vehicle = new Vehicle(Vehicle.Type.SUV, "BMW", "M3", "white");
        vehicleInformation = new VehicleInformation(vehicle);
    }

    public void consume() {
        vehicleInformation.print((vehicle) -> {
            System.out.println("Vehicle : " + vehicle);
        });
    }
}
~~~~

간단한 예제의 구현을 위해서 인자가 없는 생성자를 구현하고, 그 안에서 `vehicle` 과 `vehicleInformation` 을 생성했다.

`consume()` 메소드에서 `vehicleInformation` 의 `print()` 메소드를 실행하게 되는데, 이 `print` 메소드는 총 2개의 방식으로 구현해두었다.

~~~~ java
    public void print() {
        System.out.println(vehicle.getType());
        System.out.println(vehicle.getVendor());
        System.out.println(vehicle.getModelName());
        System.out.println(vehicle.getColor());
    }

    public void print(Consumer<Vehicle> vehicleConsumer) {
        print();
        
        vehicleConsumer.accept(vehicle);
    }
~~~~

위 2가지 방식 모두를 사용할 수 있지만, 상세정보 출력 외에도 추가적인 기능을 구현하고자 `Consumer<Vehicle>` 타입을 인자로 받는 `print` 메소드를 실행하였다.

~~~~ java
    vehicleInformation.print((vehicle) -> {
        System.out.println("Vehicle : " + vehicle);
    });
~~~~

lambda 메소드는 이렇게 inline code 로 직접 구현할 수도 있지만, 별도의 클래스로 정의할 수도 있다.

`Consumer<Vehicle>` 인터페이스를 구현한 람다 코드를 리턴하는 형태의 함수를 구현해보자.

~~~~ java
public class VehicleConsumer {
    public Consumer<Vehicle> consume() {
        return (vehicle) -> {
            System.out.println("Vehicle : " + vehicle);
        };
    }
}
~~~~

그 이후, 사용 부분에서 `VehicleConsumer` 클래스를 인스턴스화하여 사용하면 된다.

~~~~ java
    vehicleInformation.print(new VehicleConsumer().consume());
~~~~

---

# 마무리

`Consumer<T>` 는 인터페이스의 역할을 규정하지 않기 때문에, 오히려 그 사용에 더 주의해야 한다고 생각한다.

정의가 말해주듯 `Consumer<T>` 인터페이스의 사용은 부작용(*side-effects*)를 만들게 되는데, 프로그래밍에서 부작용은 꽤 자주 여러 문제들을 만들어낸다. 말 그대로, 부작용이 생길 수 있다는 점이다.

함수의 역할을 인터페이스의 정의에서 예측하기 어려운 점을 생각해보았을 때, 프로그래머가 `Consumer<T>` 인터페이스를 사용할 때엔, 그 역할을 함수의 이름으로 충실히 표현해야 할 것이다.

이러한 점에서 생각해보았을 때, 예제에서 사용한 `VehicleConsumer` 와 같은 이름은 안티패턴인 것 같다. 코드를 직접 보지 않으면 그 안에서 무슨 일을 하고 있을 지 가늠할 수 없기 때문이다.

실제 업무에서 사용할 때에는 보다 구체적인 이름을 지어서 사용하자.

<br/>
