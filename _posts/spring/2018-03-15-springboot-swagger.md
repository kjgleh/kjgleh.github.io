---
title: 'Spring boot swagger'
layout: post
categories: spring
---

## dependency
```kotlin
implementation("io.springfox:springfox-boot-starter:3.0.0")
compileOnly("io.springfox:springfox-swagger-ui:3.0.0")
```

## SwaggerConfiguration
```kotlin
@Configuration
class SwaggerConfiguration {

    @Bean
    fun api(): Docket {
        return Docket(DocumentationType.SWAGGER_2)
            .apiInfo(this.apiInfo())
            .select()
            .apis(RequestHandlerSelectors.basePackage("me.kjgleh.myshop"))
            .paths(PathSelectors.ant("/api/**"))
            .build()
            .securitySchemes(listOf(apiKey()))
    }

    private fun apiKey(): SecurityScheme {
        return ApiKey("Bearer JWT", "Authorization", "header")
    }

    private fun apiInfo(): ApiInfo {
        return ApiInfoBuilder()
            .title("Myshop API")
//            .description("Myshop API")
            .build()
    }

}
```

## Controller
```kotlin
@RestController
@Api(tags = ["Members"], description = " ")
class MemberController(
    private val findMemberService: FindMemberService
) {

    @ApiOperation(
        value = "",
        authorizations = [Authorization(value = "Bearer JWT")]
    )
    @GetMapping("/api/me")
    fun me(@ApiIgnore @RequestHeader(name = "Authorization") authorization: String): ResponseEntity<MemberView> {
        val memberView =
            findMemberService.findMember(authorization)

        return ResponseEntity.status(HttpStatus.OK).body(memberView)
    }
}
```



