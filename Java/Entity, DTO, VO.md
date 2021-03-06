# **Entity, DTO, VO**

## **Entity ๋?**
> ๐ก Entity Class๋ย ์ค์  DataBase์ ํ์ด๋ธ๊ณผ 1 : 1๋ก Mapping ๋๋ Class๋ก, DB์ ํ์ด๋ธ๋ด์ ์กด์ฌํ๋ ์ปฌ๋ผ๋ง์ ์์ฑ(ํ๋)์ผ๋กย ๊ฐ์ ธ์ผ ํ๋ค. Entity Class๋ ์์์ ๋ฐ๊ฑฐ๋ ๊ตฌํ์ฒด์ฌ์๋ ์๋๋ฉฐ, ํ์ด๋ธ๋ด์ ์กด์ฌํ์ง ์๋ ์ปฌ๋ผ์ ๊ฐ์ ธ์๋ ์๋๋ค.
- ์ต๋ํย **์ธ๋ถ์์ย `Entity`ย Class์ Data Field์ ํจ๋ถ๋ก ์ ๊ทผํ์ง ๋ชปํ๋๋ก ์ ํ**
- ํด๋น Class ์์์ ์ ๊ทผ์ ํ์ฉํ  ๋ฐ์ดํฐ๋ค์ ์ ํํ๋ฉฐ logic method์ ๊ตฌํ
- Domain Logic ๋ง ๊ฐ์ง๋ฉฐ, Presentation Logic์ ๊ฐ์ง๊ณ  ์์ด์  ์๋จ
- *Spring 3 Tier์ธย `Persistence`,ย `Buseniss`,ย `Presentation`ย Tier ์คย `Persistence Tier`์์ ์ฌ์ฉ*
- ๊ตฌํ method๋ ์ฃผ๋ก Service Layer์์ ์ฌ์ฉ,
    - Entity๋ฅผย `Persistence`ย Tier์์ ๋ฐ์์ DTO๋ก ๋ณํํ์ฌ`Presentation`ย Tier์ ์ ๋ฌํ๋ ๊ฒ์ดย `Business Tier`,ย `Service`ย ๋จ์์์ ์ญํ 

## **DTO(Data Transfer Object) ๋? ๋ฐ์ดํฐ ์ ๋ฌ์ฉ**

> ๐ก DTO(Data Transfer Object)๋ ๋ฐ์ดํฐ ์ ์ก(์ด๋) ๊ฐ์ฒด๋ผ๋ ์๋ฏธ๋ฅผ ๊ฐ์ง๊ณ  ์๋ค. DTO๋ ์ฃผ๋ก ๋น๋๊ธฐ ์ฒ๋ฆฌ๋ฅผ ํ  ๋ ์ฌ์ฉํ๋ค.

- **DTO๋ ๊ณ์ธต๊ฐ ๋ฐ์ดํฐ ๊ตํ์ ์ํ ๊ฐ์ฒด**
    - DB ์ ๋ฐ์ดํฐ๋ฅผ Service ๋ Controller ๋ฑ์ผ๋ก ๋ณด๋ผ ๋ ์ฌ์ฉํ๋ ๊ฐ์ฒด
    - DB์ ๋ฐ์ดํฐ๊ฐ `Presentation Logic Tier`๋ก ๋์ด์ฌ๋๋ DTO๋ก ๋ณํ๋์ด ์ค๊ณ ๊ฐ
    - ๋ก์ง์ ๊ฐ๊ณ  ์์ง ์์ ์์ํ ๋ฐ์ดํฐ ๊ฐ์ฒด์ด๋ฉฐ getter / setter ๋ง ๊ฐ๋๋ค. DB ์์ ๊บผ๋ธ ๊ฐ์ ๋ณ๊ฒฝํ  ํ์๊ฐ ์๊ธฐ์ setter ๊ฐ ์์

- Request์ Response์ฉ DTO๋ View๋ฅผ ์ํ ํด๋์ค
    - ์์ฃผ ๋ณ๊ฒฝ์ด ํ์ํ ํด๋์ค
    - Presentation Model
    - `toEntity()` ์ ๊ฐ์ ๋ฉ์๋๋ฅผ ํตํด์ DTO์์ ํ์ํ ๋ถ๋ถ์ ์ด์ฉํ์ฌ Entity๋ก ๋ณ๊ฒฝ
    - ๋ํ Controller Layer์์ Response DTO ํํ๋ก Client์ ์ ๋ฌ

    ![image](https://user-images.githubusercontent.com/63777714/155154205-8384a331-071f-43a3-a83a-1bb3003132df.png)



> ๐ก ์ฐธ๊ณ  **entity ํด๋์ค์ DTO ํด๋์ค๋ฅผ ๋ถ๋ฆฌํ๋ ์ด์ ** <br>
> -  View Layer์ DB Layer์ ์ญํ ์ ์ฒ ์ ํ๊ฒ ๋ถ๋ฆฌํ๊ธฐ ์ํด
> -  ํ์ด๋ธ๊ณผ ๋งคํ๋๋ Entity ํด๋์ค๊ฐ ๋ณ๊ฒฝ๋๋ฉด ์ฌ๋ฌ ํด๋์ค์ ์ํฅ์ ๋ผ์น๊ฒ ๋จ
> -  ๋ฐ๋ฉด View์ ํต์ ํ๋ DTO ํด๋์ค(Request / Response ํด๋์ค)๋ ์์ฃผ ๋ณ๊ฒฝ๋๋ฏ๋ก ๋ถ๋ฆฌํด์ผ ํจ
> -  Domain Model์ ์๋ฌด๋ฆฌ ์ ์ค๊ณํ๋ค๊ณ  ํด๋ ๊ฐ View ๋ด์์ Domain Model์ getter๋ง์ ์ด์ฉํด์ ์ํ๋ ์ ๋ณด๋ฅผ ํ์ํ๊ธฐ๊ฐ ์ด๋ ค์ด ๊ฒฝ์ฐ๊ฐ ์ข์ข ์์.
> -  ์ด๋ฐ ๊ฒฝ์ฐ Domain Model ๋ด์ Presentation์ ์ํ ํ๋๋ ๋ก์ง์ ์ถ๊ฐํ๊ฒ ๋๋๋ฐ, ์ด๋ฌํ ๋ฐฉ์์ด ๋ชจ๋ธ๋ง์ ์์์ฑ์ ๊นจ๊ณ  Domain Model ๊ฐ์ฒด๋ฅผ ๋ง๊ฐ๋จ๋ฆฌ๊ฒ ๋๋ค.
> -  ๋ํ Domain Model์ ๋ณต์กํ๊ฒ ์กฐํฉํ ํํ์ Presentation ์๊ตฌ์ฌํญ๋ค์ด ์๊ธฐ ๋๋ฌธ์ Domain Model์ ์ง์  ์ฌ์ฉํ๋ ๊ฒ์ ์ด๋ ต๋ค.
> -  ์ฆ , DTO๋ Domain Model์ ๋ณต์ฌํ ํํ๋ก, ๋ค์ํ Presentation Logic์ ์ถ๊ฐํ ์ ๋๋ก ์ฌ์ฉํ๋ฉฐ Domain Model ๊ฐ์ฒด๋ Persistent๋ง์ ์ํด์ ์ฌ์ฉํ๋ค.

## **VO(Value Object) ๋? ๊ฐ ํํ์ฉ**

- VO๋ ๊ฐ ์์ฒด๋ฅผ ํํํ๋ ๊ฐ์ฒด
- VO๋ ๊ฐ์ฒด๋ค์ ์ฃผ์๊ฐ ๋ฌ๋ผ๋ ๊ฐ์ด ๊ฐ์ผ๋ฉด ๋์ผํ ๊ฒ์ผ๋ก ์ฌ๊น
    - ์๋ฅผ ๋ค์ด, ๊ณ ์ ๋ฒํธ๊ฐ ์๋ก ๋ค๋ฅธ ๋ง์ 2์ฅ์ด ์๋ค๊ณ  ์๊ฐํ์. ์ด ๋์ ๊ณ ์ ๋ฒํธ(์ฃผ์)๋ ๋ค๋ฅด์ง๋ง 10000์(๊ฐ)์ ๋์ผํ๋ค. VO๋ย `getter`ย ๋ฉ์๋์ ํจ๊ป ๋น์ฆ๋์ค ๋ก์ง๋ ํฌํจํ  ์ ์๋ค. ๋จ,ย `setter`ย ๋ฉ์๋๋ ๊ฐ์ง์ง ์๋๋ค. ๋, ๊ฐ ๋น๊ต๋ฅผ ์ํดย `equals()`์ย `hashCode()`ย ๋ฉ์๋๋ฅผ ์ค๋ฒ๋ผ์ด๋ฉ ํด์ค์ผ ํ๋ค.
- ์๋ ์ฝ๋์ฒ๋ผ equals()์ hashCode() ๋ฉ์๋๋ฅผ ์ค๋ฒ๋ผ์ด๋ฉ ํ์ง ์์ผ๋ฉด ํ์คํธ๊ฐ ์คํจํ๋ค.

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
    @DisplayName("VO ๋๋ฑ๋น๊ต๋ฅผ ํ๋ค.")
    @Test
    void isSameObjects() {
        Money money1 = new Money("์", 10000);
        Money money2 = new Money("์", 10000);

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
    @DisplayName("VO ๋๋ฑ๋น๊ต๋ฅผ ํ๋ค.")
    @Test
    void isSameObjects() {
        Money money1 = new Money("์", 10000);
        Money money2 = new Money("์", 10000);

        assertThat(money1).isEqualTo(money2);
        assertThat(money1).hasSameHashCodeAs(money2);
    }
}
```


> ๐ก **VO(Value Object) vs DTO**
VO๋ DTO์ ๋์ผํ ๊ฐ๋์ด์ง๋ง read only ์์ฑ์ ๊ฐ์ง, VO๋ ํน์ ํ ๋น์ฆ๋์ค ๊ฐ์ ๋ด๋ ๊ฐ์ฒด์ด๊ณ , DTO๋ Layer๊ฐ์ ํต์  ์ฉ๋๋ก ์ค๊ณ ๊ฐ๋ ๊ฐ์ฒด๋ฅผ ๋งํจ

![image](https://user-images.githubusercontent.com/63777714/155154634-08ea620d-44f2-4296-aa15-987d4b5280a4.png)

---
์ฐธ๊ณ ํ ์ฌ์ดํธ
- https://velog.io/@gillog/Entity-DTO-VO-๋ฐ๋ก-์๊ธฐ