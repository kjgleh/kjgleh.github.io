---
title: 'Springboot oauth2 구글 로그인 구현'
layout: post
categories: springboot
---

## source
- <https://github.com/kjgleh/springboot-aouth2>

## dependency
```kotlin
implementation("org.springframework.boot:spring-boot-starter-oauth2-client")
```

## application.yml
```yaml
spring:
    security:
        oauth2:
            client:
                registration:
                    google:
                        client-id: client-id
                        client-secret: client-secret
                        scope: profile,email
```

## 로그인 화면
```html
<div class="col-md-12">
    <div class="row">
        <div class="col-md-6">
            // 로그인 된 경우
                Logged in as: <span id="user">사용자 이름</span>
                <a href="/logout" class="btn btn-info active" role="button">Logout</a>
            // 로그인 된 경우
            // 로그인 되지 않은 경우
                <a href="/oauth2/authorization/google" class="btn btn-success active" role="button">Google Login</a>
            // 로그인 되지 않은 경우
        </div>
    </div>
    <br>
</div>
```
- /oauth2/authorization/google: spring oauth2에서 제공하는 기본 로그인 경로
- /logout: spring oauth2에서 제공하는 기본 로그아웃 경로

## Security config
```kotlin
@EnableWebSecurity
class SecurityConfig : WebSecurityConfigurerAdapter() {

    @Autowired
    private lateinit var customOAuth2UserService: CustomOAuth2UserService

    override fun configure(http: HttpSecurity) {
        http
            .csrf().disable()
            .headers().frameOptions().disable()

            .and()
            .authorizeRequests()
            .antMatchers(
                "/",
                "/css/**",
                "/images/**",
                "/js/**",
                "/h2-console/**"
            ).permitAll()
            .antMatchers("/api/v1/**").hasRole(Role.USER.name)
            .anyRequest().authenticated()

            .and()
            .logout()
            .logoutSuccessUrl("/")

            .and()
            .oauth2Login() // OAuth2 로그인 기능에 대한 진입점
            .userInfoEndpoint() // OAuth2 로그인 성공 이후 사용자 정보를 가져올 때의 설정을 담당
            .userService(customOAuth2UserService) // 소셜 로그인 성공 시 후속 조치를 진행할 UserService 인터페이스의 구현체 등록
    }
}
```

## CustomOAuth2UserService
```kotlin
@Service
class CustomOAuth2UserService(
    private val userRepository: UserRepository,
) : OAuth2UserService<OAuth2UserRequest, OAuth2User> {

    override fun loadUser(userRequest: OAuth2UserRequest): OAuth2User {
        val delegate = DefaultOAuth2UserService()
        val oAuth2User = delegate.loadUser(userRequest)

        val registrationId = userRequest.clientRegistration.registrationId;
        val userNameAttributeName =
            userRequest.clientRegistration.providerDetails
                .userInfoEndpoint.userNameAttributeName

        val attributes = OAuthAttributes.of(
            registrationId,
            userNameAttributeName,
            oAuth2User.attributes
        )

        val user = saveOrUpdate(attributes)

        return DefaultOAuth2User(
            setOf(SimpleGrantedAuthority(user.role.key)),
            attributes.attributes,
            attributes.nameAttributeKey
        )
    }

    private fun saveOrUpdate(attributes: OAuthAttributes): User {
        val user = userRepository.findByEmail(attributes.email).map {
            it.update(
                name = attributes.name,
                picture = attributes.picture
            )
            it
        }.orElse(attributes.toEntity())
        return userRepository.save(user)
    }
}
```


