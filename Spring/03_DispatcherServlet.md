# Dispatcher Servlet

프론트 컨트롤러(Front Controller)라고도 불리는 ``DispatcherServlet``은 HTTP 프로토콜로 들어오는 모든 요청을 먼저 받아서 적합한 컨트롤러에 위임(Delegate request)는 역할을 한다.

**DispatcherServlet이 하는 일은 크게 5가지다**

1. 핸들러(Controller) 찾기
2. 핸들러를 실행할 수 있는 HandlerAdapter 찾기
3. Controller 호출 & Model 생성
4. ViewResolver로 View 선택
5. View 렌더링 지시

웹 요청이 들어오면 “누가 담당할지”, “어떻게 호출할지”, “어떤 View로 응답할지”를 모두 조정한다.

## DispatcherServlet 장점

과거 ``DispatcherServlet``이 등장하기 전에는 모든 서블릿에 대해 URL을 매핑하기 위해 관련 설정을 해주어야 하는 번거로움이 존재했습니다. (web.xml 파일) 

그러나 ``DispatcherServlet``이 등장한 이후에는 ``DispatcherServlet``이 모든 요청을 먼저 받아서 직접 적합한 컨트롤러로 위임을 해주기 때문에 위와 같은 작업은 필요없어졌고, 이로 인해 개발이 굉장히 편리해졌습니다.

## DispatcherServlet의 요청 처리 흐름 (복습)

1. 클라이언트 → HTTP 요청
2. DispatcherServlet 요청 수신
3. HandlerMapping 으로 핸들러(컨트롤러) 찾기
4. HandlerAdapter 찾기
5. HandlerAdapter가 Controller 실행
6. Controller → Model, ViewName 반환
7. DispatcherServlet → ViewResolver 호출
8. ViewResolver → View(실제 템플릿 파일) 반환
9. View.render(Model) 실행
10. HTML/JSON 응답 전송

이게 스프링 MVC의 전체 파이프라인이다.

### DispatcherServlet 내부 구성 요소

DispatcherServlet 혼자 일하는 게 아니라, 내부적으로 여러 컴포넌트와 협업한다.

① HandlerMapping
- URL과 Controller 메서드를 매핑하는 역할
- 예: /members → MemberController.list()

② HandlerAdapter
- Controller를 “어떻게 실행할지” 결정
- @Controller 메서드를 처리하는 핵심 컴포넌트

③ HandlerExceptionResolver
- Controller에서 예외가 터졌을 때 대신 처리

④ ArgumentResolver
- Controller 메서드의 파라미터 바인딩 담당
- @RequestParam, @PathVariable, @ModelAttribute 처리

⑤ ViewResolver
- "members/list" → /WEB-INF/views/members/list.jsp 같은 파일 경로 매핑

⑥ HttpMessageConverter
- @ResponseBody JSON 변환
- @RequestBody JSON → 객체 변환

즉, DispatcherServlet은 이 모든 컴포넌트를 통합 조정하는 역할.

