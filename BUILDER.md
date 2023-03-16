# 생성자에 매개변수가 많다면 빌더를 고려하라

## 기존 생성자 방식의 문제점
정적 팩토리와 생성자에는 똑같은 제약이 하나있다.
선택적 매개변수가 많을 때 적절하게 대응하기 어렵다는 점이다.

### 점층적 생성자 패턴
```java
public class User{
    private final String name; //이름
    private final int age; //나이
    private final String address; //주소
    private final String hobby; //취미
    private int height; //키
    private int weight; //몸무게
    
    //기본 생성자
    //기본 user를 생성하는 생성자
    public User(String name, int age, String address, String hobby, int height, int weight) {
        this.name = name;
        this.age = age;
        this.address = address;
        this.hobby = hobby;
        this.height = height;
        this.weight = weight;
    }
    //점층적 생성자 패턴
    //새로 태어난 아기를 생성하는 생성자
    public User(String name, String address, int height, int weight) {
        this(name, 0, address, null, height, weight);
    }
}
```
위 점층적 생성자 패턴도 쓸 수는 있지만, 매개변수 개수가 많아지면 클라이언트 코드를 작성하거나 읽기가 어려워진다.
또한 해당 생성자에 의미부여를 할 수가 없어, 해당 생성자가 어떠한 역할을 하는지 파악하기도 어렵다

### 자바빈즈 패턴
```java
public class NutritionFacts {
    // 매개변수들은 (기본값이 있다면 기본값으로 초기화된다.
    private int servingSize = -1; // 필수: 기본값 없음
    private int servings = -1;
    private int calories = 0;
    private int fat = 0;
    private int sodium = 0;
    private int carbohydrate = 0;

    public NutritionFacts() {
    } 
    
    // 세터 메서드들
    public void setServingSize(int val) {
        servingSize = val;
    }

    public void setservings(int val) {
        servings = val;
    }

    public void setCalories(int val) {
        calories = val;
    }

    public void setFat(int val) {
        fat = val;
    }

    public void setSodium(int val) {
        sodium = val;
    }

    public void setCarbohydrate(int val) {
        carbohydrate = val;
    }
}

    NutritionFacts cocaCola = new NutritionFacts(); 
    cocaCola.setServingSize(240);
    cocaCola.setServings(8); 
    cocaCola.setCalories(100); 
    cocaCola.setSodium(35); 
    cocaCola.setCarbohydrate(27);

```
점층적 생성자 패턴의 단점들이 자바빈즈 패턴에서는 더이상 보이지 않는다. 코드가 길어지긴 했지만 인스턴스를 만들기 쉽고, 그 결과 더 읽기 쉬운 코드가 되었다.

자바빈즈 패턴에서는 객체 하나를 만들려면 메서드를 여러 개 호출해야하고, 객체가 완전히 생성되기 전까지는 일관성(consistency)이 무너진 상태에 놓이게 된다.
이 일관성이 깨진 객체가 만들어지면, 버그를 심은 코드와 그 버그 때문에 런타임에 문제를 겪는 코드가 물리적으로 멀리 떨어져 있을 것이므로 디버깅도 만만치 않다.

## Builder Pattern
이 두 패턴들은 치명적인 문제점을 지니고 있다.

이러한 문제점을 해결한 패턴이 Builder Pattern이다.
점층적 생성자 패턴의 안전성과 자바빈즈 패턴의 가독성을 겸비하였다.
빌더가 어떻게 동작하는지 코드로 살펴보자

```java
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder { // 필수 매개변수
        private final int servingSize;
        private final int servings;
        // 선택 매개변수 - 기본값으로 초기화한다.
        private int calories;
        private int fat;
        private int sodium;
        private int carbohydrate = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            peading this.servings = servings;
        }

        public Builder calories(int val) {
            calories = val;
            return this;
        }

        public Builder fat(int val) {
            fat = val;
            return this;
        }

        public Builder sodium(int val) {
            sodium = val;
            return this;
        }

        public Builder carbohydrate(int val) {
            carbohydrate = val;
            returnthis;
        }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize = builder.servingSize;
        servings = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }
}
```
다음은 Builder Pattern을 사용하는 예제를 알아보자

```java
NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8).calories(100).sodium(35).carbohydrate(27).build();
```
위 코드는 쓰기도 쉽고 읽기도 편하다. 
또한 빌더 패턴은 명명된 선택적 매개변수를 흉내 낸것이다.

다음 예제를 통해 Builder Pattern을 살펴보자

```java
@Builder
public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    @Builder(builderClassName = "servingBuilder", builderMethodName = "servingBuilder")
    public NutritionFacts(String servingSize, String servings) {
        if ( servingSize.eqauls("abc")) {
            throw new Exception("올바르지 않은 값이다.");
        }
        this.servingSize = servingSize;
        this.servings = servings;
    }
}
```
위 예제는 @Builder annotation과 Builder Pattern으로 정규식을 추가한 예제이다.
위처럼 작성하면 코드를 읽기도 매우 쉬울것이고, 일관성도 지킬 수 있다.
