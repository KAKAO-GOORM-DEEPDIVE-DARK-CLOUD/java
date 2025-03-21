# 아이템 58. 전통적인 for 문보다는 for-each 문을 사용하라.

    for문 : 쓰이는 요소들이 많아지면 오류가 생길 가능성이 높아진다. 인덱스가 없는 컬렉션에서는 사용 불가능

    for-each문 : 반복자와 인덱스 변수를 사용하지 않아 코드가 깔끔해지고 오류가 날일도 적다.

**하지만, 특정 인덱스 값을 변경하거나 연산이 필요하다면 for-each문은 적철하지 않다.
단순조회에는 강력!!**

    public class Iter1 {
    public static void main(String[] args) {
    badForLoop();
    goodForEachLoop();

    }

    public static void badForLoop() {
    //        List<Integer> list = new ArrayList<>();
    List<Integer> list = new LinkedList<>();

        for (int i = 0; i < 100_000; i++) {
            list.add(i);
        }

        long startTime = System.nanoTime();

        long sum = 0;
        for (int i = 0; i < list.size(); i++) {
            sum += list.get(i);
        }

        long endTime = System.nanoTime();
        PrintFormattedTime.printTime("Bad For Loop Execution Time: " , endTime - startTime);
        System.out.println("Final Sum: " + sum);
    }

    public static void goodForEachLoop() {
    //        List<Integer> list = new ArrayList<>();
    List<Integer> list = new LinkedList<>();
    for (int i = 0; i < 100_000; i++) {
    list.add(i);
    }

        long startTime = System.nanoTime();

        long sum = 0;
        for (int num : list) {
            sum += num;
        }

        long endTime = System.nanoTime();
        PrintFormattedTime.printTime("Good For-Each Loop Execution Time: " , endTime - startTime);
        System.out.println("Final Sum: " + sum);
    }

    }

#### 각각 for문과 for-each문을 사용한 코드다. for-each문의 코드가 더 깔끔한 것을 알 수 있다.

### 성능적인 면에서는 ArrayList를 사용하면 성능상 큰차이는 없지만, LinkedList로 비교했을때는 현저하게 차이난다.
### **_정리하자면, for문과 for-each문을 둘다 쓸 수 있다면 for-each문을 사용하자._**

    public class PrintFormattedTime {
    public static void printTime(String label, long time) {
        DecimalFormat formatter = new DecimalFormat("#,###");
        System.out.println(label + ": " + formatter.format(time) + " ns");
    }
    }
위 코드 클래스를 사용해 성능 차이를 콘솔에 찍어보았다.



