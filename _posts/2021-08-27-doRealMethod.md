---
title: doRealMethod
date: 2021-08-27 02:00:00 +0900
categories: [Language, Java]
tags: [Mock, Junit, Java]
---

Mockito 프레임워크가 제공하는 Stub 기반의 테스트 방법 중

- doRealMethod()

를 알아보겠습니다.

### doRealMethod() 란?

_Mock 실행 객체 참조를 사용하여 메서드를 참조할 때 java는 해당 메서드에 대한 호출을 전혀 하지 않으며, 지정된 값이 없으면 모의 실행 값을 반환하거나 null을 찾습니다. 하지만 만약 우리가 그것을 재정의하고 java가 모의 객체 참조를 사용하여 실제 메소드를 호출하기를 원한다면, 이 메소드를 사용할 수 있습니다._

> _출처:_ [https://javasearch.buggybread.com/InterviewQuestions/questionSearch.php?searchOption=question&keyword=3813](https://javasearch.buggybread.com/InterviewQuestions/questionSearch.php?searchOption=question&keyword=3813)

테스트 코드를 통해 알아보겠습니다.

Study.class

```java
@Getter
@Setter
@NoArgsConstructor
public class Study {

    public void doRealMethodTest(){
        System.out.println("테스트입니다");
    }
}
```

StudyServiceTest.class

```java
@ExtendWith(MockitoExtension.class)
class StudyServiceTest {

    @Mock Study mockStudy;

    @Test
    void createDoRealMethodTest(){
        mockStudy.doRealMethodTest();
    }

}
```

Mock 으로 생성한 객체의 메소드를 실행하면 어떻게 될까요?

Mock 의 개념을 이해하지 못했다면 해당 메소드가 실행될것이라고 예상할 수도 있습니다.

**결과**

![junit](/assets/img/postresource/2021-08-27-doRealMethod1.png)

하지만 Mock 객체의 정의는 다음과 같습니다.

- 진짜 객체와 비슷하게 동작하지만 프로그래머가 직접 그 객체의 행동을 관리하는 객체.

그리고

**모든 Mock 객체의 행동은 다음과 같습니다.**

- Null을 리턴한다. (Optional 타입은 Optional.empty 리턴)
- Primitive 타입은 기본 Primitive 값.
- 콜렉션은 비어있는 콜렉션.
- Void 메소드는 예외를 던지지 않고 아무런 일도 발생하지 않는다.

이러한 이유로 Mock으로 생성한 객체의 메소드를 호출 해도 자바는 해당 객체의 메소드 호출을 하지 않습니다.

**doRealMethod() 메소드는 이러한 문제점을 해결해 줍니다.**

### **doRealMethod() 사용**

StudyServiceTest.class

```java
@ExtendWith(MockitoExtension.class)
class StudyServiceTest {

    @Mock Study mockStudy;

    @Test
    void createDoRealMethodTest( ){
        Mockito.doCallRealMethod().when(mockStudy).doRealMethodTest();
        mockStudy.doRealMethodTest();
    }

}
```

**결과**

![junit2](/assets/img/postresource/2021-08-27-doRealMethod2.png)

doRealMethod()를 이용하여 Mock 객체로 생성한 객체의 실제 메소드를 호출하는 모습을 볼 수 있습니다.
