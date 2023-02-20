# Spring-MVC1
스프링 MVC - 백엔드 웹 핵심기술

# Study Spring MVC1
✔ Spring MVC 학습 레파지토리

✔ 패키지 이름 내용
- servlet : servlet, jsp동작 방식에 대한 학습
- springmvc : Model, view, controller에 대한 학습

✔ 학습 내용 

패키지명 :servlet

- HTTP 요청 데이터 : Get, Post, HTTP message body 이렇게 주로 3가지 방법을 사용한다. Http Api에서 주로 JSON,XML,TEXT을 사용한다. 데이터 형식은 주로 JSON 사용

- MVC 패턴의 등장 : 비즈니스 로직은 서블릿 처럼 다른곳에서 처리하고, JSP는 목적에 맞게 HTML로 화면(View)을 그리는
일에 집중하도록 하자. 과거 개발자들도 모두 비슷한 고민이 있었고, 그래서 MVC 패턴이 등장했다. 우리도
직접 MVC 패턴을 적용해서 프로젝트를 리팩터링 해보자.

- MVC 패턴 - 적용 : 서블릿을 컨트롤러로 사용하고, JSP를 뷰로 사용해서 MVC 패턴을 적용해보자.
Model은 HttpServletRequest 객체를 사용한다. request는 내부에 데이터 저장소를 가지고 있는데,
request.setAttribute() , request.getAttribute() 를 사용하면 데이터를 보관하고, 조회할 수 있다

- 프론트 컨트롤러 도입 : 프론트 컨트롤러 서블릿 하나로 클라이언트의 요청을 받음, 프론트 컨트롤러가 요청에 맞는 컨트롤러를 찾아서 호출, 공통 처리 가능, 프론트 컨트롤러를 제외한 나머지 컨트롤러는 서블릿을 사용하지 않아도 됨. 프론트 컨트롤러를 단계적으로 도입해보자. 이번 목표는 기존 코드를 최대한 유지하면서, 프론트 컨트롤러를 도입하는 것이다. 먼저 구조를 맞추어두고 점진적으로 리펙터링 해보자

    - v1 : 하나의 컨트롤러에서 model, view(물리적이름)로이동의 기능이 다같이 이루어지는 컨트롤러

    - v2 : 뷰 리졸버를 통해서 뷰의 논리 이름을 반환하게 하였다. modelView객체를 직접만들어 model과 view의 이름을 반환하게하였다 단점 : 아직 컨트롤러가 servlet의 역할도 하고있다.

    - v3 : 프론트 컨트롤러를 제작함으로써 컨트롤러를 서블릿의 기능을 완전 분리. 또한 뷰 경로의 중복을 제거하였다.

    - v4 : 기존 구조에서 모델을 파라미터로 넘기고, 뷰의 논리이름을 반환한다는 작은 아이디어를 적용했을 뿐인데, 컨트롤러를 구현하는 개발자 입장에서 보면 이제 군더더기 없는 코드를 작성할 수 있다.

    - v5 : 지금까지 개발한것은 한가지 방식의 컨트롤러 인터페이스만 사용이 가능했다.
이 점을 보완하기위해 어댑터 패턴을 도입했다. 핸들러 어댑터는 말그대로 인터페이스이다. 핸들러는 컨트롤러의 개념에서 확장한 것이다.


- DispatcherServlet : ```org.springframework.web.servlet.DispatcherServlet``` 스프링 MVC도 프론트 컨트롤러 패턴으로 구현되어 있다. 스프링 MVC의 프론트 컨트롤러가 바로 디스패처 서블릿(DispatcherServlet)이다. 그리고 이 디스패처 서블릿이 바로 스프링 MVC의 핵심이다.
    
    - Spring MVC (dispatcherServlet) 동작순서
    
    
    1. 핸들러 조회: 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러(컨트롤러)를 조회한다.
    2. 핸들러 어댑터 조회: 핸들러를 실행할 수 있는 핸들러 어댑터를 조회한다.
    3. 핸들러 어댑터 실행: 핸들러 어댑터를 실행한다.4. 핸들러 실행: 핸들러 어댑터가 실제 핸들러를 실행한다.
    5. ModelAndView 반환: 핸들러 어댑터는 핸들러가 반환하는 정보를 ModelAndView로 변환해서
반환한다.
    6. viewResolver 호출: 뷰 리졸버를 찾고 실행한다.
JSP의 경우: InternalResourceViewResolver 가 자동 등록되고, 사용된다.
    7. View 반환: 뷰 리졸버는 뷰의 논리 이름을 물리 이름으로 바꾸고, 렌더링 역할을 담당하는 뷰 객체를
반환한다.
JSP의 경우 InternalResourceView(JstlView) 를 반환하는데, 내부에 forward() 로직이 있다.
    8. 뷰 렌더링: 뷰를 통해서 뷰를 렌더링 한다.
    
    
    
- 타임리프 : `th:xxx`가 붙은 부분은 서버사이드에서 렌더링 되고 , 기존 것을 대체한다. HTML을 파일로 직접 열면 웹브라우저는 `th:xxx`를 읽지 못함 
               따라서, HTML을 파일 보기를 유지하면서 템플릿 기능도 할 수 있다. 
    - URL 링크 표현식 : @{...} , th:"@{/css/bootstrap.min.css}"
    - 리터럴 대체 = |...|, 문자와 표현식은 분리가 되어있어 +를 사용해야 하는데 ```<span th:text="|Welcome to our application, ${user.name}!|">``` 없이 사용가능하다


- Redirect 방식 : 상품 등록에서 새로고침 시 상품이 중복 등록되는 것을 볼 수 있었다. 이때 해결 방법은 Redirect 호출 방법이다. ```> "redirect:/basic/items/" + item.getId()```를 사용하여 해결했다 하지만,  URL에 변수를 더해서 사용하는 것은 URL 인코딩이 안되기 때문에 위험하다 그래서 `RedirectAttributes` 를 사용한다.

                

