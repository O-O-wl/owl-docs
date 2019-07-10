

# 옵저버 패턴

### ``` 한 객체가 바뀌면 그 객체에 의존하는 다수의 객체들한테 연락을 보내고, 갱신되는 방식으로 일대다 의존성을 정의한다.```

------

시계를 예로 들어보자.

시계들은 시간이라는 큰 개념에 의존하고 있다.

시간이 바뀌면 시계들은 내부의 상태를 변경해야하는 의무가 있다.

어떻게 표현하는게 좋을까?

------

### 알려주기

- 시간은 계속적으로 바뀔 것이고 , 시간을 담고 있는 Time 객체는 그에 따른 변화가 있을 것이다.

- Time 객체가 **자기 자신이 바뀌었을 때**,  모든 시계들에게 바뀌었다는 사실과 데이터를 알려주면 되겠다.

  ```
  1.시간이 바뀌었을 때 시간값을 바꿔야하는 시계객체
  2.날씨가 바뀌었을 때 날씨값를 바꿔야하는 기상정보어플객체
  3.날짜가 바뀌었을 때 날짜값를 바뀌어야하는 달력객체
  은  어떤 공통점이 있을까?
  ```

------

위의 3가지 객체의 공통점은 

무언가 바뀌면 , **그에 따른 무언가가 변경된다'**는 사실이다.

시계,달력,기상정보어플 은 유일하게 하나만 존재하는 객체가 아니다.

위의 '시간,달력,날씨'를 **주제(Subject)** , '시계,기상정보어플,달력'을 **관찰자(Observer)** 라는 단어로 바꾸어보자.

'**주제**가 바뀌었을 때 **주제** 의값을 바꿔야하는 **관찰자** '가 되겠다.

위와 같이 코드를 작성해보겠다.

- ####  주제 - 데이터가 달라지면 관찰자에게  데이터를 전달해준다.

- ####  관찰자 - 주제에 의존하며 갱신시 데이터를 전달 받습니다.

---

- `Subject.java`

  - ```java
    public interface Subject {
        ArrayList<Observer> observers = new ArrayList<>();
      
        void registerObserver(ObserSver o);
        void removeObserver(Observer o);
        void notifyObserver();
    }
    ```

- `Observer.java`

  - ```java
    public interface Observer {
        public void update(int hour,int min , int sec);
    }
    ```

---

### 느슨한 결합

### `상호작용을 하는 객체가 서로의 대해 알지만 , 완벽히 모든 걸 알지는 못한다.`

#### 느슨한 결합이 가져오는 이점

	- 인터페이스만 알고 있어서 , 두 객체의 상호작용의 최소한의 약속 만을 알 고 있다.
	- 새로운 옵저버 인스턴스를 추가할 수 있다.
	- 새로운 옵저버의 타입을 추가할 수 있다.
	- 주제와 옵저버가 별도 부분에 대해서는 독립적으로 재사용이 가능하다.
	- 변경사항에 대해서 주제와 옵저바 사이의 상호의존성을 최소화 할수 있다.

---

### 실제 인터페이스들을 구현한 구상 클래스를 명시하여 예제 테스트를 해보자

- `Time.java`

- ```java
  package 디자인패턴.옵저버패턴.Subject;
  
  public class Time implements Subject {
  
      private int hour;
      private int min;
      private int sec;
  
      public Time(int hour, int min, int sec) {
          this.hour = hour;
          this.min = min;
          this.sec = sec;
      }
  
      @Override
      public void registerObserver(Observer o) {
          this.observers.add(o);
      }
  
      @Override
      public void removeObserver(Observer o) {
          this.observers.remove(o);
      }
  
      @Override
      public void notifyObserver() {
          for (Observer observer:observers){
              observer.update(this.hour,this.min,this.sec); 
            // Observer 의 인터페이스의 최소한의 동작 update 만 알고 수행
          }
      }
     public void setHour(int hour) {
          this.hour = hour;
      }
     public void setMin(int min) {
          this.min = min;
      }
     public void setSec(int sec) {
          this.sec = sec;
      }
  }
  
  ```

- `Clock.java`

- ```java
  package 디자인패턴.옵저버패턴.Observer;
  
  public class Clock implements Observer,Displayable{
  
      String currentTime ;
      String name;
      boolean form24;
  
      public Clock(String currentTime, String name, boolean form24) {
          this.currentTime = currentTime;
          this.name = name;
          this.form24 = form24;
      }
  
      @Override
      public void update(int hour, int min, int sec) {
          int finalHour = form24 ? hour : hour%12;
          currentTime = finalHour+"시 "+min+"분 "+sec+"초";
          display();
      }
  
      @Override
      public void display() {
          System.out.println(name);
          System.out.println(currentTime);
      }
  }
  ```

  ---

  ### UML

<img width="753" alt="스크린샷 2019-05-03 오후 11 25 44" src="https://user-images.githubusercontent.com/39197978/57143904-dc7de300-6dfa-11e9-8719-778ff33489e2.png">