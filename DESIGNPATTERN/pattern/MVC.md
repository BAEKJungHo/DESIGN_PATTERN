## MVC

> MVC 패턴은 옵저버 패턴, 스트래티지 패턴, 컴포지트 패턴으로 이루어진 컴파운드 패턴이다.

- 모델
  - 모델에서는 옵저버 패턴을 이용하여 옵저버들에 대한 의존성은 없애면서도 옵저버들한테 자신의 상태가 변경되었음을 알릴 수 있다.
  - 컨트롤러는 뷰에 대한 전략 객체이다. 뷰에서는 컨트롤러를 바꾸기만 하면 다른 행동을 활용할 수 있다.
  - 뷰에서는 컴포지트 패턴을 이용하여 사용자 인터페이스를 구현한다. 보통 패널이나 프레임, 버튼과 같은 중첩된 구성요소로 구성된다.

- 모델2
  - MVC 를 웹 애플리케이션에 맞게 적용한 디자인이다.
  - 컨트롤러는 서블릿으로 구현되고, 뷰는 JSP/HTML 로 구현된다.

## [MVC Design Pattern](https://www.geeksforgeeks.org/mvc-design-pattern/)

### Concepts

- The `Model` contains only the pure application data, it contains no logic describing how to present the data to a user.
- The `View` presents the model’s data to the user. The view knows how to access the model’s data, but it does not know what this data means or what the user can do to manipulate it.
- The `Controller` exists between the view and the model. It listens to events triggered by the view (or another external source) and executes the appropriate reaction to these events. In most cases, the reaction is to call a method on the model. Since the view and the model are connected through a notification mechanism, the result of this action is then automatically reflected in the view.

### Advantages

- Multiple developers can work simultaneously on the model, controller and views.
- MVC enables logical grouping of related actions on a controller together. The views for a specific model are also grouped together.
- Models can have multiple views.

### Disadvantages

- The framework navigation can be complex because it introduces new layers of abstraction and requires users to adapt to the decomposition criteria of MVC.
- Knowledge on multiple technologies becomes the norm. Developers using MVC need to be skilled in multiple technologies.

