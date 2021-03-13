---
title: '[Design Pattern] Template Method Pattern'
layout: post
categories: etc
---

## Template Method Pattern
> 실행 과정/단계는 동일한데 각 단계 중 일부의 구현이 다른 경우 사용할 수 있는 패턴

## 예시 상황
DB데이터와 LDAP을 이용해서 인증을 처리하는 클래스는 정보를 가져오는 부분의 구현만 다를 뿐 
인증을 처리하는 과정은 완전히 동일할 수 있다.

DbAuthenticator
```java
public class DbAuthenticator {
    
    public Auth authenticate(String id, String pw) {
        // 사용자 정보로 인증 확인
        User user = userDao.selectById(id);
        boolean auth = user.equalPassword(pw);
        
        // 인증 실패 시 익셉션 발생
        if (! auth) 
            throw new AuthException();
        
        // 인증 성공시, 인증 정보 제공
        return new Auth(id, user.getName());
    }
}
```

LdapAuthenticator
```java
public class LdapAuthenticator {
    
    public Auth authenticate(String id, String pw) {
        // 사용자 정보로 인증 확인
        boolean auth = ldapClient.authenticate(pw);
        
        // 인증 실패 시 익셉션 발생
        if (! auth) 
            throw new AuthException();
        
        // 인증 성공시, 인증 정보 제공
        LdapContext ctx = ldapClient.find(id);
        return new Auth(id, ctx.getAttribute("name"));
    }
}
```

실행 과정을 구현한 상위 클래스와 실행 과정의 일부 단계를 구현한 하위 클래스로 구분할 수 있다.

DbAuthenticator와 LdapAuthenticator에서 동일했던 실행 과정을 템플릿 메서드(authenticate)로 구현하고
두 클래스에서 차이가 나는 부분은 하위 클래스에서 구현하도록 별도의 추상 메서드로 분리한다.

템플릿 메서드를 제공하는 Authenticator
```java
public abstract class Authenticator {

    // 템플릿 메서드
    public Auth authenticate(String id, String pw) {
        // 사용자 정보로 인증 확인
        boolean auth = doAuthenticate(id, pw);

        // 인증 실패 시 익셉션 발생
        if (! auth) {
            throw new AuthException();
        }
    
        // 인증 성공시, 인증 정보 제공
        return createAuth(id);
    }
    
    // 하위 클래스에서 구현해야 할 부분을 추상 메서드로 분리한다.
    protected abstract boolean doAuthenticate(String id, String pw);

    protected abstract Auth createAuth(String id);
}
```

템플릿 메서드 패턴을 적용한 LdapAuthenticator의 구현
```java
public class LdapAuthenticator extends Authenticator {
    
    @Override
    protected boolean doAuthenticate(String id, String pw) {
        return ldapClient.authenticate(id, pw);
    }

    @Override
    protected boolean createAuth(String id) {
        LdapContext ctx = ldapClient.find(id);
        return new Auth(id, ctx.getAttribute("name"));
    }
}
```

## 템플릿 메서드와 전략 패턴의 조합
> 상속이 아닌 조립 방식으로 템플릿 메서드 패턴을 활용할 수 있다.

템플릿 메서드(TransactionTemplate.execute())는 파라미터로 전달받은 action의 메서드를 호출하고 있다.
action으로 적절한 전략을 전달해서 원하는 기능을 구현할 수 있다.
```java
public class TransactionTemplate {

    // 템플릿 메서드
    public <T> T execute(TransactionCallback<T> action) throws TransactionException {
        ...
        action.doInTransaction(status);
        ...
    }
}
```
템플릿 메서드 사용
```java
public class Client {

    TransactionTemplate transactionTemplate = new TransactionTemplate();
    transactionTemplate.execute(
        new TransactionCallback<String>() {
            public String doInTransaction(TransactionStatus status) {
                ...
            }
        }
    );
}
```

## Reference
- [객체 지향과 디자인 패턴](http://www.yes24.com/Product/Goods/9179120)



