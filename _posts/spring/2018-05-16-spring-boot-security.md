---
title: 'Spring boot Security'
layout: post
categories: spring
---

## dependency 추가
```groovy
implementation 'org.springframework.boot:spring-boot-starter-security'
testImplementation 'org.springframework.security:spring-security-test'
```

## Spring boot Security 자동 설정
자동 설정 클래스는 SecurityAutoConfiguration이다. <br/>
SecurityAutoConfiguration은 SpringBootWebSecurityConfiguration 클래스를 import하고 있다.

```java
@Configuration
@ConditionalOnClass(DefaultAuthenticationEventPublisher.class)
@EnableConfigurationProperties(SecurityProperties.class)
@Import({ SpringBootWebSecurityConfiguration.class, WebSecurityEnablerConfiguration.class,
		SecurityDataConfiguration.class })
public class SecurityAutoConfiguration {

	@Bean
	@ConditionalOnMissingBean(AuthenticationEventPublisher.class)
	public DefaultAuthenticationEventPublisher authenticationEventPublisher(
			ApplicationEventPublisher publisher) {
		return new DefaultAuthenticationEventPublisher(publisher);
	}

}
```

SpringBootWebSecurityConfiguration은 WebSecurityConfigurerAdapter를 상속받아서 오버라이드 하지 않고 사용하고 있다.
- @ConditionalOnMissingBean(WebSecurityConfigurerAdapter.class)
    - WebSecurityConfigurerAdapter를 상속하는 빈을 만들면 커스터마이징 할 수 있다.

```java
@Configuration
@ConditionalOnClass(WebSecurityConfigurerAdapter.class)
@ConditionalOnMissingBean(WebSecurityConfigurerAdapter.class)
@ConditionalOnWebApplication(type = Type.SERVLET)
public class SpringBootWebSecurityConfiguration {

	@Configuration
	@Order(SecurityProperties.BASIC_AUTH_ORDER)
	static class DefaultConfigurerAdapter extends WebSecurityConfigurerAdapter {

	}

}
```
Spring Security을 추가하게 되면 기본적으로 모든 경로에 인증이 필요한데 WebSecurityConfigurerAdapter의 기본설정이 그렇기 때문이다.

```java
public abstract class WebSecurityConfigurerAdapter implements
		WebSecurityConfigurer<WebSecurity> {
    ...
    protected void configure(HttpSecurity http) throws Exception {
        logger.debug("Using default configure(HttpSecurity). If subclassed this will potentially override subclass configure(HttpSecurity).");

        http
            .authorizeRequests()
                .anyRequest().authenticated()
                .and()
            .formLogin().and()
            .httpBasic();
    }
    ... 
}
```
user라는 사용자가 자동으로 추가되는 이유는 spring-boot-autoconfigure의 security쪽 자동 설정중 하나인 UserDetailsServiceAutoConfiguration의 역할이다.

```java
@Configuration
@ConditionalOnClass(AuthenticationManager.class)
@ConditionalOnBean(ObjectPostProcessor.class)
@ConditionalOnMissingBean({ AuthenticationManager.class, AuthenticationProvider.class,
		UserDetailsService.class })
public class UserDetailsServiceAutoConfiguration {
    ...
    @Bean
    @ConditionalOnMissingBean(type = "org.springframework.security.oauth2.client.registration.ClientRegistrationRepository")
    @Lazy
    public InMemoryUserDetailsManager inMemoryUserDetailsManager(
            SecurityProperties properties,
            ObjectProvider<PasswordEncoder> passwordEncoder) {
        SecurityProperties.User user = properties.getUser();
        List<String> roles = user.getRoles();
        return new InMemoryUserDetailsManager(User.withUsername(user.getName())
                .password(getOrDeducePassword(user, passwordEncoder.getIfAvailable()))
                .roles(StringUtils.toStringArray(roles)).build());
    }

    private String getOrDeducePassword(SecurityProperties.User user,
            PasswordEncoder encoder) {
        String password = user.getPassword();
        if (user.isPasswordGenerated()) {
            logger.info(String.format("%n%nUsing generated security password: %s%n",
                    user.getPassword()));
        }
        if (encoder != null || PASSWORD_ALGORITHM_PATTERN.matcher(password).matches()) {
            return password;
        }
        return NOOP_PASSWORD_PREFIX + password;
    }
    ...
}
```

## Spring Security 커스터마이징
- 웹 시큐리티 설정
- PasswordEncoder 설정
    - <https://docs.spring.io/spring-security/site/docs/5.0.x/reference/html5/#core-services-password-encoding>
- UserDetailsService 구현

#### 웹 시큐리티와 PasswordEncoder 설정
```java
@Configuration
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                // "/", "/hello"는 아무나 접근 가능하다.
                .antMatchers("/", "/hello").permitAll()
                // "/", "/hello" 외의 페이지는 인증된 사용자만 접근 가능하다.
                .anyRequest().authenticated()
                .and()
            .formLogin()
                .and()
            .httpBasic();
    }

    // PasswordEncoder 설정
    @Bean
    public PasswordEncoder passwordEncoder() {
        return PasswordEncoderFactories.createDelegatingPasswordEncoder();
    }
}
```
PasswordEncoderFactories.createDelegatingPasswordEncoder()는 DelegatingPasswordEncoder를 생성한다. <br/>
여러가지 이유로 암호화 알고리즘을 변경해야 하는데 DelegatingPasswordEncoder가 알아서 적당한 알고리즘을 적용해준다.
<br/>Spring Security 5.3.4 기준으로 Bcrypt가 기본 암호화 알고리즘으로 사용된다.

#### UserDetailsService 구현
Account Entity
```java
@Entity
public class Account {
    
    @Id @GeneratedValue
    private Long id;

    private String username;

    private String password;
    ...
}
```
Account Repository
```java
public interface AccountRepository implements JpaRepository<Account, Long> {
    Optional<Account> findByUsername(String username);
}
```
Account Service
```java
@Service
public class AccountService implements UserDetailsService {

    private final AccountRepository accountRepository;
    private final PasswordEncoder passwordEncoder;
    
    public AccountService(AccountRepository accountRepository, PasswordEncoder passwordEncoder) {
        this.accountRepository = accountRepository;
        this.passwordEncoder = passwordEncoder;
    }

    @Transactional
    public Account signup(String username, String password) {
        Account account = new Account(
            username,
            passwordEncoder.encode(password)
        );

        return accountRepository.save(account);
    }

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        Optional<Account> account = accountRepository.findByUsername(username).orElseThrow(
                () -> new UsernameNotFoundException(username)
        );
       
        return new User(account.getUsername(), account.getPassword(), authorities());
    }

    private Collection<? extends GrantedAuthority> authorities() {
        return Arrays.asList(new SimpleGrantedAuthority("ROLE_USER"));
    }
}
```