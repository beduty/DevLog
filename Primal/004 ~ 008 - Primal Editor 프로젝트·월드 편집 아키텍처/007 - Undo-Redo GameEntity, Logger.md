---
notion-id: a187386d-3da2-44a6-a5df-840c0b5a4a14
---


# 핵심 사항

- Undo/Redo를 값 기반으로 설정할 수 있는 기능 추가 
- Resource Dictionary구현
- List Selection Undo/Redo 구현
- Filtered Logger 구현, Thread safe (UI(Main)/Other)

# 화면 스크린샷

![[Untitled 23.png]]

![[Untitled 24.png]]

![[Untitled 25.png]]

# 추가된 내용

1. Undo/Redo를 값 기반으로 설정할 수 있는 기능 추가 
![[Untitled 26.png]]
![[Untitled 27.png]]
2. GameEntity의 Property에 대해 Undo/Redo 추가 (old-value / new-value)
	1. IsEnabled: check box 변경에 따른 이벤트 받아다가 boolean 변경 undo-redo
	2. Rename: selected된 GameEntity의 Name변경이벤트에 따른 undo-redo
3. ControlTemplate
	3. App내부에 공통으로 적용되는 Style등을 Globally하게 적용할 수 있다. 
		- Resource Dictionary xaml을 만들고 app.xaml에 등록해야한다. 
		- 자체적으로 등록된 ResourceDictionary가 있으므로 대체하는게 아닌 이상 `MergedDictionaries`를 사용해야한다.
![[Untitled 28.png]]
	4. 여기서는 TextBox의 `Enter`와 `Esc` 처리를 공통으로 하기 위한 작업을 진행한다. 
![[Untitled 29.png]]
![[Untitled 30.png]]
![[Untitled 31.png]]
4. GameEntity List의 Selection Changed undo/redo 기능 
	5. Selection Changed된 Item들을 Undo/Redo하기 위해서 변경된 목록을 caching해야함
![[Untitled 32.png]]
	6. Selection Changed이벤트에 따라 Undo/Redo 구현 
![[Untitled 33.png]]
		- Undo/Redo에 의해서 Selection Changed가 한번씩 더 일어나는 것을 주의한다.
		- undo/redo callback에 의한 `Project.UndoRedo.Add` 는 일어나면 안되므로 특별 처리를 해준다.
![[Untitled 34.png]]
5. Tab컨트롤로 History(Undo/Redo)와 Log 화면 전환
6. OpenProject할 게 없으면 CreateProject가 기본으로 보이도록 하기
7. addComponent할 수 있는 버튼 추가
8. Logger는 D-log참고
	- [[NOTION_PAGE:3ffe2119-20dc-42b2-8829-6114a56cfe9e]] 
