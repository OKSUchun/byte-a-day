---
icon: leaf
---

# Spring Data JPA: 연관관계 엔티티의 ID로 검색하기

JPA 에서 연관관계의 id 를 기준으로 검색할 수 있을까?

Spring Data JPA 의 메서드 쿼리 기능을 사용하면, 연관관계의 entity id 를 기준으로 직접 검색할 수 있다.

예를 들어, EpisodeComment 의 Entity 가 다음과 같이 정의되어 있을떄,

<pre class="language-java"><code class="lang-java">package com.dopamingu.be.domain.episode.domain;

@Entity
@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED) 
public class EpisodeComment extends BaseTimeEntity {
<strong>@Id
</strong>@GeneratedValue(strategy = GenerationType.IDENTITY)
@Column(name = "episode_comment_id")
private Long id;

@Length(max = 100) private String content;

@ManyToOne
@JoinColumn(name = "episode_id")
private Episode episode;

@Builder
private EpisodeComment(
        String content,
        Episode episode) {
    this.content = content;
    this.episode = episode;
}
}
</code></pre>

EpisodeComment 의 entity는 Episode와 N:1 의 관계를 가지고 있다.

이때 그럼 Episode 를 기준으로 검색을 하고 싶으면 어떨까?\
매번 Episode를 EpisodeRepository 에서 조회해야하는걸까?&#x20;

Spring Data JPA에서 **연관관계로 묶인 엔티티의 id** 를 이용한 검색도 충분히 **메서드 쿼리**로 구현이 가능하다.&#x20;

```java
// EpisodeCommentRepository.java
Optional<EpisodeComment> findAllByEpisode_Id(Long episodeId);
```

* `findBy[연관관계필드명]_[하위필드명]`&#x20;
* 연관관계필드명: Episode
* 엔티티의 하위 필드명: Id
* 이런 형태로 메서드명을 지어주면 Spring Data JPA 가 메서드명을 파싱해, 적절한 JPQL을 자동 생성해준다.&#x20;

따라서 단일 필드가 아닌 **연관관계로 묶인 엔티티의 id** 를 이용한 검색도 충분히 **메서드 쿼리**로 구현이 가능하다.&#x20;

