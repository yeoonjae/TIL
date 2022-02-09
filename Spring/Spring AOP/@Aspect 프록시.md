# **@Aspect 프록시**

## **@Aspect 프록시란?**

스프링 애플리케이션에 프록시를 적용하기 위해선 `포인트컷 + 어드바이스` 로 구성되어있는 어드바이저를 만들어서 스프링 빈으로 등록하면 자동 프록시 생성기(`AnnotationAwareAspectJAutoProxyCreator`)가 모두 자동으로 처리해줍니다. 

자동 프록시 생성기(`AnnotationAwareAspectJAutoProxyCreator`)는 스프링 빈으로 등록된 어드바이저들을 모두 찾고, 스프링 빈들에 자동으로 프록시를 적용시켜줍니다. 

스프링은 `@Aspect` 어노테이션으로 편리하게 `포인트컷 + 어드바이스`로 구성된 어드바이저 생성 기능을 지원합니다. 

>  `@Aspect`는 관점 지향 프로그래밍(AOP)을 가능하게 하는 `AspectJ` 프로젝트에서 제공하는 어노테이션입니다. 스프링은 이것을 차용하여 프록시를 통한 AOP 를 가능하게 합니다. 

---
## **@Aspect 프록시 동작 방식**
```java
@Aspect // @Aspect
public class LogTraceAspect {

  @Around("execution(* hello.proxy.app..*(..))") // 포인트컷
  public Object execute(ProceedingJoinPoint joinPoint) throws {
    // Advice 로직
  }
  
}
```

위의 코드와 같이 어드바이저가 있습니다. 자동 프록시 생성기(`AnnotationAwareAspectJAutoProxyCreator`)는 `@Aspect`를 찾아서 이것을 어드바이저로 만들어줍니다. 이름에 Annotation~~ 으로 시작하는 것처럼 어노테이션을 인식합니다. 

**@Aspect 가 어드바이저로 변환하여 저장하는 과정**은 다음과 같습니다. 
1. **실행** : 스프링 애플리케이션 로딩 시점에 자동 프록시 생성기를 호출한다. 
2. **모든 `@Aspect` 빈 조회**: 자동 프록시 생성기는 스프링 컨테이너에서 `@Aspect` 애노테이션이 붙은 스프링 빈을 모두 조회한다.
3. **어드바이저 생성**: `@Aspect` 어드바이저 빌더를 통해 `@Aspect` 애노테이션 정보를 기반으로 어드바이저를 생성한다.
4. **`@Aspect` 기반 어드바이저 저장**: 생성한 어드바이저를 `@Aspect` 어드바이저 빌더 내부에 저장한다.

![image](https://user-images.githubusercontent.com/63777714/152910722-38eab2ff-2957-4bec-b81f-54873bda6e8f.png)

> 🔍 **Aspect 어드바이저 빌더** <br>
> Aspect 어드바이저 빌더는 `BeanFactoryAspectJAdvisorsBuilder` 클래스로, `@Aspect`의 정보를 기반으로 포인트컷, 어드바이저, 어드바이스를 생성하고 보관하는 것을 담당합니다. `@Aspect`의 정보를 기반으로 어드바이저를 반들고, `@Aspect` 어드바이저 빌더 내부 저장소에 캐시합니다. 캐시에 어드바이저가 이미 만들어져 있는 경우엔 저장된 어드바이저를 반환합니다. (재사용)

다음은 자동 프록시 생성기의 작동 과정을 나타낸 그림입니다. 

![image](https://user-images.githubusercontent.com/63777714/152911657-6796c167-0a4e-44f0-bcb6-5b32d71c2ca6.png)

1. 스프링 빈 대상이 되는 객체 생성
2. 생성된 객체를 빈 저장소에 등록하기 **전**에 빈 후처리기에 전달
3. Advisor 빈 조회 & @Aspect Advisor 조회
4. 프록시 적용 대상 체크 : 3에서 조회한 Advisor에 있는 포인트컷을 사용하여 해당 객체가 프록시를 적용할 대상인지 아닌지 판단
5. 프록시 생성 : 프록시 적용 대상이면 프록시 생성 후 프록시를 반환, 아닐 경우 원본 객체를 반환
6. 반환된 객체를 스프링 빈으로 등록

---
참고
- 스프링 핵심 원리-고급편 (인프런_김영한님)