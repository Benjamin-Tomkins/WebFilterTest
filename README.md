# SecurityConfig.kt

```
bare bones auth filter
doesn't need spring security
```

```
package com.itid.notifications

import org.springframework.context.annotation.Bean
import org.springframework.context.annotation.Configuration
import org.springframework.http.HttpStatus
import org.springframework.stereotype.Component
import org.springframework.web.reactive.function.client.WebClient
import org.springframework.web.server.ServerWebExchange
import org.springframework.web.server.WebFilter
import org.springframework.web.server.WebFilterChain
import reactor.core.publisher.Mono

@Configuration
class SecurityConfig {
    @Bean fun webClient(builder: WebClient.Builder) = builder.build()
}

@Component
class SecurityWebFilter(private val webClient: WebClient) : WebFilter {

    override fun filter(exchange: ServerWebExchange, chain: WebFilterChain): Mono<Void> {
        return webClient.get()
            .uri("https://httpstat.us/200") //.uri("https://httpstat.us/401")
            .retrieve()
            .toBodilessEntity()
            .flatMap { chain.filter(exchange) }
            .onErrorResume {
                exchange.response.statusCode = HttpStatus.UNAUTHORIZED
                exchange.response.setComplete()
            }
    }

}
```
