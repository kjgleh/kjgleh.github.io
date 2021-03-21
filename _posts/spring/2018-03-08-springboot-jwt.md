---
title: 'Spring boot jwt'
layout: post
categories: spring
---

## dependency
```kotlin
implementation("io.jsonwebtoken:jjwt:0.9.1")
```

## Bearer JWT를 decode한다.
```kotlin
companion object {
    private const val SIGNING_KEY = "my-signing-key"
}

fun getData(authorization: String): String {
    // authorization: Bearer JWT
    val claims = Jwts
        .parser()
        .setSigningKey(SIGNING_KEY.toByteArray())
        .parseClaimsJws(authorization.substring("Bearer ".length)).body

    return claims["data"].toString()
}
```



