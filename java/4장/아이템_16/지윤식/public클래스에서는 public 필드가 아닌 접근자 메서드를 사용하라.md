# 아이템 16 : public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라


### 📌 핵심 메시지 :

### Public 클래스에서는 필드를 직접 노출하지 말고, 반드시 접근자(getter)/설정자(setter)를 통해 캡슐화하라.


#### 😬 잘못된 예 – 필드를 직접 노출하는 클래스

        public class Point {
        public double x;
        public double y;
        }

#### ❌ 캡슐화 실패

#### ❌ 내부 표현 변경 불가

#### ❌ 부수 작업(insert log, validate 등) 불가

#### ❌ 불변식(invariant) 유지 어려움


### ✅ 올바른 예 – 접근자 메서드 사용

    public class Point {
    private double x;
    private double y;

    public Point(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double getX() { return x; }
    public double getY() { return y; }
    public void setX(double x) { this.x = x; }
    public void setY(double y) { this.y = y; } 
    }

#### ✅ 캡슐화 가능

#### ✅ 불변식 보장

#### ✅ 내부 구현 변경 자유

#### ✅ 추후 기능 추가 용이

