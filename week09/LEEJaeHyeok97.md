> 개방 폐쇄 원칙은 “확장에는 열려 있어야 하고, 변경에는 닫혀 있어야 한다”
다시말해, “객체의 기능을 확장할 수 있으면서 기존의 코드(클라이언트)는 수정하지 않는다”
>

### 런타임 의존성과 컴파일타임 의존성

OCP는 런타임 의존성과 컴파일타임 의존성에 관한 이야기다.

![Image](https://github.com/user-attachments/assets/83fb4334-8afc-4634-bd7e-9289c2df84c8)

- Movie는 DiscountPolicy(구현체가 아닌 인터페이스나 부모클래스)에 의존하기 떄문에 자식 클래스로 OverlappedDiscountPolicy 클래스를 추가하더라도 기존의 코드는 변하지 않는다.
    - 변경의 이유가 1가지이다. 그건 바로 서비스 계층의 할인 정책 인터페이스

### 추상화

> OCP의 핵심은 추상화에 의존하는 것이다
’폐쇄’를 가능하게 하는 것은 추상화에 대한 의존성의 방향이다.
>

```java
public class Movie {
    //...
    private DiscountPolicy discountPolicy;

    public Movie(String title, Duration runningTime, Money fee) {
        // ...
        this.discountPolicy = new AmountDiscountPolicy();
    }

    // ...
}
```

- 위 코드를 보면, new AmountDiscountPolicy()를 주입받고 있는데, 이렇게 되면 인터페이스의 구현체를 의존하게 되고 OCP를 위반하는 코드라고 볼 수 있다.
- OCP는 기존 코드를 변경하지 않고 확장할 수 있어야 한다는 점 때문이다.
- 개선된 코드

```java
public class Movie {
	private DiscountPolicy discountPolicy;
	
	
	public Movie(tring title, Duration runningTime, Money fee, DiscountPolicy discountPolicy) {
	
			// ..
			
			this.discountPolicy = discountPolicy;
	}
			// ..
	}
```

- 인터페이스에만 의존하도록 하고, 구체 정책은 외부에서 결정하여 전달받는 방식으로 변경한다.
    - OCP를 준수

![Image](https://github.com/user-attachments/assets/9ded0d40-099a-41ad-b736-c0932e52d388)

- 가장 보편적인 방법은 객체 생성의 책임을 클라이언트로 옮기는 것이다.

### Factory

객체에 대한 생성과 사용을 분리하기 위해 객체 생성에 특화된 객체

![Image](https://github.com/user-attachments/assets/005a52c7-9d34-4f5c-8e1d-73a921924e70)

```java
// DiscountPolicyFactory.java
public class DiscountPolicyFactory {

    public DiscountPolicy createDiscountPolicy(String policyType) {
        // policyType 등에 따라 적절한 구현체 생성
        if (policyType.equals("AMOUNT")) {
            return new AmountDiscountPolicy();
        } else if (policyType.equals("PERCENT")) {
            return new PercentDiscountPolicy();
        }
        // 확장 가능
        return null;
    }
}

// Movie.java
public class Movie {
    private DiscountPolicy discountPolicy;

    // Factory를 통해 주입받는다.
    // 어떤 DiscountPolicy를 쓸지는 Factory가 결정
    public Movie(String title, Duration runningTime, Money fee,
                 DiscountPolicyFactory factory, String policyType) {
        // ...
        this.discountPolicy = factory.createDiscountPolicy(policyType);
    }
    
    // ...
}

// Client.java
public class Client {
    public static void main(String[] args) {
        DiscountPolicyFactory factory = new DiscountPolicyFactory();

        // 어떤 정책을 쓸지는 클라이언트가 결정하되,
        // 실제 인스턴스 생성은 factory가 담당
        Movie movie = new Movie("Avengers", Duration.ofMinutes(120), new Money(12000),
                                factory, "AMOUNT");
        
        // ...
    }
}
```

### DI

생성과 사용의 책임을 분리한 후에 사용되는 방법이 DI이다.

### DIP

자주 변하는 구체적인 클래스보다는 변하기 어려운 인터페이스나 상위 클래스와 의존 관계를 맺으라는 원칙이 애플리케이션을 변경에 용이하게 할 수 있다.

> 상위 모듈은 하위 모듈에 의존해서는 안된다. 추상화에 의존하라.
객체는 세부 사항에 의존해서는 안된다. 구체적인 사항은 추상화에 의존해야 한다.
>

### 유연한 설계는 곧 복잡한 설계

유연한 설계는 코드를 복잡하게 만들고 가독성을 해친다.

유연성이 필요한 때에만 유연한 설계를 하자.