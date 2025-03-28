# 16. public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라

### ✅ 잘못된 예시: 인스턴스 필드만을 모아놓은 클래스
```
public class Point {
    public double x;
    public double y;
}
```
=> 잘못된 이유: **캡슐화의 이점을 제공하지 못함**
- API를 수정하지 않고는 내부 표현을 바꿀 수 없음
- 불변식을 보장할 수 없음
- 외부에서 필드에 접근할 때 부수적인 로직을 추가할 수 없음


### ✅ 객체지향적인 클래스 작성
```
class Point {
  private double x;
  private double y;

  public Point (double x, double y) {
    this.x = x;
    this.y = y;
  }

  public double getX() { return x; }
  public double getY() { return y; }

  public void setX(double x) { this.x = x; }
  public void setY(double y) { this.y = y; }
}
```
=> 클라이언트는 public 메서드를 통해서만 데이터 접근이 가능

getter/setter를 제공함으로써 클래스의 내부 표현 방식을 언제든 바꿀 수 있는 **유연성을 제공**하게 됨


### ✅ default 클래스 혹은 private 중첩 클래스라면 데이터 필드를 노출해도 상관X
- default: 같은 패키지 및 같은 클래스에서만 접근 가능
- private 중첩 클래스: 내부 클래스가 private 형태로 된 클래스

=> 같은 패키지 안에서 어떤 특정 이유 때문에 사용하던가 탑 레벨 클래스에서만 접근하기 때문에
위에서 언급한 단점들이 나타날 이유가 없어 문제될 것이 없음

### ✅ 만약 public클래스의 필드가 불변이라면?
```
public final class Time {
    private static final int HOURS_PER_DAY    = 24;
    private static final int MINUTES_PER_HOUR = 60;

    public final int hour;
    public final int minute;

    public Time(int hour, int minute) {
        if (hour < 0 || hour >= HOURS_PER_DAY)
            throw new IllegalArgumentException("Hour: " + hour);
        if (minute < 0 || minute >= MINUTES_PER_HOUR)
            throw new IllegalArgumentException("Min: " + minute);
        this.hour = hour;
        this.minute = minute;
    }
    ... //생략
}
```
=> public클래스의 필드가 불변이라면 직접 노출할 때의 단점이 줄어들긴하지만, 좋은 생각은 아님
- API를 변경하지 않고는 표현 방식을 바꿀 수 없음
- 필드를 읽을 때 부수 작업을 수행할 수 없음
- 단, 불변식은 보장할 수 있음