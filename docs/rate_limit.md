# Rate Limiting with Bucket4j - Spring Boot

## Overview
Implementation of API rate limiting using Bucket4j with **10 requests per minute** limit. Requests exceeding the limit return **HTTP 409 Conflict**.

This document describes the implementation of rate limiting for API endpoints using the Token Bucket algorithm with Bucket4j library in Spring Boot. The solution limits requests to 10 requests per minute and returns HTTP 409 Conflict for exceeded requests.

The token bucket algorithm works as follows:

A bucket has a fixed capacity (10 tokens)
Tokens are added to the bucket at a fixed rate (10 tokens per minute)
Each request consumes one token
If no tokens available, request is rejected with HTTP 409

## Dependencies
```xml
<dependency>
    <groupId>com.bucket4j</groupId>
    <artifactId>bucket4j-core</artifactId>
    <version>8.7.0</version>
</dependency>
```

## Implementation

### 1. Configuration
```java
@Configuration
public class RateLimitConfig {
    @Bean
    public Bucket createBucket() {
        Bandwidth limit = Bandwidth.classic(10, Refill.intervally(10, Duration.ofMinutes(1)));
        return Bucket.builder().addLimit(limit).build();
    }
}
```

### 2. Interceptor
```java
@GetMapping
public ResponseEntity<APIResponse> getStudents() {
    ConsumptionProbe probe = bucket.tryConsumeAndReturnRemaining(1);
    if (probe.isConsumed()) {
        logger.info("Allowed - remaining requests: {}", probe.getRemainingTokens());
        List<Student> students = studentRepository.findAll();
        APIResponse response = APIResponse.builder()
                .status(HttpStatus.OK)
                .message(APIResponseMessage.SUCCESSFULLY_RETRIEVED.name())
                .data(students)
                .build();
        return ResponseEntity.ok(response);
    }
    logger.info("Over limit");

    return ResponseEntity.status(HttpStatus.CONFLICT).build();
}
```

