## 책임 주도 설계를 향해

데이터 중심에서 책임 중심의 설계로 전환하기 위해 다음 원칙을 따라야 한다.

- 데이터보다 행동을 먼저 결정하라
- 협력이라는 문맥 안에서 책임을 결정하라

## 책임 주도 설계

- 시스템이 사용자에게 제공해야 하는 기능인 시스템 책임을 파악한다
- 책임을 더 작은 책임으로 분할한다
- 분할된 책임을 수행할 수 있는 적절한 객체 또는 역할을 찾아 책임을 할당한다
- 객체가 책임을 수행하는 도중 다른 객체의 도움이 필요한 경우 이를 책임질 적절한 객체 또는 역할을 찾는다
- 해당 객체 또는 역할에게 책임을 할당함으로써 두 객체가 협력하게 된다

## 책임할당을 위한 GRASP 패턴

GRASP 패턴은 객체에게 책임을 할당할 때 지침으로 삼을 수 있는 원칙들의 집합을 패턴 형식으로 정리한 것

## 도메인 개념에서 출발하기

책임 할당 시 가장 먼저 고민해야 하는 것은 도메인 개념이다.

> 설계 시작 단계에서는 개념들의 의미나 관계가 완벽할 필요가 없다. 우리에게는 출발점이 필요할 뿐이다. 도메인 개념과 관계는 구현의 기반이 된다.
>

### 정보전문가에게 책임을 할당하라

- 애플리케이션이 제공해야하는 기능을 애플리케이션의 책임으로 생각한다
- 이 책임을 애플리케이션에 대해 전송된 메시지로 간주하고 이 메시지를 책임질 첫번째 객체를 선택하는 것으로 설계를 시작한다.

> 객체에게 책임을 할당하는 첫 번째 원칙은 책임을 수행할 정보를 알고 있는 객체에게 책임을 할당하는 것이다. GRASP에서는 정보 전문가 패턴이라고 부른다.
INFORMATION EXPERT 패턴은 객체가 자율적인 존재여야 한다는 것을 상기시킨다.
>

<aside>
💡

정보를 알고있는 객체만이 책임을 어떻게 수행할지 스스로 결정할 수 있다. 정보와 행동이 가까운데 뭉쳐 캡슐화가 유지되며, 정보별로 책임이 분산되어 응집도가 높아지고 결합도가 낮아진다. 또한 가독성이 높아진다.

</aside>

## 높은 응집도와 낮은 결합도

> LOW COUPLING 패턴
설계의 전체적인 결합도가 낮게 유지되도록 책임을 할당하라
상영이 2개와 협력하는 두번째 구조는 결합도가 높아져 좋지 않은 설계이다.(스크리닝에 필요한 필드가 정보가 생김)
>

> HIGH COHESION 패턴
높은 응집도를 유지할 수 있게 책임을 할당하라.
두번째 구조는 Screen에게 영화 요금 계산 책임이 분산된다. 즉, Screen의 책임이 늘어난다.
이는 같이 변경될 여지도 증가한다.
첫번째 구조에서 Movie의 책임은 가격 계산이기 때문에, 할인조건과 협력은 응집도 높은 설계이다.
>

## 창조자 객체에게 객체 생성 책임을 할당하라

GRASP의 CREATOR(창조자) 패턴

> 아래 조건을 최대한 많이 만족하는 B에게 객체 생성 책임을 할당하라.
- B가 A 객체를 포함하거나 참조한다
- B가 A 객체를 기록한다
- B가 A 객체를 긴밀하게 사용한다
- B가 A 객체를 초기화하는데 필요한 데이터를 가지고 있다.(B는 A에 대한 정보 전문가)
>

![image.png](https://github.com/user-attachments/assets/97728bbd-b13c-4a54-8910-aba3be473b88)

Screening은 Reservation을 생성하는데 필요한 가장 많은 정보를 가지고 있다. → 정보 전문가

## DiscountCondition 개선하기

```java
public class DiscountCondition {
    private DiscountConditionType type;
    private int sequence;
    private DayOfWeek dayOfWeek;
    private LocalTime startTime;
    private LocalTime endTime;

    public boolean isSatisfiedBy(Screening screening) {
        if (type == DiscountConditionType.PERIOD) {
            return isSatisfiedByPeriod(screening);
        }

        return isSatisfiedBySequence(screening);
    }

    private boolean isSatisfiedByPeriod(Screening screening) {
        return dayOfWeek.equals(screening.getWhenScreened().getDayOfWeek()) &&
                startTime.compareTo(screening.getWhenScreened().toLocalTime()) <= 0 &&
                endTime.compareTo(screening.getWhenScreened().toLocalTime()) <= 0;
    }

    private boolean isSatisfiedBySequence(Screening screening) {
        return sequence == screening.getSequence();
    }
}
```

> 새로운 할인 조건 추가, 순번 조건 판단 로직 변경 등 하나 이상의 변경 이유를 가지므로 응집도가 낮다.
서로 다른 시점, 서로 다른 이유로 변경되는 코드들을 분리한다.
>

## 코드를 통해 변경의 이유 파악하기

인스턴스 변수가 초기화되는 시점

- 응집도가 높은 클래스는 모든 속성을 함께 초기화한다
- 일부가 Null로 남겨지거나 나중에 set되는 클래스는 의심해본다

DiscountCondition에서의 sequence, dayOfWeek, startTime, endTime

→ 함께 초기화되는 속성을 기준으로 클래스를 분리한다.

메서드들이 인스턴스 변수를 사용하는 방식

- 메소드가 모든 속성을 사용한다면 응집도가 높다
- 메서드들이 사용하는 속성에 따라 그룹이 나뉜다면 클래스의 응집도가 낮다

### 해결 방법1: 타입 분리하기

DiscountCondition의 순번 조건과 기간 조건이라는 두 타입이 공존하고 있는데 두 개의 클래스로 분리하여 응집도를 높인다.

But, 아는 클래스가 2개로 증가하여 결합도 증가, 목록 별로 넣는 로직이 필요해 구현이 귀찮아짐

### 해결 방법2: 다형성을 이용한 분리

객체의 타입에 따라 변하는 행동이 있다면 타입을 분리하고 변화하는 행동을 각 타입의 책임으로 할당하면 된다.

→ 암시적 타입을 명시적 클래스로 정의하고 행동을 나눔으로써 응집도를 낮춤.

![image](https://github.com/user-attachments/assets/ffb76c88-42b3-4204-a90a-1d308a327ae0)

## Movie 클래스 개선하기

Movie 클래스도 Discount와 같은 문제를 안고 있다.

다형성 패턴으로 타입을 마찬가지로 분리한다.

![image](https://github.com/user-attachments/assets/2d183e10-0ba8-479c-b653-40a8d18026be)

<aside>
💡

모든 클래스의 내부 구현은 캡슐화되어 있고 모든 클래스는 변경의 이유를 오직 하나씩만 가진다.

</aside>

## 변경과 유연성

개발자가 변경에 대비하는 방법

- 코드를 이해하고 수정하기 쉽게 단순하게 설계
- 코드를 수정하지 않고 변경을 수용할 수 있도록 코드를 더 유연하게 만드는 것

대부분의 경우에 전자가 더 좋은 방법이지만 유사한 변경이 반복적으로 발생하고 있다면 복잡성이 상승하더라도 유연성을 추가하는 두 번째 방법이 더 좋다.

![image](https://github.com/user-attachments/assets/9e137ffb-cb22-49df-a4f4-8219418a7232)


> 유연성은 의존성 관리의 문제이다.
요소들 사이의 의존성 정도가 유연성의 정도를 결정한다.
유연성의 정도에 따라 결합도를 조절할 수 있는 능력은 객체지향 개발자가 갖춰야 하는 중요한 기술 중 하나다.
>