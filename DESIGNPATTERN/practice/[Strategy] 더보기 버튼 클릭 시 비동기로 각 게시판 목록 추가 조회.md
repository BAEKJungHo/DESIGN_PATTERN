# 더보기 버튼 클릭 시 비동기로 각 게시판 목록 추가 조회

예를 들어 메뉴가 다음과 같다고 가정하자.

- 최신영상
- 인기영상
- 구독영상
- 마이페이지
    - 구독채널
    - 내 채널 동영상
    - 나중에 볼 영상
    - 좋아요 영상
    - 조회 영상
    
각 메뉴마다 기본 적으로 6개의 게시물을 가져오고 `더보기` 버튼을 클릭할 때마다 6개씩 추가로 가져온다고 할 때 전략 패턴을 사용해서 쉽게 구현할 수 있다.

## 1. 패키지 생성

`seeMore` 라는 패키지 아래에 더보기를 수행할 서비스, 전략을 생성할 것이다.

## 2. DTO 생성

더보기 조회 시 필요한 속성은 다음과 같다.

- 전략 키값(String)
- 시작 페이지(firstPage)
- 페이지당 출력 개수(recordCountPerPage)

```java
public class VideoDto {

    public static class SeeMoreRequest {
        private String strategyKey;
        private int firstPage;
        private int recordCountPerpage;

        public VideoVo toEntity() {
            VideoVo vo = new VideoVo();
            vo.setStrategyKey(this.strategyKey);
            vo.setFirstPage(this.firstPage);
            vo.setRecordCountPerpage(this.recordCountPerpage);
        }
    }

}
```

## 3. 각 전략을 구분지을 키값을 가지고 있는 Enum 생성

```java
@Getter
public enum SeeMore {
    // strategyKey 가 빈으로 등록된 전략 이름이다.
    LATEST_VIDEO("seeMoreVideoLatest"),
    LIKE_VIDEO("seeMoreVideoLike"),
    SUBSCRIPTION_VIDEO("seeMoreVideoSubscription"),
    // 생략

    private String strategyKey;

    SeeMore(String strategyKey) {
        this.strategyKey = strategyKey; 
    }

    public static String findStrategyKey(String strategyKey) {
        for(SeeMore seeMore : SeeMore.values()) {
            if(strategyKey != null && strategyKey.equals(seeMore.getStrategyKey())) {
                return seeMore.getStrategyKey();
            }
        }
        return null;
    }

}
```

## 4. 더보기 전용 서비스 생성

```java
@RequiredArgsConstructor
@Service
public class SeeMoreService {

    // SeeMoreStrategy 를 구현한 빈들이 Map 에 담긴다.
    private final Map<String, SeeMoreStrategy> strategyMap;

    public List<VideoVo> findSeeMoreList(VideoDto.SeeMoreRequest dto) {
        String strategyKey = SeeMore.findStrategyKey(dto.getStrategyKey()); 
        if(strategyKey == null) {
            // "더보기 호출 중 올바르지 않은 값으로 인해 에러가 발생하였습니다."
            throw new IllegalArgumentException(Message.NOT_FOUND_STRATEGY);
        }
        SeeMoreStrategy strategy = strategyMap.get(strategyKey);

        return strategy.findSeeMoreList(dto.toEntity());
    }

}
```

## 5. 전략 인터페이스 생성

```java
public interface SeeMoreStrategy {

    List<VideoVo> findSeeMoreList(VideoVo videoVo);

}
```

## 6. 전략 구현체 생성

더보기가 존재하는 각 메뉴마다 하나의 전략 구현체를 생성한다.

```java
@RequiredArgsConstructor
@Service
public class SeeMoreVideoLatest implements SeeMoreStrategy {

    // VideoFindService 에는 각 영상 조회를 위한 기능들만 모아두었다.
    private final VideoFindService videoFindService;

    @Override
    public List<VideoVo> findSeeMoreList(VideoVo videoVo) {
        return videoFindService.findAllVideoLatest(VideoVo)
    }

}
```

## 7. 수동빈등록 vs 자동빈등록

같은 패키지에 둔다면 자동빈등록도 괜찮다. 만약 패키지가 다른 경우 명확하게 하고 싶으면 수동 빈 등록이 더 좋다.

## 8. 컨트롤러에서 사용

- 각 더보기가 붙어있는 RestController 마다 아래와 같이 사용한다.
- View 단에서 ajax 로 strategyKey 값을 필수로 넘겨야한다.

```java
@RequiredArgsConstructor
@RequestMapping("/video/latest/api")
@RestController
public class VideoLatestRestController {

    private final SeeMoreService seeMoreService;

    public ResponseEntity seeMore(@RequestBody VideoDto.SeeMoreRequest dto) {
        return ResponseEntity.ok(seeMoreService.findSeeMoreList(dto));
    }

}
```
