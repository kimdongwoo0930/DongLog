[Velog로 가기](https://velog.io/@kimdongwoo0930/알고리즘-시작-전-Java-문법-문자-char와-문자열-String-형변환과-자주-쓰는-메서드)

released at 2026-11-17 12:56:56 KST

updated at 2026-11-18 06:30:34 KST

코딩테스트에서 문자를 다루는 일이 엄청 많이 등장한다. 그렇기떄문에 Int 다음으로 Char와 String을 정리하고 넘어가자.

## Character

`char`는 문자 **하나**를 저장하는 기본형 자료형이다. 

```java
char c = 'A';
```

내부적으로 **유니코드**로 저장되어있어서, 숫자처럼 계산이 가능해요.

```java 
(int) 'A'; // 65 유니코드로 계산이 가능하다.
(int) 'a'; // 97
(int) '0'; // 48

'a' + 1; // 98 (char + int는 int가 된다.)
(char) ('a' + 1);   // b
```

### 알파벳을 숫자로 바꾸기


```java
char ch = 'c';
int idx = ch - 'a';

int[] count = new int[26];
count[ch - 'a']++; // 알바벳별 개수 세기
```

### Character 메서드

```java
Character.isDigit('7');  // 숫자인가?
Character.isLetter('a'); // 문자인가?
Character.isUpperCase('A'); // 대문자인가?
Character.isLowerCase('a'); // 소문자인가?
Character.toUpperCase('a'); // 'A' 대문자 만들기
Character.toLowerCase('A'); // 'a' 소문자 만들기
```

---

## String

한번 만든 문자열은 바꿀수 없고, 바꾸는 것처럼 보이는 메서드들은 **새로 만들어 반환**하는것이다.

```java
String s = "hello";

s.toUpperCase(); // "HELLO"를 반환하지만 값이 바뀌지는 않는다.

s = s.toUpperCase(); // 값을 다시 대입해야 변경된다.
```

### 자주 쓰는 메서드

```java
String s = "Hello World";

s.length();          // 11 공백포함이다.
s.charAt(0);         // 'H' 인덱스로 문자 가져오기
s.substring(6);      //  "World" 6번째부터 끝까지
s.substring(0,5);    // "Hello" 0번부터 4번째 까지
s.indexOf("o");      // 4 없으면 -1나옴
s.lastIndexOf("o");  // 7 마지막으로 나온 위치
s.contains("World"); // true 포함여부
s.startsWith("He");  // true ~으로 시작하는가
s.endsWith("ld");    // true ~으로 끝나는가 
s.replace("l","L");  // l을 L로 전부 교체
s.toUpperCase();     // 전부 대문자로
s.toLowerCase();     // 전부 소문자로
s.isEmpty();         // false 빈 문자열인가?
" hello ".trim();    // "hello". 공백 제거   
```

> **배열**은 `arr.length`, **List**는 `list.size()`
**문자열은** `s.length()`로 길이를 구한다.

### equals vs ==

문자열 내용을 비교할때는 `==`가 **`equals`**를 사용해야해요.

```java
String a = "hi";
String b = new String("hi");

a == b;                 	// false 같은 객체가 아니어서 다르다.
a.equals(b);				// true 내용이 같은지
a.equalsIgnoreCase("hi"); 	// true (대소문자 무시)
```

**문자열**은 `equals`로 항상 비교한다고 생각하면된다.

> 반대로 `char`는 기본형이라 `==`로 비교한다.

```java
s.charAt(0) == 'H'; // true 
```

---

## 형변환

### String ↔ char

```java
String s = "abc";

char ch = s.charAt(0); // String -> char ('a')
String s1 = String.valueOf(ch); // char -> String ("a")
String s2 = ch + ""; // char + "" -> String

char[] arr = s.toCharArray();   // String -> char[] ['a', 'b', 'c']
String s3 = new String(arr);        // char[] → String  ("abc")
```

문자열을 한 글자씩 바꾸거나 정렬해야 할때는 `char[]`로 바꿨다가 되돌리는 방법을 많이 사용해요.

```java
char[] arr = "dcba".toCharArray();
Arrays.sort(arr);
String sorted = new String(arr);    // "abcd"
```

### String ↔ int

```java
int num = Integer.parseInt("123");   // 123

String s = String.valueOf(123);    	// "123"
```

### String ↔ 배열

```java
String[] parts = "a b c".split(" ");	//  ["a", "b", "c"]
String joined = String.join("-",parts);  // "a-b-c"
```

---

## 마무리

오늘은 `char`와 `String`에 대해 알아보고 자주 사용하는 메서드들도 정리해보았다. 코테를 공부하다 보면 배열을 가지고 하는 일도 많지만 문자열을 가지고 하는 일도 많아서, 기본기를 잘 가져가야 한다고 생각했다.

이제는 직접 문제를 풀어보면서 문제 해설또는 알고리즘 공식에 대해 다뤄볼러고합니다.