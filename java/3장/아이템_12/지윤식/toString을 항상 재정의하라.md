# 아이템 12 : toString을 항상 재정의하라

Object의 기본 toString() 메서드는 클래스 이름과 해시 코드를 반환하므로, 대부분의 경우 의미 없는 정보를 제공한다.
toString()을 재정의하면 객체를 사람이 읽기 쉬운 형태로 표현할 수 있어 디버깅 및 로깅이 용이해진다.

### ✅ 좋은 toString :

객체의 주요정보를 포함해야 한다.
너무 거대하거나 복잡한 객체라면 요약 정보만 제공할 수도 있다.
포맷을 문서화할지 여부를 신중히 결정해야 한다.
- 문서화하면 일관성 유지에 도움이 되지만, 이후 포맷 변경이 어려워질 수 있음
- 문서화하지 않으면 유연성은 있지만, 명확한 형식이 없을 수 있음

#### toString()이 반환하는 정보는 별도의 접근자 메서드로도 제공해야 한다.

#### 그렇지 않으면 toString()의 결과를 직접 파싱해야 하는 문제가 발생

IDE기능을 활용할 수 있지만, 의미를 반영하는 것이 중요하다.

### 👉🏻 포맷을 정한 경우
    /**
     * 이 전화번호의 문자열 표현을 반환한다.
     * 이 문자열은 "XXX-YYY-ZZZZ" 형태의 12글자로 구성된다.
     * XXX는 지역 코드, YYY는 프리픽스, ZZZZ는 가입자 번호다.
     * 각 부분이 너무 작아 자릿수를 채울 수 없다면 앞에 0을 붙인다.
     * 예: 가입자 번호가 123이면 마지막 네 문자는 "0123"이 된다.
     */
     @Override
    public String toString() {
        return String.format("%03d-%03d-%04d", areaCode, prefix, lineNum);
    }

### 👉🏻 포맷을 정하지 않은 경우
     /**
     * 이 객체의 대략적인 설명을 반환한다.
     * 반환되는 문자열 형식은 정해지지 않았으며, 향후 변경될 수 있다.
     */
    @Override
    public String toString() {
        return String.format("[PhoneNumber: 지역 코드=%d, 프리픽스=%d, 번호=%d]", areaCode, prefix, lineNum);
    }

### ❌ 잘못된 사용 방식: toString()을 파싱해서 필드 값 추출

        PhoneNumber phone = new PhoneNumber(31, 867, 5309);
        String phoneString = phone.toString(); // "031-867-5309"

        String[] parts = phoneString.split("-"); // ["031", "867", "5309"]
        int areaCode = Integer.parseInt(parts[0]);  // 31 (정수 변환)
        int prefix = Integer.parseInt(parts[1]);    // 867
        int lineNum = Integer.parseInt(parts[2]);   // 5309

        System.out.println("지역 코드: " + areaCode);
        System.out.println("프리픽스: " + prefix);
        System.out.println("가입자 번호: " + lineNum);

* ##### toString()은 사람이 읽기 쉽게 만든 것인데, 코드가 toString()의 포맷에 의존하게 됨.

* ##### 만약 toString() 포맷이 "031 (867) 5309"로 변경되면? → 기존 파싱 코드가 깨짐!

* ##### toString()의 출력이 변할 경우, 이를 사용하는 모든 코드가 수정되어야 하는 문제 발생.


✅ 접근자 메서드 사용 (toString() 파싱 X)

        PhoneNumber phone = new PhoneNumber(567, 867, 5309);

        int areaCode = phone.getAreaCode();
        int prefix = phone.getPrefix();
        int lineNum = phone.getLineNum();

        System.out.println("지역 코드: " + areaCode);
        System.out.println("프리픽스: " + prefix);
        System.out.println("가입자 번호: " + lineNum);

* ##### toString()의 포맷이 바뀌더라도 코드가 깨지지 않음.

* ##### toString()은 사람이 읽기 쉽게, 접근자 메서드는 데이터를 안전하게 가져오도록 사용 가능.
* ##### 코드가 유지보수하기 쉬워짐.