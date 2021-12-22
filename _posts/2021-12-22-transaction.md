---
title: Transcation이란?
date: 2021-12-22 00:00:00 +0900
categories: [DataBase]
tags: [DataBase]
---

## 트랜잭션이란?

---

완결성 있게 처리되어야 하는 하나의 논리적인 작업단위를 말합니다.
이 논리적 작업 단위 내에는 여러동작 ( 질의, query ) 들이 존재하며 이들은 모두 실행되거나 모두 실행되지 않아야 합니다. 만약 작업단위가 중간에 실행이 중단 됐을 경우, 처음부터 다시 실행하는 rollback을 수행하고, 성공한다면 commit 하여 현재 데이터를 확정짓습니다.

**즉, 한번 질의가 실행되면 질의가 모두 수행되거나 모두 수행되지 않는 작업수행의 논리적 단위 입니다.**

## 특성

---

트랜잭션은 아래와 같은 4가지 특성이 있습니다.

4가지 특성의 앞 글자만 따서 **ACID 특성**이라 칭합니다.

- **원자성 ( Atomicity )**
  - 트랜잭션의 작업이 부분적으로 실행되거나 중단되지 않는 것을 보장하는 것을 말합니다.
  - 즉, All or Noting의 개념으로서 작업 단위를 일부분만 실행하지 않는다는 것을 의미합니다.
- **일관성 ( Consistency )**
  - **트랜잭션이 실행을 성공적으로 완료하면 언제나 일관성 있는 데이터베이스 상태로 유지하는 것을 의미합니다.**
  - 여기서 말하는 일관성이란, DB의 상태 ( ex. 기본키 외래키 제약과 같은 무결성 제약, DataType 등 )가 일관되는 것을 뜻합니다.
- **독립성 ( Isolation )**
  - **트랜잭션 수행시 다른 트랜잭션의 작업이 끼어들지 못하도록 보장하는 것을 말합니다.**
  - **즉, 트랜잭션끼리는 서로를 간섭할 수 없습니다.**
- **지속성 ( Durability )**
  - **성공적으로 수행된 트랜잭션은 영원히 반영이 되는 것을 말합니다.**
  - **commit을 하면 현재 상태는 영원히 보장됩니다.**

<hr class="end-line">

> references
>
> - [https://junhyunny.github.io/information/transcation-acid/](https://junhyunny.github.io/information/transcation-acid/)
> - [https://victorydntmd.tistory.com/129](https://victorydntmd.tistory.com/129)
> - [https://ko.wikipedia.org/wiki/ACID](https://ko.wikipedia.org/wiki/ACID)
