# 게시판 조회 프로세스 

- 적용된 디자인 패턴
  - 전략 패턴
  - 팩토리 메서드 패턴 

- 조회 
  - 목록 조회 : findAll or finds
  - 상세 조회 : find
  
- 목록 조회 
  - 공지사항 사용 여부에 따라 Notice, NoneNotice 로 구분된다.
  - 이전글 다음글이 위 쿼리에 포함되면 고려하지 않아도 되는데, 따로 조회해서 합치는 경우에는 Prev, NonePrev 로 구분된다.
  
- 상세 조회
  - 댓글 사용 여부에 따라 Replies, NoneReplies 로 구분된다.
  - 개인정보를 가져오는지 여부에 따라 privacy, NonePrivacy 로 구분된다.

## Example

- 알고리즘 패밀리
  - 한 유형의 알고리즘을 보유하고 있다.

```java
public interface ArticlesFinderStrategy {

    List<ArticleVo> findArticles(ArticleVo articleVo);
    
}
```

- 알고리즘 패밀리를 구현하는 각각의 전략들

```java
public class ArticlesNoticeFinder implements ArticlesFinderStrategy {

    private final ArticleService articleService;

    public void ArticlesRepliesFinder(ArticleService articleService) {
        this.articleService = articleService;
    }

    public List<ArticleVo> findArticles(ArticleVo articleVo) {
        // 공지를 포함하는 게시판 조회 쿼리 실행
        articleService.findArticles(articleVo);
    }

}
```

- 추상 클래스
  - 조회하는 행동(활동) 자체를 AbstractArticlesFinder 에 국한시킨다.

```java
public abstract class AbstractArticlesFinder {

    private ArticleMasterVo articleMasterVo;

    public AbstractArticlesFinder(ArticleMasterVo articleMasterVo) {
        this.articleMasterVo = articleMasterVo;
    }

    public List<ArticleVo> findArticles(ArticleVo articleVo) {
         ArticlesFinderStrategy strategy = createfindArticlesFinder(articleMasterVo);
         List<ArticleVo> articles = strategy.findArticles(articleVo);   // 공지여부를 판단해서 조회해놓고
         return articles;
    }

    /**
     * 추상 팩토리 메서드
     * 팩토리 메서드에서는 특정 제품(객체)를 리턴하며, 그 객체는 보통 수퍼클래스에서 정의한 메서드 내에서 쓰이게 된다.
     * 전략 객체를 만드는 일은 서브클래스의 createFindArticlesFinder 메서드에서 담당
     * 따라서 클라이언트가 어떤 구현체를 만드는지 모름 (추상화 된 것에 의존)
     */
    protected abstract ArticlesFinderStrategy createFindArticlesFinder(ArticleMasterVo articleMasterVo);

}
```

- 서브 클래스
  - 어떤 전략을 사용할 것인지 정한다.

```java
public class ArticlesFinderFactory extends AbstractArticlesFinder {

    private ArticleService articleService;
    
    public ArticlesFinderFactory(ArticleMasterVo articleMasterVo) {
        super(articleMsterVo);
        
        ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
        HttpServletRequest request = attributes.getRequest();
        WebApplicationContext wac = WebApplicationContextUtils.getWebApplicationContext(request.getServletContext());
        this.articleService = wac.getBean(ArticleService.class);
    }
    
    @Override
    protected ArticlesFinderStrategy createFindArticlesFinder(ArticleMasterVo articleMasterVo) {
        boolean isUsingNotice = "Y".equals(articleMasterVo.getNoticeSts());
        if(isUsingNotice) {
            return ArticlesNoticeFinder(articleService);
        } else {
            return ArticlesNoneNoticeFinder(articleService);
        }
    }

}
``` 

- 클라이언트

```java
@RequriedArgsConstructor
@Controller
public class ArticleController {

  @GetMapping("/article/list")
  public String list(
    @ModelAttribute("articleVo") ArticleVo articleVo,
    Model model
  ) {
      // 생략
      
      AbstractArticlesFinder articlesFinder = new ArticlesFinderFactory(articleMasterVo);
      List<ArticleVo> articles = articlesFinder.findArticles(articleVo);
  
  }

}
```
