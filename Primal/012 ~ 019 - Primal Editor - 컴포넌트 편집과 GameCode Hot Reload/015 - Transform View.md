---
notion-id: af67973d-14af-438c-b7b4-3ba3c84bb3f8
---


# 핵심 사항

1. [[NOTION_PAGE:7078425d-fdde-4f4c-aed8-434dbfc53afd]] 에서 추가된 VectorBox로 TransformView를 구현
2. MS Component 업데이트 파이프라인 구현

# 화면 스크린샷

![[Untitled 91.png]]

# 추가된 내용

3. [[NOTION_PAGE:7078425d-fdde-4f4c-aed8-434dbfc53afd]] 에서 추가된 VectorBox로 TransformView를 구현
	1. D-log [[NOTION_PAGE:745a0190-18f7-4b03-bcad-685b5fadeda8]] 참조 
	2. Transform등을 표시할 수 있는 `ComponentView` 를 구현
		- Header만 공통으로 쓰이고, 나머지는 상속받아 Custom하게 구현하도록 `ContentPresenter` 로 간단하게만 구현 
![[Untitled 92.png]]
![[Untitled 93.png]]
	3. Transform을 표시하기 위한 TransformView를 만들고, ComponentView를 사용하여 내용을 표시한다. TransformView에 적합한 ComponentContent를 각자 구현하여 표시.
![[Untitled 94.png]]
4. MS Component 업데이트 파이프라인 구현
	4. List Selection Changed가 될 때 MSEntity가 만들어지고, 연달아서 MSComponents도 만들어진다.
![[Untitled 95.png]]
![[Untitled 96.png]]
	5. DataType에 따라서 `MSTransform`이면 TransformView가 화면에 표시되도록 함.
![[Untitled 97.png]]
![[Untitled 98.png]]
		- MSTransform의 PosX,PosY,.. 가 Binding되어 Control변경되면서 해당 부분이 변경됨. 해당 부분을 Undo Redo로 구현하기 위해서 Mouse Event받는 곳에서 Undo/Redo를 구현함.
![[Untitled 99.png]]
![[Untitled 100.png]]
![[Untitled 101.png]]
		- TextBox의 KeyDown에서 `e.Handled = true` 이 처리되므로 이보다 우선되는 `PreviewMouse…` 에다 구현했다.
			(참고: [https://m.blog.naver.com/alsduddl525/140142921151](https://m.blog.naver.com/alsduddl525/140142921151))
