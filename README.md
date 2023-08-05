# Spring Reactive 연습

[https://spring.io/guides/gs/reactive-rest-service/](https://spring.io/guides/gs/reactive-rest-service/)

## Note

### Spring WebFlux

non-blocking, reactive streams을 지원하는 리액티브 웹 스택

[https://docs.spring.io/spring-framework/reference/web/webflux.html](https://docs.spring.io/spring-framework/reference/web/webflux.html)

### Basic Usage

```java
// handler
@Component
public class GreetingHandler {

    public Mono<ServerResponse> hello(ServerRequest request) {
        return ServerResponse.ok().contentType(MediaType.APPLICATION_JSON)
                .body(BodyInserters.fromValue(new Greeting("Hello, Spring!")));
    }
}

// router
@Configuration(proxyBeanMethods = false)
public class GreetingRouter {

    @Bean
    public RouterFunction<ServerResponse> route(GreetingHandler greetingHandler) {
        return RouterFunctions
                .route(GET("/hello").and(accept(MediaType.APPLICATION_JSON)), greetingHandler::hello);
    }
}

// client
@Component
public class GreetingClient {

    private final WebClient client;

    public GreetingClient(WebClient.Builder builder) {
        this.client = builder.baseUrl("http://localhost:8080").build();
    }
}
```

## WIL

- Client, Router, Handler 구조와 기초적인 사용법
- MONO vs FLUX: MONO는 0-1개의 응답, FLUX는 0-N개의 응답
- `WebClient`는 논리액티브, 블로킹 서비스와도 사용할 수 있다.

## Trivia

### Apple silicon 맥에서 발생하는 netty 오류 처리

> Unable to load io.netty.resolver.dns.macos.MacOSDnsServerAddressStreamProvider, fallback to system defaults. This may
> result in incorrect DNS resolutions on MacOS. Check whether you have a dependency on 'io.netty:
> netty-resolver-dns-native-macos'. Use DEBUG level to see the full stack: java.lang.UnsatisfiedLinkError: failed to load
> the required native library

디펜던시를 추가하되, classifier에 "osx-aarch_64"를 반드시 명시한다. (build.gradle 파일 참고)

`io.netty:netty-resolver-dns-native-macos:4.1.94.Final:osx-aarch_64`

[https://github.com/netty/netty/issues/11020](https://github.com/netty/netty/issues/11020)