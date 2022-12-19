#1
   < 스프링프레임워크의 구동 원리  > 
    DispatcherServlet : 클라이언트의 요청을 전달받아 해당 요청에 대한 컨트롤러를
         선택하여 클라이언트의 요청을 전달,
         또한 컨트롤러가 반환한 값을 View에 전달하여 알맞은 응답을 생성

    HandlerMapping : 클라이언트가 요청한 URL을 처리할 컨트롤러를 지정

    Controller : 클라이언트의 요청을 처리한 후 그 결과를 DispatcherServlet에 전달

    ModelAndView : 컨트롤러가 처리한 결과 및 뷰 선택에 필요한 정보를 저장

    ViewResolver : 컨트롤러의 처리 결과를 전달할 뷰를 지정

    View : 컨트롤러의 처리 결과 화면을 생성
---------------------------------------------------------------------------------
#2
.웹을 통해 접근할 수 있는 디렉터리 경로 : 문서의 root 경로
   톰캣의 webapps
   이클립스(2020_12) : WebContent
   이클립스(2022_09) : WebApps
따라서 업로드되는 파일 역시 html/jsp에서 참조가능한 WebContent(WebApps)에 넣는다.
   WebContent(WebApps) 아래에 [imgage]폴더를 만들고 파일을 업로드하면 이클립스에서 
   실행중인 톰캣은 이미 프로젝트를 패키지화해서 별도의 경로에서 서비스하기 때문에
   업로드된 파일을 인식하지 못하는 문제가 발생하여 프로젝트를 다시 실행하거나
    톰캣을 다시 시작해야하는 문제가 있음
   
   우리예제는 C:/Temp/image에 저장함  => 이러면 웹에서 접근을 할 수 없는 문제 발생
      이미지 로드되지 않았던 문제가 이러한 문제에서 발생
            => 톰캣에서 C:/Temp/image 폴더에 접근하도록 설정하는 것이 필요
  [Servers] -[Tomcat v9.0...] - [server.xml]의 155번째 줄을 </HOST> 앞에 다음을 추가 하고 저장한 후 다시 시작
   <Context docBase="c:/temp/image" path="/image" reloadable="true"/>

      <Host appBase="webapps" autoDeploy="true" name="localhost" unpackWARs="true">

        <!-- SingleSignOn valve, share authentication between web applications
             Documentation at: /docs/config/valve.html -->
        <!--
        <Valve className="org.apache.catalina.authenticator.SingleSignOn" />
        -->

        <!-- Access log processes all example.
             Documentation at: /docs/config/valve.html
             Note: The pattern used is equivalent to using pattern="common" -->
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs" pattern="%h %l %u %t &quot;%r&quot; %s %b" prefix="localhost_access_log" suffix=".txt"/>

      <Context docBase="newsweb" path="/newsweb" reloadable="true" source="org.eclipse.jst.jee.server:newsweb"/>
      <Context docBase="c:/temp/image" path="/image" reloadable="true"/>
      </Host>

=====================================================
DAO  : Data Access Object , db의 데이터에 접근하기 위한 객체
   db접속과 db의 데이터 처리에 해당하는 sql 쿼리문 작성...   

DTO/VO
DTO : Data Transfer Object  => db테이블의 자료를 가져다 객체로 만들기
       테이블의 필드명을 토대로 getter/setter 만들기...
      form에서 입력된 자료를 데이터베이스 테이블에 넣기
VO : Value Object 

=======================================================
웹에서 인식하는 첫 페이지 설정(index파일 설정)
   [WebContent(WebApps)] -[ web.xml ] 파일에서 설정
   기본적인 파일명이 아닌 자신만의 파일명을 쓰려면 여기서 설정
   그러나 index.html/ index.jsp / default.jsp 정도로 설정하는 것이 좋음

배포 파일 만들기
   프로젝트명(newsweb) 위에서 마우스 오른쪽 : Export - War File
      Web project : 프로젝트명
      Destination : [Browse]버튼을 눌러 war파일을 저장할 위치 선택
      Target runtime : 톰캣
      Export source File 체크하면 :  *.java 파일까지 함께 압축함
                                   체크해제 : *.class 파일만 압축
 
        압축된 war파일을 설치된 톰캣이 있는 폴더[C:\apache-tomcat-9.0.69]로
   이동해서 [ webapps ] 폴더 아래에 놓으면 끝
==========================================================
톰캣 (apache-tomcat-9.0.69)
   bin : 실행파일들이 있는 곳
   conf : 환경설정 파일들이 있는 곳
      server.xml - 포트 번호 경도 이곳에서 함
            <Connector port="8080" protocol="HTTP/1.1"
                        connectionTimeout="20000"
                           redirectPort="8443" />
   lib : 라이브러리 파일들이 있는 곳
   webapps : 개발한 사이트를 넣는 곳(개발한 프로젝트를 넣는 곳)
      만약 사이트를 여러 개(웹 페이지를 여러개(프로젝트를 여러개))만들어서 
      서버에서 구동시키려면 각각 context를 만들어 webapps 폴더 아래에 위치시킴

    webapps - ROOT : 프로그램을 개발한 사이트 넣는 곳
       이미지, html, css, javascript,....

   webapps-WEB_INF : ~.java, ~.class(자바클래스, 서블릿 등이 위치)

   jsp 파일(파일명.jsp) : 반드시 서버에서 실행
   html파일(파일명.html) : 웹브라우저에서 실행시키기 때문에 서버 없이도 아무 곳에서나 실행 가능함
-----------------------------------------------------------------------------------------------
#3
jdbc 프로그래밍

1. JDBC 드라이버 로드 :  Class.forName()
       oracle : oracle.jdbc.driver.OracleDriver
       mysql : com.mysql.jdbc.Driver
          com.mysql.cj.jdbc.Driver(8.x)
2. 데이터베이스 연결 :
   java.sql.Connetion
   DriverManger.getConnection(url, user, password)
   jdbc:하위프로토콜:데이터원본식별자
   jdbc:mysql://서버주소(localhost:3306)/DB명?characterEncoding=UTF-8&serverTimezone=UTC"

   oracle =>   jdbc:oracle:thin:@localhost:1521:SID
          jdbc:oracle:thin:@localhost:1521:XE
   mysql =>    jdbc:mysql://localhost:3306/myDB명?characterEncoding=UTF-8&serverTimezone=UTC

3. Statement 생성  => 문자열로 이루어진 sql문을 jdbc가 처리할 수 있는 객체로 변환 
   java.sql.Statement
   java.sql.PreparedStatement    => sql문을 미리 만들어 두고 변수를 따로 입력하는 방식
               (?-데이터가 들어가야할 부분
                pstmt.setString(1,request.getParameter("username");
                pstmt.setInt(2,request.getParameter("jumsu");
               Satement를 상속받기 때문에 Statement의 모든 기능을 사용할 수 있음

   PreparedStatement pstmt = conn.preparedStatement("insert into student values(?,?)")
   pstmt.setString(1,request.getParameter("username");
   pstmt.setString(2,request.getParameter("username");

4. sql문 전송
   executeQuery() : select ,   반환값은 ResultSet 클래스 타입, 
   executeUpdate() : insert, delet, update  => 반환값이 int, 처리된 데이터의 수를 반환


5. 결과 받기

6. 연결 해제
   rs.close();
   pstmt.close();
   conn.close();
------------------------------------------------------------------------------------------------------
#4
-- db만들기
create database newsdb;
-- db안으로 들어가기
use newsdb;

-- db지우기 : drop database db명

-- 테이블 생성
create table news(
   aid int auto_increment not null primary key, -- 기사 아이디
    title varchar(200) not null, -- 기사 제목
    image varchar(500) not null, -- 사진 경로
    date  timestamp,  -- 기사 등록날짜와 시간
    content varchar(1000) not null -- 기사 내용
);

select * from news;
-------------------------------------------------------------------------------------------------------
#5

1. 모델(Model) : 데이터를 처리하는 영역
   DAO, DO
2. 뷰(View) : 화면 구성을 담당하는 영역
      주어진 데이터를 출력하는 용도로만 사용하는 것 좋음
      뷰에서 데이터를 직접 가져오는 방식은 권장하지 않음
      모델, 컨트롤러와의 종속성이 없도록 구현해야 함

3. 컨트롤러(Controller) : MVC 패턴의 핵심, 모든 사용자 요청의 중심에 위치함
      사용자 요청은 특정 뷰에 바로 전달되지 않고 컨트롤러를 통해야 함
      컨트롤러는 사용자 요청에 따라 모델을 통해 데이터베이스와 연동하여
      데이터를 처리하고 뷰에 전달함
      뷰로 전달하기 위해 데이터가 들어 있는 DO 혹은 List<DO>형태의 객체를
      request 에 저장한 후 포워딩함.

4. 컨트롤러를 구성하는 세 가지 방법
   1) 사용자 요청마다 컨트롤러를 만들기
   2) 특정 모듈 단위로 하나의 컨트롤러 안에서 여러 요청 단위를 구분해 처리하기
   3) 프런트 컨트롤러를 따로 두어 모든 요청을 하나의 컨트롤러로 모은 다음 요청에
      따라 구현 컨트롤러를 호출하기
-------------------------------------------------------------------------------------------------------
#6
<뷰>
   1. 전체 상품 목록 : productList.jsp
   2. 상품 정보를 출력 : productInfo.jsp

<모델>
   3. 상품정보를 표현하기 위한 DO 객체  : Product.java
   4. 데이터베이스 없이 샘플 데이터를 제공하기 위한 클래스 : ProductSevice.java

<컨트롤러>
   5. ProductService.java
      1) service 메서드를 오버라이딩하여 컨트롤러 기본 구조 구현 
        2) 각각의 요청은 action 파라미터를 비교해 메서드를 호출한 다음 뷰로 이동하도로 구현
---------------------------------------------------------------------------------------------------------
#7
리스너(Lisener) : 컨테이넌에서 발생하는 이벤트를 모니터링하다가 
      특정 이벤트가 발생하면 실행되는 특수한 서블릿
      이벤트 리스(Event Listener)
      웹 애플리케이션 실행에 필요한 정보를 제공하거나
      톰캣 시작/종료와 같은 특정 상황에 자동으로 동작하는 
      프로그램을 구현할 때 사용 
   ServletContextListener : 톰캣의 시작, 종료와 같은 이벤트에 동작하는 리스너
   ServletContexAttributeListnener : ServletContex 객체, 즉 application scope에 속성값이
      추가, 변경되는 상황에 동작
   HttpSessionListener : 세션의 생성과 소멸 이벤트에 따라 동작
   HttpSessionAttributeListener: 각 세션별로 속성이 추가 되거나 변경될 때 동작하는 리스너
      이들을 이용하면 개별적인 정보 모니터링에 유용


필터 : 서블릿 필터
   웹 애플리케이션을 지원하기 위한 특수한 형태의 서블릿
   클라이언트 요청에 따라 서블릿이나 jsp가 실행되깆 전에 response  혹은  request 객체의 
   조작이나 추가적인 처리를 할 수 있음
   특정 요청에만 동작
   인증: 특정 페이지에서 로그인 여부나 특정 권한을 확인해야 할 때
      필터를 이용하면 애플리케이션 구조와 상관없이 기존 소스를 최대한 수정하지 않고
      인증 기능을 수행
   로깅/감사(Logging and Auditing) : 특정페이지 또는 기능에 대해 사용 현황을 모니터링하고
      로그로 관리할 필요가 있을 때 
      필터를 통해 해당 요청을 수행하기 전 로깅 처리
   국제화(Localization) : 다국어 처리는 프레임워크 등에서 제공하는 국제화 방법을 사용할 수도
      있으며 필터를 이용할 경우 특정 페이지에 들어갈 메시지 등을 해당 언어로 변환해 전달
   한글 인코딩 처리 
      서블릿이나  jsp : request.setCharacterEncoding("utf-8")을 사용함
      필터의 적용은 각 필터의  doFilter()메서드의 내용이 수행됨
         ServletRequest와 ServletResponse의 내용을 가로채 필요한 작업을 수행하고 다음
           필터로 전달하거나, 요청한 서블릿으로 이동하는 구조
----------------------------------------------------------------------------------------------
#8
REST API
   REST 또는 RESTful : 네트워크 상에서 클라이언트와 서버 사이의 통신을 구현하는 방법
   Representation : 클라이언트가 서버로 요청을 보냈을 때 서버가 응답으로 보내주는
         Resource(예 - 파일, 프로그램호출 등) 의 상태
         하나의 Resource는 여러 형태의 Representation(예-json, xml, text, rss  등)으로 
         전달할 수 있다는 개념을 의미
         REST원칙을 따르는 시스템을 RESTful 이라하며 웹 기반으로 구현된 서비스이므로
         RESTful 웹서비스라고 함
JAX-RS :Java API for RESTful Web Services
   REST 원칙을 사용하는 개발 메커니즘을 제공하는 자바 표준 API
   서비스 측 REST애플리케이션 개발을 단순화하는 인터페이스 및 Java 어노테이션의 집합체
   스프링 프레임워크의 경우 RestController라고 하는 자체 규격을 제공하며 필요에 따라
   JAX_RS를 사용할 수 있음
------------------------------------------------------------------------------------------   
#9
프레임워크
   라이브러리란, 개발자가 작성해 놓은 코드 파일을 의미하며,
   API란, 여러 라이브러리가 모여있는 모듈(JAR)을 의미한다.
   프레임워크란, API가 굉장히 많이 모여져서 덩치가 커져있는 것을 의미한다.
   개발자는 각 개개인의 능력차이가 큰 직종이고, 개발자 구성에 따라 프로젝트 결과 역시
   큰 차이를 낳는다. 이런 상황을 극복하기 위한 코드의 결과물이 바로 프레임워크이다.
   프로그램의 기본 흐름이나 구조를 정하고 모든 팀원이 이 구조에 자신의 코드를 추가하는 방식으로
   개발하게 된다.

프레임워크의 장점
   개발에 필요한 구조를 이미 코드로 만들어 놓았기 때문에, 실력이 부족한 개발자라 하더라도
   반쯤 완성한 상태에서 필요한 부분을 조립하는 형태의 개발이 가능하다.
   회사 입장에서는 프레임워크를 사용하면 일정한 품질이 보장되는 결과물을 얻을 수 있고,
   개발자 입장에서는 완성된 구조에 자신이 맡은 서비스에 대한 코드를 개발해서 넣기 때문에
   개발 시간을 단축할 수 있다.

스프링 프레임워크
   경량 프레임워크.
   예전 프레임워크는 다양한 경우를 처리할 수 있는 다양한 기능을 가지도록 만들다 보니,
   하나의 기능을 위해서는 너무 많은 구조가 필요했다.
   기술이 너무나 복잡하고 방대했기 때문에, 전체를 이해하고 개발하기에는 어려움이 많았다.
   그래서 스프링 프레임워크가 등장했고, 특정 기능을 위주로 간단한 JAR파일 등을 이용해서
   모든 개발이 가능하도록 구성되어 있다.
-----------------------------------------------------------------------------------------
#10
[POJO(Plain Old Java Object) 기반의 구성]
   오래된 방식의 간단한 자바 객체라는 의미이며, JAVA 코드에서 일반적으로
   객체를 구성하는 방식을 스프링 프레임워크에서 그대로 사용할 수 있다는 말이다.

[DI(Dependency Injection)를 통한 객체간의 관계 구성]
   의존성(Dependency)이란 하나의 객체가 다른 객체 없이 제대로 된 역할을 할 수 없다는 것을 의미한다.
   예를 들어 A객체가 B객체 없이 동작이 불가능한 상황을 "A가 B에 의존적이다"라고 표현한다.
   
   주입(Injection)은 말 그대로 외부에서 밀어 넣는 것을 의미한다.
   예를 들어 어떤 객체가 필요로 하는 객체를 외부에서 밀어 넣는 것을 의미한다.
   주입을 받는 입장에서는 어떤 객체인지 신경 쓸 필요가 없고 어떤 객체에 의존하든 자신의 역할은 변하지 않는다.

   **의존성
   ⓐ →→→→→→→→→→→→→→→→→→→→→ ⓑ
   ⓐ객체에서 ⓑ객체를 직접 생성

   **의존성 주입
   ⓐ →→→→→→→→→???↔↔↔↔↔↔↔↔↔ ⓑ
   ⓐ는 ⓑ가 필요하다는 신호를 보내고, ???가 ⓑ객체를 외부에서 생성하여 주입하게 된다.

   의존성 주입방식을 사용하기 위해서는 ???라는 존재가 필요하게 된다.
   스프링 프레임워크에서는 ApplicationContext가 ???라는 존재이며,
   필요한 객체들을 생성하고, 필요한 객체들을 주입해주는 역할을 한다.
   따라서 개발자들은 기존의 프로그래밍과 달리 객체와 객체를 분리해서 생성하고,
   이러한 객체들을 엮는(Wiring) 작업의 형태로 개발하게 된다.

[AOP 지원]
   관점 지향 프로그래밍.
   좋은 개발환경에서는 개발자가 비지니스 로직에만 집중할 수 있게 한다.
   스프링 프레임워크는 반복적인 코드를 제거해줌으로써 핵심 비지니스 로직에만 집중할 수 있는 방법을 제공한다.
   보안이나 로그, 트랜잭션, 예외처리와 같이 비지니스 로직은 아니지만, 
   반드시 처리가 필요한 부분을 횡단 관심사(cross-concern)라고 한다.
   스프링 프레임워크는 이러한 횡단 관심사를 분리해서 제작하는 것이 가능하고 횡단 관심사를 모듈로 분리하는
   프로그래밍을 AOP라고 한다. 핵심 비지니스 로직에만 집중하여 코드 개발이 가능해지고,
   각 프로젝트마다 다른 관심사 적용 시 코드 수정을 최소화할 수 있으며, 
   원하는 관심사의 유지보수가 수월한 코드로 구성이 가능해진다.

[트랜잭션의 지원]
   DB 작업 시 트랜잭션을 매번 상황에 맞게 코드로 작성하지 않고, 어노테이션이나 XML로
   트랜잭션을 쉽게 관리할 수 있다.

[단위 테스트]
   전체 Application을 실행하지 않아도 기능별 단위 테스트가 용이하기 때문에
   버그를 줄이고 개발 시간을 단축할 수 있다.
===================================================================================================
IntelliJ 설치 - 2020.01.01
   구글에 IntelliJ 검색하고 다운로드

프로젝트 기본 경로
   1) src/main/java      : 서버단 JAVA 파일
   2) test/main/java      : 단위 테스트 JAVA 파일
   3) src/main/resources      : 설정 파일 및 뷰단
   4) resources/static      : css, js 파일 경로
   5) resources/templates      : html 파일 경로
   6) pom.xml         : 라이브러리 의존성 관리
   7) application.properties   : 서버 및 DB, 라이브러리 설정 파일
------------------------------------------------------------------------------------------------------
#11
webscoekt은 서버와 클라이언트 간에 언제든지 실시간으로 양방향 통신, 데이터 전송이 가능하도록 하는
프로토콜의 일종이다.
보통은 실시간 데이터 통신 (채팅, 멀티플레이어게임, 증권거래, 화상채팅 등에 사용)
socket io는 websocket은 간단하게 사용할 수 있게 해주는 라이브러리

cors
Http message의 header가 통일해야만 통신을 주고받을 수 있는데
백엔드 서버와 프론트 엔드서버는 header와 같은 인증정보가 다름
따라서 인증 정보가 다른 서버의 요청도 허용
--------------------------------------------------------------------------------------------------------
#12
foreach()라는 function을 사용하면 단순히 array에 들어있는 모든 값을 iterate(순찰?)할 수 있음.

즉 index 0부터 마지막 index까지 한바뀌 도는건데 돌면서 그 값들을 item 또는 element(임의로 이름작성 가능)에 저장.

ex)
Array = [ a, b, c, d]
Array.foreach( (item) => console.log(item))
output:
a
b
c
d
---------------------------------------------------------------------------------------------------------
#13
동기 함수 : 순서대로 진행
비동기 함수 : 순서대로 진행되지 않고, 기다려야하는 일이 있으면 기다리는 동안 다른일을 처리한다
콜백 : 어떤 이벤트가 실행되고나서 호출되는 함수(비동기함수의 일이 끝나고 호출되는함수), 인자값으로 들어가는 함수
promise : 자바스크립트에서 비동기 task를 다룰 수 있게 도와주는 객체
async/await : Promise보다 비동기 작업의 실행을 좀 더 유연하고 '일반함수'적으로 실행하도록 해주는 함수
----------------------------------------------------------------------------------------------------------
#14
Promise
자바스크립트에서 비동기 task를 다룰 수 있게 도와주는 객체로, 비동기적 작업을 하는 함수의 return type으로 사용 된다.
함수 안의 작업이 성공적으로 동작했을 때 동작하는 'resolve' callback과 함수가 동작이 실패해서 에러가 발생했을 때 
동작하는 'reject' callback을 인자로 받는 함수를 실행한다.
Promise는 처음에는 함수가 실행되기를 쭉 기다리고 있는 Pending 상태였다가, 함수가 잘 작동이 되면 resolve 가 되면서 
resolve callback을 실행시키며 프로미스를 종료하고, 함수가 작동하다가 에러가 나면 rejected 상태가 되면서 reject callback을 실행한다.
기존 callback 사용시와 차이가 있다면, 기존에는 resolve, reject 각각의 상황에 대응하는 함수를 직접만들어 대입을 했어야 했지만,
Promise의 경우 dafault로 resolve, reject callback method가 내장되있어 보다 편리하고 심플하게 사용할 수 있다는 장점이 있다.
.then(), .catch()
Promise를 사용하면 함께 사용할 수 있는 두가지 무적의 메소드 .then() 과 .catch() 가 있다.
Promise 사용 시 이 두가지를 가지고 callback 지옥없이! 연속적으로 비동기적인 동작을 하는 코드를 작성이 가능하다!
.then() 의 경우, 앞의 데이터를 받아온 다음에(then) 그 값으로 무언가를 실현한다는 것으로, chaining을 통하여 callback을 구현한 것으로 볼 수 있다.
.catch() 의 경우, 앞의 데이터 중 error만을 받아와(catch해) 그 값으로 무언가를 실현하는 것으로, error 상황에 특정한 callback을 구현한다.
주의사항
Promise를 .then으로 체이닝해서 연속적으로 실행 가능하다는 점을 보면 알듯이, Promise는 Promise를 반환한다 (.then과 .catch를 포함한 Promise 관련 메소드들도 포함됨).
⇒ 이로 인하여 위험한 상황이 발생하는데, Promise를 얹고 얹으면서 반환값이 중간에서 꼬여버린다면, 콜백지옥은 코드를 스윽 읽어라도 볼 수 있지만,
Promise는 일일히 반환값을 디버거로 뜯어봐야한다..!
중간에 다른 값이 필요할 때에도 callback과 마찬가지로 코드를 읽기 어렵도록 하여 가독성을 해친다.
→ 결국) Promise의 then과 catch가 쌓이면 가독성이 떨어지며 디버깅도 어려워진다.
------------------------------------------------------------------------------------------------------------------------------
#15
DBCP
   사용자 요청이 있을 때 마다 DB연결을 한다면 코드가 복잡해지며,
   많은 요청이 있을 때 연결 속도가 저하될 수 있다.
   따라서 미리 Connection을 만들어 두고, 필요 시 저장된 공간에서
   가져다 쓴 후 반환하는 기법이다.

JNDI
   디렉터리 서비스에서 제공하는 데이터 및 객체를 발견하고 참고하기 위한
   자바 API이며, 외부에 있는 객체를 가져오기 위한 기술이다.

MyBatis Framework
   소스코드 안에 SQL문을 작성하면 코드가 길어지고 섞여 있어서 유지보수 및 분업이 쉽지 않다.
   MyBatis는 기존 JDBC 방식과는 달리 SQL문을 XML파일에 작성함으로써 코드가 줄어들고,
   SQL문 수정이 편해진다. 또한 DBCP를 사용하여 커넥션을 여러 개 생성하기 때문에 JDBC만
   사용하는 것보다는 작업효율과 가독성이 좋아진다.
------------------------------------------------------------------------------------------------------------------------------
#16
axios, fetch

        axios란?

        브라우저, nodejs를 위한 Promise API (HTTP를 활용한 비동기 통신 라이브러리)
        비슷한 라이브러리에는 ajax가 있음(ajax는 구버전이기 때문에 현재 사용하지 않습니다)

        * API
            두 애플리케이션(서버) 특정 프로토콜(Http와 같은)을 사용하여 서로 통신
            (데이터를 주고 받게) 할 수 있게 하는 것

        Promise 형태를 return ( then, catch 사용이 가능 )
        => HTTP를 통해 서버끼리 요청 메시지와 응답 메세지를 전달해주는 역할
    
    aixos vs fetch
            fetch
                자바스크립트의 빌트인 객체(기본 지원)
                문자열을 반환하기 때문에 json형태로 파싱해주어야만(바꿔주어야만) 사용 가능
                axios에 비해 많은 기능을 지원x, 브라우저에 상위버전에서만 호환
                간단한 요청은 fetch로 진행하는 경우가 많음

            axios
                라이브러리, 별도의 설치 필요
                다양한 보안 관련 옵션 추가, status에 따라 다양한 상태 값 지정
                JSON 형태로 반환하기 때문에 따로 파싱할 필요 없음
                요청을 중간에 취소할 수 있고 요청, 응답을 중간에 가로챌 수 있음
                ( 중간에 값을 가로채서 특정 값 추가(조작) )
                거의 모든 브라우저(크롬,엣지,사파리... ) 버전 지원 
------------------------------------------------------------------------------------------------------              
#17
document.getElementById('hello').innerHTML = '안녕';

document -> html 웹문서

마침표 -> ~의 

getElementById('@') -> 아이디가 '@'인 html 요소 (일명 element) 를 찾아라

innerHTML -> 내부 HTML이라는 뜻

= -> 등호는 프로그래밍에서 오른쪽에 있는걸 왼쪽에 대입하라는 뜻

'안녕' -> 안녕이라는 문자
--------------------------------------------------------------------------------------------------------
