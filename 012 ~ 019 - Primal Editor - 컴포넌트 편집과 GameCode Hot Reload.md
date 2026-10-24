---
title: "012 ~ 019 - Primal Editor - 컴포넌트 편집과 GameCode Hot Reload"
source: Notion
tags: [primal, devlog]
---

# Primal Editor의 통합 개발 환경

Primal Editor는 게임 오브젝트의 컴포넌트를 시각적으로 편집하고, C++ Script를 생성·빌드한 뒤 결과 DLL을 다시 로드하는 하나의 통합 개발 환경이다.

이 문서는 개별 기능의 개발 순서를 나열하는 대신, **사용자가 Entity를 편집하고 GameCode를 실행 가능한 상태로 반영하는 전체 과정**을 하나의 주제로 설명한다.


> 핵심 목표
>
> Editor를 종료하지 않고 **컴포넌트 편집 → Script 생성 → 빌드 → DLL 재로딩**까지 수행할 수 있는 빠른 반복 개발 환경을 구축한다.

# 전체 워크플로우

```text
프로젝트 열기
    ↓
Entity 선택
    ↓
Component Inspector에서 Transform 편집
    ↓
New Script로 C++ 클래스 생성
    ↓
Visual Studio Project에 소스 자동 등록
    ↓
Editor에서 Build Config 선택 및 빌드
    ↓
기존 GameCode DLL 해제
    ↓
빌드 성공 시 새 DLL 로드
    ↓
변경된 GameCode를 Editor에서 확인
```

이 흐름은 크게 세 영역으로 구성된다.

1. **Editor UI:** 프로젝트와 컴포넌트를 조작하는 WPF 인터페이스
1. **Authoring:** Entity Script 생성과 Visual Studio 프로젝트 관리
1. **Runtime Integration:** GameCode 빌드와 DLL Hot Reload

# 1. Editor UI와 컴포넌트 편집

## Editor Shell

기본 Window Chrome을 제거하고 타이틀과 시스템 버튼을 포함한 상단 영역을 직접 구성했다. Editor 전체에는 Dark Theme을 적용하고, 프로젝트 열기와 생성 화면 사이에는 애니메이션 전환을 추가했다.

이 UI는 단순한 스타일 변경이 아니라 이후의 Inspector, Build Config, New Script 같은 도구를 일관된 환경 안에 배치하기 위한 기반이다.

## 수치 편집 컨트롤

Transform처럼 여러 실수 값을 자주 조작해야 하는 Editor에서는 일반 TextBox만으로 빠른 편집 경험을 제공하기 어렵다. 이를 해결하기 위해 NumberBox를 중심으로 입력 컨트롤을 구성했다.

### NumberBox

NumberBox는 두 가지 입력 방식을 지원한다.

- TextBlock 위에서 마우스를 수평으로 드래그해 값을 연속 변경
- 클릭 후 값이 변하지 않았다면 TextBox를 열어 정확한 값 입력

TextBox가 활성화되면 기존 TextBlock은 숨겨지며, 편집이 끝나면 다시 표시된다.

### ScalarBox와 VectorBox

ScalarBox는 NumberBox를 상속하고 Dark Theme 스타일을 적용한 단일 값 편집 컨트롤이다.

VectorBox는 최대 4개의 NumberBox를 조합해 Vector 값을 편집한다.

- Orientation에 따라 입력 칸 배치 변경
- VectorType에 따라 X·Y·Z·W 중 필요한 축만 표시
- 일반적인 3차원 Transform에서는 W 입력을 숨김

## ComponentView 구조

컴포넌트 UI마다 공통 Header와 서로 다른 편집 내용을 함께 제공하기 위해 `ComponentView`를 만들었다.

- 공통 영역: 컴포넌트 이름과 Header
- 가변 영역: `ContentPresenter`에 컴포넌트별 UI 배치

Transform 컴포넌트는 이 구조를 상속한 `TransformView`에서 표시한다. Position, Rotation, Scale은 VectorBox와 연결되어 사용자가 값을 직접 조작할 수 있다.

## Entity 선택과 화면 갱신

Entity를 선택하면 Inspector는 다음 순서로 갱신된다.

1. 목록에서 Selection Changed 이벤트 발생
1. 선택된 Entity에 대응하는 `MSEntity` 생성
1. `MSEntity`에 포함된 `MSComponent` 생성
1. 각 컴포넌트의 DataType 확인
1. `MSTransform`이면 TransformView 생성
1. Position·Rotation·Scale 속성을 각 VectorBox에 Binding

이 구조를 통해 다른 Component Type도 전용 View만 추가하면 같은 Inspector 파이프라인에 연결할 수 있다.

## Undo와 Redo

Transform 값은 Binding으로 즉시 변경되지만, Editor 작업 기록에도 포함되어야 한다. 따라서 값 변경을 감지하는 Mouse Event에서 이전 값과 새 값을 기록하고 Undo/Redo 명령을 생성한다.

TextBox의 `KeyDown` 단계에서는 이벤트가 이미 처리될 수 있으므로 Preview 계열 이벤트를 활용해 더 앞선 시점에 입력을 감지한다.

# 2. GameCode 작성 환경

## MSVC Solution 구성

프로젝트를 생성할 때 사용자가 C++ Entity Script를 작성할 수 있도록 GameCode용 Visual Studio Solution과 Project를 함께 생성한다.

GameCode의 출력 형태는 용도에 따라 달라진다.

| 용도 | 출력 | 설명 |
|---|---|---|
| 게임 실행 | EXE | 독립 실행 가능한 게임 결과물 |
| Editor 연동 | DLL | Level Editor에서 Script를 로드하고 테스트 |

동일한 GameCode를 Build Type에 따라 EXE 또는 DLL로 만들 수 있도록 프로젝트 설정을 분리했다.

## Visual Studio 자동화

Visual Studio COM 객체를 제어하는 클래스를 두어 Solution과 Project를 Editor에서 관리한다.

주요 기능:

- Solution 열기와 닫기
- 프로젝트 변경 사항 저장
- 소스 파일을 Visual Studio Project에 추가
- 지정한 Build Config로 빌드 실행

## New Script

New Script 기능은 C++ 클래스 작성에 필요한 반복 작업을 자동화한다.

1. Dialog에서 Script 이름 입력
1. 입력된 이름으로 `.h`, `.cpp` 파일 생성
1. 생성한 클래스가 `entity_script`를 상속하도록 템플릿 적용
1. 두 파일을 GameCode Project에 자동 등록
1. Visual Studio에서 바로 편집할 수 있는 상태로 저장

따라서 사용자는 파일 생성, 상속 코드 작성, Visual Studio Project 편집을 각각 수행할 필요가 없다.

관련 구현: [Adding source files to the game solution using Visual Studio automation](https://github.com/TheGameEngineers/Primal/commit/c21f6b16c7341246e896ffde6a310bdee6fb0046)

# 3. Build와 DLL Hot Reload

## Build Config 선택

Editor 상단에서 Build Config를 선택할 수 있도록 ComboBox를 제공한다. 네 가지 Build Type은 enum으로 관리하며, Primal Editor의 Platform은 x64로 고정한다.

프로젝트의 역할에 따라 활성화되는 Build 대상도 구분한다.

- Engine DLL은 C#에서 사용하는 Wrapper이므로 Editor Build만 활성화
- Engine Test는 DLL을 사용하지 않으므로 EXE Build만 활성화
- F7 입력 시 Batch Build를 실행해 필요한 설정을 한 번에 빌드

## Hot Reload 순서

GameCode를 새로 빌드하려면 기존 DLL이 파일을 점유하지 않도록 먼저 해제해야 한다.

```text
Build 요청
  → Build 시작 이벤트 발생
  → 기존 GameCode DLL unload
  → 이전 PDB 정리
  → Visual Studio Solution build
  → 빌드 결과 확인
  → 성공: 새 DLL load
  → 실패: 기존 상태를 유지하고 오류 전달
  → Build 완료 이벤트 발생
```

DLL Handle은 `.cpp` 파일의 익명 namespace에 보관한다. 이를 통해 Handle을 외부에 노출하지 않으면서 로딩 상태를 단일하게 관리한다.

## PDB 충돌 방지

동적으로 로드된 DLL을 반복해서 빌드할 때 같은 PDB 파일을 사용하면 Visual Studio 빌드가 실패할 수 있다.

이를 방지하기 위해:

- 빌드마다 새로운 이름의 PDB 생성
- MSVC가 새로 생성된 PDB를 참조하도록 설정
- 불필요한 파일 누적을 막기 위해 빌드 전에 기존 PDB 삭제

이 처리로 Editor를 실행한 상태에서도 GameCode를 반복 빌드할 수 있다.

# 4. 비동기 처리와 UI 안정성

## UI Thread 차단 문제

프로젝트를 불러오는 `OnDeserialized` 과정에서 Visual Studio Build와 DLL Load를 동기적으로 실행하면 작업이 끝날 때까지 UI가 멈춘다.

따라서 GameCode 준비 과정을 별도 비동기 흐름으로 변경했다. Editor 화면은 먼저 표시되고, Visual Studio 작업은 UI Thread를 점유하지 않은 상태에서 진행된다.

## Command Binding 초기화 문제

비동기화 이후 새로운 문제가 발생했다.

- New Script는 code-behind 함수를 직접 호출해 정상 동작
- Undo, Redo, Build는 Command Binding을 사용해 동작하지 않음

원인은 UI가 먼저 초기화되고 Command 객체가 나중에 생성되면서, 초기 값이 없는 상태로 Binding된 것이었다. Command가 이후 설정되더라도 `PropertyChanged`가 호출되지 않아 화면은 새 값을 알 수 없었다.

## 해결 방법

- 비동기 작업을 호출하는 상위 경로도 async 흐름으로 변경
- Command 생성이 완료된 시점에 `PropertyChanged` 명시적 호출
- 이미 생성된 UI가 Command 속성을 다시 읽도록 갱신

이로써 프로젝트 로딩 중 UI 응답성을 유지하면서 모든 명령이 정상적으로 연결된다.

# 5. 시스템 구조

```text
┌─────────────────────────────────────────────┐
│                 WPF Editor                  │
│ Dark Theme · Project UI · Build Controls    │
└──────────────────────┬──────────────────────┘
                       │
┌──────────────────────▼──────────────────────┐
│             Component Inspector             │
│ MSEntity · MSComponent · ComponentView      │
│ NumberBox · VectorBox · TransformView        │
│ Undo / Redo                                 │
└──────────────────────┬──────────────────────┘
                       │
┌──────────────────────▼──────────────────────┐
│              GameCode Authoring             │
│ New Script · C++ Template · VS Automation   │
└──────────────────────┬──────────────────────┘
                       │
┌──────────────────────▼──────────────────────┐
│               Build Pipeline                │
│ Config Selection · DLL Unload · MSVC Build  │
│ PDB Cleanup · DLL Load · Build Events       │
└──────────────────────┬──────────────────────┘
                       │
┌──────────────────────▼──────────────────────┐
│               Editor Runtime                │
│        변경된 GameCode를 즉시 반영           │
└─────────────────────────────────────────────┘
```

# 결과

Primal Editor는 단순한 Level 표시 도구에서 다음 기능을 갖춘 통합 개발 환경으로 확장되었다.

- Dark Theme 기반의 일관된 WPF Editor UI
- 마우스 드래그와 직접 입력을 지원하는 수치 편집 컨트롤
- Entity 선택과 Component View 생성을 연결하는 Inspector
- Transform 변경에 대한 Undo/Redo
- C++ Entity Script 템플릿 생성
- Visual Studio Solution 및 Project 자동 제어
- Editor 내부 Build Config 선택과 빌드
- DLL unload/build/load 기반 Hot Reload
- PDB 충돌을 방지하는 반복 빌드 환경
- UI를 차단하지 않는 비동기 초기화


> 최종적으로 사용자는 Primal Editor 안에서 Entity를 편집하고 C++ Script를 추가한 뒤, Editor를 재시작하지 않고 빌드 결과를 다시 로드해 확인할 수 있다.

# 참고한 원본 기록
