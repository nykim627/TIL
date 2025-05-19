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
        
<br>

**💻 2025.05.12**

### 4. 데이터를 템플릿에 전달하기

: 질문 목록이 담긴 데이터를 조회하여 이를 템플릿을 통해 화면에 전달하기

- `QuestionController.java` 수정하기
    - QuestionRepository로 질문 목록 조회 후 Model 클래스를 사용하여 템플릿에 전달하기
    
    ```java
    package com.mysite.sbb.question;
    
    import java.util.List;
    
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.GetMapping;
    
    import lombok.RequiredArgsConstructor;
    
    @RequiredArgsConstructor
    @Controller
    public class QuestionController {
    	
    	private final QuestionRepository questionRepository;
    	
    	@GetMapping("/question/list")
    //	@ResponseBody    //템플릿을 사용하기 때문에 필요없어짐
    	public String list(Model model) {
    		List<Question> questionList = this.questionRepository.findAll();
    		model.addAttribute("questionList", questionList);
    		return "question_list";   //템플릿 파일 이름 리턴
    	}
    }
    ```
    
    - `@RequiredArgsConstructor` : 롬복(Lombok)이 제공하는 어노테이션으로, final이 붙은 속성을 포함하는 생성자를 자동으로 만들어 주는 역할
    - `Model` 객체 : 자바 클래스와 템플릿 간의 연결 고리 역할
        - 생성한 `questionList` 데이터를 ‘questionList’라는 이름으로 `Model` 객체에 저장
        - `Model` 객체에 값을 담아두면 템플릿에서 그 값을 사용 가능
        - `Model` 객체는 따로 생성할 필요 없이 컨트롤러의 메서드에 매개변수로 지정하기만 하면 **스프링 부트가 자동으로 Model 객체 생성**

### 5. 데이터를 화면에 출력하기

- `question_list.html` 템플릿 수정하기
    
    ```java
    <h2>Hello Template</h2>
    
    <table>
    	<thead>
    		<tr>
    			<th>제목</th>
    			<th>작성일시</th>
    		</tr>
    	</thead>
    	<tbody>
    		<tr th:each="question : ${questionList}">
    			<td th:text="${question.subject}"></td>
    			<td th:text="${question.createDate}"></td>
    		</tr>
    	</tbody>
    </table>
    ```
    
    - `<tr th:each=”question : ${questionList}”>` 코드에 주목
        - `th:` 는 타임리프에서 사용하는 속성임을 나타냄. 이 부분이 자바 코드와 연결됨
        - `questionList`에 저장된 데이터를 하나씩 꺼내 `question` 변수에 대입한 후 `questionList`의 개수만큼 반복하며 `<tr> … </tr>` 문장을 출력하라는 의미 (like 자바의 for each)
    - `<td th:text=”${question.subject}”></td>`
        - `question` 객체의 subject를 `<td>` 태그로 출력하기 (createDate도 마찬가지)
- 브라우저에서 다시 http://localhost:8080/question/list 에 접속하기

  <p align="center"><img src="https://github.com/user-attachments/assets/9ce48d9d-d5e1-40f8-b95f-09b499385b6b" alt="서버 실행 결과 브라우저 화면" width=500></p>
    
    ⇒ 게시판의 웹페이지 하나 완성!
    

### 6. 자주 사용하는 타임리프(Timeleaf)의 3가지 속성

: 타임리프의 여러 속성 중 다음 3가지 속성을 자주 사용. (이 3가지만 알아도 일반적인 화면 만들기에 충분!)

1. 분기문 속성 (if문, else if문, …)
    
    ```java
    th:if="${question != null}"
    ```
    
    - question 객체가 null이 아닌 경우에만 이 속성을 포함한 요소가 표시됨
2. 반복문 속성 (자바의 for each문과 유사)
    
    **[방1]**
    
    ```java
    th:each="question : ${questionList}"
    ```
    
    **[방2]**
    
    ```java
    th:each="question, loop : ${questionList}"
    ```
    
    - 추가한 loop 객체를 이용하여 루프 내에서 다음과 같이 사용 가능
        - loop.index : 루프의 순서(루프의 반복 순서, **0부터** 1씩 증가)
        - loop.count : 루프의 순서(루프의 반복 순서, **1부터** 1씩 증가)
        - loop.size : 반복 객체의 요소 개수(크기) (ex. questionList의 요소 개수)
        - loop.first : 루프의 첫 번째 순서인 경우 true 반환
        - loop.last : 루프의 마지막 순서인 경우 true
        - loop.odd : 루프의 홀수 번째 순서인 경우 true
        - loop.even : 루프의 짝수 번째 순서인 경우 true
        - loop.current : 현재 대입된 객체 (여기서는 question과 동일)
3. 텍스트 속성 : 해당 요소의 텍스트값 출력
    
    ```java
    th:text="${question.subject}"
    ```
    
    - 텍스트는 `th:text` 속성 대신에 다음처럼 **대괄호를 사용하여 값을 직접 출력**할 수도 있음
        
        ```java
        <tr th:each="question : ${questionList}">
        	<td>[[${question.subject}]]</td>
        	<td>[[${question.createDate}]]</td>
        </tr>
        ```
        

<br>

**💻 2025.05.13**

## [12] 루트 URL 사용하기

> ❓ **루트 URL**
> 
> - 서버의 URL을 요청할 때 도메인명 뒤에 아무런 주소도 덧붙이지 않는 URL
>     
>     ex)구글의 루트 URL은 google.com
>     
> - 루트 URL을 요청했을 때 보여지는 페이지를 메인 페이지라고 함

: SBB 서비스도 루트 URL을 요청했을 때 질문 목록 화면으로 이동하도록 해보자.

- MainController.java 수정하기
    
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
    		return "안녕하세요 sbb에 오신 것을 환영합니다.";
    	}
    	
    	@GetMapping("/")
    	public String root() {
    		return "redirect:/question/list";
    	}
    
    }
    ```
    
    - root 메서드 추가 후 `/` URL 매핑
    - `redirect:/question/list` : `/question/list` URL로 페이지를 **리다이렉트**하라는 명령어
        
        > **❓ 리다이렉트**
        > 
        > - 클라이언트가 요청하면 새로운 URL로 전송하는 것
    - 이제 루트 URL인 `localhost:8080`으로 접속하면 `localhost:8080/question/list`로 주소가 바뀌면서 질문 목록이 있는 웹 페이지로 연결됨

<br>

## [13] 서비스 활용하기

: 질문 목록에서 질문의 제목을 클릭하면 해당 질문과 관련된 상세 내용이 담긴 화면으로 넘어가게끔 하려고 함

- 지금까지는 `QuestionController`에서 `QuestionRepository`를 직접 접근해 질문 목록 데이터를 조회했지만, 대부분의 규모 있는 스프링 부트 프로젝트는 **컨트롤러에서 레포지토리를 직접 호출하지 않고 중간에 서비스(Service)를 두어 데이터를 처리**함. 서비스를 사용하여 SBB 프로그램을 개선해 보기.

### 1. 서비스(Service) 이해하기

- 스프링에서 데이터 처리를 위해 작성하는 클래스
- 서비스 없이도 웹 프로그램을 동작시키는 데 문제가 없지만, 굳이 서비스를 사용하는 이유?
    1. 복잡한 코드의 모듈화 가능
        - A 컨트롤러와 B 컨트롤러가 모두 C라는 레포지토리의 메서드 a, b, c를 순서대로 실행할 때, 모듈화를 하지 않는다면 두 컨트롤러가 중복된 코드를 가지게 될 것.
        - C 레포지토리의 메서드 a, b, c를 호출하는 기능을 서비스로 만들고(모듈화) 이 서비스를 호출하여 사용한다면, 모듈화가 가능
    2. 앤티티 객체를 DTO 객체로 변환 가능
        - 앞에서 작성한 Question, Answer 클래스는 모두 엔티티 클래스.
        - 엔티티 클래스는 데이터베이스와 직접 맞닿아 있는 클래스이므로 컨트롤러 또는 타임리트와 같은 템플릿 엔진에 전달해 사용하는 것은 좋지 않음.
            - 엔티티 객체에는 민감한 데이터가 포함될 수 있는데, 타임리프에서 엔티티 객체를 직접 사용하면 민감한 데이터가 노출될 위험이 있기 때문
            
            → 엔티티를 대신해 사용할 DTO(Data Transfer Object) 클래스가 필요
            
            → 엔티티 객체를 DTO 객체로 변환하는 작업도 필요
            
        
        ⇒ 서비스는 컨트롤러와 레포지토리 중간에서 엔티티 객체와 DTO 객체를 서로 변환하는 처리를 수행한 후 양방향에 전달하는 역할을 함
        
        > 이 책은 간결한 설명을 위해 별도의 DTO를 만들지 않고 엔티티 객체를 컨트롤러와 타임리프에서 그대로 사용할 예정. **but 실제 프로그램을 개발할 때는 엔티티 클래스를 대신할 DTO 클래스를 만들어 사용하기를 권장!**


<br>

**💻 2025.05.14**

### 2. 서비스 만들기

- com.mysite.sbb.question 패키지에 QuestionService.java 파일 생성 후 다음과 같이 코드 작성하기
    
    ```java
    package com.mysite.sbb.question;
    
    import java.util.List;
    
    import org.springframework.stereotype.Service;
    
    import lombok.RequiredArgsConstructor;
    
    @RequiredArgsConstructor
    @Service
    public class QuestionService {
    	private final QuestionRepository questionRepository;
    	
    	public List<Question> getList(){
    		return this.questionRepository.findAll();
    	}
    }
    
    ```
    
    - 생성한 클래스를 서비스로 만들기 위해 클래스명 위에 @Service 어노테이션 작성 시 스프링 부트가 자동으로 서비스로 인식
    - @RequiredArgsConstructor 과 final 선언을 통해 questionRepository 주입
    - 질문 목록 데이터를 조회하여 리턴하는 getList 메서드 추가 (Controller의 list 메서드에 사용했던 코드를 그대로 사용)

### 3. 컨트롤러에서 서비스 사용하기

- [QuestionController.java](http://QuestionController.java) 파일로 돌아가 QuestionController가 레포지토리 대신 서비스를 사용하도록 수정하기
    
    ```java
    package com.mysite.sbb.question;
    
    import java.util.List;
    
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.GetMapping;
    
    import lombok.RequiredArgsConstructor;
    
    @RequiredArgsConstructor
    @Controller
    public class QuestionController {
    	
    	private final QuestionService questionService;
    	
    	@GetMapping("/question/list")
    //	@ResponseBody    //템플릿을 사용하기 때문에 필요없어짐
    	public String list(Model model) {
    		List<Question> questionList = this.questionService.getList();
    		model.addAttribute("questionList", questionList);
    		return "question_list";   //템플릿 파일 이름 리턴
    	}
    }
    
    ```
    
    - repository → service로 모두 변경
    - http://localhost:8080/question/list 페이지에 접속하면 레포지토리를 사용했을 때와 동일한 화면 확인 가능
        <p align="center"><img src="https://github.com/user-attachments/assets/173e8e8d-8a68-46ae-9871-f7ba1fffb4fe" width="500"></p>

        
- 앞으로 작성할 다른 컨트롤러들도 이와 같이 레포지토리를 직접 사용하지 않고 **컨트롤러 → 서비스 → 레포지토리** 순서로 접근하는 과정을 거쳐 데이터를 처리할 것!
 
<br>

**💻 2025.05.15**

## [14] 상세 페이지 만들기

: 질문 목록에서 질문의 제목을 클릭하면 해당 질문과 관련된 상세 내용이 담긴 페이지로 넘어가게끔 기능을 추가해보자

### 1. 질문 목록의 제목에 링크 추가하기

- question_list.html 수정
    
    ```html
    <h2>Hello Template</h2>
    
    <table>
    	<thead>
    		<tr>
    			<th>제목</th>
    			<th>작성일시</th>
    		</tr>
    	</thead>
    	<tbody>
    		<tr th:each="question : ${questionList}">
    			<td>
    				<a th:href="@{|/question/detail/${question.id}|}" th:text="${question.subject}"></a>
    			</td>
    			<td th:text="${question.createDate}"></td>
    		</tr>
    	</tbody>
    </table>
    ```
    
    - <td> 태그 내에 <a> 태그 추가하여 질문의 상세 내용이 담긴 웹페이지로 이동할 수 있는 링크로 변경
    - 제목에 상세 페이지 **URL을 연결하기 위해 타임리프의 th:href 속성 사용**
        - 규칙1) URL은 반드시 `@{`와 `}` 문자 사이에 입력해야 함
        - 규칙2) 문자열과 자바 객체의 값을 더할 때는 좌우를 반드시 `|`로 감싸 주어야 함!
            - `/question/detail/`(문자열)과 `${question.id}`(자바 객체) 값을 조합하여 `/question/detail/${question.id}`로 작성

### 2. 상세 페이지 컨트롤러 만들기

1. 브라우저를 통해 질문 목록 페이지에 접속하여 링크를 클릭해보면, 다음과 같은 오류가 발생할 것

   <p align="center"><img src="https://github.com/user-attachments/assets/06c111d4-ac7b-4810-a738-44100fe1e0de" width=500></p>
    
   <p align="center"><img src="https://github.com/user-attachments/assets/5dae5109-b95d-494a-a136-7558a3fb288e" width=500></p>

    
    - 아직 `http://localhost:8080/question/detail/2` 를 매핑하지 않았기 때문에 404 오류 발생
3. QuestionController에 질문 상세 페이지 URL을 매핑하기
    
    ```java
    package com.mysite.sbb.question;
    
    import java.util.List;
    
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PathVariable;
    
    import lombok.RequiredArgsConstructor;
    
    @RequiredArgsConstructor
    @Controller
    public class QuestionController {
    	
    	private final QuestionService questionService;
    	
    	@GetMapping("/question/list")
    //	@ResponseBody    //템플릿을 사용하기 때문에 필요없어짐
    	public String list(Model model) {
    		List<Question> questionList = this.questionService.getList();
    		model.addAttribute("questionList", questionList);
    		return "question_list";   //템플릿 파일 이름 리턴
    	}
    	
    	@GetMapping(value = "/question/detail/{id}")
    	public String detail(Model model, @PathVariable("id") Integer id) {
    		return "question_detail";
    	}
    }
    
    ```
    
    - 변하는 파라미터값(id)을 얻을 때메는 `@PathVariable` 어노테이션 사용.
        - `@GetMapping(value=”/question/detail/{id}”)`에서 사용한 id와 `@PathVariable(”id”)`의 매개변수 이름이 동일해야 함!!
    - 다시 로컬 서버 실행 후 브라우저에서 해당 URL을 입력하면 404가 아닌 500 에러가 발생.

     <p align="center"><img src="https://github.com/user-attachments/assets/b3f457f3-6de9-4723-9b1e-255e8aa86b4b" width=500></p>

        
        - 응답으로 리턴한 quesiton_detail 템플릿이 없기 때문
4. 응답 템플릿 생성하기
    - templates에 question_detail.html 파일 새로 생성 후 다음 내용 작성
    
    ```html
    <h1>제목</h1>
    <div>내용</div>
    ```
    
    - 다시 로컬 서버 재시작 후 URL 요청 시 오류 없이 다음과 같은 화면 출력!
        
      <p align="center"><img src="https://github.com/user-attachments/assets/2bb0d0ba-730e-43e3-84cf-468ea0f6f594" width=500></p>


<br>

**💻 2025.05.16**

### 3. 상세 페이지 출력하기

: 화면에 문자열 대신 질문 데이터의 제목과 내용을 출력하기

1. 질문데이터 조회 : `QuestionService.java` 수정하기 
    
    ```java
    package com.mysite.sbb.question;
    
    import java.util.List;
    import java.util.Optional;
    
    import org.springframework.boot.context.config.ConfigDataLocationNotFoundException;
    import org.springframework.boot.context.config.ConfigDataNotFoundException;
    import org.springframework.stereotype.Service;
    
    import lombok.RequiredArgsConstructor;
    
    @RequiredArgsConstructor
    @Service
    public class QuestionService {
    	private final QuestionRepository questionRepository;
    	
    	public List<Question> getList(){
    		return this.questionRepository.findAll();
    	}
    	
    	public Question getQuestion(Integer id) {
    		Optional<Question> question = this.questionRepository.findById(id);
    		if(question.isPresent()) {
    			return question.get();
    		}else {
    			throw new DataNotFoundException("question not found"); //현재 해당 클래스 존재x
    		}
    	}
    }
    
    ```
    
2. `DataNotFoundException` 클래스 정의하기 (com.mysite.sbb 패키지에 생성)
    
    ```java
    package com.mysite.sbb;
    
    import org.springframework.http.HttpStatus;
    import org.springframework.web.bind.annotation.ResponseStatus;
    
    @ResponseStatus(value = HttpStatus.NOT_FOUND, reason = "entity not found")
    public class DataNotFoundException extends RuntimeException{
    	private static final long serialVersionUID = 1L;
    	public DataNotFoundException(String message) {
    		super(message);
    	}
    }
    ```
    
    - 이 예외 발생 시 스프링 부트는 설정한 HTTP 상태 코드(404 - not found)와 이유를 포함한 응답을 생성하여 클라이언트에게 반환함
    
    > ❓ RuntimeException : 실행 시 발생하는 예외
    > 
3. `QuestionController.java` 수정하기
    
    ```java
    package com.mysite.sbb.question;
    
    import java.util.List;
    
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PathVariable;
    
    import lombok.RequiredArgsConstructor;
    
    @RequiredArgsConstructor
    @Controller
    public class QuestionController {
    	
    	private final QuestionService questionService;
    	
    	@GetMapping("/question/list")
    //	@ResponseBody    //템플릿을 사용하기 때문에 필요없어짐
    	public String list(Model model) {
    		List<Question> questionList = this.questionService.getList();
    		model.addAttribute("questionList", questionList);
    		return "question_list";   //템플릿 파일 이름 리턴
    	}
    	
    	@GetMapping(value = "/question/detail/{id}")
    	public String detail(Model model, @PathVariable("id") Integer id) {
    		Question question = this.questionService.getQuestion(id);   //service 호출
    		model.addAttribute("question", question);   //question 객체를 템플릿에 저장 
    		return "question_detail";
    	}
    }
    ```
    
4. `question_detail.html` 수정하기
    
    ```html
    <h1 th:text="${question.subject}"></h1>  //저장해둔 question 객체 사용하기
    <div th:text="${question.content}"></div>
    ```
    
5. 이후 상세 페이지를 다시 요청하면 아래와 같은 화면 확인 가능
    
   <p align="center"><img src="https://github.com/user-attachments/assets/e0a9349c-3b78-4054-8126-d9b32b5912d7" width=500></p>

    
    - 아래와 같이 존재하지 않는 id값에 대한 페이지 요청 시 아래와 같이 DataNotFoundException 예외 클래스가 실행되어 404 NOT FOUND 에러와 설정해 둔 에러 메시지가 출력됨
        
      <p align="center"><img src="https://github.com/user-attachments/assets/b8109199-bf33-488e-aeaf-3821ec50ac58" width=500></p>
        

### 4. URL 프리픽스 알아두기

: `QuestionController`에 속하는 URL 매핑은 항상 `/question` 프리픽스로 시작하도록 설정하기

- `QuestionController` 클래스명 위에 `@RequestMapping(”/question”)` 어노테이션을 추가하고, 클래스 내부의 메서드 단위에서는 /question을 생략하고 그 뒷부분만을 적으면 됨
    
    (아래 코드의 RequestMapping, GetMapping 확인)
    
    ```java
    package com.mysite.sbb.question;
    
    import java.util.List;
    
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.PathVariable;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    import lombok.RequiredArgsConstructor;
    
    @RequestMapping("/question")
    @RequiredArgsConstructor
    @Controller
    public class QuestionController {
    	
    	private final QuestionService questionService;
    	
    	@GetMapping("/list")
    //	@ResponseBody    //템플릿을 사용하기 때문에 필요없어짐
    	public String list(Model model) {
    		List<Question> questionList = this.questionService.getList();
    		model.addAttribute("questionList", questionList);
    		return "question_list";   //템플릿 파일 이름 리턴
    	}
    	
    	@GetMapping(value = "/detail/{id}")
    	public String detail(Model model, @PathVariable("id") Integer id) {
    		Question question = this.questionService.getQuestion(id);   //service 호출
    		model.addAttribute("question", question);   //question 객체를 템플릿에 저장 
    		return "question_detail";
    	}
    }
    
    ```
    
- 다른 프로젝트 진행 시 컨트롤러의 성격에 맞게 프리픽스 사용 여부를 결정하자

## [15] 답변 기능 만들기

: 질문에 답변을 입력하고, 입력한 답변을 질문 상세 페이지해서 확인할 수 있도록 구현해보자

### 1. 텍스트 창과 등록 버튼 만들기

: 질문 상세 페이지에서 답변을 입력하는 텍스트 창을 만들고, 답변을 등록하기 위한 [답변 등록] 버튼을 생성해 보자

1. `question_detail.html` 수정하기
    
    ```html
    <h1 th:text="${question.subject}"></h1>
    <div th:text="${question.content}"></div>
    
    <form th:action="@{|/answer/create/${question.id}|" method="post">
    	<textarea name="content" id="content" rows="15"></textarea>
    	<input type="submit" value="답변등록">
    </form>
    ```
    
2. 로컬 서버 실행 후 질문 상세 페이지 접속
    - 답변을 입력할 수 있는 텍스트창과 [답변 등록] 버튼 생성
    - 텍스트 창에 답변을 작성하고, [답변 등록] 버튼을 클릭하면 /answer/create/2 (’2’는 질문 데이터의 고유 번호)와 같은 URL이 post 방식으로 호출될 것!
3. 아직 URL을 매핑하지 않았으므로 버튼을 누르면 다음과 같은 404 페이지가 나타남
    
    > POST 방식은 주로 데이터를 저장하는 용도로 사용한다는 점을 한 번 더 상기하자
    > 
    
    → 오류 해결을 위해 답변 컨트롤러를 만들고 URL을 매핑하자
    

### 2. 답변 컨트롤러 만들기

1. `/answer/AnswerController.java` 생성 후 내용 작성
    
    ```java
    package com.mysite.sbb.answer;
    
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.PathVariable;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RequestParam;
    
    import com.mysite.sbb.question.Question;
    import com.mysite.sbb.question.QuestionService;
    
    import lombok.RequiredArgsConstructor;
    
    @RequestMapping("/answer")
    @RequiredArgsConstructor
    @Controller
    public class AnswerController {
    	
    	private final QuestionService questionService;
    	
    	public String createAnswer(Model model, @PathVariable("id") Integer id, @RequestParam(value="content") String content) {
    		Question question = this.questionService.getQuestion(id);
    		
    		// TODO: 답변을 저장한다
    		return String.format("redirect:/question/detail/%s",  id);
    	}
    }
    
    ```
    
    - `/answer/create/{id}`와 같은 URL 요청 시 `createAnswer` 메서드가 호출되도록 `@PostMapping` 매핑 (post 요청을 처리하기 때문)
    - `@RequestParam(value=”content”) String content` : 앞서 작성한 템플릿(`question_detail.html`)에서 답변으로 입력한 내용(content)을 얻기 위해 추가한 것
        - value는 템플릿 `<textarea>`의 name 속성명과 매핑
    - `/create/{id}`에서 `{id}`는 질문 엔티티의 id이므로 이 id 값으로 질문을 조회하고 값이 없을 경우는 404 오류 발생
        
        > `// TODO :`(해야할일)와 같이 주석을 작성하여 개발자들이 주로 코드 내에서 아직 해결되지 않은 문제나 추가로 작업해야 하는 부분을 표시함.
        > 

<br>

**💻 2025.05.20**
        
