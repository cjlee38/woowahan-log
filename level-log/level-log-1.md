## final

- final은 재할당을 금지하는 것
- 변수에 붙이는 경우 → 변수의 재할당을 금지한다.
    - 필드에 붙이는 경우 → 반드시 생성자에서 초기화되어야 함.
    - 지역변수에 붙이는 경우 → lazy initialization이 가능함.
- 클래스에 붙이는 경우 → 상속을 금지한다
- 메소드에 붙이는 경우 → 오버라이딩을 금지한다.

## 원시값 포장
- 어떠한 값을 primitive 타입으로 사용하지 않고, 클래스로 묶어둔 것.
- 원시값 포장을 통해 표현하고자 하는 개념을 명확히 드러낼 수 있다.
- e.g. `int age`는 음수가 될 수 있지만, `Age age`는 음수 할당을 막을 수 있다.

## 일급컬렉션

- 원시값 포장과 마찬가지로, primitive type이 아닌 컬렉션에 대한 추상화
- 비즈니스에 종속적인 자료구조를 만들 수 있다.
- Collection의 불변성을 보장할 수 있다. (final 컬렉션은 내부 값 수정이 가능.)

## 방어적 복사

- `Collections.unmodifiableList();` → 리스트를 수정하는 메소드를 금지시킨다.
- `new ArrayList<>();` → 새로운 참조를 갖는다.
- `List.copyOf();` → 새로운 참조 + 수정 불가능

## 불변객체

- 불변객체란 ?
    - 한번 값이 할당되고 나면, 그 값을 바꿀수 없는 객체 (↔ 가변객체)
- 불변객체로 만들면 무엇이 좋은가?
    - thread-safe하다.
    - 실패원자적인 메소드를 만들 수 있다.
    - 캐시를 활용할 수 있다.
        - 캐시로부터 넘겨준 객체는 변하지 않고, 변하더라도 새로운 값에 할당되기 때문에 원본 객체는 안전하다.
- 어떻게 만들어야 하는가 ?
    - 외부에서 접근이 불가능하도록 private + (가능하면) final 키워드를 붙임
    - getter가 필요한 경우 복사해서 전달
    - 값의 변경이 필요한 경우 새로운 객체를 생성해서 전달
- 주의점
    - 기존 값을 기반으로 새로운 값을 생성하는 경우에, 기존 값을 변경해서는 안된다.
    - e.g.
    
    ```java
    public class CustomInteger {
    		private final int value;
    
    		public CustomInteger(int value) {
    				this.value = value;
    		}
    		
    		public CustomInteger increaseOne() { // BAD
    				return new CustomInteger(++this.value);
    		}
    		
    		public CustomInteger increaseOne() { // OK
    				return new CustomInteger(this.value + 1);
    		}
    }
    ```
    

	```java
	public class CustomList {
			private final List<Integer> values;

			public CustomList(Integer... values) {
					this.values = Arrays.asList(values);
			}

			public CustomList add(int value) { // BAD
					this.values.add(value);
					return new CustomList(this.values);
			}

			public CustomList add(int value) { // OK
					List<Integer> newValues = new ArrayList<>(this.values);
				newValues.add(value);
					return new CustomList(newValues);
			}
	```

## 표준예외 vs 커스텀예외

- 표준예외 입장
    - 표준예외를 사용해도 충분히 의미를 전달할 수 있다.
    - 예외에 대한 자세한 내용은 stacktrace 및 예외 메시지로 전달할 수 있다.
    - 커스텀 예외를 만드는 것 또한 관리 포인트가 늘어나는 것 → 즉, 비용
    - 대부분의 java 개발자가 이해할 수 있는 예외이다.
- 커스텀 예외 입장
    - 표준예외보다 구체적이고, 예외 메시지보다 추상적인 단계를 제공할 수 있다.
    - 예외에 대한 처리를 효과적으로 분류할 수 있다.
        - 체스에서, 말이 이동할 수 없는 곳으로 움직인 것
        - 혹은 이동 과정에 다른 말이 존재하는 것
        - 이 두가지를 모두 IllegalStateException으로 처리하기보다, 별도의 custom exception 두가지로 구분해서 처리할 수 있다.
    

## 디미터의 법칙

- ‘한 줄에 점을 하나만 찍는다.’, ‘낯선 이에게 말하지 마라’, ‘최소 지식 원칙’
- A 객체가, 이웃하고 있는(의존하고 있는) 다른 객체 B 가 갖고있는 속성을 알 수 없도록 해야 한다.
- B 객체는 적절한 캡슐화가 이루어지지 않은 것이다.
- B 객체의 내부 속성이 변경되었을 경우, 그 속성에 의존하고 있는 모든 객체가 영향을 받는다.
    - 점을 하나만 찍는다고 해서, 스트림이나 빌더패턴이 문제가 되는 것은 아니다.

## 함수형인터페이스

- 추상메소드를 단 하나만 갖는 인터페이스
- java.util.function 패키지에 표준으로 사용될 수 있는 함수형 인터페이스들이 있다.
- 람다식을 활용할 수 있음.
    - 즉, 메소드(즉, behavior)를 파라미터로 넘겨줄 수 있게 됨
    - = 일급 객체화 (변수에 저장할 수 있고, 파라미터로 넘겨줄 수 있고, 리턴타입으로 사용할 수 있다)
