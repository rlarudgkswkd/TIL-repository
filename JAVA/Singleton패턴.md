## Singleton 패턴
- 객체의 인스턴스가 오직 1개만 생성되는 패턴을 의미한다. 
- 예시 코드
```
public class Singleton {

    private static Singleton instance = new Singleton();
    
    private Singleton() {
        // 생성자는 외부에서 호출못하게 private 으로 지정해야 한다.
    }

    public static Singleton getInstance() {
        return instance;
    }

    public void say() {
        System.out.println("hi, there");
    }
}
```
- 사용 이유 : 오직 1개만 생성하기에 메모리 이점, 데이터 공유가 쉽다는 장점
- 링크 : https://tecoble.techcourse.co.kr/post/2020-11-07-singleton/
