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