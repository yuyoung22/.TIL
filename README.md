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
