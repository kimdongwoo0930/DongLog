[Velog로 가기](https://velog.io/@kimdongwoo0930/알고리즘-시작-전-Java-문법-배열-VS-List)

released at 2026-11-20 14:40:29 KST

updated at 2026-11-28 04:24:41 KST

|[ArrayList](https://velog.io/tags/ArrayList)|[Java](https://velog.io/tags/Java)|[List](https://velog.io/tags/List)|
|----|----|----|

요즘 코드를 직접 짜는 일이 많이 줄었다. AI가 대신 짜주는 게 너무 당연해졌기 때문이다.

그런데 최근에 간단한 코드를 직접 짜다가 자꾸 막혔다. 편해진 건 좋은데, 그만큼 기본기가 녹슬고 있었다는 걸 그때 알았다.

그래서 코딩테스트를 준비하기 전에, 많이 쓰는 문법부터 다시 공부해보려고 한다.
주 언어는 Spring Boot를 다루고 있기 때문에 Java로 정했다.

> 첫 번째 주제는 가장 자주 쓰게 될 `배열`과 `List`다.

## 배열과 List의 차이점

코딩테스트에서는 입력과 반환값이 `int[]`로 주어지는 경우가 많고,
풀이 중간에는 `List`를 쓰는 경우가 많다.
그래서 사용법을 보기 전에 `int[]`와 `List<Integer>`가 어떻게 다른지부터 알아보자.

| | 배열 (`int[]`) | `List<Integer>` |
|---|---|---|
| 크기 | 만들 때 고정 | 필요에 따라 늘어남 |
| 길이 확인 | `arr.length` | `list.size()` |
| 원소 접근 | `arr[index]` | `list.get(index)` |
| 값 변경 | `arr[index] = 5` | `list.set(index, 5)` |
| 값 추가 | 불가 | `list.add(5)` |
| 값 삭제 | 불가 | `list.remove(index)` |
| 저장 형식 | `int` | `Integer` |

## 배열 

`배열`은 **같은 타입의 값을 정해진 개수만큼 한 번에 만들어두고, 인덱스로 접근하는** 자료구조다.
처음에 크기를 정하면 이후에 늘리거나 줄일 수 없고, 이미 만들어진 칸의 값만 바꿀 수 있다.
![](https://velog.velcdn.com/images/kimdongwoo0930/post/da527c02-9480-447b-99a9-47a6766d255f/image.png)


```java
int[] arr = {3, 1, 2};
arr[0]; // 3        

arr.length;  // 3  ( 배열의 크기 )
arr.clone(); // 배열 복사본을 생성
```


### Arrays 클래스
```java
import java.util.Arrays;

Arrays.toString(arr);          // "[3, 1, 2]"  
Arrays.sort(arr);              // [1, 2, 3]  (오름차순 정렬)
Arrays.fill(arr, 0);           // [0, 0, 0]  (전부 같은 값으로 채우기)
Arrays.copyOf(arr, 5);         // 길이 5로 복사 (모자란 칸은 0)
Arrays.copyOfRange(arr, 1, 4); // 인덱스 1~3만 잘라서 복사
Arrays.equals(arr1, arr2);     // 내용 비교
Arrays.deepToString(arr2d);    // 2차원 배열 출력용
```

> `System.out.println(arr)`는 [I@6d06d69c 같은 주소가 찍힌다. 
그래서 Arrays.toString(arr)를 써야 하는데, List는 그냥 출력해도 [3, 1, 2]로 나온다.



## List 

`List`는 **값을 넣으면 크기가 늘어나고, 빼면 크기가 줄어드는** 자료구조다.
배열처럼 인덱스로 접근할 수 있지만, 크기를 미리 정해둘 필요가 없다.
![](https://velog.velcdn.com/images/kimdongwoo0930/post/3dd0c2d4-8e1d-4b8d-9542-627019dec59c/image.png)


#### List 생성
```java
import java.util.ArrayList;
import java.util.List;

List<Integer> list = new ArrayList<>();
```

#### 추가 / 조회 / 수정 / 삭제

```java
list.add(10);        // [10]
list.add(20);        // [10, 20]
list.add(1, 15);     // [10, 15, 20]  (인덱스 1에 삽입)

list.get(0);         // 10
list.set(1, 25);     // [10, 25, 20] (추가X 변경O)
list.remove(0);      // [25, 20]  (인덱스 0 삭제)
```

#### 크기 / 검색 / 상태 확인

```java
list.size();         // 2 (크기)
list.contains(20);   // true (포함 여부)
list.indexOf(20);    // 1  (요소가 없다면 -1)
list.lastIndexOf(20);
list.isEmpty();      // false
list.clear();        // []
```

- `indexOf(값)`: 값이 처음 나오는 인덱스를 반환한다. 중복된 값이 있으면 가장 앞의 위치를 알려준다.
- `lastIndexOf(값)`: 값이 마지막으로 나오는 인덱스를 반환한다. 중복된 값이 있으면 가장 뒤의 위치를 알려준다.
- 두 메서드 모두 값이 없으면 `-1`을 반환한다.

## 마무리

코테를 준비한다고 하면서 기본 문법부터 공부하기 위해 작성했지만, 다시 한번 정리해보니 내가 멍청해진 게 맞는거 같다. 
앞으로도 코테 준비를 하면서 많이 사용하는 문법들을 정리할 생각이다.
