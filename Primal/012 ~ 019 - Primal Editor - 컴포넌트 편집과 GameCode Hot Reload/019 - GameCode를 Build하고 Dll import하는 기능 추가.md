---
notion-id: 2913698d-022b-44de-9b60-69970dad2d40
---


# 핵심 사항

1. GameCode를 Build하고 생성된 dll을 import하는 기능 추가
2. Editor에서 Build를 수행할 수 있는 기능을 추가

# 화면 스크린샷

![[Untitled 115.png]]

# 추가된 내용

3. GameCode를 Build하고 생성된 dll을 import하는 기능 추가
	1. 기존 load된 dll을 해제하고, solution build를 수행한다. 
	2. build가 성공하면 dll을 load한다.
![[Untitled 116.png]]
	3. Project가 OnDeserialized될 때 GameCode를 빌드하는데, UI Thread에서 VisualStudio Build가 수행되면 UI가 Block되므로 Async로 수행되도록 한다.
![[Untitled 117.png]]
4. Editor에서 Build를 수행할 수 있는 기능을 추가
	4. Build Config를 선택할 수 있는 콤보박스 추가 
![[Untitled 118.png]]




> [!note]+ 잠금
> 5. GameCode를 Build하는 버튼 추가
>     1. 4가지 Type의 Build 타입이 있으므로 Enum화 
>     2. 
> 6. Game Code를 Build하는 기능 추가
> ![[Untitled 119.png]]
>     - 기존 dll unload하고 build
>     - build가 성공했다면 build한 결과물 새로 load
> ![[Untitled 120.png]]
>     - Build 시작했을때 와 완료되었을때 이벤트를 받을 수 있다.
> ![[Untitled 121.png]]
> ![[Untitled 122.png]]
>     - Primal Editor에 Build Platform을 x64로 고정
> ![[Untitled 123.png]]
>     - F7 로 build할 때 Batch Build를 하여 모든 설정에 다 build 할수 있도록 한다. 
> ![[Untitled 124.png]]
>     - Engine DLL은 c#에서 사용하기 위한 Wrapper이므로 Editor Build만 활성화 한다. 
> ![[Untitled 125.png]]
>     - Engine Test는 dll사용 안하므로 exe build만 활성화
> ![[Untitled 126.png]]
>     - Primal Editor
> ![[Untitled 127.png]]
>     - Batch build 핫키 설정
> ![[Untitled 128.png]]
>     - ㄴㅇ
> ![[Untitled 129.png]]
> ![[Untitled 130.png]]
>     - 
> 7. dll 로딩
>     - dll 열려있으면 unload하고 다시 loading
>     - static하게 .cpp에서만 HMODULE 가질 수 있도록 익명 namespace에 정의했다.
> ![[Untitled 131.png]]
> 8. pdb파일이 동일하면 안된다. 
> ![[Untitled 132.png]]
>     - 빌드할 떄마다 새로운 이름의 pdb파일이 생성되므로 visual studio 빌드에 성공한다.
> ![[Untitled 133.png]]
>     - MSVC는 새로 생성된 pdb를 refer한다. 잦은 build해도 문제 없음
>     - 다만 지저분하게 생성되는 pdb파일을 제거하고자 build 전에 기존 pdb파일을 지운다.
> ![[Untitled 134.png]]
> 9. Editor 실행할 때 Load DLL을 하는데, VisualStudio가 실행되는 시간때문에 로딩 시간이 오래걸린다. → GameCode Dll을 여는 과정을 Async로 처리한다. 
>     3. UI스레드에서 기다릴 필요가 없게 된다. 별도의 스레드에서 수행되므로 UI스레드에서눈 기다릴 필요가 없게된다.
> ![[Untitled 135.png]]
>     4. 호출하는 부분도 async로 설정해줘야 한다. 
> ![[Untitled 136.png]]
> ![[Untitled 137.png]]
>     5. New Script만 되고 나머지 command가 동작안하는 문제가 발생한다. 
>         - New SCript는 code behind의 함수를 바로 호출하는 반면
>         - Undo,, REdo, Build 버튼은 Command에 묶여 있다. 
>         - Build await되는 동안 UI가 모두 initialize되었고, 제대로된 Binding이 안되면서 발생하는 문제다. UnInitilzed된 Command가 Binding되면서 발생하는 문제다. 
> ![[Untitled 138.png]]
>             - 이후에 Set되더라고 OnPropertyChanged가 호출되는 구조가 아니므로 ui에서 새로 바인딩 하지 않는다.
>             - ProjectBrowserDialog에서 먼저 Projected Loading이 될 때 Command가 생성되고 이후 world editor view가 생성될 때 Binding되는 구조이므로 async되어 이후에 Command가 생성되는 경우의 대처가 필요한다.
>         - Property changed를 호출해줘야 한다.
> ![[Untitled 139.png]]
>             - setter를 바꾸는 방법도 있지만 그렇게 하지 않고 1회만 호출하면 되므로 위와 같이 변경 
> 
















