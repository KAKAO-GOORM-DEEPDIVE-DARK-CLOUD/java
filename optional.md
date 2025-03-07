# Optional<T>

<img width="765" alt="image" src="https://github.com/user-attachments/assets/c080a7f3-d252-4c0c-a4a4-8f344e3b28bc" />

## 1. 기존 방식의 문제점  
- 전통적인 방식에서 메서드가 특정 조건에서 값을 반환할 수 없을 때, **예외를 던지거나 `null`을 반환**하는 방식이 일반적이었다.  
- 그러나 `null`을 반환하면 호출하는 측에서 **별도의 `null` 체크 코드가 필요**하며, 이를 누락하면 `NullPointerException`이 발생할 위험이 있다.  

## 2. `Optional<T>`의 도입  
<img width="768" alt="image" src="https://github.com/user-attachments/assets/3fe26366-20cb-46d0-8f64-70286f5b1f7d" />

- Java 8 이후, **`Optional<T>`가 도입**되어 `null`을 직접 반환하는 대신, **값이 있을 수도 없을 수도 있는 경우를 안전하게 표현**할 수 있게 되었다.  
- `Optional<T>`를 반환하면 `null`을 반환하는 방식보다 **명확하고 안전한 코드 작성이 가능**하며, 예외를 던지는 방식보다 **예측 가능하고 사용하기 쉬운 API 설계가 가능**하다.  

---
## 예제 코드
User.java
```java
public class User {
    private String name;
    private Integer age;

    public User(String name, Integer age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public Integer getAge() {
        return age;
    }
}
```
UserDB.java
```java
import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

public class UserDB
{
    private static final List<User> users = new ArrayList<>();

    static
    {
        users.add(new User("이승욱", 27));
        users.add(new User("홍길동", 30));
        users.add(new User("침착맨", 22));
    }

    // User 객체를 반환하는 메서드 (null이 반환될 수 있음)
    public static User getUser(String name)
    {
        for (User user : users)
        {
            if (user.getName().equals(name)) return user;
        }
        return null; // 없을 경우 null 반환
    }

    // Optional<User>을 반환하는 메서드
    public static Optional<User> findUser(String name)
    {
        for (User user : users)
        {
            if (user.getName().equals(name)) return Optional.of(user); // Optional로 감싸서 반환
            // Optional.of(user) 에서 user가 null이면 NPE 발생
        }
        return Optional.empty(); // 사용자가 없으면 빈 Optional 반환
    }

}

```
Exercise.java
```java
import java.util.Optional;

public class Exercise
{
    public static void main(String[] args)
    {
        // getUser() 사용 (null 가능)
        User user1 = UserDB.getUser("이승욱");

        User user2 = UserDB.getUser("ㅁㄴㅇㄹㅁㄴㅇㄹ");
        System.out.println(user2.getAge()); // NPE 발생

        User user3 = UserDB.getUser("ㅁㄴㅇㄹㅁㄴㅇㄹ");
        if (user3 != null) System.out.println(user3.getName() + " - " + user3.getAge());
        else System.out.println("User not found");

        // 리턴 타입 자체가 Optional 이라서 null 값이 반환될 수 있음을 알기 쉽다.
        UserDB.findUser("ㅁㄴㅇㄹㅁㄴㅇㄹ");

        Optional.ofNullable(UserDB.getUser("ㅁㄴㅇㄹㅁㄴㅇㄹ")); // 빈 optional 반환

        // findUser() 사용 (Optional)
        Optional<User> findUser = UserDB.findUser("홍길동");
        if (findUser.isPresent()) // 빈 optional 이 아닐 때
        {
            User user4 = findUser.get();
            System.out.println(user4.getName() + " - " + user4.getAge());
        }
        if(findUser.isEmpty()) // 빈 optional 일 때
        {
            System.out.println("User not found");
        }

        // 옵셔널을 이용한 기본값 설정
        // findUser()를 이용한 기본값 설정(예외 X) -> 예외 생성 비용이 들지 않음
        User defaultUser = UserDB.findUser("ㅁㄴㅇㄹㅁㄴㅇ").orElse(new User("알 수 없음", 0));
        System.out.println(defaultUser.getName() + " - " + defaultUser.getAge());

        // 값이 없으면 예외 발생
        UserDB.findUser("ㅁㄴㅇㄹㅁㄴㅇ").orElseThrow(() -> new RuntimeException("사용자를 찾을 수 없습니다."));

        Optional<User> result = UserDB.findUser("ㅁㄴㅇㄹㅁㄴㅇㄹ"); // 빈 optional 반환
        System.out.println(result.get().getAge());// 빈 optional 을 벗기면 NULL 이고 null에 접근하는 순간 NoSuchELementException

    }
}
```

## 옵셔널 반환을 고려할 때 주의할 점  

### 1. Optional을 활용한 값 반환  
✅ null을 반환하는 대신 `Optional.empty()`를 반환하는 것이 낫다.
```java
return null; // X

return Optional.empty(); // O
```
<br>

✅ `Optional.of(value)`를 사용할 때 `null`을 넣으면 `NullPointerException`이 발생하므로 주의해야 한다.
```java
public static String getName() {
    return null; // null 반환
}

public static void main(String[] args) {
    Optional<String> name = Optional.of(getName()); // NullPointerException 발생
}

```
<br>

✅ `Optional.ofNullable(value)`를 사용하면 `null`을 `Optional.empty()`로 안전하게 변환할 수 있다.
- Optional.ofNullable(value)는 null 값도 허용하는 Optional이다.
- 즉, value가 null이면 Optional.empty()를 반환하고, value가 null이 아니면 Optional.of(value)를 반환한다.
```java
Optional.ofNullable(UserDB.getUser("ㅁㄴㅇㄹㅁㄴㅇㄹ")); // 빈 optional 반환

Optional.ofNullable(UserDB.getUser("이승욱")); // 값이 있는 optional 반환
```
<br>

### 2. 옵셔널을 활용한 기본값 설정  
✅ `.orElse("기본값")`을 사용하여 기본값을 제공할 수 있다. 
<img width="757" alt="image" src="https://github.com/user-attachments/assets/de56506b-85c7-4942-9a03-9484e3915385" />

```java
// 옵셔널을 이용한 기본값 설정
// findUser()를 이용한 기본값 설정(예외 X) -> 예외 생성 비용이 들지 않음
User defaultUser = UserDB.findUser("ㅁㄴㅇㄹㅁㄴㅇ").orElse(new User("알 수 없음", 0));
```
<br>

✅ `.orElseThrow()`를 활용하면 원하는 예외를 던질 수 있다.  
```java
// 값이 없으면 예외 발생
UserDB.findUser("ㅁㄴㅇㄹㅁㄴㅇ").orElseThrow(() -> new RuntimeException("사용자를 찾을 수 없습니다."));
```
<br>

✅ `.get()`을 사용하면 값이 없을 경우 `NoSuchElementException`이 발생하므로, 반드시 값이 존재한다고 확신하는 경우에만 사용해야 한다.
<img width="778" alt="image" src="https://github.com/user-attachments/assets/1fa5aa24-4c8c-4a04-bae7-080e2bcb744e" />

```java
Optional<User> result = UserDB.findUser("ㅁㄴㅇㄹㅁㄴㅇㄹ"); // 빈 optional 반환
System.out.println(result.get().getAge());// 빈 optional 을 벗기면 NULL 이고 null에 접근하는 순간 NoSuchELementException

//그래서 아래와 같이 사용할 수도 있다.
// findUser() 사용 (Optional)
Optional<User> findUser = UserDB.findUser("홍길동");
if (findUser.isPresent()) // 빈 optional 이 아닐 때
{
    User user4 = findUser.get();
    System.out.println(user4.getName() + " - " + user4.getAge());
}
```
<br>

### 3. 옵셔널을 활용한 안전한 연산  
✅ `.isPresent()`를 사용하여 값이 존재하는지 확인할 수 있지만, **가능하면 `.orElseThrow(), .orElse(), orElseGet()` 같은 메서드로 대체하는 것이 좋다.** 
- 이유는 Optional의 장점을 제대로 활용하지 못하고, 전통적인 null 체크 코드처럼 사용하게 되기 때문임.
<br>

참고 orElseGet 사용 예제
```java
User user1 = UserDB.findUser("이승욱")
                           .orElseGet(() -> new User("Guest", 0)); // ✅ 기본값 동적 생성
System.out.println(user1.getName() + " / " + user1.getAge());

User user2 = UserDB.findUser("없는유저") // 없는 유저 조회
                   .orElseGet(() -> new User("Guest", 0)); // ✅ 기본값 동적 생성
System.out.println(user2.getName() + " / " + user2.getAge());
```
  

### 4. 옵셔널을 잘못 사용하는 사례  
❌ 성능이 중요한 상황에서는 `Optional<T>`를 남용하면 **불필요한 객체 할당으로 인해 성능 저하가 발생할 수 있다.**  
-> optional 도 엄연히 새로 할당하고 초기화해야 하는 객체이고 , 그 안에서 값을 꺼내려면 메서드를 호출해야 하니 한 단계를 더 거치는 셈
<br>

✅ 박싱된 기본 타입을 감싼 `Optional<Integer>`보다는 `OptionalInt` 같은 기본형 전용 클래스를 사용하는 것이 더 효율적이다. 
- int, Long,double 전용 옵셔널 클래스들이 이미 존재한다.
- '덜 중요한 기본 타입' 용인 Boolean, Byte, Character, Short, Float은 없다고 한다.
```java
import java.util.OptionalInt;

public class Main {
    public static void main(String[] args) {
        OptionalInt presentValue = OptionalInt.of(42); // ✅ 값이 존재하는 OptionalInt
        OptionalInt emptyValue = OptionalInt.empty();  // ✅ 값이 없는 OptionalInt

        // 값이 있을 때 가져오기
        System.out.println(presentValue.getAsInt()); // 42

        // 값이 없을 때 기본값 설정
        System.out.println(emptyValue.orElse(100)); // 100

        // 값이 없을 때 동적으로 값 생성
        System.out.println(emptyValue.orElseGet(() -> (int) (Math.random() * 100))); // 동적 기본값 생성
    }
}

```

---

## 핵심 정리  
값을 반환하지 못할 가능성이 있고 , 호출할 때마다 반환값이 없을 가능성을 염두에 둬야하는 메서드라면 옵셔널을 반환해야 할 상황일 수 있다. 하지만 옵셔널 반환에는 성능 저하가 뒤따르니 , 성능에 민감한 메서드라면 null 을 반환하거나 예외를 던지는 편이 나을수 있다. 그리고 옵셔널을 반환값 이외의 용도로 쓰는 경우는 매우 드물다.
