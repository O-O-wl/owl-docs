# O-O-wl 's Standard  🦉📖

---

나만의 기준을 찾아가는 중

<br>



## 나의 기준

---

- #### 클래스

<br>

- #### 열거형

<br>

- #### 구조체

<br>

- #### 프로토콜

<br>

- #### 제네릭

<br>

- #### 옵셔널

  - 강제 옵셔널 언래핑은 변수에서는 사용하지 말자. 상수 또는 하드코딩된 보장할 수있는 값 에서만 사용하자.

  ```
  변수는 어디서 값이 정해지는 지 또 어디서 변하는 지 모호하다. 상수로 된 값이나 리터럴은 이걸 보장할 수 있다.
  ```

<br>

---

<br><br>

## 나쁜 습관과 피해야할 코드

---



- ### 클래스

  - 내부에서 다른 클래스와 구조체의 생성자를 호출하지말자

    ```
    생성자는 구체적인 타입에 의존하는 코드이고, 외부에 의존성이 숨어서 테스트도 힘든 구조가 된다.
    ```

    



## 고민해야 하는 문제

- #### `computed property` vs `method`

  - 

- #### `struct` vs `class`

  - 

- #### `if`  vs `guard` 

  - 

- #### 객체내 타입 표현 vs 익스텐션을 통한 타입 표현



- #### `lazy property`  vs `eager property`

  - 해당 프로퍼티의 사용이 무조건적이지 않다면  `lazy`를 고민해보자
  - 객체를 생성하는 데 걸리는 오버헤드가 존재한다.  **객체가 쓰일 상황에 즉각적인 응답을 원한다면  `lazy`를 사용하지말자**

  