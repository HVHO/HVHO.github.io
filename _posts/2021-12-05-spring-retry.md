---
layout: post
title: "[ Spring Framework ] spring retry 로 간편하게 재수행 로직 추가하기" 
description: Retyrable 어노테이션의 사용법을 알아보자  
comments: true
tags: Spring
---


> Spring Framework 의 Retry 사용법을 정리한 글입니다


# 들어가며..

서비스를 개발하다 보면 실패에 따른 재수행 로직을 추가하고 싶어지는 때가 오는데, 이때 Spring Framwork 의 Retry 사용하면 어노테이션 기반으로 간편하게 재수행 로직을 추가할 수 있다.

# Retry Template VS Retry Annotation

spring retry 를 사용하는 방법에는 
- 프로그래밍 방식 ( Retry Template ) 
- 선언적 방식 ( Retry Annotation )
이 있다. 

전자는 아래처럼 명시적으로 `RetryTemplate` 를 사용하여 Retry 를 실행할 callback 을 RetryTemplate 에 넘겨주는 방식이고,
<script src="https://gist.github.com/HVHO/22a035816f597b8afbc5c17c266c6467.js"></script>


후자는 Retry 를 적용할 method 에 `Retryable` 어노테이션을 적용시켜주면 스프링 AOP 에서 위임받아 Retry 를 적용시켜 주는 방식이다.
<script src="https://gist.github.com/HVHO/922cf53e07e9eaef30e3174fbea7ab73.js"></script>

`RetryTemplate` 을 사용하면 RetryTemplate 에 대한 설정과, Retry 할 메소드를 직접 람다로 말아서 넣어줘야 하는 불편함 때문에 annoation 방식을 선호한다.  
따라서 이 글에서는 `Retryable` 어노테이션을 사용한 방법을 기준으로 설명하려 한다.

# Retryable 설정하기

## Dependency 추가 

`build.gradle` 에 아래와 같이 depencency 를 추가해준다  
선언적 방식의 Retry 는 `Spring AOP` 를 사용하기 때문에 Aspect depenency 까지 추가해주어야 한다.
<script src="https://gist.github.com/HVHO/88fdb318c6382f3bb4c1fbe72bcbc506.js"></script>

## Retry Enable 하기 
설정 파일에 `@EnableRetry` 어노테이선을 추가하여 Retry 설정을 켜준다

<script src="https://gist.github.com/HVHO/847d167d2b5e1c028e6399423ae74b5a.js"></script>

## 원하는 메소드에 Retryable 어노테이션 추가하기

실패시 Retry 를 원하는 메소드에 Retryable 어노테이션을 추가한다. 

<script src="https://gist.github.com/HVHO/ad928339b11b16b733506daf0769a872.js"></script>

## Retry 동작 확인

위 코드에 나와있는 `randomFail` 함수는 일정 확률로 `RuntimeException` 을 throw 한다.  
`randomFail` 을 부르는 `doWithRetry` 함수에 `Retryable` 어노테이션을 달았으니,  
어노테이션에 의해 최대 3번( 기본 설정이 최대 3번 )지 함수호출을 시도하고 그때도 실패한다면 `Exception` 을 throw 한다.

![failed-1-time](https://user-images.githubusercontent.com/25953981/144748938-46b7225a-d56e-48e6-b5d6-86f911ba4589.png)*1번 재시도후 성공*

![failed-3-times](https://user-images.githubusercontent.com/25953981/144748893-52570731-3871-4cfa-bbc8-64972bd5c324.png)*3번의 시도에도 실패하여 Exception 발생*

# Retryalbe 추가 설정

위처럼 단순하게 특정 회수를 재수행하게 로직을 설정할 수도 있지만, 더 복잡힌 Retry 로직도 지원한다.

## 특정 Exception 에만 Retry 시도하도록 설정하기

`Retryable` 어노테이션에 `include` 를 설정해주면 특정 Exception 에 대해서만 Retry 를 시도하게 설정할 수 있다.  
List 형태를 지원하기 때문에 여러 Exception 을 잡는것도 가능하다.

<script src="https://gist.github.com/HVHO/98ac24dc7a162b4127b7a1339069131d.js"></script>

## 최대 Retry 회수와 Retry 간의 Delay 설정하기

위와 동일하게 `backOff` 를 설정해주면 재시도 간의 time delay 를 설정할 수 있다.  
외부 서버의 요청을 retry 하고싶은데, rate limit 등의 이유로 요청간의 일정한 시간 간격을 가져야 할 경우에 유용하다.  

<script src="https://gist.github.com/HVHO/7a9878c5fc94ce4cebab739a7740291e.js"></script>

## Retry Listener 설정하기

재시도를 수행할 때, 두 수행 사이에 특정 동작을 하게 하는 리스너 을 구성할 수 있다.  
아래처럼 `RetryListner` 인터페이스를 구현한 구현체를 bean 으로 등록하여 어노테이션의 `retryListner` 에 입력하면,  
해당 메소드를 실행하기 전/중/후 에 리스너가 동작한다.

내부 동작은 open -> 타켓 메소드 실행(실패) -> onError -> 타겟 메소드 실행(성공) -> close 의 순서대로 동작한다.

<script src="https://gist.github.com/HVHO/f03e01444f86fe71b73ab6c3bd44a858.js"></script>

## Recover 설정하기

특정 Exception 에 대한 Retry 를 수행해서 최종적으로 Exception 이 발생하면, 특정 동작을 수행하게 할 수 있다.  
주의할 점은 처음 exception 인자를 제외한 나머지 입력/출력이 타겟 메소드와 동일해야 한다는 점이다.

<script src="https://gist.github.com/HVHO/89429ab83798e114cd7c190b1f3bf83b.js"></script>



# 함께 읽으면 좋은 글
- [Spring Retry 공식문서](https://docs.spring.io/spring-batch/docs/current/reference/html/retry.html)
- [Spring Retry Review](https://daddyprogrammer.org/post/12091/spring-retry-review/)