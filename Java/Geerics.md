# 🔍 **제네릭(Generics) 이해하기**

## **제네릭(Generics) 란**
* 제네릭은 다양한 타입의 객체들으 다루는 메소드나 컬렉션 클래스에 컴파일 시의 타입체크를 해주는 기능
* 타입체크를 컴파일 시에 해주기 때문에 객체의 타입 안정성은 높아지고 형변환의 번거로움이 줄어든다. 

> 💡 **제네릭의 장점**
> * 타입의 안정성을 제공한다. 
> * 타입체크와 형변환을 생략할 수 있으므로 코드가 간결해진다. 

---
## **제네릭 클래스 선언**
* 제네릭 타입은 클래스와 메소드에 선언할 수 있다. 

```java
class Box<T> {
    Object item;

    void setItem(T item) { this.item = item; }
    T getItem() { return item; }

}
```
* `Box<T>` : 제네릭 클래스.
* `<T>` : 타입변수 또는 타입 매개변수. Type의 앞글자를 따온 것으로 다른 기호를 사용해도 된다.
* `Box` : 원시 타입(raw type)

```java
Box<String> box = new Box<String>;  // 타입 T 대신 실제 타입을 지정
box.setItem(new Object());  // 에러 발생. String 이외의 타입은 지정 불가
box.setItem("ABC"); 
String item = b.getItem();  // 형변환이 필요 없음
```

위와같이 코드를 작성했다면 Box 클래스는 다음과 같이 지정된 것과 같다. 
```java
class Box<String> {
    String item;

    void setItem(String item) { this.item = item; }
    String getItem() { return item; }

}
```
### **제네릭의 제한**
```java
Box<Apple> appleBox = new Box<Apple>(); // Apple 객체만 지정 가능
Box<Grape> grapeBox = new Box<Grape>(); // Grape 객체만 지정 가능
```
* 위의 코드와 같이 제네릭 타입을 지정하면 지정한 타입객체만 사용할 수 있다. 
* 또한 static 맴버에 타입변수 T 를 사용할 수 없다. 
    * static 맴버는 인스턴스 변수를 참조할 수 없다. 
    * T는 인스턴스 변수로 간주되기 때문에 static 맴버에 사용할 수 없다. 
    * 스태틱 맴버는 대입된 타입 변수의 종류에 관계없이 동일한 것이어야 하기 때문이다. 
* 제네릭 타입의 배열 생성이 안된다. 
```java
class Box<T> {
    T[] itemArr; // 가능
    T[] toArray() {
        T[] tmpArr = new T[itemArr.length]; // 에러. 제네릭 배열 생성 불가
        return tepArr;
    }
}
```
제네릭 배열을 생성할 수 없는 것은 new 연산자 때문인데, 이 연산자는 컴파일 시점에 타입 T가 뭔지 정확하게 알아야 한다. 하지만, 위의 코드는 대입된 제네릭 타입이 무엇인지 알 수 없다. 

반드시 생성해야 한다면 new 연산자 대신 `newInstance()`와 같이 동적으로 객체를 생성하는 메소드를 사용하는 방법이 있다. 

---
## **제네릭 클래스의 객체 생성과 사용**
* 제네릭 클래스의 한가지의 종류만 타입을 지정할 수 있다. 
```java
class Box<T> {
    ArrayList<T> list = new ArrayList<T>();

    voud add(T item)            { list.add(item);           }
    T get(int i)                { return item.get(i);       }
    ArrayList<T> getList()      { return item;              }
    int getSize()               { return item.size();       }
    public String toString()    { return item.toString();   }

}
```
위와 같은 코드가 있다고 가정했을 떄 `Box<T>`의 객체를 생성할 떄 참조변수와 생성자에 대입된 타입이 일치해야한다. 
```java
Box<Apple> appleBox = new Box<Apple>(); // 정상. Apple 객체만 지정 가능
Box<Apple> grapeBox = new Box<Grape>(); // 에러 발생
```
두 Apple과 Grape클래스가 Fruit라는 클래스를 상속하고 있다고 가정해보자. 어떻게 될까?
```java
Box<Fruit> grapeBox = new Box<Apple>(); // 에러 발생. 대입된 타입이 다름
```
에러가 발생한다. 대입된 타입이 상속관계에 있다고 해도 대입된 타입자체가 동일해야 한다. 

그렇다면 메소드를 이용해서 추가할 땐 어떨까?
```java
Box<Fruit> fruitBox = new Box<Fruit>();
fruitBox.add(new Fruit());  // 정상
fruitBox.add(new Apple());  // 정상
```
가능하다. 대압된 타입이 Fruit가 되었으니 Fruit의 자손은 매개변수가 될 수 있다. 

---
## **제한된 제네릭 클래스**
* `extends`를 사용하여 특정 타입의 상속관계에 있는 오브젝트만 대입할 수 있게 할 수 있다.
* 인터페이스를 구현해도 `implements`가 아닌 `extends`를 사용한다. 
```java
class FruitBox<T extends Fruit> {   // Fruit의 상속을 받은 오브젝트만 타입으로 지정 가능
    ...
}
```
* Fruit를 상속받은 오브젝트이면서 Eatable 인터페아스를 구현한 오브젝트여야 한다면 `&`기호로 연결한다. 
```java
class FruitBox<T extends Fruit & Eatable> {   // Fruit의 상속을 받은 오브젝트만 타입으로 지정 가능
    ...
}
```
---
## **와일드 카드**
```java
class Juicer {
    static Juice makeJuice(FruitBox<Fruit> box) {
        String temp = "";
        for(Fruit f : box.getList()) temp += f + " ";
        return new Juice(temp);
    }
}
```
위와 같은 클래스가 있다고 가정했을 때 Juicer 클래스를 사용하려면 타입 매개변수 T에 특정 타입을 반드시 지정해주어야 한다. 따라서 만약 타입 매개변수를 Apple로 지정하고 makeJuice() 메소드를 사용하기 위해선 다음과 같이 오버로딩을 해주어야 한다. 
```java
class Juicer {
    static Juice makeJuice(FruitBox<Fruit> box) {
        String temp = "";
        for(Fruit f : box.getList()) temp += f + " ";
        return new Juice(temp);
    }

    static Juice makeJuice(FruitBox<Apple>> box) {
        String temp = "";
        for(Fruit f : box.getList()) temp += f + " ";
        return new Juice(temp);
    }
}
```

하지만, 이와 같이 오버로딩하면 컴파일 에러가 발생한다. 
* 제네릭 타입이 다른 것만으로는 오버로딩이 성립하지 않기 때문
* 그래서 나온 방법이 와일드 카드 `?`
* 와일드 카드는 어떠한 타입도 될 수 있다. 
* `?`만으로는 Object와 다를 것이 없으므로 `extends`와 `super`로 상한과 하한을 제한할 수 있다. <br>
> `<? extends T>` : 와일드 카드의 상한 제한. T와 그의 자손들만 가능<br>
> `<? super T>` : 와일드 카드의 하한 제한. T와 그의 조상들만 가능<br>
> `<?>` : 와일드 카드. 제한 없음. 모든 타입이 가능

따라서 makeJuice() 메소드는 다음과 같이 변경할 수 있다. 

```java
class Juicer {
    static Juice makeJuice(FruitBox<? extends Fruit> box) {
        String temp = "";
        for(Fruit f : box.getList()) temp += f + " ";
        return new Juice(temp);
    }
}
```
다음과 같이 선언하면 Fruit를 상속받은 오브젝트 Apple도 makeJuice() 메소드를 사용할 수 있다. 

---
## **제네릭 메소드**
* 메소드의 선언부에 제네릭 타입이 선언된 메소드를 제네릭 메소드라고 한다. 
```java
class FruitBox<T> {
    static <T> void sort(List<T> list, comparator<? super T> c) { ... }
}
```
* 제네릭 클래스 `<T>`에 선언된 타입 매개변수와 제네릭 메소드에 선언된 `<T>`타입 매개변수는 서로 다른 것이다. 
* 제네릭 메소드에는 static을 사용할 수 있다. 
    * 위의 예시 makeJuice() 메소드를 제네릭 메소드로 바꾸면 다음과 같다. 
```java
static <T extends Fruit> Juice makeJuice(FruitBox<T> box) {
    String temp = "";
    for(Fruit f : box.getList()) temp += f + " ";
    return new Juice(temp);
}
```

위의 메소드를 호출하기 위해선 다음과 같이 타입변수에 타입을 대입해야 한다. 
```java
FruitBox<Fruit> fruitBox = new FruitBox<Fruit>();
FruitBox<Apple> appleBox = new FruitBox<Apple>();
...
System.out.println(Juiver.<Fruit>makeJuice(fruitBox));  // 정상 <Fruit> 생략 가능
System.out.println(Juiver.<Apple>makeJuice(appleBox));  // 정상 <Apple> 생략 가능

```
---
참고<br>
* 자바의 정석