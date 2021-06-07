---
title: '@SecondaryTable'
layout: post
categories: jpa
---

## @SecondaryTable
한 엔티티에 여러 테이블을 매핑할 수 있다.

```java
@Entity
@Table(name = "article")
@SecondaryTable(
    name = "article_content",
    pkJoinColumns = @PrimaryKeyJoinColumn(name = "id")
)
public class Article {
    @Id @GeneratedValue
    private Long id;
    private String title;
    
    @AttributeOverrides({
        @AttributeOverride(name = "content", column = @Column(table = "article_content")),
        @AttributeOverride(name = "contentType", column = @Column(table = "article_content"))
    })
    private ArticleContent content;
}
``` 

## @SecondaryTable vs @OneToOne
@SecondaryTable은 항상 두 테이블을 조인해서 조회하므로 대상 테이블을 조회할 필요가 없는 경우 성능의 이슈가 발생할 수 있다.  
@OneToOne 매핑을 사용하던가 조회 전용 기능을 구현해야 한다.
