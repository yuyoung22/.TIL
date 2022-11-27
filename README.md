# .TIL

#jdbc 프로그래밍

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
====================
const express = require('express');
const app = express();
// morgan
/* 
log를 찍어주는 미들웨어는
다양한 모드를 지원
ex) dev, combined, common, short, tiny
보통 개발용으로는 dev를 사용하고
배포용으로는 combined를 사용

npm i morgan
*/

const morgan = require('morgan');
app.use(morgan('dev'));

// body-parser
// req message로 전달받은 body의 데이터를 해석
// express 4.16.0 이상부터는 기본 내장

app.use(express.json()); // json 데이터를 읽는 것을 허용
app.use(express.urlencoded({ extended: false }));
// url에 있는 정보 (param, qurey-string) express 내부에 있는 해석툴로 해석을 할 것인지
// false면 내장된 것을 사용
// true면 추가로 라이브러릴 설치해서 내가 원하는 라이브러리로 해석
// qs라는 모듈을 내장하고 있으나 다른 라이브러리를 사용하여 url에 담긴 데이터를 해석할 수도 있음

// static
// express에 내장, 정적인 파일(리소스, html, css, js, img, mp4... ) 을 서버에 제공

const path = require('path');
// ()안에 있는 경로를 문자열로 나타냄, 기본값 root 폴더
app.use('/', express.static(path.join(__dirname, 'public')));
// URL "/"로 접속 시 public 폴더로 접근이 가능

app.get('/', (req, res) => {
    res.send('Hello express');
});

app.listen(3000, () => {
    console.log('3000번으로 서버 실행 중');
});
