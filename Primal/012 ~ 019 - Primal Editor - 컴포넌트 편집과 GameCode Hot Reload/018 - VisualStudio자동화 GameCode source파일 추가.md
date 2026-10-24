---
notion-id: a8f5b782-84f2-4e7c-b406-69b342fbf412
---

[**Adding source files to the game solution using Visual Studio automation.**](https://github.com/TheGameEngineers/Primal/commit/c21f6b16c7341246e896ffde6a310bdee6fb0046)

# 핵심 사항

1. VisualStudio Control하는 Class 추가
2. New Script의 이름을 입력받는 Dialog 추가

# 화면 스크린샷

![[Untitled 106.png]]

![[Untitled 107.png]]

![[Untitled 108.png]]

![[Untitled 109.png]]

# 추가된 내용

3. VisualStudio Control Class
	1. New Project 하면서 GameCode작업을 위한 VisualStudio sln과 project가 생성된 상태다. Entity Script를 추가하고, Script을 작업하기 위해서 VisualStudio를 Control 할 수 있는 기능을 추가한다.
![[Untitled 110.png]]
	2. VisualStudio를 Control할 수 있는 COM객체를 추가하고, Open/Close/Save할 수 있는 기능을 작업한다
![[Untitled 111.png]]
4. New Script의 이름을 입력받는 Dialog 추가
	3. 입력받은 이름으로 entity_script를 상속받은 클래스파일(.h, .cpp)를 템플릿으로 생성
![[Untitled 112.png]]
	4. GameCode에 생성한 파일을 project에 추가
![[Untitled 113.png]]
![[Untitled 114.png]]