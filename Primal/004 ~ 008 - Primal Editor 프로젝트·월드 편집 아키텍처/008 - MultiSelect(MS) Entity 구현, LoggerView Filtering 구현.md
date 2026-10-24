---
notion-id: 0488f8e5-6887-450c-8ecd-e1b6e9c6c06e
---


# 핵심 사항

- 필터에 따른 로그 구현
- MSEntity구현( 값 변경, Undo/Redo)
![[Untitled 35.png]]

# 화면 스크린샷

![[Untitled 36.png]]

# 추가된 내용

1. 필터에 따른 로그 구현
	1. 버튼으로 Log Message Filter 세팅
2. MSEntity구현
	2. 선택된 GameEntity들의 기본속성(Name, Enabled)과 Component를 동시에 변경시키고 싶을 수 있다.(+ 변경된 내용이 Undo/Redo되어야 함)
![[Untitled 37.png]]
	3. GameEntity Array를 멤버로 하는 MSEntity클래스를 정의
![[Untitled 38.png]]
	4. GameEntity List의 Selection Changed될 때마다 MSEntity를 생성한다.
![[Untitled 39.png]]
![[Untitled 40.png]]
3. MSEntity의 값 변경
	5. List Selection되는 곳에서 MSEntity를 생성하고 GameEntityView의 DataContext로 꽂아줌.
![[Untitled 41.png]]
	6. Name과 IsEnabled 변경하는 Control에서 MSEntity의 Property를 변경
		- IsEnabled변경
![[Untitled 42.png]]
		- Name변경
![[Untitled 43.png]]
	7. MSEntity생성하면서 등록한 PropertyChanged가 호출되면서 Selected Entities의 Property를 모두 동일하게 setting
![[Untitled 44.png]]
![[Untitled 45.png]]
4. MSEntity의 Undo/Redo 구현
	8. MSEntity Undo/Redo 특징
		- 하나하나로 Undo/Redo구현시 Multi Entity가 변경되는 상황의 undo/redo구현이 어려움.
		- EntityList가 GUI의 TextBox와 CheckBox에서 변경되므로 해당 부분의 이벤트에서 undo./redo를 구현하도록 한다. → 각 Entity레벨의 Command는 제거.
![[Untitled 46.png]]
	9. 변경 전 Entity들의 Property를 Caching
![[Untitled 47.png]]
![[Untitled 48.png]]
	10. Property변경하고 Redo만들어서 Undo/Redo 등록
![[Untitled 49.png]]
![[Untitled 50.png]]
![[Untitled 51.png]]
	11. Undo/Redo로 PropertyChanged무한 호출 문제 
		- Undo/Redo로 인해 MSEntity의 Property가 변경됨에 따라서 다음과 같은 문제가 발생한다. → _enableUpdates가 제대로 설정되지 않으면 UpdateGameEntities가 한번 더 호출되면서 제대로 Undo/Redo가 안됨.
![[Untitled 52.png]]
		- Undo/Redo에서는 Refresh를 호출하게끔하고 Refresh될 때는 PropertyChanged가 되지 않도록 조치가 되어있다.
![[Untitled 53.png]]
![[Untitled 54.png]]