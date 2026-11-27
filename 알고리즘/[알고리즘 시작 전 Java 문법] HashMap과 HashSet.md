[Velog로 가기](https://velog.io/@kimdongwoo0930/알고리즘-시작-전-Java-문법-HashMap과-HashSet)

released at 2026-11-26 23:08:15 KST

updated at 2026-11-27 00:46:32 KST

![](https://velog.velcdn.com/images/kimdongwoo0930/post/6fc1ca33-d7d9-4f0a-8bfd-f6d47e50a6ea/image.png)
## 한 줄 결론

`HashMap`은 `키-값` 쌍을, `HashSet`은 `중복 없는 값`을 해시로 저장한다. 조회·삽입·삭제가 평균 O(1)이라서, 코테에서 "있는지 확인", "개수 세기", "중복 제거"가 나올때 먼저 생각하면 좋다.

## HashMap과 HashSet의 차이점

| | `HashMap` | `HashSet` |
|---|---|---|
| 저장 형태 | key - value  | 값 |
| 중복 | 키 중복 불가 (같은 키면 값을 덮어씀) | 값 중복 불가 |
| 순서 | 보장 안 됨 | 보장 안 됨 |
| 추가 | `map.put(k, v)` | `set.add(x)` |
| 조회 | `map.get(k)` | `set.contains(x)` |
| 주 용도 | 개수 세기, 값 매핑 | 존재 여부 확인, 중복 제거 |

> `HashSet`은 내부적으로 `HashMap`의 키만 사용해서 만들어져 있다.

## 왜 빠른가

값을 그대로 비교하지 않고, `hashCode()`로 저장 위치를 계산해서 바로 찾아간다. 그래서 데이터가 많아져도 조회 속도가 거의 변하지 않는다. 같은 값인지는 `equals()`로 최종 확인한다.


## HashMap

`키`로 `값`을 찾는 자료구조다. 같은 키로 `put`하면 새로 추가되지 않고 값이 덮어써진다.

```java
Map<String, Integer> map = new HashMap<>();

map.put("a", 1);              // {a=1}
map.put("b", 2);              // {a=1, b=2}
map.put("a", 3);              // {a=3, b=2}  (같은 키면 덮어쓴다)

map.get("a");                 // 3
map.get("z");                 // null  (없는 키)
map.getOrDefault("z", 0);     // 0     (없으면 기본값)
map.containsKey("b");         // true
map.containsValue(2);         // true
map.remove("b");              // {a=3}
map.size();                   // 1
map.isEmpty();                // false
```

#### 순회

```java
for (String key : map.keySet()) { }       // 키만
for (int value : map.values()) { }        // 값만
for (Map.Entry<String, Integer> e : map.entrySet()) {
    e.getKey();                           // 키
    e.getValue();                         // 값
}
```

#### 코테에서 자주 쓰는 패턴

```java
map.put(k, map.getOrDefault(k, 0) + 1);                  // 개수 세기
map.merge(k, 1, Integer::sum);                           // 위와 같은 동작
map.computeIfAbsent(k, x -> new ArrayList<>()).add(v);   // 키별로 리스트 모으기
// 하지만 이것들은 외우기 너무 어렵기때문에 자주쓰면 익혀두자.
```

## HashSet

값의 `존재 여부`만 필요할 때 쓴다. `add`는 새로 들어가면 `true`, 이미 있으면 `false`를 반환한다.

```java
Set<Integer> set = new HashSet<>();

set.add(1);          // true   [1]
set.add(1);          // false  [1]  (이미 있음)
set.add(2);          // true   [1, 2]
set.contains(2);     // true
set.remove(1);       // true   [2]
set.size();          // 1
set.isEmpty();       // false
```

List를 `new HashSet<>(list)`에 넣으면 중복이 제거된 Set이 된다.
Set을 `new ArrayList<>(set)`에 넣으면 중복이 없는 list가 된다.

## 코테에서 자주 걸리는 함정

- **기본형 사용 불가**: `HashMap<int, int>`는 컴파일 에러다. `Integer`, `Long` 같은 래퍼 클래스를 쓴다.

- **`get`의 `null`**: 없는 키를 `int`에 바로 받으면 `NullPointerException`이 난다. `getOrDefault`를 쓴다.

- **`Integer`를 `==`로 비교**: `Integer`는 기본형이 아니기 때문에 값 비교는 `equals`를 쓴다.

- **순회 중 수정**: `for-each` 도중에 원소를 `remove`하면 `ConcurrentModificationException`이 난다. 순회 중 제거를 하지않고 제거할 요소를 모아두고 한번에 제거하는 식으로 해야한다.

- **순서 보장 안 됨**: 입력 순서가 필요하면 `LinkedHashMap`, 정렬이 필요하면 `TreeMap`을 쓰거나 꺼낸 뒤 정렬한다.

- **배열을 키로 사용**: `Set<int[]>`는 내용이 같아도 다른 값으로 취급된다. 배열은 내용이 아니라 객체 자체로 해시를 계산하기 때문이다. `List<Integer>`나 문자열(`x + "," + y`)로 바꿔서 넣는다.

## 마무리

`Set`은 중복 체크, `Map`은 개수 세기 같은 문제에서 많이 쓰이기 때문에 꼭 알고 가야 하는 자료구조다.
앞으로 자주 쓰는 메서드를 몇 가지 더 알아보고, 프로그래머스 Level 1 문제를 풀어보면서 익숙해져 보자.