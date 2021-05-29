---
layout: post
title: 자바 8 함수형 인터페이스 - Predicate<T>
comments: true
categories: Java 
---

[자바의 람다 표현식 - 정의와 사용방법](https://7772.github.io/2021-05-27-lambda-in-java/)에 이어서 [자바의 람다 표현식 - 활용 방법](https://7772.github.io/2021-05-28-lambda-in-java-examples/) 까지 알아보았다.<br/>

자바 8 버전에는 람다와 메소드 참조를 쉽게 사용할 수 있도록 도와주는 함수형 인터페이스들을 제공한다.<br/>

함수형 인터페이스들은 `java.util.function` 패키지에 정의되어 있다. 제공하는 인터페이스의 종류는 [공식문서](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html)에서 확인하자.

> 이 포스팅에서 사용한 예제코드는 [Github](https://github.com/7772/study/tree/master/lambdastudy) 에서 확인할 수 있다.

<br/>

## 1. 사전 준비

우리는 `Predicate<T>`, `Consumer<T>` 그리고 `Function<T, R>` 를 알아볼 것이다.<br/>
각각의 함수형 인터페이스를 사용할 객체로 `Vehicle` 클래스를 만들어서 사용해보자.

```java
public class Vehicle {
    private Type type;
    private String vendor;
    private String modelName;
    private String color;

    public Vehicle(
        Type type,
        String vendor,
        String modelName,
        String color
    ) {
        this.type = type;
        this.vendor = vendor;
        this.modelName = modelName;
        this.color = color;
    }

    public enum Type {
        SUV,
        SEDAN,
        TRUCK
    }

    // getters
}
```

타입, 제조사, 모델 이름, 색상을 프로퍼티로 갖는 클래스이다.

<br/>

## 2. 조건 검사 - if else

`Vehicle` 클래스를 인스턴스화 시킨 `vehicle`이 있다.

```java
Vehicle vehicle = new Vehicle(Vehicle.Type.SUV, "BMW", "M3", "white");
```

`vehicle` 이 `SUV` 타입인지 검사하고자 할 때, 가장 기본적인 방법은 if else 조건문이다.

```java
if (Vehicle.Type.SUV.equals(vehicle.getType())) {
    // ...
} else {
    // ...
}
```

일반적으로 위와 같은 조건 검사 코드를 위임하고 싶을 때가 있는데, 조건 검사 코드가 매우 많아져서 주요 관심사를 확인하기 어렵거나, 다양한 방식으로 응용이 필요한 순간이다.

조건 검사를 실행하는 역할을 가진 객체를 분리하여 기능을 위임해 보려고 한다.

<br/>

## 3. [`Predicate<T>`](https://docs.oracle.com/javase/8/docs/api/java/util/function/Predicate.html)

`java.util.function.Predicate<T>` 인터페이스는 `test` 메소드를 추상 메소드로 선언한 함수형 인터페이스다.<br/>

실제 코드는 아래와 같은 모습이다.

```java
@FunctionalInterface
public interface Predicate<T> {
    boolean test(T t);
    // ...
}
```

<br/>

### 4. `Predicate<T>` 인터페이스를 구현한 구체 클래스 만들기

`Predicate<T>` 를 구현한 구체 클래스부터 만들어보자.<br/>

`Vehicle` 객체의 인스턴스가 `SUV` 타입인지 검사하는 구체 클래스이다.

```java
public class VehicleSUVPredicate implements Predicate<Vehicle> {
    @Override
    public boolean test(Vehicle vehicle) {
        return Vehicle.Type.SUV.equals(vehicle.getType());
    }
}
```

`Predicate<T>` 인터페이스의 Generic 타입에 `Vehicle` 클래스를 사용하겠다고 명시해 두었다. 따라서 클래스의 이름에도 prefix로 `Vehicle`을 사용했다.<br/>
구현한 코드는 `SUV` 여부를 확인하고 있다.

<br/>

### 5. 구체 클래스를 이용하여 `Vehicle` 타입 검사하기

구현한 구체 클래스를 사용할 서비스 클래스를 선언한다.

```java
public class VehiclePredicateService {
    private final Vehicle vehicle;
    private final VehicleSUVPredicate vehicleSUVPredicate;
    
    public VehiclePredicateService() {
        vehicle = new Vehicle(Vehicle.Type.SUV, "BMW", "M3", "white");
        vehicleSUVPredicate = new VehicleSUVPredicate();
    }

    public void predicate() {
        boolean isSuv = vehicleSUVPredicate.test(vehicle);
    }
}
```

`Predicate<T>` 를 구현한 코드를 `VehicleSUVPredicate` 클래스에 캡슐화하였고, 클라이언트에서는 해당 클래스의 인스턴스에 의존하여 결과를 얻고 있다.

<br/>

### 6. 람다 적용

함수형 인터페이스를 인수로 받는 메소드가 준비되면, 람다를 사용할 수 있다.

이번에는 `SUV`와 같은 특정 요소에 대한 검사가 아니라, `Vehicle` 객체에 대한 검사를 할 수 있는 역할을 가진 클래스를 만들어보자.

```java
public class VehiclePredicator {
    private final Vehicle vehicle;

    public VehiclePredicator(Vehicle vehicle) {
        this.vehicle = vehicle;
    }

    public boolean predicate(Predicate<Vehicle> predicate) {
        return predicate.test(vehicle);
    }
}
```

`VehiclePredicator` 는 `vehicle`을 프로퍼티로 가지며, `Predicate<Vehicle>`을 인수로 받고 `boolean`을 리턴하는 메소드 `predicate`를 가지고 있다.<br/>
이제 이 클래스가 람다와 함께 사용되어 어떤 역할을 수행할 수 있는지 확인해보자.

```java
public class VehiclePredicateService {
    // ...
    
    public void predicate() {
        boolean isSuv = vehiclePredicator.predicate(
            (Vehicle vehicle) -> vehicle.getType().equals(Vehicle.Type.SUV)
        );
        boolean isSedan = vehiclePredicator.predicate(
            (Vehicle vehicle) -> vehicle.getType().equals(Vehicle.Type.SEDAN)
        );
        boolean isSedanAndGreenColor = vehiclePredicator.predicate(
            (Vehicle vehicle) -> vehicle.getType().equals(Vehicle.Type.SEDAN) &&
                vehicle.getColor() == "green"
        );
        boolean isBMW = vehiclePredicator.predicate(
            (vehicle) -> vehicle.getVendor() == "BMW"
        );
    }
}
```

`VehiclePredicator`는 `Vehicle`에 관련된 검사를 수행할 수 있고, 검사 로직이 람다 표현식으로 제공될 수 있다.<br/>
검사를 수행하는 로직이 클래스의 메소드 내에서만 존재할 수 있었던 이전과 달리, 자바 8 버전부터는 람다를 이용하여 검사 로직을 독립적인 함수의 형태로 구현할 수 있다.

따라서 여러 검사 로직을 하나 하나 구체 클래스로 구현하지 않아도, 다양한 형태의 구현이 가능해졌다.<br/>

다음 포스팅에서는 `Consumer<T>`를 알아도록 하겠다.

<br/><br/>
