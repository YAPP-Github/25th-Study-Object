# 5장 : 책임 할당하기

- 코드는 책임 중심으로 설계되어야함
- 그런데 어떤 객체에 어떤 책임을 할당하는 것이 최선인지는 상황과 문맥에 따라 달라짐
- 이 장에서 살펴볼 GRASP 패턴이 책임 할당을 도와줄 수 있음
- 이번 장에서는 2장에서 소개한 코드의 설게과정을 따라가며 책임 할당의 기본적인 원리를 살펴봄

### 책임 주도 설계를 향해

- 책임 중심의 설계를 위해서는 객체의 데이터가 아니라 책임과 협력에 초점을 맞춰야함
- 데이터보다 행동을 먼저 결정하라
    - 데이터에 초점을 맞추면 캡슐화 약화
    - so, 책임 먼저 결정 → 책임 수행에 필요한 데이터 결정
    - 그래. 책임 먼저 결정하는거 알겠는데, 누구에게 책임 할당하나?
- 협력이라는 문맥 안에서 책임을 결정하라
    - 책임의 품질은 협력에 적합한 정도로 결정
        - 객체 입장보다 협력안에서 적합한지 생각해야함
    - 협력에 적합한 책임이란, 메시지 전송자에게 적합한 책임
        - 메시지 전송하는 클라이언트의 의도에 적합한 책임 할당해야함
        - 그러니까 메시지 먼저 결정하고 객체 선택하는것
        - 이전의 설계 과정
            - 이 클래스 필요하긴 한데, 이 클래스 뭐해야하지?
        - 올바른 설계 과정
            - 이 메시지 전송해야하는데, 누구한테 전송하지?
    - 메시지를 먼저 결정하기 때문에 수신자에 대한 어떠한 가정도 불가능
        - 이러면 깔끔하게 캡슐화 가능
        - 따라서 데이터에 집중하는 것보다 메시지에 집중하는것이 캡슐화 쉬워지는 것
    - 즉, 협력이라는 문맥을 고려하는 것은, 클라이언트 관점에서 적절한 책임을 고려한다는 의미임

### 책임 할당을 위한 GRASP 패턴

- GRASP란?
    - 일반적인 책임 할당을 위한 소프트웨어 패턴(General Responsibility Assignment Software Pattern)
    - 객체에게 책임 할당할 때 지침으로 삼을 수 있는 원칙들
    - 지금까지 배운 책임 할당 관련 지식들에 이름을 붙인 것
- 영화 예매 시스템을 책임 중심으로 설계하는 과정 따라가보기
    - 도메인 개념에서 출발하기
        
        ![image](https://github.com/user-attachments/assets/3f52ce44-928a-4b78-a7c4-ff7ed358667c)

        
        - 설계 전에 도메인에 대한 개략적인 모습 그려보면 유용함 → 코드에 도메인 모습 투영하기 수월
        - 출발점이라는 의미가 큼 (완벽하지 않아도 된다는 뜻)
        - 너무 많은 시간을 들이지 말자. 어차피 수정될 수 있음
    - 정보 전문가에게 책임을 할당하라
        - 애플리케이션이 제공해야하는 기능을 애플리케이션의 책임으로 생각하기
            
            ![image 1](https://github.com/user-attachments/assets/cd6a0528-fb89-4be5-bfd9-b75baf272059)
            
        - 이 메시지를 책임질 첫 번째 객체 선택
            - 책임을 수행할 정보를 알고 있는 객체에게 책임 할당
                - 정보전문가 패턴
                - 주의점
                    - 정보를 알고 있다는 것이 정보를 저장한다는 뜻은 아님
                    - 다른 객체를 알거나 필요한 정보 계산해서 제공할수도
                    - 애초에 데이터를 먼저 결정하지 않으니…
            
            ![image 2](https://github.com/user-attachments/assets/3b0ac849-12e8-4542-b2d4-c61827702c38)
            
        - 메시지 완료하기 위한 메시지 요청하고 객체 선택
            
            ![image 3](https://github.com/user-attachments/assets/a67666e8-dbde-4f0b-97de-97bff1fd0a50)
            
        - 이 과정을 반복
            
            ![image 4](https://github.com/user-attachments/assets/1d67cb24-0e7d-43e8-81c4-785f3341da16)
            
- 높은 응집도와 낮은 결합도
    - 동일한 기능 구현하는 여러 설계중 어떤 설계를 선택해야할까?
    - 예를 들어, 이 그림은 이전 설계와 같은 동작을 하는데 뭘 선택해야하지?
        
        ![image 5](https://github.com/user-attachments/assets/1fb2c11f-177c-4480-bd0c-addd31c6e7dc)
        
    - 응집도와 결합도를 생각해서 선택해야함
        - 낮은 결합도 패턴
            - Movie는 이미 DiscountCondition의 목록 가지고 있으므로 협력한다고 해서 결합도 높아지지 않음
            - 따라서 Movie와 DiscountCondition이 협력하는 것이 좋음
        - 높은 응집도 패턴
            - Screening의 가장 중요한 책임은 예매를 생성하는 것인데, 할인 조건과 협력하면 요금 계산 관련 책임 일부 떠안게됨
            - Screening은 서로 다른 이유로 변경되는 책임 짊어지게되므로 응집도 떨어짐
            - 반면 Movie는 원래 요금 계산 책임을 가지고 있었으므로 협력해도 응집도 안떨어짐
- 창조자에게 객체 생성 책임을 할당하라
    - 영화예매 협력의 결과물로 누군가는 Reservation 인스턴스를 생성해야함
    - CREATOR 패턴
        - 객체를 생성할 책임을 어떤 객체에 할당할지에 대한 지침
        - 아래 조건을 최대한 많이 만족하는 B에게 객체 생성 책임 할당
            - B가 A 객체를 포함하거나 창조
            - B가 A 객체를 기록
            - B가 A 객체를 긴밀하게 사용
            - B가 A객체를 초기화하는 데 필요한 데이터 가짐
    - 따라서 Screening이 객체 생성 책임을 가지는 것이 좋음
        
        ![image 6](https://github.com/user-attachments/assets/54ff2eca-3139-485f-a484-7938969bc5ae)
        
- 현재까지의 책임 분배는 설계 시작하기 위한 대략적인 스케치
- 실제 설계는 코드 작성하면서 이뤄짐
- 코드 작성하고 실행해 봐야 협력과 책임 제대로 동작하는지 확인 가능

### 구현을 통한 검증

- 메시지를 처리할 수 있는 메서드 구현
- 책임 수행하는데 필요한 인스턴스 변수 결정
- 책임 수행 로직 구현
- 메시지는 수신자가 아니라 송신자의 의도를 표현해야함
    - 그래야 내부 구현을 깔끔하게 캡슐화 가능

### DiscountCondition 개선하기

- DiscountCondition의 기존 구현 코드는 아래와 같음
    
    ![image 7](https://github.com/user-attachments/assets/8249bfe6-3657-472c-bac5-7df51be9abf3)
    
- 이 코드는 변경에 취약한 코드이다.
    - 만약 새로운 할인 조건이 추가된다면?
    - 순번 조건을 판단하는 로직이 변경된다면?
    - 기간 조건을 판단하는 로직이 변경된다면?
- 하나 이상의 변경 가능성을 가져서 응집도가 낮아 생기는 일
- 이런 문제있는 클래스는 어떻게 찾을까?
    - 변경의 이유가 하나 이상인 클래스 찾으면 됨
    - 그런데 그걸 어떻게 찾지?
        - 인스턴스 변수 초기화 시점 살펴보기
            - 응집도 낮으면 일부만 초기화됨
            - 예를들어, 순번조건이면 기간조건 초기화 관련 속성 초기화 안됨
        - 메서드들이 인스턴스 변수 사용하는 방식 살펴보기
            - 모든 메서드가 모든 속성 사용해야 응집도 높음
            - 사용하는 속성에 따라 그룹나뉘면 응집도 낮음
- 문제 해결방법 - 타입 분리하기
    - 순번조건과 기간조간이 공존하는게 문제니까 쪼개면 어떨까
        
        ![image 8](https://github.com/user-attachments/assets/a0e179bb-15ec-461a-93f2-19bdcde8e16e)
        
        - 앞에서 언급한 문제점 모두 해결
        - 하지만, 두개의 인스턴스와 Movie가 협력해야함
        - Movie에서 목록으로 유지하면?
            - 양쪽 모두에게 결합됨
            - 전체적인 결합도 높아짐
            - 새로운 할인 조건 추가 어려워짐
- 문제 해결 방법 - 다형성을 통해 분리하기
    
    ![image 9](https://github.com/user-attachments/assets/74dd88d5-8568-48f0-b805-dbaf9648afe3)
    
    - 다형성 패턴
    - 구체적인 타입 몰라도 됨
- 변경으로부터 보호하기
    - 변경보호패턴
    - 변경을 캡슐화하도록 책임 할당
    - 새로운 할인 조건 추가해도 movie에 영향안감
- Movie 클래스 개선하기
    - 금액 할인 정책 영화랑 비율 할인 정책 영화 두가지 타입을 하나의 클래스로 구현해서 응집도 낮음
    - 두개를 다형성을 이용해 나누면 됨
- 변경과 유연성
    - 상속 대신 합성을 이용하면 코드를 더 유연하게 만들 수 있음

### 책임 주도 설계의 대안

- 책임주도 설계는 어려움
    - 그래서 잘 안될때는 일단 개발하고 리팩토링 해라
- 메서드 응집도
    - 매우긴 메서드(몬스터 메서드)
        - 작게 분해해서 메서드 응집도 높인다
        - 재사용성 향상, 변경 수월
- 객체를 자율적으로 만들자
    - 자신이 소유하고 있는 데이터 스스로 처리하게 만든다
    - 메서드를 데이터가 존재하는 곳으로 이동시킴
