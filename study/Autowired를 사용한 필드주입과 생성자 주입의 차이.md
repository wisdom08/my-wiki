# Autowired 를 사용한 필드주입과 생성자 주입의 차이
## 생성자 주입
```java
@Controller
public class InjectionController {
    private InjectionService service;
 
    @Autowired
    public InjectionController(InjectionService service) {
        this.service = service;
    }
}
```
- 생성자를 통해 의존성을 주입 받는다.
- 생상자를 통해 필수 의존성들을 제공함으로써, 클래스의 생성 시점에는 해당 클래스가 올바르게 작동할 준비가 되었다는 걸 확신 할 수 있음
- 필드에 final 을 선언할 수 있어 immutable 객체로 사용할 수 있다.
    - 생성자의 파라미터를 통해 의존관계를 쉽게 파악할 수 있고 리팩토링의 필요성을 얻을 수 있다.
        - 생성자의 파라미터가 많아짐과 동시에 하나의 클래스가 많은 책임을 떠안는다는 걸 알게 된다.
    - 필드 주입일때에 null 값이 되는게 가능하기 때문에 `NullPointerException` 발생 위험이 있는데 생성자 주입은 null 이 불가능하다.
- 서로 다른 객체들 간의 순환 참조를 막을 수 있다.
- 해당 클래스의 DI 프레임워크와의 결합을 완전히 분리해낼 수 있음(스프링 4.3 이상의 버전)

## 필드 주입
```java
@Controller
public class InjectionController {
    @Autowired
    private InjectionService service;
}
```
- 주입받을 객체에 `@Autowired` 어노테이션만 붙이면 된다.

## 필드 인젝션의 문제
### SRP 위반
> single responsibility principle
> - 하나의 클래스는 오직 하나의 책임을 가진다.
> - 클래스는 단 한가지의 변경 이유만을 가져야 한다.

- 너무 많은 의존성을 가지게 될 수 있다.

### Hiding Dependencies
- 의존성을 감추는 문제는 스프링 DI가 원래 추구하고자 했던 방향과 정 반대이다.

### immutable 불가
- setter 주입과 필드 인젝션으로 주입받는 클래스는 final 로 선언할 수가 없어 state safe 하지 않다.
    - 해당 객체를 새로 할당하더라도 알기 어렵다.
    - final 로 선언한 불변 객체였다면 코드 작성 과정에서 바로 알아차릴 수 있음

### 강한 결합
> 객체 내부에서 다른 객체를 생성하는 것은 강한 결합도를 가지는 구조다.
> - A 클래스 내부에서 B 라는 객체를 직접 생성하고 있다면 B 객체를 C 객체로 바꾸고 싶은 경우에 A 클래스도 수정해야 하는 방식이기 때문에 강한 결합이다.
- `@autowired` 를 이용한 필드 인젝션을 하면 스프링을 통해서만 의존성 주입이 가능하기 때문에 해당 빈들이 스프링의 DI 컨테이너와의 강한 결합을 하게 된다.
    - 스프링의 DI 컨테이너는 의존하는 빈 간에 느슨한 결합을 제공해줌

### 단위 테스트
- 필드 인젝션으로 주입한 객체를 테스트 하려면 무거운 스프링 컨테이너를 띄워야 한다.
    - `Mockito` 로 대응할 수는 있다.


참고
> 느슨한 결합
> - 객체를 주입 받는다는 것은 외부에서 생성된 객체를 인터페이스를 통해서 넘겨받는 것이다. 이렇게 하면 결합도를 낮출 수 있고, 런타임 시에 의존 관계가 결정되기 때문에 유연한 구조를 가진다.
> - SOLID 원칙에서 O에 해당하는 Open Closed Principle 을 지키기 위해서 디자인 패턴 중 전략 패턴을 사용하게 되는데 생성자 주입을 사용하게 되면 전략패턴을 사용하게 된다. 