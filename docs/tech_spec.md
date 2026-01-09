# Prompt Cheater - 기술 명세서 (Technical Specification)

## 1. 시스템 아키텍처 (System Architecture)

```mermaid
graph LR
    User[User (Tmux Pane A)] -->|Input| CLI[Cheater CLI (Python)]
    CLI -->|Request| Gemini[Gemini API (Google)]
    Gemini -->|Optimized XML| CLI
    CLI -->|Send Keys| Tmux[Tmux System (Pane B)]
    Tmux -->|Execute| Claude[Claude Code Agent]
```

## 2. 기술 스택 (Tech Stack)

| 구분 | 기술 | 선정 이유 |
|---|---|---|
| **Language** | **Python 3.10+** | 풍부한 라이브러리, 빠른 개발 속도, AI SDK 지원 우수. |
| **CLI Framework** | **Typer** | 현대적이고 직관적인 CLI 구축, Type Hint 기반. |
| **UI Library** | **Rich** | 터미널 내 아름다운 로딩 바, 마크다운 렌더링, 입력 프롬프트 제공. |
| **AI SDK** | **google-generativeai** | Gemini API 공식 SDK. 사용하기 쉽고 가벼움. |
| **System Control** | **libtmux** | Tmux 세션/윈도우/페인을 객체지향적으로 제어하는 안정적인 라이브러리. `subprocess` 직접 호출보다 훨씬 안전하고 기능이 많음. |

## 3. 데이터 흐름 및 핵심 로직

### 3.1. 메타 프롬프트 (Meta-Prompt) 전략
Gemini에게 보낼 시스템 프롬프트는 다음과 같은 역할을 수행해야 한다.
*   **Role:** "당신은 Anthropic Claude Code를 위한 프롬프트 엔지니어링 전문가입니다." 
*   **Task:** 사용자의 입력을 분석하여 의도를 파악하고, 이를 구체적이고 단계적인 지시사항으로 확장한다.
*   **Format:** 오직 XML 블록만 출력한다. 마크다운 코드블록(\`\\\`)\\)이나 사족을 붙이지 않는다.
*   **Template:**
    ```xml
    <purpose>
    {사용자의 의도를 한 문장으로 요약}
    </purpose>
    <instructions>
    1. {구체적 지시사항 1}
    2. {구체적 지시사항 2}
    ...
    </instructions>
    ```

### 3.2. Tmux Pane Discovery 알고리즘
1.  현재 프로세스의 부모 프로세스 등을 추적하거나 `TMUX_PANE` 환경변수를 통해 **Current Pane ID**를 식별한다.
2.  `libtmux`를 사용하여 현재 Window의 모든 Pane 리스트를 가져온다.
3.  **Target Pane 선정 로직:**
    *   Pane 개수가 2개일 때: `Current Pane`이 아닌 나머지 하나를 선택.
    *   Pane 개수가 2개 초과일 때: (옵션) 사용자에게 선택하게 하거나, "Next Pane" (순환 순서상 다음)을 선택. 초기 버전은 **2개 Pane 시나리오**에 집중.

### 3.3. 입력 주입 (Injection)
*   `pane.send_keys(cmd, enter=True)` 메서드를 사용한다.
*   XML 텍스트가 길 경우 버퍼 이슈가 발생할 수 있으므로, 안정성을 위해 텍스트 파일로 저장 후 `cat`하거나, 클립보드를 경유하는 방식보다는 직접 스트림 전송을 우선 시도한다. (속도 최적화)

## 4. 환경 변수 및 설정
*   `GEMINI_API_KEY`: Google AI Studio에서 발급받은 API 키.
*   (Optional) `CHEATER_TARGET_PANE`: 명시적으로 보낼 Pane ID를 지정하고 싶을 때 사용.

## 5. 디렉토리 구조 (Directory Structure)
```
prompt_cheater/
├── prompt_cheater/
│   ├── __init__.py
│   ├── main.py        # Entry point (Typer)
│   ├── ai.py          # Gemini API Wrapper
│   ├── tmux.py        # libtmux integration
│   └── ui.py          # Rich TUI helpers
├── docs/
├── tests/
├── pyproject.toml     # Poetry configuration
└── README.md
```
