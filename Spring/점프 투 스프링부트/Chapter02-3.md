# Chapter02. 스프링 부트의 기본 기능 익히기 (3)
> 📖 출처 : [점프 투 스프링부트](https://wikidocs.net/book/7601) 를 읽고 학습한 내용을 기반으로 작성한 TIL입니다.
> 

<br><br>

**💻 2025.05.21**

## [16] 웹 페이지 디자인하기

### 1. 스태틱 디렉터리와 스타일 시트 이해하기

- src/main/resources/static : CSS 파일 저장 장소
    
    > CSS : Cascading Style Sheets
    > 
- `static/style.css` 파일 생성 후 코드 작성
    
    ```css
    textarea {
    	width: 100%;
    }
    
    input[type-submit] {
    	margin-top: 10px;
    }
    ```
    
    - 답변 등록 시 사용하는 텍스트 창의 넓이를 100%로 설정
    - [답변 등록] 버튼 상단에 마진을 10픽셀로 설정

### 2. 템플릿에 스타일 적용하기

: 작성한 스타일시트 파일(style.css)을 `question_detail.html` 템플릿에 적용해보자

```css
<!-- 링크 추가!! static 파일들의 루트 디렉토리는 static 디렉토리!! -->
<link rel="stylesheet" type="text/css" th:href="@{/style.css}">
  
<h1 th:text="${question.subject}"></h1>
<div th:text="${question.content}"></div>

<h5 th:text="|${#lists.size(question.answerList)}개의 답변이 있습니다.|"></h5>
<div>
	<ul>
		<li th:each="answer : ${question.answerList}" th:text="${answer.content}"></li>
	</ul>
</div>

<form th:action="@{|/answer/create/${question.id}|}" method="post">
	<textarea name="content" id="content" rows="15"></textarea>
	<input type="submit" value="답변등록">
</form>
```

- `question_detail.html` 파일 상단에 `static/style.css`를 사용할 수 있는 링크 추가
    - static 디렉토리가 스태틱 파일들의 루트 디렉토리이므로 경로를 “`/style.css`”로 적음
- 브라우저에서 스타일이 적용된 화면 확인 가능
    
  <p align="center"><img src="https://github.com/user-attachments/assets/1a8e1fe3-a43d-45d4-a56e-0f84117b36aa" width="600"></p>

    

## [17] 부트스트랩으로 화면 꾸미기

> **❓ 부트스트랩**
> 
> - 웹 디자이너의 도움 없이도 개발자 혼자서 상당히 괜찮은 수준의 웹페이지를 만들 수 있게 도와주는 프레임워크
> - 트위터를 개발하면서 만들어졌으며 현재 지속적으로 관리되고 있는 오픈소스 프로젝트

### 1. 부트스트랩 설치하기

```
# bootstrap URL
https://getbootstrap.com/docs/5.3/getting-started/download/ 

# download 버튼 클릭하고 다음과 같은 이름의 파일을 내려받자 (버전은 최신버전)
bootstrap-5.3.6-dist.zip

# 압축 파일 안에 많은 파일들이 있는데 이 중 '/css/bootstrap.min.css' 파일을 복사하여 스태틱 디렉토리에 저장하기
(드래그 앤 드롭도 가능)
- 이 파일은 STS 에디터에서 열지 x. ('응답없음' 오류 발생 가능)

# 압축 파일 안에 자바스크립트 파일도 사용할 예정이므로 압축 파일 삭제x
```

### 2. 부트스트랩 적용하기

1. 질문 목록 템플릿(`question_list.html`)
    
    ```html
    <link rel="stylesheet" type="text/css" th:href="@{/bootstrap.min.css}"> <!-- 추가 -->
    
    <div class="container my-3"> <!-- 추가 -->
    	<table class="table"> <!-- 수정 -->
    		<thead class="table-dark"> <!-- 수정 -->
    			<tr>
    				<th>번호</th> <!-- 추가 -->
    				<th>제목</th>
    				<th>작성일시</th>
    			</tr>
    		</thead>
    		<tbody>
    			<tr th:each="question, loop : ${questionList}"> <!-- 수정 -->
    				<td th:text="${loop.count}"></td> <!-- 수정 -->
    				<td>
    					<a th:href="@{|/question/detail/${question.id}|}" th:text="${question.subject}"></a>
    				</td>
    				<td th:text="${#temporals.format(question.createDate, 'yyyy-MM-dd HH:mm')}"></td> <!-- 수정 -->
    			</tr>
    		</tbody>
    	</table>
    </div>
    ```
    
    - 가장 윗줄에 bootstrap.min.css를 사용할 수 있도록 링크를 추가 후 부트스트랩에 이미 정의되어 있는 클래스들로 간격을 조정하고 테이블에 스타일 지정
    - `loop.count`를 사용하여 테이블 항목에 번호 추가
        - `loop.count` : `questionList`의 항목을 `th:each`로 반복할 때 현재의 순서를 나타냄
    - 날짜를 보기 좋게 출력하기 위해 타임리프의 `#temporals.format(날짜 객체, 날짜 포맷)` 기능 추가
    - 결과 화면
      
      <p align="center"><img src="https://github.com/user-attachments/assets/1695f0ad-17a3-474f-9707-05add171aae6"></p>
        
2. 질문 상세 템플릿(`question_detail.html`)
    
    ```html
    <!-- 전체 수정 -->
    <link rel="stylesheet" type="text/css" th:href="@{/bootstrap.min.css}">
    
    <div class="container my-3">
    	<!-- 질문 -->
    	<h2 class="border-bottom py-2" th:text="${question.subject}"></h2>
    	<div class="card my-3">
    		<div class="card-body">
    			<div class="card-text" style="white-space: pre-line"; th:text="${question.content}"></div>
    			<div class="d-flex justify-content-end">
    				<div class="badge bg-light text-dark p-2 text-start">
    					<div th:text=${#temporals.format(question.createDate, 'yyyy-MM-dd HH:mm')}"></div>
    				</div>
    			</div>
    		</div>
    	</div>
    	<!-- 답변의 개수 표시 -->
    	<h5 class="border-bottom my-3 py-2"
    		th:text="|${#lists.size(question.answerList)}개의 답변이 있습니다.|"></h5>
    	<!-- 답변 반복 시 -->
    	<div class="card my-3" th:each="answer : ${question.answerList}">
    		<div class="card-body">
    			<div class="card-text" style="white-space: pre-line;" th:text="${answer.content}"></div>
    			<div class="d-flex justify-content-end">
    				<div class="badge bg-light text-dark p-2 text-start">
    					<div th:text="${#temporals.format(answer.createDate, 'yyyy-MM-dd HH:mm')"></div>
    				</div>
    			</div>
    		</div>
    	</div>
    	<!-- 답변 반복 끝 -->
    	<!-- 답변 작성 -->
    	<form th:action="@{|/answer/create/${question.id}|}" method="post" class="my-3">
    		<textarea name="content" id="content" rows="10" class="form-control"></textarea>
    		<input type="submit" value="답변등록" class="btn btn-primary my-2">
    	</form>
    </div>
    ```
    
    - `style=”while-space: pre-line;”` : 인라인 css 스타일 지정. 사용자가 입력한 대로 줄바꿈이 적용됨
    - ***card 컴포넌트 : 어떤 내용을 그룹화하여 보여줄 때 사용. 여기서는 질문과 답변을 각각 card로 그룹화***
    - 질문 상세 템플릿에서 사용한 부트스트랩 클래스
        
        
        | **부트스트랩 클래스** | **설명** |
        | --- | --- |
        | `card`, `card-body`, `card-text` | card 컴포넌트를 적용하는 클래스 |
        | `badge` | badge 컴포넌트를 적용하는 클래스 |
        | `form-control` | 텍스트 입력 창에 붙이면 **보기 좋고 일관된 폼 스타일을 적용**해줌 |
        | `border-bottom` | 아래 방향 테두리 선을 만드는 클래스 |
        | `my-3` | 상하 마진값으로 3을 지정하는 클래스 (m은 margin, y는 y축) |
        | `py-2` | 상하 패딩값으로 2를 지정하는 클래스 (p는 padding) |
        | `p-2` | 상하좌우 패딩값으로 2를 지정하는 클래스 |
        | `d-flex justify-content-end` | HTML 요소를 오른쪽으로 정렬하는 클래스 |
        | `bg-light` | 연회색으로 배경을 지정하는 클래스 |
        | `text-dart` | 글자색을 검은색으로 지정하는 클래스 |
        | `text-start` | 글자를 왼쪽으로 정렬하는 클래스 |
        | `btn btn-primary` | 버튼 컴포넌트를 적용하는 클래스 |
    - 결과 화면
        
      <p align="center"><img src="https://github.com/user-attachments/assets/12c3d5e3-052e-4287-b890-cdb04c4e4761"></p>
        

<br>

**💻 2025.05.22**
