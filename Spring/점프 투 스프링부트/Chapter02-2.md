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
