# 생성자 대신 정적 팩토리 메소드를 고려하라

## 정적 팩토리 메소드는 무엇인가?

정적 팩토리 메소드란 객체 생성의 역할을 하는 클래스 메소드라는 의미이다.

```java
public static Boolean valueOf(boolean b) {
    return b ? Boolean.TRUE : Boolean.FALSE;
}
```

위 메소드를 사용하면 기본타입인 boolean을 받아 Boolean 객체 참조로 변환한다.

그렇다면, 생성자와 정적 팩토리 메소드는 어떤 차이가 있는지 알아보자.

## 장점

### 1.  생성자에 넘기는 매개변수와 이름을 가질 수 있다.

생성자에 넘기는 매개변수와 생성자 자체만으로는 반환된 객체의 특성을 제대로 설명하지 못한다.\
반면 정적 팩토리 메소드는 이름만 잘 지우면 반환될 객체의 특성을 쉽게 묘사할 수 있다.

```java
public class Member{
    private String name;
    private int age;
    
    // 외국인 멤버 생성자 방식
    public Member(String firstName, String lastName, int age){        this.name = firstName + lastName;
        this.name = firstName + lastName;
        this.age = age;
    }
    
    // 외국인 멤버 정적 팩토리 메소드 방식
    public static Member createForeignMember(String firstName, String lastName, int age) {
        this.name = firstName + lastName;
        this.age = age + 2;
    }
}
```

### 2. 두 번째, 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.

### 3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.

Member라는 상위타입을 상속받고 있는 Men, Woman이라는 객체가 있다.

```java
public class Member {
    public static Member of (boolean menYN) {
        if (menYN) 
            return new Men();
        else
            return new Woman();
    }
}
```

이렇게 작성하면 하위 타입 객체를 반환 할 수 있다.

### 4. 입력 매개변수 따라 매번 다른 클래스의 객체를 반환 할 수 있다.

EnumSet 클래스는 public 생성자 없이 오직 정적 팩토리만 제공한다.

원소가 64개 이하면 원소들을 long 변수 하ㅗ 관리하는 RegularEnumSet의 인스턴스를 65개 이상이면 long 배열로 관리하는 JumboEnumSet의 인스턴스를 반환한다.

### 5. 정적 팩토리 메소드를 작성하는 시점에는 반환할 객체의 클래스가 존재지 않아도 된다.



> 정적 팩토리 메소드를 사용하는 경우가 더 많으므로, 무조건 public 생성자를 제공하던 습관이 있다면 고치자.

