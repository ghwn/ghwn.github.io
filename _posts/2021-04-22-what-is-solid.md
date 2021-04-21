---
layout: post
title: "SOLID 원칙"
tags: [oop]
comments: true
---

객체 지향 프로그래밍 방법론에서 말하는 **SOLID**란 소프트웨어를 더 이해하기 쉽고, 유연하고, 유지보수하기에 좋게 만드는
다섯 가지 설계 원칙을 말한다. 이 이름은 클린 코드 저자 Robert Martin에 의해 처음 소개되었다.

## S: Single responsibility principle (SRP, 단일 책임 원칙)

- 한 클래스는 하나의 책임만 가져야 한다.
- 사실 "하나의 책임"이라는 말은 모호하다.
    - 클 수도 있고 작을 수도 있다.
    - 문맥과 상황에 따라 다르다.
- 중요한 기준은 **변경**이다. 변경이 있을 때 파급 효과가 적으면 단일 책임 원칙을 잘 따른 것이다.


## O: Open/closed principle (OCP, 개방-폐쇄 원칙)

- 소프트웨어 요소는 **확장에는 열려**있으나 **변경에는 닫혀** 있어야 한다.
- 역할과 구현의 분리를 이끌어내는 다형성을 활용한다.
- 아래 코드와 같은 맥락인데 인터페이스를 구현하는 클래스를 **얼마든지 만들어 확장**할 수 있고 그에 따른 **코드의 변경이 일어나지 않는다**는 뜻이다. 그런 면에서 사실 아래 코드는 엄밀히 말하면 새로 확장된 구현 클래스를 선택하기 위해 코드를 변경했기 때문에 개방-폐쇄 원칙을 지켰다고 말할 수는 없다. 변경에 닫혀있다고 말할 수 있으려면 `Service` 클래스에서 코드의 변경이 일어나면 안 된다. 이 원칙을 제대로 지키려면 객체를 생성하고 연관관계를 맺어주는 별도의 조립, 설정자가 필요하다.
    ```java
    public class Service {
        // private Repository repository = new MemoryRepository();
        private Repository repository = new JdbcRepository(); // 새로운 구현 클래스로 변경
    }

    public class MemoryRepository implements Repository {
        // 인터페이스 구현
        @Override
        ...
    }
    // 인터페이스를 구현하는 클래스를 얼마든지 만들어 확장할 수 있다.
    public class JdbcRepository implements Repository {
        // 인터페이스 구현
        @Override
        ...
    }
    ```


## L: Liskov substitution principle (LSP, 리스코프 치환 원칙)

상위 타입의 객체를 하위 타입의 객체로 변경해도 프로그램이 정확성을 유지하며 동작해야 한다는 원칙이다.
예를 들어 자동차의 엑셀을 다른 부품으로 갈아 끼웠을 때에도 엑셀을 밟으면 자동차는 여전히 앞으로 전진해야 한다.
만약 반대로 차가 후진하는 엑셀을 끼운 경우, 자동차에 호환은 될지라도(컴파일이 성공할지라도) 프로그램의 의도에
맞지 않게 동작하므로 정확성이 깨진다. 이런 경우 리스코프 치환 원칙이 위배되었다고 말한다.


## I: Interface segregation principle (ISP, 인터페이스 분리 원칙)

특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다는 원칙이다.
이로써 클라이언트는 자신이 꼭 필요한 메서드들만 가지고 있게 된다.
따라서 인터페이스가 명확해지고 대체 가능성이 높아진다.
예를 들어, 만약 인터페이스가 크면 조그마한 목적을 가진 클라이언트 하나를 생성하는 데에도
쓰지도 않을 메서드를 구현하느라 시간을 낭비할 것이니 대체 가능성이 높을리 없다.


## D: Dependency inversion principle (DIP, 의존관계 역전 원칙)

- 프로그래머는 "추상화에 의존해야지, 구체화에 의존하면 안된다." 즉, 역할에 집중해야 한다.
- 구체화에 의존한 예시:
    ```java
    public class Service {
        // repository가 무엇인지 구체적으로 설명된 구현 클래스를 직접 사용함
        private Repository repository = new MemoryRepository();
    }
    ```
- 추상화에 의존한 예시:
    ```java
    public class Service {
        private Repository repository;

        // repository가 구체적으로 뭔지 잘 모르겠지만 일단 사용은 할 수 있도록 처리함.
        public Service(Repository repository) {
            this.repository = repository;
        }
    }
    ```
