[Velog로 가기](https://velog.io/@kimdongwoo0930/알고리즘-시작-전-Java-문법-정수형-int-형변환과-자주-쓰는-메서드)

released at 2026-11-04 18:31:49 KST

updated at 2026-11-14 04:28:58 KST

코테뿐만 아니라 개발을 하다 보면 정수형 변수와 형변환을 많이 사용하게 된다.
그래서 이번에는 여러 타입을 공부할 겸 정리해보려고 한다.

---

## int란
`int`는 정수를 저장하는 **기본형** 자료형이다. 코딩테스트에서 숫자를 다룰 때 가장 많이사용된다.

```java
int a = 10;
int b = -5;
int c;           // 선언만 했다. 값을 넣기 전에는 읽을 수 없다
```

저장 가능한 범위는 약 -21억 ~ 21억이다.

```java
Integer.MAX_VALUE;   // 2147483647
Integer.MIN_VALUE;   // -2147483648
```

이 이하또는 이상일때는 `long`을 사용해야한다.

---

## 정수 연산


```java
5 / 2;     // 2    (정수끼리 나누면 소수점이 버려진다)
5 % 2;     // 1    (나머지)
-7 % 2;    // -1   (나머지의 부호는 왼쪽 값을 따른다)
```
### 자릿수 다루기

> 자리수를 나누어 사용하는 방식은 여러 문제에서 많이 사용된다. 알아두기!

```java
int num = 1234;
num % 10;      // 4    (일의 자리)
num / 10;      // 123  (일의 자리 제거)
```
---

## 형변환

### int ↔ String

```java
int n = Integer.parseInt("123");        // 문자열 → int
String s1 = String.valueOf(123);        // int → 문자열
String s2 = Integer.toString(123);      // 위와 같은 결과
String s3 = 123 + "";                   // 이것도 가능
```

### int ↔ char

```java
char ch = '7';
int n = ch - '0';           // 7   (문자 숫자 → int)
char back = (char) (n + '0');   // '7' (int → 문자 숫자)
```

---

## Math 메서드

```java
Math.max(3, 5);      // 5  더 높은값
Math.min(3, 5);      // 3  더 낮은값
Math.abs(-7);        // 7  절댓값
Math.pow(2, 3);      // 8.0   거듭제곱
Math.sqrt(16);       // 4.0   루트값
```


## Integer.MAX_VALUE / MIN_VALUE 활용

```java
int min = Integer.MAX_VALUE;   //  정수 최대값
int max = Integer.MIN_VALUE;   // 정수 최솟값
```
> 최댓값 또는 최솟값을 구할때 변수 초기값으로 많이 사용한다.

---


## 마무리


언어를 처음 공부할 때는 변수형이 가장 쉬운 부분이라고 생각했는데, 다시 정리해보니 형변환이나 범위처럼 놓치기 쉬운 부분이 생각보다 많았다.
앞으로도 계속 사용하게 될 기본이라, 이번 기회에 다시 한번 정리해두었다.