# 10. equals는 일반 규약을 지켜 재정의하라

✅ 모든 클래스는 전부 Object클래스를 상속 받음

✅ **equals 메서드**를 만들지 않아도 된다면 만들지 않는게 **최선**

✅ ”@Override”로 재정의 하지 않으면 그 클래스의 인스턴스는 오직 자기자신과만 같게 됨

✅ 주로 값 클래스: Integer, String처럼 값을 표현하는 클래스

✅ 두 값 객체를 equals로 비교한다는 것을 객체가 같은지가 아니라 값이 같은지 알고싶은 것


- 🎯 **equals 메서드를 만들지 않아도 되는 상황**

  1️⃣ 값을 표현하는 게 아니라 동작하는 개체를 표현하는 클래스의 경우 ex) Thread

  2️⃣ 인스턴스의 ‘논리적 동치성’을 검사할 필요가 없을 때

  3️⃣ 상위 클래스에서 재정의한 equals가 정의되어 있을 때

  4️⃣ 클래스가 private이거나 default이고 equals메서드를 호출할 일이 없을 때


### ✅ Java `equals` 메서드 재정의 예제

예를 들어, 아래와 같이 Menu 클래스가 있을 때
```java
public class Menu {
    private final String name;
    private final int price;

    public Menu(final String name, final int price) {
        this.name = name;
        this.price = price;
    }
}
```
name과 price 값이 똑같은 두 Menu 객체를 비교하면
```java
@Test
@DisplayName("같은 객체를 equals 비교")
void equals() {
//given
Menu friedChicken = new Menu("후라이드치킨", 16_000);
Menu friedChicken2 = new Menu("후라이드치킨", 16_000);
//when & then
assertThat(friedChicken).isEqualTo(friedChicken2);
}
```
⇒ 테스트 실패❗️

⇒ 그 이유는 두 객체의 주소값이 다르기 때문

따라서, 두 객체를 같도록 하려면 Menu 클래스에 equals 메서드를 재정의 해야 함

```java
public class Menu {
private final String name;
private final int price;

    public Menu(final String name, final int price) {
        this.name = name;
        this.price = price;
    }
	
    // equals 재정의
    @Override
    public boolean equals(Object o) {
        if (this == o)
            return true;
        if (!(o instanceof Menu menu)) //Menu 클래스의 객체가 아니면 비교할 필요도 없음
            return false;
        return price == menu.price &&
            Objects.equals(name, menu.name);
    }
}
```