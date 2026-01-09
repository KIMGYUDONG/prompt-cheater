# Prompt Cheater - 요구사항 명세서

## 1. 개요 (Overview)
*   **프로젝트명:** Prompt Cheater
*   **목적:** `claude` CLI 에이전트(Claude Code) 사용자 경험을 극대화하기 위한 보조 도구. 자연어 입력을 Claude 친화적인 XML 프롬프트로 변환하여, Tmux 환경에서 자동으로 실행 중인 Claude 에이전트에 주입한다.
*   **핵심 가치:** 
    1.  복잡한 XML 태그(`<purpose>`, `<instructions>` 등)를 직접 타이핑하는 수고 제거.
    2.  Gemini의 추론 능력을 빌려 프롬프트 엔지니어링 자동화 (1차 가공).
    3.  Tmux 워크플로우(좌: 코딩 / 우: AI)의 끊김 없는 통합.

## 2. 사용자 페르소나 (User Persona)
*   **대상:** Tmux와 CLI 환경을 선호하는 숙련된 개발자.
*   **환경:** macOS, iTerm2/Terminal, Tmux, Python 환경.
*   **특징:** 마우스 사용을 지양하고 키보드로 모든 워크플로우를 통제하길 원함.

## 3. 사용자 시나리오 (User Stories)
1.  **실행:** 사용자는 Tmux의 한쪽 Pane에서 작업하다가, `cheater` 명령어를 입력하여 입력 모드로 진입한다.
2.  **입력:** TUI(Text User Interface) 입력창이 뜨면, 사용자는 자연어로 지시사항을 입력한다. (예: "이 함수 에러 처리 좀 강화하고 로깅 추가해줘")
3.  **처리:** 엔터를 치면, 도구는 입력을 Gemini API로 전송하여 구조화된 XML 프롬프트를 생성한다.
4.  **전송:** 생성된 XML 텍스트가 자동으로 **다른 Pane**(일반적으로 Claude가 실행 중인 Pane)에 입력된다.
5.  **완료:** 텍스트 입력 후 자동으로 실행(Enter)까지 수행되어, 사용자는 즉시 Claude의 작업 결과를 볼 수 있다.

## 4. 기능적 요구사항 (Functional Requirements)

### 4.1. 사용자 인터페이스 (CLI/TUI)
*   `cheater` 단일 명령어 실행.
*   여러 줄(Multi-line) 입력이 가능한 텍스트 입력 인터페이스 제공.
*   입력 중 `Ctrl+C` 등으로 취소 가능.
*   처리 상태(Loading Spinner) 표시 ("Gemini가 프롬프트를 깎는 중...", "Tmux로 배달 중..." 등).

### 4.2. 프롬프트 엔진 (AI Core)
*   **모델:** Gemini 1.5 Flash (속도와 가성비 최우선).
*   **기능:**
    *   사용자의 불분명한 입력을 명확한 지시사항으로 구체화.
    *   Anthropic Claude Code가 선호하는 XML 구조로 변환.
    *   필수 태그 포함: `<purpose>`, `<context>`(필요시), `<instructions>`.

### 4.3. 시스템 제어 (Tmux Integration)
*   **Pane 감지:** 현재 실행 중인 Pane을 제외한 **활성 상태의 다른 Pane**을 자동으로 타겟팅.
    *   기본 로직: 화면에 2개의 Pane이 있을 경우, "내가 아닌 다른 하나"를 선택.
*   **키 입력 전송:** 타겟 Pane에 텍스트를 문자열 스트림으로 전송하고 실행 신호(Enter)를 보냄.

## 5. 비기능적 요구사항 (Non-Functional Requirements)
*   **속도:** 입력 완료 후 대상 Pane에 텍스트가 입력되기까지 3초 이내 목표.
*   **보안:** API Key(`GEMINI_API_KEY`)는 환경 변수로 관리하며 코드에 하드코딩하지 않음.
*   **호환성:** macOS 환경의 Tmux 세션 내에서 오작동 없이 동작해야 함.
