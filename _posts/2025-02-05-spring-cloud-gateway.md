---
layout: post
title: Spring Cloud Gateway
subtitle: 
cover-img:
thumbnail-img: 
share-img: /assets/img/path.jpg
tags: [Spring, Spring Cloud Gateway, Circuit Breaker, Load Balancing, Spring Webflux]
author: keylearn
---

Spring Cloud Gateway is an API gateway that provides a way to route requests to multiple micro-services. It is designed to be a simple and lightweight solution for routing HTTP requests. It is built on top of the Spring Boot framework and provides a simple API for configuring routing rules.

Let’s discuss more about Spring Cloud Gateway , how it works, and its benefits.

Spring Cloud Gateway works by providing a way to route requests based on different criteria. The criteria can include the request method, the URI path, the headers, and other parameters. When a request comes in, Spring Cloud Gateway checks the routing rules and forwards the request to the appropriate micro-service.

Spring Cloud Gateway also provides a way to handle errors and fallback requests. If a request fails to reach a micro-service, Spring Cloud Gateway can send a fallback response or redirect the request to a different micro-service.

It provides a reactive and non-blocking approach to building API gateways in micro-services architecture using the Spring Framework. One of the key components of Spring Cloud Gateway is the integration with the Netty server, which is a high-performance, event-driven, asynchronous, and non-blocking I/O framework for building network applications.

**There are several benefits of using Spring Cloud Gateway:**

1. Simplified routing: Spring Cloud Gateway provides a simple and flexible way to route requests. This makes it easy to add new micro-services and update routing rules.

2. Load balancing: Spring Cloud Gateway can distribute requests to multiple instances of a micro-service. This helps to improve the performance and availability of your application.

3. Security: Spring Cloud Gateway provides a way to secure your application by handling authentication and authorization.

4. Monitoring: Spring Cloud Gateway provides metrics and monitoring capabilities to help you keep track of the performance of your application.

5.Resiliency ( remediation action during the failure of a service, rate limiting)

![Crepe](/assets/img/spring-cloud-gateway.webp)

Let’s try a demo application to understand the implementation of cloud gateway.

You can find the spring cloud gateway demo application in [here](https://github.com/lahirumw/spring-boot-samples/tree/main/spring-cloud-gateway-demo).

Also, download [payment application](https://github.com/lahirumw/spring-boot-samples/tree/main/payment-app) and [booking application](https://github.com/lahirumw/spring-boot-samples/tree/main/booking-app) as downstream application to test the gateway routing.

Routing rules are defined in a property file which is `application.properties` or `application.yml`. In this file, you can define the routes for your Spring Cloud Gateway application like below.

Also, you can define a Circuit Breaker for your routes using the `spring.cloud.gateway.routes.filters` property in your configuration file.

~~~
  cloud:
    gateway:
      routes:
        - id: route1
          uri: http://localhost:8081
          predicates:
            - Path=/booking/health
          filters:
            - name: CircuitBreaker
              args:
                name: circuitBreaker
                fallbackUri: forward:/fallback
                statusCodes:
                  - 404
~~~

To configure the Circuit Breaker in Spring Cloud, you can create a bean of type `Resilience4JCircuitBreakerFactory` and customize it with the desired configuration.

~~~
@Bean
  public Customizer<Resilience4JCircuitBreakerFactory> defaultCustomizer() {
    return factory -> factory.configureDefault(id -> new Resilience4JConfigBuilder(id)
            .timeLimiterConfig(TimeLimiterConfig.custom().timeoutDuration(Duration.ofSeconds(4)).build())
            .circuitBreakerConfig(CircuitBreakerConfig.ofDefaults())
            .build());
  }
~~~

**Steps to Run the App**

1. Download and start booking app. The application runs in port `8081`

2. Download and start payment app. The service is running in port `8082`

3. Finally, download and start gateway app, which runs in port `8080`

Let’s call `localhost:8080/booking/health` and `localhost:8080/payment/health`.

You will notice that Spring Cloud Gateway routes incoming requests to the relevant service based on the path specified in the URL.

That’s all for now. Let’s talk more about spring cloud gateway in future.

Thanks for reading.