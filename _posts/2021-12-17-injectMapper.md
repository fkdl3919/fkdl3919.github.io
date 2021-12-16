---
title: (MyBatis) Mapper등록
date: 2021-12-17 00:00:00 +0900
categories: [Java, MyBatis]
tags: [Java, MyBatis]
---

mybatis-spring 프레임워크를 사용할 때 mapper를 springContext에 주입하는 방법으로 크게 2가지가 있습니다

1. Mapper 수동 등록
2. Mapper 스캔을 이용한 자동 등록

두가지 방법을 알아보겠습니다.

> 목차

- [Mapper 수동 등록하기](#mapper-수동-등록하기)
  - [XML설정 사용](#xml설정-사용)
- [Mapper 스캔](#mapper-스캔)
  - [I. mybatis:scan 엘리먼트 사용](#i-mybatisscan-엘리먼트-사용)
    - [base-package](#base-package)
    - [annotation](#annotation)
    - [marker-interface](#marker-interface)
  - [II. 스프링 XML파일을 사용해서 `MapperScannerConfigurer`를 등록](#ii-스프링-xml파일을-사용해서mapperscannerconfigurer를-등록)

## Mapper 수동 등록하기

---

1. **XML설정 사용**
2. **자바설정 사용**

Mapper 수동등록은 XML을 이용한 Mapper 등록을 알아보겠습니다.

### XML설정 사용

매퍼는 다음처럼 XML설정파일에 `MapperFactoryBean`을 두는 것으로 스프링에 등록됩니다.

```xml
<!--1. Mapper 등록-->
<bean id="userMapper" class="org.mybatis.spring.mapper.MapperFactoryBean">
    <property name="mapperInterface" value="com.gil.mybatis.mapper.MyBatisMapper" />
    <property name="sqlSessionTemplate" ref="sqlSessionTemplate" />
</bean>
```

## Mapper 스캔

---

Mybatis는 스프링과 같이 사용 할 때 Dao(Mapper) 를 빈에 주입할 수 있는 다양한 방법을 제공합니다.

1. `<mybatis:scan/>` 엘리먼트 사용
2. `@MapperScan` 애노테이션 사용
3. 스프링 XML파일을 사용해서 `MapperScannerConfigurer`를 등록

2번 방법은 1번방법과 xml방식 클래스방식의 차이만 있을 뿐 같은 기능이기 때문에 따로 다루지 않겠습니다.

### I. mybatis:scan 엘리먼트 사용

기본적으로 제일 편리하고 사용하기 쉬운 방법입니다.

`<mybatis:scan/>` 은 다음과 같은 속성을 가지고 있습니다.

- base-package ( required )
- annotation
- marker-interface
- factory-ref
- template-ref
- default-scope
- lazy-initialization
- mapper-factory-bean-class
- name-generator

이 글에서는

- base-package
- annotation
- marker-interface

를 알아보겠습니다.

#### base-package

mapper를 주입시켜줄 package를 지정합니다.

```xml
<mybatis:scan base-package="com.gil.mybatis.mapper"/>
```

해당 패키지에 아래에 있는 모든 interface들을 mapper로 등록해 줍니다.

#### annotation

mapper를 검색할 annotation을 등록할 수 있습니다.

**`<mybatis:scan/>` 설정**

```xml
<mybatis:scan base-package="com.gil.mybatis.mapper" annotation="com.gil.utils.GilMapper" />
```

**mapper를 명시해줄 annotation**

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface GilMapper{
}
```

`<mybatis:scan/>` 엘리먼트에 annotation 속성을 추가하여 basePackage 내의 mapper들을 필요에 따라 필터링 할 수 있습니다.

![](/assets/img/postresource/2021-12-17-injectMapper1.png)

2개의 mapper가 basePackage 내에 만들어져 있습니다.

```java
@GilMapper
public interface MyBatisMapper {
    MybatisVo requestUser();
}

public interface MyBatisMapper2 {
    MybatisVo requestUser();
}
```

위 코드와 같이 둘 중 하나의 Mapper에게 어노테이션을 부여하였습니다.

**테스트 코드**

```java
@Test
@DisplayName("Mapper 주입 테스트")
public void mapperInjectTest(){
    assertDoesNotThrow(() -> {
        MyBatisMapper myBatisMapper1 = context.getBean(MyBatisMapper.class);
    }, "MyBatisMapper1 은 null 입니다.");

    assertDoesNotThrow(() -> {
        MyBatisMapper2 myBatisMapper2 = context.getBean(MyBatisMapper2.class);
    }, "MyBatisMapper2 은 null 입니다.");
}
```

위 두 mapper인터페이스 중 하나의 mapper에만 어노테이션을 부여하여 원하는 의도대로 mapper스캔이 이루어 졌는지 assertDoesNotThrow메소드를 이용하여 context에 등록되지 않았다면 오류메세지가 출력되도록 테스트코드를 작성하였습니다.

**결과**

![](/assets/img/postresource/2021-12-17-injectMapper2.png)

예상대로 어노테이션이 입력되지 않은 mapper가 context에 등록되지 않아 NoSuchBeanDefinitionException 을 발생시키는 걸 볼 수 있습니다.

#### marker-interface

`marker-interface` 프로퍼티는 검색할 상위 인터페이스를 지정할 수 있습니다.

**`<mybatis:scan/>` 설정**

```xml
<mybatis:scan base-package="com.gil.mybatis.mapper" marker-interface="com.gil.utils.BaseGilMapper" />
```

**상위 인터페이스**

```java
public interface BaseGilMapper {
    MybatisVo requestUser();
}
```

**상위 인터페이스를 상속받은 mapper**

```java
public interface MyBatisMapper extends BaseGilMapper {
}
```

위와 같이 marker-interface로 설정해준 상위 인터페이스를 상속받은 mapper 인터페이스만 context에 등록 되게 됩니다. 동시에 상위 인터페이스의 메소드를 사용함으로써 자주 사용하는 기능들을 매번 작성해야하는 수고를 덜 수 있습니다.

**MyBatisMapper를 namespace로 가지는 xml**

```xml
<mapper namespace="com.gil.mybatis.mapper.MyBatisMapper">
    <select id="requestUser" resultType="mybatisVo">
        select *
        from tb_user
        limit 1
    </select>
</mapper>
```

**테스트 코드**

```java
@Test
@DisplayName("Mapper 주입 테스트")
public void mapperInjectTest(){
    assertDoesNotThrow(() -> {
        MyBatisMapper myBatisMapper1 = context.getBean(MyBatisMapper.class);
    }, "MyBatisMapper1 은 null 입니다.");

    assertDoesNotThrow(() -> {
        MyBatisMapper2 myBatisMapper2 = context.getBean(MyBatisMapper2.class);
    }, "MyBatisMapper2 은 null 입니다.");
}
```

테스트 코드는 위의 annotation 프로퍼티를 이용한 테스트와 같은 조건으로 MyBatisMapper 인터페이스만 상위 인터페이스 상속을 받아 진행하였습니다.

**결과**

![](/assets/img/postresource/2021-12-17-injectMapper3.png)

상위 인터페이스를 상속받은 mapper인터페이스 만 context에 주입되는 것을 확인 할 수 있습니다.

**또 한가지**

저는 `marker-interface` 를 이용한 상속 방법이 과연 xml매퍼 파일로 등록된 쿼리문을 실행시킬 수 있을지 궁금하였습니다.

**테스트 코드**

`marker-interface`를 상속받은 MyBatisMapper의 `requestUser` 메소드를 실행하는 service 클래스입니다.

```java
@Service
public class MyBatisServiceImpl implements MyBatisService {

    private final MyBatisMapper myBatisMapper;

    public MyBatisServiceImpl(MyBatisMapper myBatisMapper) {
        this.myBatisMapper = myBatisMapper;
    }

    @Override
    public MybatisVo requestTest1() {
        return myBatisMapper.requestUser();
    }
}
```

```java
@Test
@DisplayName("유저 조회 테스트")
public void userSelectTest(){
    MybatisVo mybatisVo = myBatisService.requestTest1();
    System.out.println("--------------------------------");
    System.out.println("mybatisVo = " + mybatisVo);
    System.out.println("--------------------------------");

}
```

**결과**

![](/assets/img/postresource/2021-12-17-injectMapper4.png)

`marker-interface` 를 상속받은 메소드도 xml매퍼 파일에 접근이 가능하다는걸 볼 수 있습니다.

### II. 스프링 XML파일을 사용해서 `MapperScannerConfigurer`를 등록

```xml
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="com.gil.mybatis.mapper"/>
    <property name="sqlSessionTemplateBeanName" value="sqlSessionTemplate"/>
</bean>
```

MapperScannerConfigurer을 bean으로 등록해주는 방법으로도 mapper들을 등록할 수 있습니다.

<hr class="end-line">

> Github source
>
> - [https://github.com/fkdl3010/java-and-spring/tree/main/5_mybatis/injectMapper](https://github.com/fkdl3010/java-and-spring/tree/main/5_mybatis/injectMapper)
>
> references
>
> - [mybatis-spring 공식홈페이지](https://mybatis.org/spring/ko/mappers.html)
> - [https://okky.kr/article/448042](https://okky.kr/article/448042)
