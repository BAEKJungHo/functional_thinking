# 진화하라

> 객체지향적인 명령형 프로그래밍 언어에서 재사용의 단위는 클래스와 그것들이 주고받는 메시지들이다. 이것들은 클래스 도표(class diagram)로 표시되곤 한다.

## 자바의 폴-스루 형식(fall-through)

폴-스루 형식이란 switch에서 각각 경우마다 return 이나 break로 마쳐줘야 하는 경우를 의미한다.

## 함수형 자료구조

- 특징
  - 부수효과가 없는 순수함수를 선호
  - 함수형 언어들은 주로 값을 처리(리턴 값에 반응)
  - 참조 투명성
  
## 함수형 오류 처리

- Map을 사용하여 다중 리턴 값을 처리하기

```java
public static Map<String, Object> divide(int x, int y) {
  Map<String, Object> resut = new HashMap<String, Object>();
  if(y == 0)
    result.put("exception", new Exception("div by zero");
  else 
    result.put("answer", (double) x/y);
}
```

위 예제의 접근 방식에는 문제가 있다. 첫째, Map 에 들어가는 값은 타입 세이프하지 않기 때문에 컴파일러가 오류를 잡아낼 수 없다.
둘째, 메서드 호출자는 리턴 값을 가능한 결과들과 비교해보기 전에는 성패를 알 수 없다. 셋째, 두 가지 결과가 모두 리턴 Map에 존재할 수가 있으므로,
그 경우에는 결과가 모호해진다.

여기서 필요한 것은 타입 세이프하게 둘 또는 더 많은 값을 리턴할 수 있게 해주는 매커니즘이다.(`Either 클래스`)

### Either 클래스

함수형 언어에서는 다른 두 값을 리턴해야 하는 경우가 종종 있는데 그런 행동을 모델링하는 자료구조가 Either 클래스이다. Either는 왼쪽 또는
오른쪽 값 중 하나만 가질 수 있게 설계되었다. 이런 자료구조를 `분리합집합(disjoint union)` 이라고 한다. 분리합집합은 두 자료형이 들어갈 자리가 있지만 둘 중 하나만 지닐 수 있다.

- Either 클래스를 통해 타입 세이프한 두 값을 리턴하기

```java
public class Either<A,B> {
  private A left = null;
  private B right = null;
  
  private Either(A a, B b) {
    left = a;
    right = b;
  }
  
  public static <A,B> Either<A,B> left(A a) {
    return new Either<A,B>(a,null);
  }
  
  public A left() {
    return left;
  }
  
  public boolean isLeft() [
    return left != null;
  }
  
  public boolean isRight() {
    return right != null;
  }
  
  public B right() {
    return right;
  }
  
  public static <A,B> Either<A,B> right(B b) {
    return new Either<A,B>(null,b);
  }
  
  public void fold(F<A> leftOption, F<B> rightOption) {
    if(right == null)
      leftOption.f(left);
    else 
      rightOption.f(right);
   }
}
```

Either 클래스는 생성자가 private 이기 때문에 실제 생성은 정적 메서드인 left(A a)와 right(B b)가 담당한다.

Either를 사용하면 타입 세이프티 유지하면서 예외 또는 제대로 된 결과 값을 리턴하는 코드를 만들 수 있다. 함수형의 보편적인 관례에 따라
Either 클래스의 왼쪽이 예외, 오른쪽이 결과값이다.

- 로마숫자 파싱하기

```java
public static Either<Exception, Integer> parseNumber(String s) {
  if(!s.matches("[IVXLXCDM]+"))
    return Either.left(new Exception("Invalid Roman numeral"));
  else
    return Either.right(new RomanNumeral(s).toInt());
}
```

위 예제의 Either는 왼쪽은 Exception 오른쪽은 Integer 타입을 받는다.

## Option 클래스

Either는 두 부분을 가진 값을 간편하게 리턴할 수 있는 개념이다. 이것은 트리 모양 자료구조와 같은 일반적인 자료구조를 만드는데 유용하다.
Either와 유사한 `Option` 클래스가 있는데 이것은 적당한 값이 존재하지 않을 경우를 의미하는 none, 성공적인 리턴을 의미하는 some을 사용하여
예외 조건을 더 쉽게 표현한다.

```java
public static Option<Double> divide(double x, double y) {
  if(y == 0) return Option.none();
  return Option.some(x/y);
}
```

Either는 어떤 값이든 저장할 수 있는 반면, Option은 주로 성공과 실패의 값을 저장하는 데 쓰인다.

