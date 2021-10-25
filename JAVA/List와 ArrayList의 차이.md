
# List와 ArrayList의 차이
<br>

## 1. List
* 인터페이스 , 포괄적인 기능을 수행할수 있음. 

### 예시코드
```
  List<String> list = new ArrayList<>();
  System.out.println(list.getClass());
  list = new LinkedList<>();
  System.out.println(list.getClass());
```

### 결과
```
class java.util.ArrayList
class java.util.LinkedList
```

* List의 특성은 최초로 ArrayList로 생성하였지만 추후에 다시 LinkedList로 선언 하였을때에는 타입이 변경되어 생성된다.
* Generic(제네릭)에 대한 개념

<br>
<br>

## 2. ArrayList
* 클래스

### 예시코드
```
  ArrayList<String> arrList = new ArrayList<>();
  System.out.println(arrList.getClass());
  arrList = new LinkedList<>();
```

### 결과
```
컴파일 에러
Cannot infer type arguments for LinkedList<>
```


### 참조링크

https://ko.gadget-info.com/difference-between-list

https://yoon-dailylife.tistory.com/7
