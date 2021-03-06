---
layout: post
title:  "Design Pattern"
date:   2021-02-15 00:0054 +0900
categories: java
---

### 디자인패턴 vs 알고리즘

알고리즘은 문제 해결을 위한 처리 절차.

ex 1) 서울에서 부산으로 가는 방법
1. 걸어서 간다
2. 자전거를 타고 간다.
3. 버스를 타고 간다.
4. KTX를 타고 간다.
5. 비행기를 타고 간다.

ex 2) 여러 개의 데이터가 있을 때 순서에 맞게 정렬하는 방법
1. 단순 정렬
2. 버블 정렬
3. 삽입 정렬
4. 쉘 정렬
5. 퀵 정렬

디자인 패턴

효율적인 프로그래밍을 하고 있다는 판단하는 기준
1. 중복된 코드가 없다.
2. 변경된 요구사항을 순조롭게 받아들일 수 있어야 한다.
(이 둘중에 어떤게 더 우선이 될까??)

디자인 패턴을 적용하여 프로그램을 만드는 중요한 이유는 다양한 추가 요구사항에 대해 좀 더 쉽게 대응하기 위해서이다.
= 과거에 이런 상황에 이렇게 하니 요구사항 변경에 따라 코드 수정을 하기 좋았다.

디자인패턴도 같은 상황에 하나의 패턴만이 정답은 아니다.

디자인패턴도 만드는 서비스의 종류에 따라서 다양하게 나뉜다.
1. 오리지널 파운데이션 패턴(GoF 패턴)
2. 썬의 J2EE 패턴
3. JSP 패턴
4. 아키텍쳐 패턴
5. 게임 디자인 패턴

GoF 패턴에는 아래와 같은 패턴들이 있음.
1. 싱글턴 패턴
2. 플라이웨이트 패턴
3. 빌더 패턴
4. 옵저버 패턴
5. 어댑터 패턴
6. 파사드 패턴
7. 데코레이션 패턴
8. 브릿지 패턴
9. 스트래티지 패턴


### 디자인 패턴과 클래스의 다형성

1. 참조타입 사이에서의 형변환
클래스 사이에서도 형변환이 일어나는데, 클래스 사이의 형변환은 상위클래스와 하위클래스 사이에서 일어나게 된다.
-> 하위클래스의 객체는 상위클래스의 객체에 대입할 수 있다.
-> 상위클래스 객체를 하위클래스 객체에 대입할 수 없다.
: 다형성의 특징.

객체지향 프로그래밍을 하는데 굉장히 큰 근간이 되는 특징이 다형성이다.

### 클래스의 약한 결합, 강한 결합의 이해

DI(Dependency Injection) 의존성 주입 : 객체지향 프로그래밍에서 다른 객체를 사용하는 것을 다른 객체에 의존한다고 표현.
다른 객체를 사용하기 위해서는 다른 객체를 생성해야 하는데. 직접 생성도 가능하고, 미리 생성되어있는 다른 객체를 받아올수도 있다.
이런 것들이 모두 의존성 주입의 방법들

```java

class A {
    B b;
    C c;

    public A(C c) {
        this.c = c;
    }

    // Setter 주입
    public setB(B b) {
        this.b = b;
    }
}

```

생성자 주입이나, Setter 주입은 기존에 있는 객체를 가져와 주입하기 때문에 결합도가 낮아지지만, A 에서 직접 생성하게 되면 의존성 관리도 A 객체가 직접 해야하기 때문에
결합도가 높아진다.

강한 결합의 예시.

```java
public class UnderstandDI {
    public static void main(String[] args) {

    }

    // 강한 결합
    public static void getDate() {
        Date date = new Date();
        System.out.println(date);
    }

    // 약한 결합
    public static void getDate(Date d) {
        Date date = d;
        System.out.println(date);
    }

    // 강한 결합 : Member를 직접 생성
    public static void memberUse1() {
        Member m1 = new Member();
    }

    //약한 결합 : 생성된 것을 주입 받음
    public static void memberUse2(Member m) {
        Member m2 = m;
    }
}

class Member {
    String name;
    String nickname;
    
    public Member() {

    }
}
```

Utility 클래스는 보통 싱글톤 방식으로 사용하기 때문에 내부적으로 private 생성자를 사용.


### 싱글턴 패턴 : 최대 N개의 객체 생성을 제한하는 패턴

예시) 
    데이터베이스 커넥션 풀
    로그 라이터
    사운드 매니저
    스코어 매니저


싱글톤이 아닌경우의 예시 -> Database 객체가 계속 생성된다.

```java

public class Database {
    private String name;

    public Datebase(String name) {
        super();
        this.name = name;
    }

    public String getName() {
        return name;
    }
}

public class TestPattern1 {
    public static void main(String[] args) {
        Database d1 = new Database("1");
        Database d2 = new Database("2");
        Database d3 = new Database("3");
        Database d4 = new Database("4");
        Database d5 = new Database("5");
        Database d6 = new Database("6");

        System.out.println("database use");
    }
}
```

싱글톤인 경우

```java

public class Database {
    private static Database singleton;
    private String name;

    public Database(String name) {
        super();
        this.name = name;
    }

    public static Database getInstance(String name) {
        if(singleton == null) {
            singleton = new Database(name);
        }

        return singleton;
    }

    public String getName() {
        return name;
    }
}

public class TestPattern1 {
    public static void main(String[] args) {
        Database database;

        database = Database.getInstance("첫 번째 Database");
        System.out.println("This is the " + database.getname() + " !!!");

        database = Database.getInstance("두 번째 Database");
        System.out.println("This is the " + database.getname() + " !!!");
    }
}

```

위 예시의 문제점은 생성자 생성 방식을 제한하지 못한다. getInstance() 이 함수 말고 생성자로 호출하게 되면 싱글톤이 적용이 안된다.
그래서 생성자의 접근제어지시자를 private 으로 변경한다.

```java

public class Database {
    private static Database singleton;
    private String name;

    private Database(String name) {
        super();
        this.name = name;
    }

    public static Database getInstance(String name) {
        if(singleton == null) {
            singleton = new Database(name);
        }

        return singleton;
    }

    public String getName() {
        return name;
    }
}

```

멀티쓰레드 환경의 경우 문제점 : 각 쓰레드가 Database 객체에 접근하려고 하면 모두 새롭게 생성.
왜 그럴까 for문은 엄청 빠르다. 다른 쓰레드가 도는 시점에 singleton 객체는 모두 null임.
그래서 모두 새롭게 생성하는 것임.

```java
public class TestPattern2 {
    static int nNum = 0;

    public static void main(String[] args) {
        Runnable task = () => {
            try {
                nNum++;
                database database = Database.getInstance(nNum + "번째 Database");
                System.out.println("This is the " + database.getName() + " !!!");
            } catch (Exception e) {
            }
        };
    }

    for(int i=0; i<10; i++) {
        Thread t = new Thread(task);
        t.start();
    }
}

```

멀티스레드환경에서도 안정적인 싱글톤 구현하기 : 객체 생성시에 synchonized 선언 -> 비동기 프로그래밍시 중요한 사항! setter에 해당하는 코드들은
synchronized를 붙여야한다. 그러면 이부분은 블로킹되고 순서대로 처리된다. -> 비용이 비쌈 / 병목현상이 일어남 / 블로킹됨

```java

public class Database {
    private static Database singleton;
    private String name;

    private Database(String name) {
        super();
        this.name = name;
    }

    public synchronized static Database getInstance(String name) {
        if(singleton == null) {
            singleton = new Database(name);
        }

        return singleton;
    }

    public String getName() {
        return name;
    }
}

```

싱글톤 코드의 비용을 줄여보자.

```java
        if(singleton == null) {
            singleton = new Database(name);
        }
```

이 코드가 처음에 싱글톤 객체가 생성될때에만 유효하고 그 이후부터는 쓸데없는 코드. 생각보다 낭비가 많은 코드이다. 이거 수정해보자.

```java
public class Database {
    private static database singleton = new Datebase("products");
    private String name;

    private Database(String name) {
        try {
            Thread.sleep(100);
            this.name = name;
        } catch (Exception e) {
        }
    }

    public static Database getInstance(String name) {
        return singleton;
    }

    public String getName() {
        return name;
    }
}

```

synchronized 개선, if(singleton == null) 코드 개선



### 실전 : 로그 라이터 만들기

싱글톤 패턴을 적용해보기 위해서 로깅하는 객체를 만들어 보자.

```java

public class LogWriter {
    private static LogWriter singleton = new LogWriter();
    private static BufferedWriter bw;

    private LogWriter() {
        try {
            bw = new BufferedWriter(new FileWriter("log.txt"));
        } catch (Exception e) {

        }
    }

    public static LogWriter getInstance() {
        return singleton;
    }

    public synchronized void log(String str) {
        try {
            bw.write(str + "\n");
            bw.flush();+
        } catch(Exception e) {

        }
    }

    @Override
    protected void finalize() {
        try {
            
        } catch (Throwable ex) {

        }
    }
}

public class TestPattern1 {
    public static void main(String[] args) {
        LogWriter logger;

        logger = LogWriter.getInstance();
        logger.log("홍길동");

        logger = LogWriter.getInstance();
        logger.log("전우치");
    }
}

```

### 플라이웨이트 패턴 이해하기

싱글턴 패턴과 마찬가지로 오브젝트의 생성을 제한하는 패턴

플라이웨이트 패턴은 비용이 큰 자원을 공통으로 사용할 수 있도록 만드는 패턴
자원에 대한 비용은 크게 두가지로 나눠 볼 수 있다.

1. 중복 생성될 가능성이 높은 경우.
-> 자주 사용될 가능성이 높다는 의미

2. 자원 생성 비용은 큰데 사용 빈도가 낮은 경우
-> 요청시에 생성하며, 마무리되면 정리한다.

나무를 생성한다고 치면 나무의 필요한 요소는 잎사귀, 뿌리, 가지 등이 있다.
잎사귀, 뿌리, 가지에 대한 정보가 고유하게 가져아할 정보가 있는 객체가 아니라면 이 객체들을 싱글톤으로 생성하고
이 객체들을 참조 형식으로 나무가 바라보도록 할 수 있다.

이렇게 하면 나무를 위해서 잎사귀, 뿌리, 가지등의 객체가 매번 새롭게 생성되지 않으며, 각각 하나의 객체만을 가지고
많은 나무들을 구현할 수 있다.
나무는 단지 각 객체를 참조할 수 있는 값만 가지면 된다.

실제로 쓰기 위해서는. 나무도 고유한 값을 가지는 데이터들이 잇을 거시다. 이런 값들은 어쩔수 없고
고유하지 ㅇ낳아도 되는 데이터들만 묶어서 그거를 참조하게끔 구현한다.

** 한 객체 내에서 싱글톤으로 쓸수 있는 객체를 새로운 객체로 표현하고 이를 참조하는 구조를 가짐.

장점
-> 많은 객체를 만들  때 성능을 향상시킬 수 있다.
-> 많은 객체를 만들 때 메모리를 줄일 수 있다.
-> state pattern과 쉽게 결합될 수 있다.

단점
-> 특정 인스턴스 공유 컴포넌트를 다르게 설정할 수 없다.


### 기존 자바에서 코드에서 볼 수 있는 플라이웨이트 패턴

```java

public class TestPattern {
    public static void main(String[] args) {
        String str1 = new String("홍길동");
        String str2 = new String("홍길동");
        String str3 = "홍길동";
        String str4 = "홍길동";
    }
}
```

str1, str2 은 별도의 인스턴스
str3, str4 은 같은 인스턴스 String 객체가 싱글톤 패턴을 사용하고 있음을 알 수 있다.

```java

public class TestPattern {
    public static void main(String[] args) {
        Mydata md1 = new Mydata();
        md1.xpos = 10;
        md1.ypos = 11;
        md1.name = "홍길동";

        MyData md2 = new MyData();
        md2 = md1;

        MyData md3 = new MyData();
        md3.xpos = 20;
        md3.ypos = 21;
        md3.name = "손오공"

        md2.name = "전우치"
        md2.xpos = 5;
    }
}

class MyData {
    int xpos;
    int ypos;
    String name;
}

```

### 플라이웨이트 패턴 구현하기.

```java

public class Subject {
    private String name;

    public Subject(String name) {
        this.name = name;
    }
}

public class FlyweightFactory {
    private static Map<String, Subject> map = new HashMap<String, Subject>();

    public Subject getSubject(String key) {
        Subject subject = map.get(key);

        if(subject == null) {
            subject = new Subject(key);
            map.put(key, subject);

            System.out.println("새로 생성 " + key);
        } else {
            System.out.println("재사용 " + key);
        }

        return subject;
    }
}

public class TestPattern {
    public static void main(String[] args) {
        FlyweightFactory flyweight = new Flyweightfactory();
        flyweight.getSubject("a");
        flyweight.getSubject("a");
        flyweight.getSubject("b");
        flyweight.getSubject("b");
    }
}

```

### 빌더 패턴

빌더패턴은 객체를 생성할 때 흔하게 사용하는 패턴이다.
자바로 코딩할 때 다음과 같은 스타일로 객체를 생성하는 코드가 있다면, 빌더 패턴을 사용했다고 살 수 있다.
체이닝 기법을 활용한다.

객체 일관성, 변경 불가능성의 특징을 가지고 있기 때문에 빌더패턴을 사용.
코드의 가독성과 유지보수하기 쉬워진다

생성자 인자가 많을 때는 빌더패턴을 사용하라.

```java

@Getter
@Setter
public class Person {
    private String firstName;
    private String lastName;
    private LocalDate birthDate;
    private String addressOne;
    private String addressTwo;
    private String sex;
    private boolean driverLicence;
    private boolean married;

    public static PersonBuilder builder() {
        return new PersonBuilder();
    }
}

public class PersonBuilder {
    private String firstName;
    private String lastName;
    private LocalDate birthDate;
    private String addressOne;
    private String addressTwo;
    private String sex;
    private boolean driverLicence;
    private boolean married;

    public PersonBuilder firstName(String firstName) {
        this.firstName = firstName;
        return this;
    }

    public PersonBuilder lastName(String lastName) {
        this.lastName = lastName;
        return this;
    }

    public PersonBuilder birthDate(LocalDate birthDate) {
        this.birthDate = birthDate;
        return this;
    }

    public PersonBuilder addressOne(String addressOne) {
        this.addressOne = addressOne;
        return this;
    }

    public PersonBuilder addressTwo(String addressTwo) {
        this.addressTwo = addressTwo;
        return this;
    }

    public PersonBuilder sex(String sex) {
        this.sex = sex;
        return this;
    }

    public PersonBuilder driverLicence(boolean driverLicence) {
        this.driverLicence = driverLicence;
        return this;
    }

    public PersonBuilder married(boolean married) {
        this.married = married;
        return this;
    }

    public Person build() {
        Person person = new Person();
        person.setFirstName(firstName);
        person.setLastName(lastName);
        person.setAddressOne(addressOne);
        person.setAddressTwo(addressTwo);
        person.setBirthDate(birthDate);
        person.setSex(sex);
        person.setDriverLicence(driverLicence);
        person.setMarried(married);
        return person;
    }
}

public class TestPatterm {
    public static void main(String[] args) {
        Person p1 = Person.builder()
            .firstName("FirstName")
            .lastName("LastName")
            .addressOne("금천구 가산동 월드메르비앙2차 KOSMO")
            .addressTwo("AddressTwo")
            .birthDate(LocalDate.of(1995, Month.APRIL, 13))
            .sex("male")
            .driverLicence(true)
            .married(true)
            .build();

        System.out.println(p1.getAddressOne());
    }
}

```

빌더안에 Person 필드들을 주는거보다 Person 객체를 그냥 주는게 훨씬 나음 보일러플레이트 코드 개쩜
그리고 빌더의 목적 중 하나는 값이 중간에 변경이 불가능하도록 하는 건데 저렇게하면 변경이 결국 가능
setter를 제거하고 buider 내부에서는 한번에 생성 되도록 final 키워드 선언해야됨

static 선언하는거 저거 하나 건졌다.


### 옵저버 패턴

한 객체의 상태가 바뀌면 그 객체에 의존하는 다른 객체들한테 연락이 가고 자동을 ㅗ내용이 갱신되는 방식으로
일대다 의존성을 정의.

Subject 객체가 있음. 이 객체는 bCheck 값을 가짐
Observer 객체 1,2,3 Subject의 bCheck 값을 감시한다.
bCheck 값이 변경되면 등록되어있는 필요한 동작 수행

```java

public class PlayController extends Observable {
    private boolean bPlay;

    public PlayController() {

    }

    public void setFlag(boolean bPlay) {
        this.bPlay = bPlay;
        setChanged();
        notifyObservers();
    }

    public boolean getFlag() {
        return bPlay;
    }
}

/** Observer 객체 */
public class MyClassA implements Observer {
    Observable observable;
    private boolean bPlay;

public MyClassA(Observable o) {
    this.observable = o;
    observable.addObserver(this);
}

    @Override
    public void update(Observable o, Object arg) {
        if(o instanceof PlayController) {
            PlayController myControl = (PlayController) o;
            this.bPlay = myControl.getFlag();
            myActControl();
        }
    }

    public void myActControl() {
        if(bPlay) {
            System.out.println("MyClassA : 동작을 시작합니다.");
        } else {
            System.out.println("MyClassA : 동작을 정지합니다.");
        }
    }
}

/** Observer 객체 */
public class MyClassB implements Observer {
    Observable observable;
    private boolean bPlay;

public MyClassB(Observable o) {
    this.observable = o;
    observable.addObserver(this);
}

    @Override
    public void update(Observable o, Object arg) {
        if(o instanceof PlayController) {
            PlayController myControl = (PlayController) o;
            this.bPlay = myControl.getFlag();
            myActControl();
        }
    }

    public void myActControl() {
        if(bPlay) {
            System.out.println("MyClassB : 동작을 시작합니다.");
        } else {
            System.out.println("MyClassB : 동작을 정지합니다.");
        }
    }
}

public class TestPattern {
    public static void main(String[] args) {
        PlayController controller = new PlayController();

        MyClassA classA = new MyClassA(Controller);
        MyClassB classB = new MyClassB(controller);
    }
}

```

### 자바 내장 옵저버 패턴의 단점과 한계

(1) Observable은 클래스다.
Observable이 클래스기 때문에 서브클래스를 만들어야 한다는 점이 문제다. 이미 다른 수퍼클래스를 확장하고 있느 클래스에 Observable의 기느을 추가할 수 없기 때문이다.
그래서 재사용성에 제약이 생긴다.

(2) Observable 클래스의 핵심 메서드를 외부에서 호출할 수 없다.
Observable API를 살펴보면 setChanged() 메서드가 protected로 선언되어 있다.
Observable의 서브클래스에서만 setChanged()를 호출할 수 있다. 결국 직접 어떤 클래스를 만들고 , Observable 서브클래스를 인스턴스 변수로 사용하는 방법도 쓸 수 없다. 이런 디자인은 상속보다는 구성을 사용한다는 디자인 원칙도 위배된다.

```java

public interface Pulisher {
    public void addObserver()
}
```


### 어댑터 패턴의 이해와 구현하기

이미 제공되어 있는것 과 필요한 것의 차이를 없애주는 것이 Adapter 패턴.

한 클래스의 인터페이스를 클라이언트에서 사용하고자 하는 다른 인터페이스로 변환.
Adapter 패턴은 Wrapper 패턴으로 불리기도 한다.

두가지 종류가 있음
클래스에 의한 Adapter 패턴
인스턴스에 의한 Adapter 패턴

어떤 경우에 사용하는가.
-> 기존에 존재하는 클래스를 활용하여 코드를 작서ㅇ할 떄.
-> 기존 클래스를 개조해서 필요한 클래스를 만든다.
-> 이 패턴으로 필요한 메서드를 발 빠르게 만들 수 있따.
-> 만약 버그가 발생해도 기존의 클래스에는 버그가 없으므로 Adapter 역할의 클래스를 중점적으로 조사하면 되고, 프로그램 검사도 상당히 쉬워진다.

어댑터 패턴이란 기존에 존재하는 클래스를 커스터마이징 해서 새로운 클래스를 만드는거

이미 만들어진 클래스를 새로운 인터페이스에 맞게 개조시킬 떄는 당연히 Adapter 패턴을 사용해야 한다.
그러나 실제 우리가 새로운 인터페이스에 맞게 개조시킬 때는 기존 클래스의 소스를 바꾸어서 '수정' 하려고 한다.
이것을 조금 바꿈녀 분명 작업은 끝이나 라고 생각하기 쉽다.
그러나 그렇게 하면 동작 테스트가 이미 끝난 기존의 클래스를 수정한 후에 다시 한번 테스트 해야한다.
-> 기존 클래스를 변경하게 되면 그에 대한 테스트를 다시 시행해야하기 때문에 작업 범위가 넓어진다는 의미

adapter 패턴은 기존의 클래스를 전혀 수정하지 않고 목적한 인터페이스에 맞추려는 것.
adapter 패턴은 기존 클래스의 소스를 반드시 가질 필요가 없으며 기존 클래스의 사양(Interface) 만 알면 새로운 클래스를 만들 수있다


Adapter 패턴과 유사한 두가지 패턴 Bridge, Decorator

### 인스턴스에 의한 Adapter 패턴

```java
public interface APlayer {
    void play(String fileName);
    void stop();
}

public class APlayerImpl implements Aplayer {
    @Override
    public void play(String fileName) {
        System.out.println("(A) " + fileName);
    }

    @Override
    public void stop() {

    }
}


public interface BPlayer {
    void playFile(String fileName);
    void stopFile();
}

public class BPlayerImpl implements BPlayer {
    @Override
    public void playFile(String fileName) {
        System.out.println("(B) " + fileName);
    }

    @Override
    public void stopFile() {

    }
}

public class BToAAdapter implements APlayer {
    private BPlayer media;

    public BToAAdapter(BPlayer media) {
        this.media = media;
    }

    @Override
    public void play(String fileName) {
        System.out.print("Using Adapter : ");
        media.playFile(fileName);
    }

    @Override
    public void stop() {

    }
}

```

APlayer , BPlayer 간의 어댑터.
기존에 잘 동작하다가 BPlayer로 변경시 문제가 발생한다면
BToAAdapter 이쪽 부분만 테스트하면 된다. 

### 클래스에 의한 어댑터

```java

public interface APlayer {
    void play(String fileName);
    void stop();
}

public class APlayerImpl implements Aplayer {
    @Override
    public void play(String fileName) {
        System.out.println("(A) " + fileName);
    }

    @Override
    public void stop() {

    }
}

public abstract class BPlayer {
    public abstract void playFile(String fileName);
    public abstract void stopFile();
}

public class BPlayerImpl extends BPlayer {
    
}
```