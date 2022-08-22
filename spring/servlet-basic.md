# Servlet

## 서블릿 이전 초창기 애플리케이션

![process-cgi](https://user-images.githubusercontent.com/50051656/185947404-a6124354-b344-4f9a-adf3-f62196421941.png)

- 초창기에는 클라이언트가 요청을 보내면 정적인 데이터만을 전달했고 사용자에게 다양한 자료를 전달할 수 없었다.
- 동적인 데이터를 전달하기 위해 CGI(Common Gate Interface)를 구현하여 데이터를 전달하였다.
- 웹 서버는 요청마다 프로세스를 생성하여 처리하였는데 매번 프로세스를 생성하고, 같은 로직임에도 불구하고 CGI 구현체를 매번 생성한다는 문제점이 존재했다.

## 서블릿 등장

<img width="826" alt="thread-cgi" src="https://user-images.githubusercontent.com/50051656/185947415-19340526-438b-4eac-ab55-0f82fd2d0d11.png">

- 위에 문제점을 해결한 자바 진영에서 동적 데이터를 전달하기 위해 CGI를 구현한 프로그램이다.
- 매 요청 Process -> Thread로 변경하였고 CGI 구현체를 계속 생성하지 않고 싱글턴 패턴을 이용해 한 번만 생성하여 재사용하였다.
- 서블릿을 이용하면 HTTP 메서드에 대한 분기 처리와 메세지를 받고 파싱하는 작업 또한 하지 않아 개발자가 비즈니스 로직에 더욱 집중할 수 있는 환경을 제공한다. 

## 서블릿 컨테이너

<img width="693" alt="servlet-container" src="https://user-images.githubusercontent.com/50051656/185947423-52161135-50e1-46d5-9d55-7ec4a1f377b6.png">

- 서블릿 생성을 담당하는 컨테이너이다.
- 서블릿은 init - service - destory 생명주기를 가지고 있고 컨테이너를 이를 관리한다.
- 요청이 들어오면 컨테이너는 매핑되는 서블릿을 찾게 되고 존재하지 않다면 서블릿을 생성하여 처리하게 된다. 이때 처리 후 서블릿을 소멸시키는 게 아니라 컨테이너에서 관리하게 되고 동일한 요청이 들어오게 되면 서블릿이 재사용되게 된다.

## 서블릿 문제점

<img width="684" alt="each-servlet" src="https://user-images.githubusercontent.com/50051656/185948918-355d5438-645c-4a76-916b-99482b97ed45.png">

- 엔드포인트 하나당 하나의 서블릿을 가지기 때문에 서블릿 내에 중복되는 로직들이 발생하여 리소스가 낭비된다.

## Front Controller Pattern

<img width="693" alt="front-controller" src="https://user-images.githubusercontent.com/50051656/185948937-8dc7bc1f-c5bf-4000-b9da-69fd22cbd2fc.png">

- 공통 로직을 앞에서 처리할 수 있도록 Front Controller를 생성하여 중복 코드 문제점을 해소하였다.
- 또한, 모든 요청이 진입점이 같아져서 관리하기 수월해졌다.
- Front Controller는 요청에 맞는 컨트롤러 정보를 보관 및 호출, 결과를 생성과 반환 등 여러 가지 작업을 하면서 역할을 분리할 필요가 있었다.

## Spring MVC

<img width="550" alt="spring-mvc" src="https://user-images.githubusercontent.com/50051656/185951837-3f34366a-ed31-41c8-9ff0-461c1a272130.png">

# References

- [https://www.youtube.com/watch?v=h0rX720VWCg](https://www.youtube.com/watch?v=h0rX720VWCg)