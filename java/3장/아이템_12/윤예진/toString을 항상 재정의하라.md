# 12. toString을 항상 재정의하라

### ✅ 먼저 toString 메서드에 대해 간단히 알아보자면
- java의 Object클래스에 정의된 메서드
- 객체의 정보를 문자열로 반환할 때 사용
- 모든 java 객체는 Object를 상속받기 때문에 toSting 메서드를 가지고 있음

하지만 이 메서드는 PhoneNumber@abdbd처럼 단순히 **클래스_이름@16진수로 표시한 해시코드** 를 반환함

### ✅  **toString**의 일반 규약에 따르면

=> <u>'간결하면서 사람이 읽기 쉬운 형태의 유익한 정보'</u>를 반환해야 함

=> PhoneNumber@abdbd 보다는 010-1111-1111처럼 적합한 값이 직접 나오는 것이 유익함


### ✅  **toString**메서드는 아래 상황에서 자동으로 호출됨
- println, printf
- 문자열 연결 연산자(+)
- assert 구문에 넘길 때
- 디버거가 객체를 출력할 때

### ✅  **toString**에 아무 짓도 안 했을 때
```
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

### ✅  **toString**이 반환한 값에 포함된 정보를 얻어올 수 있는 API를 제공하자
- getter와 같은 메서드를 제공하라는 의미
- 어차피 toString으로 공개된 데이터라면, toString을 구성하는 각각의 데이터 따로따로 받을 수 있는 메서드를 제공하자는 것

### ✅ 예제
❗️오버라이딩 하지 않았을 때
```
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}

public static void main(String[] args) {
    Book book = new Book("IT", "SQLPlus", 50000, 5);
    
    System.out.println("toString: "+ book.toString());
    System.out.println("일반: "+ book);
}

```
```
toString: bookstore.Book@26f0a63f
일반: bookstore.Book@26f0a63f
```

❗️오버라이딩 했을 때
```
public class Book {
    @Override
    public String toString() {
        return this.category  + " " + this.bookName + " "
                + this.bookPrice + " " + this.bookDiscountRate ;
    }

}

public static void main(String[] args) {
    Book book = new Book("IT", "SQLPlus", 50000, 5);
    
    System.out.println("toString: "+ book.toString());
    System.out.println("일반: "+ book);
}

```
```
toString: IT SQLPlus 50000.0 5.0
일반: IT SQLPlus 50000.0 5.0
```