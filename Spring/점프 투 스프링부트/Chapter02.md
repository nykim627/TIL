![image](https://github.com/user-attachments/assets/66b0ea25-8904-45bb-a283-cbcfefd8eb23)# Chapter02. 스프링 부트의 기본 기능 익히기
> 📖 출처 : [점프 투 스프링부트](https://wikidocs.net/book/7601) 를 읽고 학습한 내용을 기반으로 작성한 TIL입니다.
> 

<br><br>

**💻 2025.04.29**

## [0] 이 장의 목표

- 앞으로 만들어 볼 웹 프로그램인 SBB 게시판 서비스를 개발하기 위해 필요한 기본 내용을 다룰 것
    - 컨트롤러를 이용해 URL과 매핑되는 메서드 관리하기
    - JPA를 이용해 데이터베이스 제어하기
    - HTML, CSS 등을 활용하여 웹 프로그램 꾸미기

<br>

## **[1] 스프링 부트 프로젝트의 구조 이해하기**

<p align="center"><img src="https://github.com/user-attachments/assets/ebd9d70a-a0c0-426f-814d-84e39354ccda" alt="스프링 부트 프로젝트 구조" width=500/></p>


### 1. src/main/java 폴더 살펴보기

**: 자바 파일을 저장하는 공간**

- SbbApplication.java
    - 모든 프로그램에는 프로그램의 시작을 담당하는 파일이 있는데, 스프링 부트로 만든 프로그램에서는 그 파일의 이름이 프로젝트명+Application.java 파일이다.
    - 스프링 부트 프로젝트를 생성할 때 입력한 프로젝트명에 따라 해당 파일이 자동으로 생성됨.
- SbbApplication 클래스에는 반드시 @SpringBootApplication 어노테이션이 적용되어 있어야 함. 이 어노테이션을 통해 스프링 부트 애플리케이션 시작 가능
    
    ```java
    package com.mysite.sbb;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    
    @SpringBootApplication
    public class SbbApplication {
    
        public static void main(String[] args) {
            SpringApplication.run(SbbApplication.class, args);
        }
    }
    ```
    

### 2. src/main/resources 폴더 살펴보기

**: 자바 파일을 제외한 HTML, CSS, 자바스크립트, 환경 파일 등을 저장하는 공간**

> ❓ **환경 파일** : 프로젝트의 설정 정보를 저장하는 파일
> 

> ❓ **HTML, CSS, 자바스크립트(JS)란?**
> 
> - 웹 개발의 필수 언어
> - HTML : 웹 페이지의 기본 구조를 만들 수 있는 마크업 언어
> - CSS : 웹 페이지에 옷을 입히는 스타일 시트 언어
> - JS : 웹 페이지의 상호 작용과 동작 등을 할 수 있게 만드는 프로그래밍 언어
- template 폴더
    - 자바 코드를 삽입할 수 있는 HTML 형식의 템플릿 파일 저장
    - 스프링 부트에서 생성한 자바 객체를 HTML 형태로 출력 가능
    - SBB 게시판 서비스에 필요한 “질문 목록”, “질문 상세” 등의 웹 페이지를 구성하는 HTML 파일 저장
- static 폴더
    - sbb 프로젝트의 스타일시트(css 파일), 자바스트립트(js 파일), 그리고 이미지 파일(jpg 파일, png 파일 등) 등을 저장.
- application.properties 파일
    - sbb 프로젝트의 환경을 설정함. sbb 프로젝트의 환경 변수, 데이터베이스 등의 설정을 이 파일에 저장함.

### 3. src/test/java 폴더 살펴보기

: sbb 프로젝트에서 작성한 파일을 테스트하는 코드를 저장하는 공간

- JUnit과 스프링 부트의 테스트 도구를 사용하여 서버를 실행하지 않은 상태에서 src/main/java 폴더에 작성한 코드를 테스트할 수 있음
    
    > ❓ **JUnit** : 테스트 코드를 작성하고, 작성한 테스트 코드를 실행할 때 사용하는 자바의 테스트 프레임워크
    > 

### 4. build.gradle 파일 살펴보기

: 그레이들(Gradle)이 사용하는 환경 파일

> ❓ **Gradle(그레이들)?**
> 
> - 그루비(Groovy)를 기반으로 한 빌드 도구로, Ant, Maven과 같은 이전 세대의 단점을 보완하고 장점을 취합하여 만든 도구
- build.gradle 파일에는 프로젝트에 필요한 플러그인과 라이브러리를 설치하기 위한 내용을 작성함
    
    > **❓ 그루비(Groovy)와 빌드 도구**
    > 
    > - Groovy는 Gradle 빌드 스크립트를 작성하는 데 사용하는 스크립트 언어로, 문법이 간결하고 가독성이 높음
    > - 빌드 도구는 소스 코드를 컴파일하고 필요한 라이브러리를 내려받을 때 사용함. SBB 프로젝트를 완성하면 단 한 개의 jar 파일로 패키징하여 서버에 배포할 수 있는데, 이때에도 역시 빌드 도구를 사용함

<br>

## [2] 간단한 웹 프로그램 만들기

: 웹 브라우저에서 `http://localhost:8080/sbb` 페이지를 요청했을 때 '안녕하세요 sbb에 오신 것을 환영합니다.'라는 문자열을 출력하도록 만들어 보자.

### 1. URL 매핑과 컨트롤러 이해하기

1. Boot Dashboard에서 시작 버튼을 눌러 로컬 서버 구동하기
    
   <p align="center"><img src="https://github.com/user-attachments/assets/c1f2895f-6681-4b26-b73d-1eda0c7743e4" alt="부트 대시보드" width=500/></p>

    
3. 브라우저에서 [`http://localhost:8080/sbb`](http://localhost:8080/sbb) 페이지 요청해보기
    
   <p align="center"><img src="https://github.com/user-attachments/assets/007e1519-986d-4798-bc6f-79d3daa29d45" alt="로컬호스트8080/sbb 요청" width=500/></p>
    
    ⇒ 404(Not Found) 에러 뜰 것 : 스프링 부트 서버가 [`http://localhost:8080/sbb`](http://localhost:8080/sbb) 라는 요청을 해석할 수 없기 때문에 오류 발생
    
   <p align="center"><img src="https://github.com/user-attachments/assets/fde67bc3-f7fe-4e4d-81fc-72c88ce20735" alt="요청흐름" width=500/></p>

    
5. 문제 해결을 위해 `/sbb` URL에 대한 매핑을 추가하기
    - 브라우저(클라이언트)의 페이지 요청이 발생하면 스프링 부트(서버)는 가장 먼저 컨트롤러에 등록된 URL 매핑을 찾고, 해당 URL 매핑을 발견하면 URL 매핑과 연결된 메서드를 실행함.
      
   <p align="center"><img src="https://github.com/user-attachments/assets/3a5fabf3-f87d-4c1f-8562-2049cefb08b5" alt="요청흐름" width=500/></p>
    
    > **❓ URL 매핑?**
    > 
    > - URL과 컨트롤러의 메서드를 일대일로 연결하는 것
    > - 컨트롤러의 메서드에 @GetMapping 또는 @PostMapping과 같은 어노테이션을 적용하면 해당 URL과 메서드가 연결됨

### 2. 컨트롤러 만들어서 URL 매핑하기

1. src/main/java 폴더의 com.mysite.sbb 패키지에 MainController.java 파일 생성 및 @Controller 어노테이션 적용하기 
    
    ```java
    package com.mysite.sbb;
    
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.GetMapping;
    
    @Controller
    public class MainController {
        @GetMapping("/sbb")
        public void index() {
            System.out.println("index");
        }
    }
    ```
    
    - 스프링 부트는 웹 브라우저로부터 `http://localhost:8080/sbb` 요청이 발생하면 /sbb URL과 매핑되는 index 메서드를 MainController 클래스에서 찾아 실행함.
        
        > @GetMapping에는 `http://localhost:8080`과 같은 도메인명과 포트는 적지 않음.
        > 
        > - 도메인명과 포트는 서버 설정에 따라 변하기 때문
2. 다시 `http://localhost:8080/sbb` URL을 호출해보면, 404가 아닌 다른 에러가 뜰 것
    - 404 해결 : MainController 클래스의 index 메서드가 호출되긴 했지만 다른 오류 발생
   
   <p align="center"><img src="https://github.com/user-attachments/assets/678ace31-44a8-43dd-9780-c58e78ba6616" alt="로컬호스트8080/sbb 요청" width=500/></p>
    
    - 500 오류 코드로 바뀜 : 원래 URL과 매핑된 메서드는 결괏값을 리턴해야 하는데, 아무 값도 리턴하지 않아서 오류 발생.
        - 즉, 오류를 해결하려면 클라이언트(브라우저)로 응답을 리턴해줘야 함
4. MainController.java 수정하기
    
    ```java
    package com.mysite.sbb;
    
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.ResponseBody;
    
    @Controller
    public class MainController {
    
        @GetMapping("/sbb")
        @ResponseBody
        public String index() {
            return "index";
        }
    }
    ```
    
    - 반환타입 void → String 수정
    - @ResponseBody 어노테이션 추가
    
    > 만약 @ResponseBody 어노테이션을 생략한다면 스프링 부트는 ‘index’라는 문자열을 리턴하는 대신 index라는 이름의 템플릿 파일을 찾게 됨.
    > 
    
    ```java
    @GetMapping("/sbb")
    @ResponseBody
    public String index() {
        return "안녕하세요 sbb에 오신 것을 환영합니다.";
    }
    ```
    
    - 반환값 수정
5. 브라우저에서 변경한 문자열이 잘 출력되었는지 확인
    
   <p align="center"><img src="https://github.com/user-attachments/assets/4fd19710-103f-49a2-b672-3619563318a8" alt="로컬호스트8080/sbb 요청" width=500/></p>
    
    - STS의 Spring Boot Devtools에 의해 서버를 재시작하지 않고 브라우저만 새로 고침하여 변경사항 확인 가능
    
   <p align="center"><img src="https://github.com/user-attachments/assets/e9111280-f67d-4183-aa39-fc130f922c9b" alt="요청흐름" width=500/></p>
    

<br>

## [3] JPA로 데이터베이스 사용하기

### 1. 데이터베이스(database, DB)

- 데이터를 모으고 관리하는 저장소

    <p align="center"><img src="https://github.com/user-attachments/assets/caeed0a4-03be-4653-8463-c8397a3862d7" alt="DB" width=500/></p>
    
- DB관리를 위해서는 SQL(Structured Query Language) 언어를 사용해야 함.
- 데이터베이스는 자바를 이해하지 못하지만, ORM(Object Relational Mapping)이라는 도구를 사용하면 자바 문법으로도 데이터베이스를 다룰 수 있음
    - 즉, ORM 이용 시 개발자는 SQL을 직접 작성하지 않아도 데이터베이스의 데이터 처리가 가능!

### 2. ORM이란?

- SQL을 사용하지 않고 데이터베이스를 관리할 수 있는 도구
- 데이터베이스의 테이블을 자바 클래스로 만들어 관리할 수 있음
- SQL의 쿼리(query)문과 ORM 코드(자바로 작성된…)를 비교해보자
    - question 테이블
        
        
        | id | subject | content |
        | --- | --- | --- |
        | 1 | 안녕하세요 | 가입 인사드립니다^^ |
        | 2 | 질문 있습니다 | ORM이 궁금합니다 |
        | … | … | … |
    - SQL 쿼리문은 다음과 같이 작성
        
        ```sql
        insert into question (id, subject, content) values (1, '안녕하세요', '가입 인사드립니다^^');
        insert into question (id, subject, content) values (2, '질문 있습니다', 'ORM이 궁금합니다');
        ```
        
    - ORM 사용하면 자바 코드로 작성 가능
        
        ```java
        Question q1 = new Question();
        q1.setId(1);
        q1.setSubject("안녕하세요");
        q1.setContent("가입 인사드립니다^^");
        
        Question q2 = new Question();
        q2.setId(2);
        q2.setSubject("질문 있습니다");
        q2.setContent("ORM이 궁금합니다");
        this.questionRepository.save(q2);
        ```
        
        - 코드의 양이 더 많아 보이지만 별도의 SQL 문법을 배우지 않아도 데이터베이스를 사용할 수 있기 때문에 매우 편리함.
- 엔티티(entity)
    - Question과 같이, 데이터를 관리하는 데 사용하는 ORM의 자바 클래스
    - 데이터베이스의 테이블과 매핑되는 자바 클래스
- ORM의 장점
    - ORM을 이용하면 MySQL, 오라클 DB, MS SQL과 같은 DBMS의 종류에 관계 없이 일관된 자바 코드를 사용할 수 있어서 프로그램을 유지∙보수하기 편리함
    - 코드 내부에서 안정적인 SQL 쿼리문을 자동으로 생성해주므로, 개발자가 달라도 통일된 쿼리문을 작성할 수 있고, 오류 발생률도 줄일 수 있음
    
    > ❓ DBMS (database management system)
    > 
    > - 데이터베이스를 관리하는 소프트웨어
    > - DB는 데이터를 담은 통, DBMS는 이 통을 관리하는 소프트웨어
    >
    > <p align="center"><img src="https://github.com/user-attachments/assets/a1d99ea2-be52-4cc2-b6b4-17cec144b802" alt="DBMS" width=500/></p>
    > 

<br>

**💻 2025.04.30**

### 3. JPA(Java Persistence API)란?

- 스프링 부트가 객체를 데이터베이스에 저장하고 관리하기 위한 ORM 기술의 표준으로 사용하는 인터페이스 모음
- JPA는 인터페이스 모음이므로, 이 인터페이스를 구현한 실제 클래스가 필요. ⇒ 하이버네이트(Hibernate)
- 하이버네이트 : JPA의 인터페이스를 구현한 실제 클래스이자 자바의 ORM 프레임워크로, 스프링 부트에서 데이터베이스를 관리하기 쉽게 도와줌

### 4. H2 데이터베이스 설치하기

- JPA를 사용해 데이터를 관리하기 위해 먼저 데이터베이스를 설치할 것.
- **H2 데이터베이스** : ***자바로 작성된 관계형 데이터베이스 관리 시스템***
    - 설치가 필요 없고 용량이 가벼우며 **개발용 로컬 DB로 사용하기 좋은** DBMS
    - MySQL, 오라클 DB, MS SQL 등의 굵직한 DBMS보다 설치하기도 쉽고 사용하기도 편리함.
    - 개발 시에는 H2 데이터베이스를 사용하여 빠르게 개발하고 실제 운영 시스템에는 좀 더 규모 있는 DBMS를 사용하는 것이 일반적인 방법
- 설치 방법
    1. build.gradle 파일의 `dependencies`에 `runtimeOnly 'com.h2database:h2’` 를 추가로 입력해 H2 데이터베이스를 설치함
        
        → 이후 해당 파일에서 마우스 오른쪽 버튼 누르고 [Gradle → Refresh Gradle Project] 클릭!
        
    2. 설치한 H2 데이터베이스르 사용하기 위해 `application.properties` 파일에 아래와 같은 내용 작성
        
        (작성 전에 해당 파일은 아무런 내용이 없을 것)
        
        ```
        # DATABASE
        spring.h2.console.enabled=true
        spring.h2.console.path=/h2-console
        spring.datasource.url=jdbc:h2:~/local
        spring.datasource.driverClassName=org.h2.Driver
        spring.datasource.username=[DB사용자명]
        spring.datasource.password=[DB비밀번호]
        ```
        
        - `spring.h2.console.enabled` : H2 콘솔에 접속할 것인지를 묻는 항목. true로 설정.
            
            > H2 콘솔은 H2 데이터베이스를 웹 UI로 보여줌
            > 
        - `spring.h2.console.path` : H2 콘솔로 접속하기 위한 URL 경로.
        - `spring.datasource.url` : 데이터베이스에 접속하기 위한 경로
        - `spring.datasource.driverClassName` : 데이터베이스에 접속할 때 사용하는 드라이버 클래스명
        - `spring.datasource.username` :  데이터베이스의 사용자명 (사용자명은 기본값인 sa로 설정)
        - `spring.datasource.password` : 데이터베이스의 비밀번호 (여기서는 로컬에서 개발 용도로만 사용하므로 비밀번호를 설정하지 않고 비워둠)
    3. spring.datasource.url에 설정한 경로에 해당하는 데이터베이스 파일 만들기
        - `spring.datasource.url`을 `jdbc:h2:~/local`로 설정했으므로 사용자의 홈 디렉터리 아래에 H2 데이터베이스 파일로 `local.mv.db`라는 파일을 생성해야 함.
            
            > 만약 jdbc:h2:~/test라고 설정했다면 test.mv.db라는 파일을 생성해야 함
            > 
        - (macOS 기준) 명령 프롬프트 실행 후 사용자의 홈 디렉터리 경로에서 `touch local.mv.db` 명령 입력해서 파일 생성하기 → ls local.mv.db 명령 입력해서 파일 생성 확인
            
            > macOS 환경이라면 사용자의 홈 디렉터리는 /Users/(사용자명)
            > 
            
          <p align="center"><img src="https://github.com/user-attachments/assets/c770dbfd-97e2-4429-a6c3-138508d5d212" alt="명령 프롬프트" width=500/></p>
            
    4. 홈 디렉터리에서 local.mv.db 파일을 확인할 수 있음
        - 이때 파일은 아무 내용이 없는 빈 파일로 생성됨
        - 파일 사이즈는 반드시  0kb로 생성되어야 함. 만약 1kb로 생성되었다면 파일이 잘못 생성된 것!

       <p align="center"><img src="https://github.com/user-attachments/assets/31faa688-1eac-4961-b571-3d41b9693c78" alt="홈 디렉터리" width=500/></p>
        
    6. H2 콘솔을 통해 데이터베이스에 접속하기
        - 로컬 서버를 다시 시작한 후 브라우저에서 다음 URL 주소로 H2 콘솔에 접속하기
            
            ```
            http://localhost:8080/h2-console
            ```
            
        - 콘솔 화면 확인 (언어 설정 ‘한국어’로 변경 가능)
          
          <p align="center"><img src="https://github.com/user-attachments/assets/1de43ad3-968d-41c3-aa55-f44095ed9bae" alt="H2 콘솔 화면" width=500/></p>
            
        - 콘솔 화면에서 JDBC URL 경로를 `application.properties` 파일에 설정한 데이터베이스 연결 주소인 `jdbc:h2:~/local`로 변경 후 [연결] 버튼 클릭하기
            
            캡처
            
            > ❓ **JDBC**
            > 
            > - Java Database Connectivity의 줄임말로, 자바 프로그램에서 데이터베이스와 상호 작용을 하는 자바 API 기술
            > - 자바 프로그램과 DB를 연결하여 데이터베이스 작업을 수행할 수 있음
        - 다음과 같이 접속된 화면 확인 가능!!
            
          <p align="center"><img src="https://github.com/user-attachments/assets/4fa9c5af-0df2-48f2-a0f7-cfc6e964310a" alt="H2 콘솔 접속화면" width=500/></p>
            

<br>

**💻 2025.05.01**

### 5. JPA 환경 설정하기

- 자바 프로그램에서 데이터베이스에 데이터를 저장하거나 조회하려면 JPA를 사용해야 함.
    1. build.gradle 파일 수정 : dependencies에 `implementation 'org.springframework.boot:spring-boot-starter-data-jpa’` 코드 추가 및 Refresh Gradle해서 JPA 라이브러리 설치하기
        
        > ❓ **implementation**
        > 
        > - build.gradle 파일에서 작성한 implementation은 필요한 라이브러리 설치를 위해 가장 일반적으로 사용하는 설정
        > - 해당 라이브러리가 변경되더라도 이 라이브러리와 연관된 모든 모듈을 컴파일하지 않고, **변경된 내용과 관련이 있는 모듈만 컴파일**하므로 프로젝트를 리빌드(rebuild)하는 속도가 빠름
    2. application.properties 파일 수정 : 아래 항목 추가
        
        ```
        # JPA
        spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.H2Dialect
        spring.jpa.hibernate.ddl-auto=update
        ```
        
        - `spring.jpa.properties.hibernate.dialect` : 스프링 부트와 하이버네이트를 함께 사용할 때 필요한 설정 항목. 표준 SQL이 아닌 하이버네이트만의 SQL를 사용할 때 필요한 항목으로, 하이버네이트의 `org.hibernate.dialect.H2Dialect`를 설정함.
        - `spring.jpa.hibernate.ddl-auto` : 엔티티를 기준으로 데이터의 테이블을 생성하는 규칙을 설정
            - `none` : 엔티티가 변경되더라도 데이터베이스를 변경하지 않음
            - `update` : 엔티티의 변경된 부분만 데이터베이스에 적용함
            - `validate` : 엔티티와 테이블 간에 차이점이 있는지 검사만 함
            - `create` : 스프링 부트 서버를 시작할 때 테이블을 모두 삭제한 후 다시 생성함
            - `create-drop` : `create`와 동일하지만 스프링 부트 서버를 종료할 때에도 테이블을 모두 삭제함
            - 개발 환경에서는 보통 `update` 모드 사용, 운영 환경에서는 `none` 또는 `validate`를 주로 사용

<br>

## [4] 엔티티로 테이블 매핑하기

: JPA를 사용하기 위해 데이터베이스의 구성 요소와 엔티티에 대해 알아볼 것

### 1. 데이터베이스 구성 요소

<p align="center"><img src="https://github.com/user-attachments/assets/52bae31d-7d72-4499-a598-071af22033cc" alt="H2 콘솔 접속화면" width=500/></p>

`출처 : [https://velog.io/@donghoim/정보처리산업기사-70강-관계형-데이터베이스의-구조](https://velog.io/@donghoim/%EC%A0%95%EB%B3%B4%EC%B2%98%EB%A6%AC%EC%82%B0%EC%97%85%EA%B8%B0%EC%82%AC-70%EA%B0%95-%EA%B4%80%EA%B3%84%ED%98%95-%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%EC%9D%98-%EA%B5%AC%EC%A1%B0)` 

### 2. 엔티티 속성 구성하기

: SBB는 질문과 답변을 할 수 있는 게시판 서비스이므로, SBB의 질문과 답변 데이터를 저장할 데이터베이스 테이블과 매핑되는 질문과 답변 엔티티가 필요

> **❓ 엔티티**
> 
> - 데이터베이스 테이블과 매핑되는 자바 클래스
> - 모델 또는 도메인 모델이라고도 함. 이 책에서는 이것을 구분하지 않고 테이블과 매핑되는 클래스를 모두 엔티티라 지칭할 것.
> - 엔티티의 각 속성은 데이터베이스 테이블의 열과 매핑이 될 것.
- 질문(Question) 엔티티
    
    
    | **속성 이름** | **설명** |
    | --- | --- |
    | id | 질문 데이터의 고유 번호 |
    | subject | 질문 데이터의 제목 |
    | content | 질문 데이터의 내용 |
    | createDate | 질문 데이터를 작성한 일시 |
- 답변(Answer) 엔티티
    
    
    | **속성 이름** | **설명** |
    | --- | --- |
    | id | 답변 데이터의 고유 번호 |
    | question | 질문 데이터 (어떤 질문에 대한 답변인지 알아야 하므로 이 속성이 필요) |
    | content | 답변 데이터의 내용 |
    | createDate | 답변 데이터를 작성한 일시 |

### 3. 질문 엔티티 만들기

: src/main/java 폴더의 com.mysite.sbb 패키지에 Question 클래스 생성하기

```java
package com.mysite.sbb;

import java.time.LocalDateTime;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
@Entity
public class Question {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Integer id;
	
	@Column(length = 200)
	private String subject;
	
	@Column(columnDefinition = "TEXT")
	private String content;
	
	private LocalDateTime createDate;
}
```

- @Entity : 스프링 부트가 Question 클래스를 엔티티로 인식하기 위한 어노테이션
- @Getter, @Setter : Getter, Setter 자동생성 위해 롬북의 어노테이션 적용
- @Id : 중복되면 안되는 고유 번호들을 나타내는 기본키 지정
- @GeneratedValue : 데이터 저장 시 해당 속성에 값을 일일이 입력하지 않아도 자동으로 1씩 증가하여 저장
    - `strategy` 옵션 : 고유한 번호를 생성하는 방법을 지정하는 옵션.
    - `GenerationType.IDENTITY` : 해당 속성만 별도로 번호가 차례대로 늘어나도록 할 때 사용
    - 이 옵션 생략 시 해당 어노테이션이 지정된 모든 속성에 번호를 차례로 생성하므로 순서가 일정한 고유 번호를 가질 수 없으므로 보통 `strategy = GenerationType.IDENTITY`를 많이 사용
- @Column : 테이블의 열에 대한 세부 설정을 위해 사용.
    - length 옵션 : 열의 길이 설정 시 사용
    - columnDefinition은 열 데이터의 유형이나 성격을 정의할 때 사용
        - columnDefinition = “TEXT” : “텍스트”를 열 데이터로 넣을 수 있음. 글자 수를 제한할 수 없는 경우 사용
    
    > **❓ @Transient**
    > 
    > - 엔티티의 속성을 테이블의 열로 만들지 않고 클래스의 속성 기능으로만 사용하고자 할 때 적용
    > - 엔티티의 속성은 @Column을 사용하지 않더라고 자동으로 테이블의 열로 인식. 만약 테이블의 열로 인식하고 싶지 않다면 @Transient를 사용해야 함.
    
    > ❓ **엔티티의 속성 이름과 테이블의 열 이름의 차이**
    > 
    > - Question 엔티티에서 createDate 속성의 이름은 데이터베이스의 테이블에서는 create_date라는 열 이름으로 설정.
    > - 즉, 엔티티에서 카멜 케이스(camelCase) 형식의 이름은 데이터베이스의 테이블에서는 snake_case 형식으로 변경됨.
    
    > ❓ **일반적으로 엔티티 생성 시 Setter 메서드를 사용하지 않기를 권장**
    > 
    > - 엔티티는 데이터베이스와 바로 연결되는데, 데이터를 자유롭게 변경할 수 있는 Setter 메서드를 허용하는 것이 안전하지 않다고 판단
    > - 그렇다면 **Setter 메서드 없이 어떻게 엔티티에 값을 저장**하는가?
    >     - 엔티티는 생성자에 의해서만 엔티티의 값을 저장할 수 있게 하고, 데이터를 변경해야 할 경우에는 메서드를 추가로 작성하면 됨.
    > - ***but, 이 책은 복잡도를 낮추고 원활한 설명을 위해 Setter 메서드를 추가하여 진행할 것***

### 4. 답변 엔티티 만들기

: src/main/java 폴더의 com.mysite.sbb 패키지에 Answer 클래스 생성하기

```java
package com.mysite.sbb;

import java.time.LocalDateTime;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.ManyToOne;
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
@Entity
public class Answer {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Integer id;
	
	@Column(columnDefinition = "TEXT")
	private String content;
	
	private LocalDateTime createDate;
	
	@ManyToOne   //답변에서 질문 참조하기
	private Question question;
}
```

- 답변을 통해 질문의 제목을 알고 싶다면 answer.getQuestion().getSubject()를 사용해 접근가능할 것
- question 속성만 추가하면 안되고, 질문 엔티티와 연결된 속성이라는 것을 답변 엔티티에 표시해야 함
    - 게시판 서비스에서는 하나의 질문에 여러 개의 답변이 달릴 수 있으므로, question 속성에 @ManyToOne 을 적용해 질문 엔티티와 N : 1 관계를 나타냄. (실제 데이터베이스에서는 외래키 관계가 생성될 것)
    - N : 1에서 1에 대응하는 질문 엔티티가 부모 엔티티, N에 대응하는 답변 엔티티가 자식 엔티티!

> **❓ int 대신 Integer를 사용하는 이유**
> 
> - JPA(Hibernate)에서 기본 키 타입으로 null을 허용해야 하기 때문
> - JPA가 새 엔티티를 DB에 저장할 때, `id == null`이면 아직 저장 안 됨 (즉 저장 시 `INSERT`), `id ≠ null`이면 이미 저장됨 (즉, 저장 시 `update`)로 인식.
> - 하지만 int은 기본값이 0이고 null을 가질 수 없으므로 JPA가 혼동할 수 있음
> - @Id는 JPA가 null을 체크할 수 있도록  보통 Integer, Long 같은 래퍼 클래스의 사용을 권장

### 5. 질문에서 답변 참조하기

: 양방향 참조를 위해서는 필요. 

```java
package com.mysite.sbb;

import java.time.LocalDateTime;
import java.util.List;

import jakarta.persistence.CascadeType;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.OneToMany;
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
@Entity
public class Question {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Integer id;
	
	@Column(length = 200)
	private String subject;
	
	@Column(columnDefinition = "TEXT")
	private String content;
	
	private LocalDateTime createDate;
	
	@OneToMany(mappedBy = "question", cascade = CascadeType.REMOVE)
	private List<Answer> answerList;
}
```

- `Answer` 객체들로 구성된 `answerList`를 `Question` 엔티티의 속성으로 추가하고 `@OneToMany` 적용.
- 이제 질문에서 답변을 참조하려면 `question.getAnswerList()`를 호출하면 됨!
- `mappedBy` 옵션 : 참조 엔티티의 속성명 정의. 즉, `Answer` 엔티티에서 `Question` 엔티티를 참조한 속성인 `question`을 `mappedBy`에 전달해야 함!
    - "이 연관관계는 저쪽이 주인이야"라고 선언하였기 때문에, 테이블에서는 answerList라는 속성이 따로 보이지는 않음
- `cascade` 옵션 : JPA에서 엔티티 간의 연관 관계 정의 시 사용. 주로 부모 엔티티의 상태 변경에 따라 자식 엔티티에도 동일한 상태 변경을 적용하려는 경우 사용. `CascadeType.REMOVE`를 사용했으므로, 부모 엔티티가 삭제될 때, 자식 엔티티도 함께 삭제하라는 설정.

### 6. 테이블 확인하기

- H2 콘솔에 접속해 엔티티를 통해 Question과 Answer 테이블이 자동 생성된 것을 확인
    
  <p align="center"><img src="https://github.com/user-attachments/assets/9d42939d-df59-4dce-ae14-a2432347bdfa" alt="H2 콘솔 접속화면" width=500/></p>
    

<br>

**💻 2025.05.02**

## [5] 리포지터리로 데이터베이스 관리하기
### 1. 레포지토리 생성하기

- 엔티티와 같이 데이터를 관리하려면 데이터베이스와 연동하는 JPA 레포지터리가 반드시 필요함.
- 레포지토리 : 데이터베이스 테이블의 데이터들을 저장(C), 조회(R), 수정(U), 삭제(D) 등을 할 수 있도록 도와주는 인터페이스. 테이블에 접근하고, 데이터를 관리하는 메서드를 제공함.
1. com.mysite.sbb 패키지에서 QuestionRepository 인터페이스 생성하기
    
    ```java
    package com.mysite.sbb;
    
    import org.springframework.data.jpa.repository.JpaRepository;
    
    public interface QuestionRepository extends JpaRepository<Question, Integer> {
    
    }
    ```
    
2. 생성한 인터페이스를 레포지토리로 만들기 위해 JpaRepository 인터페이스를 상속받기. 
    
    > **❓ JpaRepository**
    > 
    > - JPA가 제공하는 인터페이스 중 하나로 CRUD 작업을 처리하는 메서드들을 이미 내장하고 있어 데이터 관리 작업을 좀 더 편리하게 처리할 수 있음
    > - JpaRepository<Question, Integer>는 Question 엔티티로 레포지토리를 생성하며, Question 엔티티의 기본키가 Integer라는 의미
    
    > **❓ CRUD**
    > 
    > - Create, Read, Update, Delete
    > - 데이터 처리의 기본 기능
3. 마찬가지로 AnswerRepository 인터페이스 생성하기
    
    ```java
    package com.mysite.sbb;
    
    import org.springframework.data.jpa.repository.JpaRepository;
    
    public interface AnswerRepository extends JpaRepository<Answer, Integer> {
    
    }
    ```
    

### 2. JUnit 설치하기

- 레포지토리를 이용하여 데이터를 저장하려면 질문을 등록하는 화면과, 사용자가 입력한 질문 관련 정보를 저장하는 컨트롤러, 서비스 파일 등이 필요함
- 하지만 JUnit 사용 시 이러한 프로세스를 따르지 않고 **레포지토리만 개별적으로 실행해 테스트해볼 수 있음**
- JUnit : 테스트 코드를 작성하고, 작성한 테스트 코드를 실행할 때 사용하는 자바의 테스트 프레임워크. 레포지토리뿐만 아니라 소프트웨어 개발 시 테스트 작업을 수행할 때 많이 사용.
1. build.gradle : dependencies 마지막에 아래와 같은 내용 추가 + 라이브러리 설치
    
    ```
        testImplementation 'org.junit.jupiter:junit-jupiter'
        testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
    ```
    
    - testRuntimeOnly : 해당 라이브러리가 테스트 실행 시에만 사용됨
  
<br>

**💻 2025.05.05**

### 3. 질문 데이터 저장하기

: 질문 엔티티로 테이블을 만들었으니 이제 만들어진 테이블에 데이터를 생성하고 저장해 보자.

1. `com.mysite.sbb` 패키지에 `SbbApplicationTests.java` 파일 수정하기
    
    ```java
    package com.mysite.sbb;
    
    import java.time.LocalDateTime;
    
    import org.junit.jupiter.api.Test;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.test.context.SpringBootTest;
    
    @SpringBootTest
    class SbbApplicationTests {
    	
    	@Autowired
    	private QuestionRepository questionRepository;
    	
    	@Test
    	void testJpa() {
    		Question q1 = new Question();
    		q1.setSubject("sbb가 무엇인가요?");
    		q1.setContent("sbb에 대해서 알고 싶습니다.");
    		q1.setCreateDate(LocalDateTime.now());
    		this.questionRepository.save(q1); //첫번째 질문 저장
    		
    		Question q2 = new Question();
    		q2.setSubject("스프링부트 모델 질문입니다.");
    		q2.setContent("id는 자동으로 생성되나요?");
    		q2.setCreateDate(LocalDateTime.now());
    		this.questionRepository.save(q2); //두번째 질문 저장
    	}
    
    	@Test
    	void contextLoads() {
    	}
    
    }
    
    ```
    
    - 질문 엔티티의 데이터를 생성할 때 레포지터리(QuestionRepository)가 필요하므로 @Autowired 어노테이션을 통해 QuestionRepository의 객체를 주입함
        
        > **❓ 의존성 주입(DI)**
        > 
        > - 스프링이 객체를 대신 생성하여 주입하는 기법
    - @Autowired : questionRepository 변수는 선언만 되어 있고 그 값이 비어 있음. 하지만 @Autowired 어노테이션을 해당 변수에 적용하면 스프링 부트가 questionRepository 객체를 자동으로 만들어 주입함.
    - 순환 참조 문제와 같은 이유로 개발 시 @Autowired 보다는 생성자를 통한 객체 주입 방식을 권장하지만, **테스트 코드의 경우 JUnit이 생성자를 통한 객체 주입을 지원하지 않으므로 테스트 코드 작성 시에만 @Autowwired를 사용**하고 실제 코드 작성 시에는 생성자를 통해 객체 주입 방식을 사용해볼 것.
    - @Test : testJpa 메서드가 테스트 메서드임을 나타냄
        - SbbApplicationTests 클래스를 JUnit으로 실행 시 @Test 어노테이션이 붙은 testJpa 메서드가 실행됨
    - testJpa : q1, q2라는 질문 엔티티의 객체를 생성하고 QuestionRepository를 이용하여 그 값을 데이터베이스에 저장함.
        
        
        | ID | Content | CreateDate | Subject |
        | --- | --- | --- | --- |
        | 1 | sbb에 대해서 알고 싶습니다. | 2023-09-01-15:30:30 | sbb가 무엇인가요? |
        | 2 | id는 자동으로 생성되나요? | 2023-09-01-15:30:30 | 스프링 부트 모델 질문입니다. |
2. `SbbApplicationTests` 클래스 실행하기 
    - [Run → Run As → JUnit Test] 순서대로 선택
    - 로컬 서버가 이미 구동 중이라면 `The file is locked: nio:/Users/pahkey/local.mv.db`와 비슷한 오류가 발생할 것
        - H2 데이터베이스는 파일 기반의 데이터베이스인데, 이미 로컬 서버가 동일한 데이터베이스 파일(local.mv.db)을 점유하고 있어 발생하는 오류
        - 테스트할 때는 먼저 로컬 서버를 중지해야 함.
    - 오류가 발생한다면 로컬 서버 중시 후 다시 테스트 실행해보기
        
        <p align="center"><img src="https://github.com/user-attachments/assets/c720a1ee-a2f7-4dc8-995c-79700cfe6425" alt="Junit test" width=500/></p>
        
        - 초록색 바가 표시되면 성공이고 빨간색 바가 표시되면 실패를 의미
3. 실제 데이터베이스에 값이 잘 들어갔는지 확인하기 위해 다시 로컬 서버를 시작하고 H2 콘솔에 접속하여 다음 쿼리문 실행
    
    ```
    SELECT * FROM QUESTION
    ```

    <p align="center"><img src="https://github.com/user-attachments/assets/5a446335-11d7-47c9-8111-67c6448645c0" alt="h2 콘솔 접속화면" width=500/></p>
    
    - 우리가 저장한 Question 객체 값이 데이터베이스의 데이터로 저장된 것을 확인 가능
    - id는 질문 엔티티의 기본키로, 2-04절에서 질문 엔티티를 생성할 때 `@GeneratedValue`를 활용해 설정했던 대로 속성값이 자동으로 1씩 증가하는 것을 확인 가능
    - 내가 test를 두번 했더니 데이터가 두 번 생성된 것으로 보임

<br>

**💻 2025.05.06**

### 4. 질문 데이터 조회하기
