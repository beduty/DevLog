---
notion-id: 7078425d-fdde-4f4c-aed8-434dbfc53afd
---


# 핵심 사항

# 화면 스크린샷

![[Untitled 87.png]]

# 추가된 내용

1. Number Box
![[Untitled 88.png]]
	- TextBlock을 기준으로 MouseMove Horizontal 이벤트로 Value를 업데이트
	- Mouse Button Up일때 변경된 값이 없으면 TextBox를 띄운다. 
	- textBox가 Visible이면 textBlock은 collapsed되도록 함.
2. Scaler Box
![[Untitled 89.png]]
	- number box를 상속받아 구현
	- number box에 Dark Theme Style을 매김
3. Vector Box
![[Untitled 90.png]]
	- NumberBox 4개를 배치한다. (W는 Visible collapsed)
	- Data Trigger로 Orientation에 따라서 위치시킬 Grid를 결정한다. 
	- Data Trigger로 VectorType에 따라서 Show/Hide할 NumberBox를 결정한다.
	- 
