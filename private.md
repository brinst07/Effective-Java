# private 생성자나 열거 타입으로 싱글턴임을 보증하라

## 싱글턴이란?

싱글턴이란 인스턴스를 오직 하나만 생성할 수 있는 클래스를 말한다.

그런데 클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트를 테스트하기가 어려워질 수 있다.

타입을 인터페이스로 정의한 다음 그 인터페이스를 구현해서 만든 싱글턴이 아니라면, 싱글턴 인스턴스를 가짜(mock) 구현으로 대체할 수 없기 때문이다.

## 싱글톤을 만드는 방법

### 첫번째 방법

첫번째 방법은 public static final 방식의 싱글턴이다.

```java
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() {...}
    
    public void leaveTheBuilding() {...}
}
```

private 생성자는 public static final 필드인 Elvis.INSTANCE를 초기화할 때 딱 한 번만 호출된다.

public이나 protected 생성자가 없으므로 Elvis 클래스가 초기화 될때 만들어진 인스턴스가 전체 시스템에서 하나뿐임이 보장된다.

#### 장점

1. 해당 클래스가 싱글턴임이 API에 명확하게 드러난다.
2. 간결하다.

### 두번째 방법

두번째 방법은 정적 팩터리 방식의 싱글턴이다.

```java
public class Elvis {
    private static final Elvis INSTANCE = new Elvis(); 
    private Elvis(){ ... }
    public static Elvis getInstance() { return INSTANCE; } 
    public void leaveTheBuilding() { ... }
}
```

Elvis.getInstance는 항상 같은 객체의 참조를 반환하므로 제 2의 Elvis 인스턴스란 결코 만들어지지 않는다.

#### 장점

1. API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다는 점이다.
2. 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다.
3. 정적 팩터리의 메서드 참조를 공급자로 사용할 수 있다.

위 두 방법으로 만든 싱글턴 클래스를 직렬화 하려면 단순히 Serializable을 구현한다고 선언하는 것만으로는 부족하다.

모든 인스턴스 필드를 일시적이라고 선언하고 readResolve 메서드를 제공해야한다. 이렇게 하지 않을 경우, 직렬화된 인스턴스를 역직렬화 할때마다 새로운 인스턴스가 만들어진다.

```java
private Object readResolve() {
    //진짜 Elvis를 반환하고, 가짜 Elvis는 GC에게 맞긴다.
    return INSTANCE;
}
```

### 세번째 방법

마지막 세번째 방법은 원소가 하나인 열거 타입을 선언하는 것이다.

해당 책에서는 이 방법이 바람직한 방법이라고 한다.

```java
public enum Elvis {
    INSTANCE;
    
    public void leaveTheBuilding() { ... }
}
```

#### 장점

1. 다른 방식들 보다 더 간결함
2. 추가노력 없이 직렬화가 가능함
3. 아주 복잡한 직렬화 상황이나 리플렉션 공격에도 제 2의 인스턴스가 생기는 일을 막아준다.

**대부분의 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이라고 한다.**
