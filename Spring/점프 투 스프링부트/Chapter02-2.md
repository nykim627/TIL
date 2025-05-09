# Chapter02. 스프링 부트의 기본 기능 익히기 (2)
> 📖 출처 : [점프 투 스프링부트](https://wikidocs.net/book/7601) 를 읽고 학습한 내용을 기반으로 작성한 TIL입니다.
> 

<br><br>

**💻 2025.05.08**

## [10] 도메인별로 분류하기

: 지금까지 많은 자바 파일을 패키지를 활용하여 분류하기

- 다음 표와 같이 SBB 프로젝트의 도메인별로 패키지 구성
    
    
    | 도메인 이름 | 패키지 이름 | 설명 |
    | --- | --- | --- |
    | question | `com.mysite.sbb.question` | 게시판의 질문과 관련된 자바 파일 모음 |
    | answer | `com.mysite.sbb.answer` | 게시판의 답변과 관련된 자바 파일 모음 |
    | user | `com.mysite.sbb.user` | 사용자와 관련된 자바 파일 모음 |
    - 나머지 파일들은 특정 도메인에 속하지 않으므로 `com.mysite.sbb` 패키지에 그대로 놔둠
    
  <p align="center"><img src="https://github.com/user-attachments/assets/f4abc594-adb1-4974-b6d8-590c510c9857" alt="스프링 부트 프로젝트 구조" width=500/></p>

    
- 자바 파일을 도메인에 따라 패키지로 나누어 관리 시 비슷한 기능이나 관련된 개념을 함께 묶어 코드들을 구조화하여 정리하므로 **코드의 읽고 쓰기, 유지보수 시 편리**함

<br>

**💻 2025.05.09**

## [11] 질문 목록 만들기

: http://localhost:8080/question/list 주소에 접속할 때 질문 목록이 담긴 페이지가 나오도록 만들기

### 1. 질문 목록 URL 매핑하기

1. `QuesitonController.java` 파일 생성 및 아래 코드 작성
    
    ```java
    package com.mysite.sbb.question;
    
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.ResponseBody;
    
    @Controller
    public class QuestionController {
    	
    	@GetMapping("/question/list")
    	@ResponseBody
    	public String list() {
    		return "question list";
    	}
    }
    
    ```
    
    - 로컬 서버 실행 후 주소에 접속해보면 다음과 같이 “question list”라는 문자열이 출력될 것
        
      <p align="center"><img src="https://github.com/user-attachments/assets/c611a0a7-c273-4e64-afb4-29bd7bac856c" alt="서버 실행 결과" width=500/></p>
        

### 2. 템플릿 설정하기

- 일반적으로 브라우저에 응답하는 문자열은 위의 예처럼 자바 코드에서 직접 만들기보다는, 템플릿 방식을 사용
    
    > ❓ **템플릿(template)**
    > 
    > - 자바 코드를 삽입할 수 있는 HTML 형식의 파일
- 스프링 부트는 템플릿 엔진을 지원하는데, 이 책에서는 스프링 진영에서 추천하는 [타임리프(Thymeleaf)](https://www.thymeleaf.org/) 템플릿 엔진을 사용할 것
    
    > 대표적인 템플릿 엔진 : Thymeleaf, Mustache, Groovy, Freemarker, Velovity 등…
    > 
- build.gradle 파일을 수정 및 Refresh하여 타임리프 설치하기
    
    ```
    (... 생략 ...)
    
    dependencies {
        (... 생략 ...)
        implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
        implementation 'nz.net.ultraq.thymeleaf:thymeleaf-layout-dialect'
    }
    
    (... 생략 ...)
    ```
    
- 타임리프를 적용하려면 로컬 서버를 재시작해야 하므로 반드시 로컬 서버를 재시작하고 이후 과정을 진행하자.

### 3. 템플릿 사용하기

1. `src/main/resources/templates` 아래 `question_list.html` 템플릿 생성 후 아래 코드 작성
    
    ```html
    <h2>Hello Template</h2>
    ```
    
2. `QuestionController.java` 파일 수정
    
    ```java
    package com.mysite.sbb.question;
    
    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.GetMapping;
    
    @Controller
    public class QuestionController {
    	
    	@GetMapping("/question/list")
    //	@ResponseBody    //템플릿을 사용하기 때문에 필요없어짐
    	public String list() {
    		return "question_list";   //템플릿 파일 이름 리턴
    	}
    }
    
    ```
    
    - 다시 로컬 서버 실행 후, http://localhost:8080/question/list 주소에 접속하면 `question_list.html` 파일에 작성한 `<h2>Hello Template</h2>` 내용이 브라우저에 출력되는 것 확인 가능
        
      <p align="center"><img src="https://github.com/user-attachments/assets/587814ea-87bb-4f37-80ac-3322463612ef" alt="서버 실행 결과" width=500/></p>
        
