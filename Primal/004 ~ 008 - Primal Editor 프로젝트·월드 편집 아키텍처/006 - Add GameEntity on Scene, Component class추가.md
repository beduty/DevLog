---
notion-id: 25574fe6-39d5-475a-9923-dbbf95c170a2
---


# 핵심 사항

- GameEntity와 Component 클래스 추가
- 선택된 GameEntity의 멤버를 볼 수 있는 View 추가

# 화면 스크린샷

![[Untitled 17.png]]

# 추가된 내용

1. GameEntity와 Component 클래스 추가
	1. GameEntity는 고유 이름과 함께 엮여 있는 Component들을 관리한다.
![[Untitled 18.png]]
	2. Component는 묶여있는 부모 GameEntity를 pointer로 갖는다.
![[Untitled 19.png]]
	3. Component를 상속받아 Transform, Camera, Light등이 구현될 예정
![[Untitled 20.png]]
	4. Project파일을 읽어올 때 Scene의 GameEntity도 함께 Deserialize한다. Component는 Base클래스이므로 Deserialize할 크기를 모른다. 그렇기에 GameEntity의 DataContract에 Component가 의미할 수 있는 클래스를 명시해놓는다.
		- `KnownType(typeof(Transform))` 
2. 선택된 GameEntity의 멤버를 볼 수 있는 View 추가
	5. ProjectLayoutView에서 선택된 GameEntity를 GameEntityView의 DataContext로 꽂아준다.
![[Untitled 21.png]]
	6. GameEntityView에서는 받아온 dataContext로 멤버 정보를 출력 및 수정하는데에 사용할 수 있다.
![[Untitled 22.png]]
