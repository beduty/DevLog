---
notion-id: 00c093e5-58c8-416f-aa42-bd34194e96e0
---


# 핵심 사항

- Open된 Project의 Scene을 관리하는 WorldEditView추가
![[Untitled 6.png]]
- Undo Redo System 추가

# 화면 스크린샷

![[Untitled 7.png]]

# 추가된 내용

1. MainWindow에 WorldEditorView 추가
![[Untitled 8.png]]
	- Open된 Project의 Scene을 관리(Add, Remove)
2. UndoRedo System구현
	1. UndoRedoAction 구현 (action 2개 받음)
![[Untitled 9.png]]
	2. Undo List와 Redo List 구현
![[Untitled 10.png]]
	3. UI 이벤트에 Command 바인딩하고, call-back에서 UndoRedo List에 Action추가
![[Untitled 11.png]]
![[Untitled 12.png]]
	4. Undo/Redo 버튼클릭하면 Undo/Redo List에서 Action꺼내와서 처리
![[Untitled 13.png]]
![[Untitled 14.png]]
![[Untitled 15.png]]
3. UndoRedo Command구현 
![[Untitled 16.png]]
![[Untitled 17.png]]
