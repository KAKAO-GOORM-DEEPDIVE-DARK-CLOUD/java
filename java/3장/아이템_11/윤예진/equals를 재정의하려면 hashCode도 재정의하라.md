# 11. equals를 재정의하려면 hashCode도 재정의하라

✅ Object 클래스 명세에서 hashCode 에 관한 일반 규약

- equals 비교에 사용되는 정보가 변경되지 않았다면, 애플리케이션이 실행되는 동안 객체의 hashCode 메서드는 여러 번 호출해도 **항상 같은 값을 반환함**

- equals 메소드가 같다고 판정한 두 객체는 같은 hash 값을 반환해야 함

- equals(Object)가 두 객체를 다르다고 판단했더라도, 두 객채의 hashCode 값은 같을 수 있다. (해시 충돌)

✅ equals를 재정의한 클래스 모두에서 hashCodede도 재정의 해야 함



### ✅ Java `hashCode` 재정의 예제

아래 코드를 실행했을 때 count가 10일 것을 기대하지만 결과는 그렇지 않다.
```java
@Test
@DisplayName("같은 값 객체는 해시값이 같은지 체크")
void hashcode_menu() {
  //given
  Map<Menu, Integer> menus = new HashMap<>();
  menus.put(new Menu("치킨", 16_000), 10);
  menus.put(new Menu("감자튀김", 8_000), 2);
  menus.put(new Menu("콜라", 2_000), 7);
  //when
  Menu menu = new Menu("치킨", 16_000);
  int count = menus.get(menu);
  //then
  assertThat(count).isEqualTo(10);
}
```
테스트 코드를 실행하면 `NullPointerException`이 발생

⇒ 그 이유는 `menu` 객체에 대한 **해시값**을 `menus`에서 찾을 수 없기 때문

⇒  **HashMap의 key값으로 Menu 클래스를 사용하기 위해서는 Menu 클래스에 hashCode() 메서드를 재정의 해줘야 함.** 그래야 같은 값을 가진 객체가 항상 같은 해시값을 갖게됨
```java
public class Menu {
  private final String name;
  private final int price;

  public Menu(final String name, final int price) {
    this.name = name;
    this.price = price;
  }

  @Override
  public boolean equals(Object o) {
    if (this == o)
      return true;
    if (!(o instanceof Menu menu))
      return false;
    return price == menu.price &&
            Objects.equals(name, menu.name);
  }

  // hashcode 재정의
  @Override
  public int hashCode() {
    return Objects.hash(name, price);
  }
}
```
⇒ hashcode를 재정의 해주었으므로 같은 값을 가지는 객체는 같은 해시값을 갖는다. 따라서 테스트 코드는 통과


✅ equals()와 hashcode()를 같이 재정의해야 하는 이유

1. equals()만 재정의하면?

- 같은 값의 객체라도 해시값이 다를 수 있음
- HashMap, HashSet 같은 컬렉션에서 제대로 동작하지 않음 (객체를 찾지 못할 수도 있음)


2. hashcode()만 재정의하면?

- 같은 해시값을 가질 수 있지만, equals()를 재정의하지 않으면 값 비교가 안 됨
- HashMap.get() 같은 메서드가 null을 반환할 수 있음
