# Chapter01. 스프링부트 개발 준비하기

> 📖 출처 : [점프 투 스프링부트](https://wikidocs.net/book/7601) 를 읽고 학습한 내용을 기반으로 작성한 TIL입니다.
> 

<br><br>

`💻 2025.04.23`

## [1] 스프링 부트란?

웹 어플리케이션을 쉽고 빠르게 만들 수 있도록 도와주는 자바의 웹 프레임워크.

스프링 프레임워크에 톰캣(Tomcat)이라는 서버를 내장하고 여러 편의 기능들을 추가하여 제공한다.

<br> 

## [2] 스프링 부트를 배워야 하는 이유

### 1. 보안 공격 방어

- 스프링 부트는 SQL 인젝션, XSS(Cross-Site Scripting), CSRF(Cross-Site Request Forgery), 클릭재킹(clickjacking)과 같은 보안 공격을 기본으로 잘 막아주어서 우리가 코드를 짤 필요가 없다.
    
    > ❓ 사이버 공격 유형 (극히 일부임)
    > 
    > - SQL 인젝션 : 악의적인 SQL을 주입하여 공격하는 방법
    > - XSS(Cross-Site Scripting) : 자바스크립트를 삽입해 공격하는 방법
    > - CSRF(Cross-Site Request Forgery) : 위조된 요청을 보내는 공격 방법
    > - 클릭재킹(clickjacking) : 사용자가 의도하지 않은 클릭은 유도하는 공격 방법
    

### 2. 이미 준비되어 있는 기능

- 스프링 부트는 로그인, 페이징 기능 등 웹 프로그램을 개발하는 데 필요한 도구와 기능이 대부분 준비되어 있다.
    
    > ❓ 로그인 기능은 네이버 로그인처럼 사용자를 인증하는 기능이고, 페이징 기능은 한 페이지에 데이터가 많으면 페이지별로 나누어 조회할 수 있게 하는 기능이다.
    > 
- 이미 만들어져 있는 기능을 새로 만드느라 애써 고생할 필요가 없다!

### 3. WAS가 필요없다.

- 스프링 부트 대신 스프링만 사용하여 웹 애플리케이션을 개발한다면 실행을 위한 WAS가 필요하다.
- WAS의 종류는 매우 다양하며 설정 방식도 제각각이라 WAS만으로도 공부해야 할 내용이 상당하다.
- 하지만 스프링 부트에는 톰캣 서버가 내장되어 있고 설정이 자동 적용되며, 배포되는 jar 파일에도 톰캣 서버가 내장되어 실행된다.

  > ❓ WAS란?
  > 
  > : 웹 애플리케이션과 서버 환경을 연결하는 중간 역할을 하는 소프트웨어 플랫폼
  > 
  > - WAS의 종류로는 아파치 톰캣, 오라클 웹 로직, IBM 웹스피어, 와일드플라이, 제우스 등이 있다.

### 4. 스프링 부트는 설정이 쉽다.

- 스프링은 설정 지옥☠️. 심지어 한번 설정한 기능들이 스프링의 버전업으로 변경되거나 없어지기도 했다.
- 스프링 부트는 스프링의 복잡한 설정을 자동화∙단순화하였다.

### 5. 재미있다.

- 과연…?

<br> 

`💻 2025.04.24`

## [3] 스프링 부트 개발 환경 준비
1) 운영체제에 맞는 JDK 설치하기
2) STS 설치 및 실행
3) 스프링 부트 프로젝트 생성

   <p align="center"><img src="https://github.com/user-attachments/assets/5bb657ff-4446-4df7-aee6-ed50f257189b" alt="스프링 부트 프로젝트 생성" width=500/></p>
   
    - name : 프로젝트 이름 입력. sbb(Spring Boot Board) 입력
    - Type : 프로젝트 관리 도구 선택. 기본값은 'Gradle - Groovy'. (싸피에서는 Maven으로 배웠는데, 이 기회에 Gradle도 써볼 예정). 더 자세한 내용은 2-01절에서 다룸.
    - Java Version : 자바 버전 선택. 책에서는 20 선택했지만 나는 20이 안보여서 21 선택. (이후 버전 차이로 인한 에러는 하나씩 해결해보는걸로...)

4) 빌드 실행 중 예외 해결 (GPT와 함께...) & 인코딩 방식 경고 해결 (UTF-8로 설정)
   - Gradle 버전에 맞는 JDK 호환 버전 사용해야 함

<br>
  
## [4] 스프링부트 맛보기

### 1. 들어가기 전) 웹 서비스는 어떻게 동작할까?

1. 클라이언트와 서버 구조
    - 클라이언트 : 크롬, 사파리와 같은 웹 브라우저
    - 서버 : 브라우저로 접속 가능한 원격 컴퓨터
    - 웹 서버는 요청에 대한 응답으로 HTML 문서나 다른 리소스들을 브라우저에 표시한다.

    <p align="center"><img src="https://github.com/user-attachments/assets/60b5dc4f-136a-47aa-84f4-46a0da5cb375" alt="클라이언트와 서버 구조" width=500/></p>

        
1. IP 주소와 포트 
    - 서버는 웹 서비스뿐만 아니라 FTP, 이메일 서비스 등도 운용할 수 있다. 이때 하나의 서버 주소(IP주소)에서 여러 포트를 사용하여 매우 많은 서비스를 운용할 수 있는데, 대표적인 서비스의 종류는 아래 표와 같다. 표에 적혀 있는 포트 번호는 기본 포트 번호로, 클라이언트나 서버 등에서는 대부분 이 번호를 기본값으로 설정하지만 필요에 따라 포트 번호를 별도로 정의하고 변경할 수 있다.
        
        
        | **프로토콜** | **서비스 내용** | **포트** |
        | --- | --- | --- |
        | **HTTP** | 웹 서비스 | 80 |
        | **HTTPS** | SSL을 적용한 웹 서비스 | 443 |
        | **FTP** | 파일 전송 서비스 | 21 |
        | **SSH, SFTP** | 보안이 강화된 TELNET(텔넷), FTP 서비스 | 22 |
        | **TELNET** | 원격 서버 접속 서비스 | 23 |
        | **SMTP** | 메일 전송 서비스 | 25 |

2. localhost:8080 이해하기
    - localhost라는 도메인명은 127.0.0.1이라는 IP 주소를 의미한다. 이 주소는 내 컴퓨터를 의미한다.
    - 8080은 8080번 포트로 서비스를 운용한다는 의미이다.
    - 즉 localhost:8080은 내 컴퓨터에 8080번 포트로 실행된 서비스를 의미하는 것이다.
    - 내 컴퓨터에 실행된 서버는 로컬 서버이므로 외부에서는 접속할 수 없다.


### 2. 컨트롤러 만들기 (**HelloController.java)**

```java
package com.mysite.sbb;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class HelloController {
	@GetMapping("/hello")
	@ResponseBody
	public String hello() {
		return "Hello World";
	}
}
```

- @Controller : 스프링 부트가 HelloController 클래스를 컨트롤러로 인식하기 위한 어노테이션
    
    > **❓ Annotaton**
    > 
    > 
    > : 자바의 클래스, 메서드, 변수 등에 정보를 부여하여 부가 동작을 할 수 있게 하는 목적으로 사용
    > 
- @GetMapping(”/hello”) : http://localhost:8080/hello URL 요청이 발생하면 해당 메서드가 실행됨을 의미. 즉 /hello URL과 메서드를 매핑(mapping)하는 역할
    - Post 방식의 URL 요청을 위해서는 @PostMapping 사용
- @ResponseBody : 메서드의 출력 결과가 문자열 그 자체임을 나타냄 (경로가 아니라!)
    
    > **❓ GET 방식과 POST 방식**
    > 
    > - GET : 데이터를 URL에 노출시켜 요청. 주로 데이터 조회에 사용
    > - POST : 데이터를 숨겨서 요청. 로그인 정보와 같은 민감데이터를 서버에 제출하거나 저장할 때 사용

### 3. 로컬 서버 실행 후 브라우저로 확인

- 브라우저 주소 창에 http://localhost:8080/hello 입력 시 ‘Hello World’ 문자열이 잘 출력되는 것 확인.

<br>

`💻 2025.04.25`

## [5] 스프링부트 도구 설치하기
웹 프로그램 개발을 도와주는 스프링 부트의 도구(라이브러리)에 대해 알아보자.

> ❓ **라이브러리**
> 
> 
> : 개발 시 자주 사용하는 코드를 모아둔 것
> 

### 1. Spring Boot Devtools

| Devtools 추가 전 | 프로젝트 내부 파일이 변경되더라도 로컬 서버가 변경된 클래스를 즉시 반영하지 못하여, 매번 서버를 재시작해야 함. |
| --- | --- |
| Devtools 추가 후 | 서버를 재시작하지 않아도 클래스를 변경할 때 서버가 자동으로 재가동. |

- Spring Boot Devtools를 사용하려면 Spring Boot Devtools를 그레이들(Gradle)로 설치해야 함.
    - build.gradle 파일 수정하기 - “의존성(dependencies)” 부분에 아래 코드 추가
        
        : `developmentOnly 'org.springframework.boot:spring-boot-devtools'`
        
        > ❓ **developmentOnly**
        > 
        > - 해당 라이브러리는 개발 환경에만 적용된다는 의미.
        > - 운영 환경에 배포되는 jar, war 파일에는 이 라이브러리가 포함되지 않음.
  
    - build.gradle 파일 수정 후에는 반드시 해당 파일을 선택 → 마우스 오른쪽 버튼 클릭 후 [Gradle → Refresh Gredle Project] 클릭해 라이브러리를 설치해야 함!
 
      	<p align="center"><img src="https://github.com/user-attachments/assets/b45531bd-bd85-4fd0-add7-4b1b594b104b" alt="gradle에 devtools 설치" width=500/></p>
        
- 설치가 완료되면 Boot Dashboard의 서버명이 sbb에서 sbb [devtools]로 바뀜 → 서버 재시작하기
    
    <p align="center"><img src="https://github.com/user-attachments/assets/80e1711c-a55e-416a-a308-23502c6bc70c" alt="gradle에 devtools 설치" width=300/></p>

    
- 이제 서버를 재시작하지 않아도 프로그램 변경 사항이 잘 반영될 것. 만약 반영되지 않는 것 같다면 브라우저를 새로고침해보기.

<br>

`💻 2025.04.28`

### 2. 롬복 설치하기

- 롬복(Lombok) 라이브러리:  소스 코드를 작성할 때 자바 클래스에 Annotation을 작성하여 자주 쓰는 **Getter 메서드, Setter 메서드, 생성자 등을 자동으로 만들어주는** 도구. 롬복을 사용하면 좀 더 짧고 깔끔한 소스 코드를 만들 수 있음.


1. 아래 URL에서 롬복 플러그인 내려받기
    
    [`https://projectlombok.org/download`](https://projectlombok.org/download)
    
    > ❓ 플러그인 (plug-in)
    > 
    > - 응용 프로그램에 추가 기능을 연결하고 확장하는 데 사용하는 소프트웨어 모듈

2. 내려받은 lombok.jar 파일 설치하기. 
    - 방1) 명령 프롬프트 창 이용: lombok.jar 파일이 있는 위치로 이동 후 다음 명령을 실행해야 함.
        
        `java -jar lombok.jar`
        
    - 방2) 내려받은 lombok.jar 파일 더블클릭하여 설치.

3. 설치 창에서 STS가 설치된 경로 선택 후 롬복 설치
    
    **Mac 경로 : application > SpringToolSuite4.app > Contents > SpringToolSuite4.ini 선택 후 우측 하단 `open` 클릭*

    <p align="center"><img src="https://github.com/user-attachments/assets/231dfb01-4fbd-494c-a8f7-1b18fbf67074" alt="롬북 설치" width=500/></p>

    
5. STS 다시 시작 → build.gradle 파일 다음과 같이 수정하기
    
    ```xml
    (... 생략 ...) 
    
    dependencies { 
        implementation 'org.springframework.boot:spring-boot-starter-web' 
        testImplementation 'org.springframework.boot:spring-boot-starter-test' 
        developmentOnly 'org.springframework.boot:spring-boot-devtools' 
        compileOnly 'org.projectlombok:lombok' 
        annotationProcessor 'org.projectlombok:lombok'
    } 
    
    (... 생략 ...)
    
    ```

    - dependencies 내에 compileOnly, annotationProcessor 추가
    - 컴파일할 때 롬복 라이브러리가 적용되도록 코드 추가

### 3. 롬복(Lombok)으로 Getter, Setter 메서드 만들기

- HelloLombok 클래스 생성 후 다음과 같이 소스 코드 작성하여 롬복이 정상적으로 동작하는지 확인
    
    ```java
    package com.mysite.sbb;
    
    import lombok.Getter;
    import lombok.Setter;
    
    @Getter
    @Setter
    public class HelloLombok {
    	private String hello;
    	private int lombok;
    	
    	public static void main(String[] args) {
    		HelloLombok helloLombok = new HelloLombok();
    		helloLombok.setHello("헬로");
    		helloLombok.setLombok(5);
    		
    		System.out.println(helloLombok.getHello());
    		System.out.println(helloLombok.getLombok());
    	}
    
    }
    
    ```
    
    - 롬복 활용 시 속성에 대한 Getter, Setter 메서드를 별도 작성하지 않고 어노테이션으로 메서드 생성
        
        > 소스 코드에는 Getter, Setter 메서드가 눈으로 보이지 않지만, **컴파일 단계에서 롬복이 대신 생성해주므로 컴파일된 클래스(.class)에는 Getter와 Setter 메서드가 실제로 포함**됨.

### 4. 롬복으로 생성자 만들기

- HelloLombok 클래스를 다음과 같이 수정하기
    
    ```java
    package com.mysite.sbb;
    
    import lombok.Getter;
    import lombok.RequiredArgsConstructor;
    import lombok.Setter;
    
    @RequiredArgsConstructor //생성자를 위한 어노테이션 추가 (이 경우 setter 필요없으므로 삭제)
    @Getter
    public class HelloLombok {
    	private final String hello;  //final로 수정
    	private final int lombok;    //final로 수정
    	
    	public static void main(String[] args) {
    		HelloLombok helloLombok = new HelloLombok("헬로", 5);
    		System.out.println(helloLombok.getHello());
    		System.out.println(helloLombok.getLombok());
    	}
    
    }
    ```
    
    - hello, lombok 속성에 final 추가 후 `@RequiredArgsConstructor` 어노테이션 적용
        
        → 해당 속성을 필요로 하는 생성자가 롬복에 의해 자동으로 생성됨!!
        
        > ❗️ **주의**
        > 
        > - 만약 클래스 속성을 정의한 코드에 final이 없다면 생성자에 자동으로 포함되지 않음.
        > - final 적용 시 속성값을 변경할 수 없기 때문에 @Setter는 의미가 없으며, Setter 메서드 또한 사용불가.
    - `@RequiredArgsConstructor` 어노테이션은 2장부터 자주 사용하니 꼭 기억하기!






<br><br> 

---
#springboot #was #java
