## 예외는 진짜 예외 상황에서만 사용하라
* 예외를 잘 못 사용한 예
```
try {
    int i = 0;
    while(true) range[i++].climb();
} catch (ArrayIndexOutOfBoundsException e) {
}
```
  - 추론) 배열에 접근할 때마다 경계를 검사하는데 이를 루프에서도 하게 되므로 중복 검사를 줄이고자 예외로 처리
  - 반론) 예외를 이용한 루프 종료를 하면 안되는 이유
    - JVM 구현자 입장에서 예외에 대한 최적화를 별로 신경 안 썼을 가능성이 있음
    - try-catch 블록 안에 넣으면 JVM이 적용할 수 있는 최적화가 제한
    - 배열을 순회하는 표준 관용구는 중복 검사를 수행하지 않음. JVM이 알아서 최적해서 없애줌
  - 다른 배열에 잘못 접근해서 ArrayIndexOutOfBoundsException 예외 발생시 잘못된 처리를 하게 됨
* 수정본
```
for(Mountain m : range) {
    m.climb();
}
```
* 예외는 예외상황에서만 써야 한다. 절대로 일상적인 제어 흐름용으로 쓰여선 안된다.
* 상태 의존적 메소드를 제공하는 클래스는 '상태 검사' 메서드도 함께 제공해야 한다.
  - ex> Iterator 인터페이스 next(상태 의존적 메서드), hasNext(상태 검사 메서드)
* 제어 흐름 처리 방법
  1. 상태 검사 메서드
  2. 빈옵셔널
  3. 특정 값 중 하나(ex> null)
* 선택 방법
  1. 외부 동기화 없이 여러 스레드가 동시에 접근할 수 있거나 외부 용인으로 상태가 변할 수 있다면 옵셔널이나 특정 값 사용
  2. 성능이 중요한 상황에서 상태 검사 메서드가 상태 의존적 메서드의 작업 일부를 중복 수행한다면 옵셔널이나 특정 값 사용
  3. 1,2를 제외한 경우에는 상태 검사 메서드 방식이 더 나음 