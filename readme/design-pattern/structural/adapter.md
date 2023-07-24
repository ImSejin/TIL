# Adapter (어댑터)

## 목적

클래스를 클라이언트가 원하는 타입으로 변환하여 호환되지 않는 객체를 활용할 수 있습니다.



## 예시

220V를 필요로 하는 컴퓨터가 있다. 해외의 호텔 방에 투숙하니 콘센트는 모두 110V입니다.\
110V 플러그에 어댑터를 끼워 220V 전자제품과 호환되도록 합니다.



클래스 `Computer` 가 동작하기 위해서 `Plug110V`를 주입받아야 합니다.

```java
public interface Plug110V {

    int charge();

}

public class Computer {
    
    private Plug110V plug110V;
    
    public void setPlug110V(Plug110V plug110V) {
        this.plug110Vug = plug110V;
    }
    
    public void run() {
        System.out.println("Computer is running on " + plug110V.charge() + "V");
    }
    
}
```

하지만 우리는 `Plug110V`를 직접 생성할 수 없고, `Plug220V`만 갖고 있다고 해봅시다.

```java
public class Plug220V {

    public int getVoltage() {
        return 220;
    }

}
```

이럴 때 어댑터를 사용할 수 있습니다.



## 구현

### Object Adapter (객체 어댑터)

클라이언트가 원하는 타입을 상속하고 [우리가 핸들링할 수 있는 객체](#user-content-fn-1)[^1]를 필드로 선언하는 형태입니다.

```java
public class PlugAdapter implements Plug110V {

    private Plug220V plug220V;

    public void setPlug(Plug220V plug220V) {
        this.plug220V = plug220V;
    }

    @Override
    public int charge() {
        return plug220V.getVoltage();
    }

}
```

인터페이스 `Plug110V`를 구현한 어댑터 클래스를 만듭니다. 이 어댑터는 `Plug220V`를 받아서 `Plug110V` 구현에 활용하고 있습니다. 마치 220V를 110V로 변환하는 것처럼요.

이제  `Plug220V`로 `Computer`를 마음껏 사용할 수 있습니다.

```java
PlugAdapter adapter = new PlugAdapter();
adapter.setPlug(new Plug220V());

Computer computer = new Computer();
computer.setPlug110V(adapter);
```



### Class Adapter (클래스 어댑터)









```java
public interface Plug {

    int getVoltage();

}

public class Product {
    
    private Plug plug;
    
    public void setPlug(Plug plug) {
        this.plug = plug;
    }
    
    public void doSomething() {
        System.out.println("")
    }
    
}
```

### Converter (컨버터)와 차이점





## 참고

{% embed url="https://refactoring.guru/design-patterns/adapter" %}

{% embed url="https://java-design-patterns.com/patterns/adapter/" %}

[^1]: 어댑터에게 제공하는 객체라는 뜻에서 **adaptee**라고 합니다.
