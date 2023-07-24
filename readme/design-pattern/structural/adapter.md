# Adapter (어댑터)

## 목적

클래스를 클라이언트가 원하는 타입으로 변환하여 호환되지 않는 객체를 활용할 수 있습니다.



## 예시

원형 블럭을 필요로 하는 장난감이 있다고 가정해봅시다. 우리가 갖고 있는 블럭은 네모 블럭밖에 없는 상황에서 이 문제를 어떻게 해결해야 할까요? 직접 원형 블럭을 만들자니 네모 블럭이랑 형태만 다르지 쓰임새는 비슷하고, 억지로 끼우자니 구멍에 안 맞는 상황. 이럴 때 어댑터를 사용할 수 있습니다.



클래스 `Toy`가 동작하기 위해서 `CircleBlock`를 주입받아야 합니다.

```java
public interface CircleBlock {

    double area();

}

@Setter
public class Toy {
    
    private CircleBlock circleBlock;
    
    public void play() {
        System.out.println("Toy is joined with circle block whose area is " + circleBlock.area());
    }
    
}
```

하지만 우리는 `SquareBlock`만 갖고 있다고 해봅시다.

```java
@RequiredArgsConstructor
public class SquareBlock {

    private final int width;
    
    public int getArea() {
        return this.width * 2;
    }

}
```

이럴 때 어댑터를 사용할 수 있습니다.



## 구현

### Object Adapter (객체 어댑터)

클라이언트가 원하는 타입을 상속하고 [우리가 핸들링할 수 있는 객체](#user-content-fn-1)[^1]를 필드로 선언하는 형태입니다.

```java
@Setter
public class BlockAdapter implements CircleBlock {

    private SquareBlock squareBlock;

    @Override
    public double area() {
        int width = squareBlock.getArea() / 2;
        return width * 3.14;
    }

}
```

인터페이스 `CircleBlock`를 구현한 어댑터 클래스를 만듭니다. 이 어댑터는 `SquareBlock`를 받아서 `CircleBlock`구현에 활용하고 있습니다. 마치 네모 블럭을 원형 블럭으로 변환하는 것처럼요.

이제  `SquareBlock`으로 `Toy`를 마음껏 사용할 수 있습니다.

```java
BlockAdapter adapter = new BlockAdapter();
adapter.setSquareBlock(new SquareBlock(4));

Toy toy = new Toy();
toy.setCircleBlock(adapter);
toy.play(); // Toy is joined with circle block whose area is 12.56
```



### Class Adapter (클래스 어댑터)

만약 `SquareBlock`이 인터페이스라면 다중 상속으로 해결할 수도 있습니다.

```java
public interface SquareBlock {

    int getArea();

}
```

`CircleBlock`과 `SquareBlock`를 모두 구현하는 어댑터를 만들면 됩니다.

```java
@RequiredArgsConstructor
public class BlockAdapter implements SquareBlock, CircleBlock {

    private final int width;

    @Override
    public double area() {
        return this.width * 3.14;
    }

    @Override
    public int getArea() {
        return this.width * 2;
    }

}
```

이제 Adapter와 Adaptee를 따로 생성할 필요가 없습니다.

```java
Toy toy = new Toy();
toy.setCircleBlock(new BlockAdapter(6));
toy.play(); // Toy is joined with circle block whose area is 18.84
```



#### 주의사항

클래스 어댑터는 제약사항이 하나 있습니다. 인터페이스의 Method Signature가 동일하고 반환 타입이 서로 호환되지 않는 관계라면 컴파일 에러가 발생하는 점입니다.

만약 `CircleBlock`이 아래와 같았다고 가정해봅시다.

```java
public interface CircleBlock {

    double getArea();

}
```

BlockAdapter가 두 인터페이스를 구현할 때 문제가 발생합니다.

```java
@RequiredArgsConstructor
public class BlockAdapter implements SquareBlock, CircleBlock {

    private final int width;

    @Override
    public int getArea() { // Compile Error
        return this.width * 2;
    }

}
```

`getArea()` 메서드의 반환 타입이 서로 호환되지 않는 관계이기에 `int getArea()`, `double getArea()` 메서드를 2개를 구현해야 합니다. 하지만 Java 언어의 제약으로 Signature가 같은 메서드는 하나의 클래스에서 둘 이상 만들 수 없습니다.

이런 경우에는 아래의 방법으로 해결할 수 있겠죠.

1. `SquareBlock`의 Method Signature를 변경한다. (`CircleBlock`을 수정할 수 없을 경우)
2. 객체 어댑터 방식으로 해결한다.

1번은 그닥 좋은 방법이 아닙니다. `SquareBlock`를 사용하는 기존 클래스가 많을수록 변경의 영향이 커집니다. 게다가 Toy가 아래와 같이 변경된다면 어댑터 패턴을 사용한 게 오히려 독이 될 수도 있습니다.

```java
public interface TriangleBlock {

    long getArea();

}

public class Toy {
    
    private CircleBlock circleBlock;

    private TriangleBlock triangleBlock;

    public void setCircleBlock(CircleBlock circleBlock) {
        this.circleBlock = circleBlock;
        this.triangleBlock = null;
    }

    public void setTriangleBlock(TriangleBlock triangleBlock) {
        this.triangleBlock = triangleBlock;
        this.circleBlock = null;
    }

    public void play() {
        Number area = this.circleBlock == null ? this.triangleBlock.getArea() : this.circleBlock.getArea();
        System.out.println("Toy is joined with circle block whose area is " + area);
    }
    
}
```

클라이언트에서 원하는 타입들의 Method Signature가 동일한 상황입니다. 이 경우 어댑터 클래스가 `CircleBlock`와 `TriangleBlock`를 다중상속하는 순간 똑같이 컴파일 에러가 발생합니다. 심지어 `SquareBlock`은 구현하지도 않았는데도 말이죠.


### Converter (컨버터)와 차이점





## 참고

{% embed url="https://refactoring.guru/design-patterns/adapter" %}

{% embed url="https://java-design-patterns.com/patterns/adapter/" %}

[^1]: 어댑터에게 제공하는 객체라는 뜻에서 **adaptee**라고 합니다.
