# DDD-practice.js

#### DDD(Domain Driven Design, 도메인 주도 디자인) 
- 도메인 전문가들로부터 입력을 받아 도메인에 소프트웨어 모델링을 매치시키는데 초점을 맞추는 소프트웨어 디자인 접근 방법.
- 왜 DDD에 대해서 이야기를 하는가
```
Complexity : 제품의 복잡성을 해결하는 방향에서의 DDD
Communications : 개발 과정에서 여러 팀, 사람들간 효율적인 의사 소통을 위한 방향에서의 DDD
Similarity : 기존 Frontend에서 사용하고 있었던 기술, 패턴이 DDD와 크게 차이가 없음.
```

#### Complexity

```
영화 예매 시스템을 구축한다고 가정
→ 수 많은 요구사항들 (e.g 고객이 예매 가능한 좌석을 알려달라 하면 알려준다 등)
→ 팀 간에 업무를 분배 한다면 어떻게 나눌 수 있을까?
→ 새로운 요구사항에 대해서 수정 범위는 얼마나 되는 걸까?
→ 이러한 궁금증에 대해 DDD가 도움을 줄 수 있다.
단순함은 궁극의 정교함이다. - 레오나르도 다빈치
```

#### Communications

- Class?
```
디자이너는 css 를 스타일링 하는 클래스로 이해할 것
개발자는 객체를 이야기하는 클래스로 이해할 것
→ 같은 개발 직군에서도 소통하는데 혼동할 수 있다.
```

- Product?
```
Order Team : 가격이나 재고, 상품의 옵션
Customer Support Team : 제품의 배송 기간, 별점이나 리뷰
→ 다른 관점으로 바라봄.
→ 모델에 혼용을 줄 수 있다.
→ DDD에서는 모든 구성원들이 공통적으로 쓰는 말을 유비쿼터스 언어라고 지칭한다.
```

#### Similarity
Redux vs DDD’s CQRS Pattern : 상당히 비슷

- Event Sourcing Pattern과도 흡사
- 상태를 변경하는 Command를 위한 모델
- 상태를 제공하는 Query를 위한 모델
- 어플리케이션의 상태는 이벤트에 의해 변경될 것.
  - 이 이벤트는 변하지 않는 오브젝트, 어플리케이션에 의해 날아옴
  
- Redux
```
Client → UI → (create) → Action → (dispatch) → Reducers → (update) → Store → UI
```

- CQRS
```
Client → UI→ Query → Read DB → Command → Write DB → (Event Bus) → Read DB
```

#### DDD란 무엇일까?
DDD 두 가지로 나눌 수 있다.

##### Strategic Design

  - Ubiquitous Language : Bounded Context 안에서 사용되는 공통적인 언어 커뮤니케이션 비용 발생 절감에 중점
  - Bounded Context : 다양한 도메인에서 같은 용어가 많이 사용이 된다.
    - 고객이 바라보는 상품과, 주문에서의 상품은 다르다.
    - 모든 도메인에서 사용되는 모델이 아닌, 각각의 도메인에 맞게 모델을 따로따로 만들어야 하는 것이 맞다.

##### Tactical Design
  - 시스템 구현 그 자체에 의미를 둔다.  
  - 다양한 공간에서 다양한 커뮤니케이션을 하는데 중점.
  - 기술적인 리소스가 어떻게 쓰이게 되는지.
  - 리소스는 하나의 Bounded Context 안에서 동작
```
  다섯 가지 개념
  - Entity : 고유한 식별자가 있는 변경 가능한 객체 (e.g. uuid), mutable
  - Value Object : 고유한 id가 없고 속성 값으로만 정의, 업데이트 하려면 새로운 인스턴스를 생성해야 함, immutable
  - Aggregate : 하나 이상의 Entity의 클러스터, Value Object도 포함할 수 있다.
  
  - Services : 상태를 부여하지 않고 로직을 구현하는 객체
    - Domain Service : 도메인 기능 캡슐화, 도메인 종속적 Operation ex) 비즈니스 로직도메인 동작
    - Application Service : 기술적인 기능(어플리케이션 workflow) 제공. 외부 컨슈머가 시스템과 커뮤니케이션하는 매개체 ex) 어플리케이션 로직
  - Domain Events
    - 변경이 있을 때 시스템의 다른 부분에 이를 알려주는데 사용한다.
```

#### 어떻게 DDD를 적용할 수 있을까?

##### 어플리케이션이 어떤 형태로 디자인이 되는가?
- Layered 구조
  ```
  User Interface
  Application
    - 도메인 객체의 직접적인 클라이언트
    - 비즈니스 상 필요한 Use Case를 구현한 객체들의 집합
  Domain
    - 도메인과 관련된 객체들의 집합
    - Entity, Value Object, 그리고 Domain Event가 존재
  Infrastructure
    - 다른 계층들을 지탱
    - 구현체의 집합
    
  * 의존성의 방향은 위에서 아래로 (UI → App → Domain → Infra)
  ```
  
- Hexagonal 구조
  ```
  User Interface
  Adapter(Port)
  Application
  Domain
  Infrastructure
  
  * 의존성은 바깥에서 안으로만 향함(단방향) UI → Adapter → Infra → Use Cases → Domain
  ```

   
- 부분적인 수정이 필요할 때 전체에 대한 수정 필요 없이 Infra 및 Adapter에서만 수정을 해 주면 됨.
  - 프레임워크 : UI, Adapter
  - 프레임워크에 독립적 : Infra, App, Domain
  
#### 코드 레벨에서의 이야기
의존성에 맞춰서 폴더 구조 짜볼 수 있다.
```
src    
└───ui
└───adapter
└───infrastructure
└───application
└───domain
└───common-ui
└───utils
```
- UI Layer는 일반적으로 우리가 생각하는 그 레이어
- Adapter Layer
  - 흔히 사용하는 Redux나 Vuex
  - 뷰가 참조할 State 정의, selector로 필요한 값을 참조함
- Infrastructure Layer
  - 서비스에 대한 구현 그 자체
  - 외부 시스템과 커뮤니케이션
  - 저수준 모듈의 구현체를 Infra layer에 정의한다.
  - Dependency Injection
- Application Layer
  - 비즈니스 로직이 들어있는 Use Cases가 존재
- Domain Layer
  - 비즈니스 도메인을 지원하는 책임
  - Entity, Value Object를 포함

#### 마주칠 수 있는 문제점?
```
여러 도메인이 동일한 프레젠테이션 컴포넌트를 필요로 할 경우
여러 도메인이 동일한 API 호출
여러 도메인이 동일한 hook이나 helper function을 사용
```
