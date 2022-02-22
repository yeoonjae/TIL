# **Entity, DTO, VO**

## **Entity 란?**
> 💡 Entity Class는 실제 DataBase의 테이블과 1 : 1로 Mapping 되는 Class로, DB의 테이블내에 존재하는 컬럼만을 속성(필드)으로 가져야 한다. Entity Class는 상속을 받거나 구현체여서는 안되며, 테이블내에 존재하지 않는 컬럼을 가져서도 안된다.
- 최대한 **외부에서 `Entity` Class의 Data Field에 함부로 접근하지 못하도록 제한**
- 해당 Class 안에서 접근을 허용할 데이터들을 제한하며 logic method을 구현
- Domain Logic 만 가지며, Presentation Logic을 가지고 있어선 안됨
- *Spring 3 Tier인 `Persistence`, `Buseniss`, `Presentation` Tier 중 `Persistence Tier`에서 사용*
- 구현 method는 주로 Service Layer에서 사용,
    - Entity를 `Persistence` Tier에서 받아와 DTO로 변환하여`Presentation` Tier에 전달하는 것이 `Business Tier`, `Service` 단에서의 역할

## **DTO(Data Transfer Object) 란? 데이터 전달용**

> 💡 DTO(Data Transfer Object)는 데이터 전송(이동) 객체라는 의미를 가지고 있다. DTO는 주로 비동기 처리를 할 때 사용한다.

- **DTO란 계층간 데이터 교환을 위한 객체**
    - DB 의 데이터를 Service 나 Controller 등으로 보낼 때 사용하는 객체
    - DB의 데이터가 `Presentation Logic Tier`로 넘어올때는 DTO로 변환되어 오고감
    - 로직을 갖고 있지 않은 순수한 데이터 객체이며 getter / setter 만 갖는다. DB 에서 꺼낸 값은 변경할 필요가 없기에 setter 가 없음

- Request와 Response용 DTO는 View를 위한 클래스
    - 자주 변경이 필요한 클래스
    - Presentation Model
    - `toEntity()` 와 같은 메서드를 통해서 DTO에서 필요한 부분을 이용하여 Entity로 변경
    - 또한 Controller Layer에서 Response DTO 형태로 Client에 전달

    ![image](https://user-images.githubusercontent.com/63777714/155154205-8384a331-071f-43a3-a83a-1bb3003132df.png)



> 💡 참고 **entity 클래스와 DTO 클래스를 분리하는 이유** <br>
> -  View Layer와 DB Layer의 역할을 철저하게 분리하기 위해
> -  테이블과 매핑되는 Entity 클래스가 변경되면 여러 클래스에 영향을 끼치게 됨
> -  반면 View와 통신하는 DTO 클래스(Request / Response 클래스)는 자주 변경되므로 분리해야 함
> -  Domain Model을 아무리 잘 설계했다고 해도 각 View 내에서 Domain Model의 getter만을 이용해서 원하는 정보를 표시하기가 어려운 경우가 종종 있음.
> -  이런 경우 Domain Model 내에 Presentation을 위한 필드나 로직을 추가하게 되는데, 이러한 방식이 모델링의 순수성을 깨고 Domain Model 객체를 망가뜨리게 된다.
> -  또한 Domain Model을 복잡하게 조합한 형태의 Presentation 요구사항들이 있기 때문에 Domain Model을 직접 사용하는 것은 어렵다.
> -  즉 , DTO는 Domain Model을 복사한 형태로, 다양한 Presentation Logic을 추가한 정도로 사용하며 Domain Model 객체는 Persistent만을 위해서 사용한다.

## **VO(Value Object) 란? 값 표현용**

- VO는 값 자체를 표현하는 객체
- VO는 객체들의 주소가 달라도 값이 같으면 동일한 것으로 여김
    - 예를 들어, 고유번호가 서로 다른 만원 2장이 있다고 생각하자. 이 둘은 고유번호(주소)는 다르지만 10000원(값)은 동일하다. VO는 `getter` 메소드와 함께 비즈니스 로직도 포함할 수 있다. 단, `setter` 메소드는 가지지 않는다. 또, 값 비교를 위해 `equals()`와 `hashCode()` 메소드를 오버라이딩 해줘야 한다.
- 아래 코드처럼 equals()와 hashCode() 메소드를 오버라이딩 하지 않으면 테스트가 실패한다.

```java
// Money.java
public class Money {
    private final String currency;
    private final int value;

    public Money(String currency, int value) {
        this.currency = currency;
        this.value = value;
    }

    public String getCurrency() {
        return currency;
    }

    public int getValue() {
        return value;
    }
}

// MoneyTest.java
public class MoneyTest {
    @DisplayName("VO 동등비교를 한다.")
    @Test
    void isSameObjects() {
        Money money1 = new Money("원", 10000);
        Money money2 = new Money("원", 10000);

        assertThat(money1).isEqualTo(money2);
        assertThat(money1).hasSameHashCodeAs(money2);
    }
}
```

```java
// Money.java
public class Money {
    private final String currency;
    private final int value;

    public Money(String currency, int value) {
        this.currency = currency;
        this.value = value;
    }

    public String getCurrency() {
        return currency;
    }

    public int getValue() {
        return value;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Money money = (Money) o;
        return value == money.value && Objects.equals(currency, money.currency);
    }

    @Override
    public int hashCode() {
        return Objects.hash(currency, value);
    }
}

// MoneyTest.java
public class MoneyTest {
    @DisplayName("VO 동등비교를 한다.")
    @Test
    void isSameObjects() {
        Money money1 = new Money("원", 10000);
        Money money2 = new Money("원", 10000);

        assertThat(money1).isEqualTo(money2);
        assertThat(money1).hasSameHashCodeAs(money2);
    }
}
```


> 💡 **VO(Value Object) vs DTO**
VO는 DTO와 동일한 개념이지만 read only 속성을 가짐, VO는 특정한 비즈니스 값을 담는 객체이고, DTO는 Layer간의 통신 용도로 오고가는 객체를 말함

![image](https://user-images.githubusercontent.com/63777714/155154634-08ea620d-44f2-4296-aa15-987d4b5280a4.png)

---
참고한 사이트
- https://velog.io/@gillog/Entity-DTO-VO-바로-알기