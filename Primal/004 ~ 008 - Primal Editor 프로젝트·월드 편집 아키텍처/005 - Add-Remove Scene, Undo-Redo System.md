---
notion-id: 00c093e5-58c8-416f-aa42-bd34194e96e0
---


# 핵심 사항

- Open된 Project의 Scene을 관리하는 WorldEditView추가
![[Untitled 5.png]]
- Undo Redo System 추가

# 화면 스크린샷

![[Untitled 6.png]]

# 추가된 내용

1. MainWindow에 WorldEditorView 추가
![[Untitled 7.png]]
	- Open된 Project의 Scene을 관리(Add, Remove)
2. UndoRedo System구현
	1. UndoRedoAction 구현 (action 2개 받음)
![[Untitled 8.png]]
	2. Undo List와 Redo List 구현
![[Untitled 9.png]]
	3. UI 이벤트에 Command 바인딩하고, call-back에서 UndoRedo List에 Action추가
![[Untitled 10.png]]
![[Untitled 11.png]]
	4. Undo/Redo 버튼클릭하면 Undo/Redo List에서 Action꺼내와서 처리
![[Untitled 12.png]]
![[Untitled 13.png]]
![[Untitled 14.png]]
3. UndoRedo Command구현 
![[Untitled 15.png]]
![[Untitled 16.png]]
