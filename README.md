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
package com.koreait.myapp.di;

import java.time.LocalDateTime;

public class  MemberRegisterService {

   // MemberDao - DB처리를 위해 
   
    private MemberDao memberDao = new MemberDao();  
   

   public void regist(RegisterRequest req) {
        // 이메일로 회원 데이터(Member)조회
       Member member = memberDao.selectByEmail(req.getEmail());
       if (member != null) {
            // 같은 이메일을 가진 회원이 이미 존재하면 예외 발생
          throw new DuplicateMemberException("dup email " + req.getEmail());
       }
       //같은 이메일 가진 회원이 존재하지 않으면 DB에 삽입
       Member newMember = new Member(
             req.getEmail(), req.getPassword(), req.getName(), 
             LocalDateTime.now());
        memberDao.insert(newMember);
 }
}
-----------------------------------------------------------------------------------------
