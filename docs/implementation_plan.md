# Prompt Cheater - 구현 계획 (Implementation Roadmap)

## Phase 1: 프로젝트 셋업 및 환경 구성 (Setup)
- [ ] **프로젝트 초기화:** `poetry`를 사용하여 Python 프로젝트 구조 생성.
- [ ] **의존성 설치:** `typer`, `rich`, `google-generativeai`, `libtmux`, `python-dotenv` 설치.
- [ ] **환경 변수 설정:** `.env` 파일 템플릿 생성 및 `.gitignore` 설정.
- [ ] **Hello World 테스트:** CLI가 실행되는지 확인하는 기본 뼈대 코드 작성.

## Phase 2: 핵심 모듈 구현 (Core Modules)

### Step 2-1: Gemini AI 모듈 (`ai.py`)
- [ ] `google-generativeai` 클라이언트 설정.
- [ ] `generate_xml_prompt(user_input)` 함수 구현.
- [ ] 시스템 프롬프트(Meta-Prompt) 작성 및 튜닝.
- [ ] 단위 테스트: 단순 문자열 입력 시 XML 포맷 반환 확인.

### Step 2-2: Tmux 제어 모듈 (`tmux.py`)
- [ ] `libtmux`를 이용해 현재 세션/윈도우 정보 가져오기.
- [ ] **Target Pane 감지 로직:** 현재 Pane을 제외한 타겟 Pane 식별 함수 구현.
- [ ] `send_to_pane(pane_id, text)` 함수 구현.
- [ ] 통합 테스트: Python 스크립트 실행 시 옆 Pane에 "Hello" 출력 확인.

## Phase 3: CLI 및 UI 통합 (Integration)

### Step 3-1: 사용자 인터페이스 (`main.py`, `ui.py`)
- [ ] `cheater` 명령어 정의.
- [ ] `rich.prompt` 또는 `sys.stdin`을 이용한 멀티라인 입력 구현.
- [ ] `rich.status`를 이용한 로딩 인디케이터(Spinner) 구현.

### Step 3-2: 전체 파이프라인 연결
- [ ] Input -> AI Processing -> Tmux Sending 흐름 연결.
- [ ] 예외 처리: API 에러, Tmux Pane 못 찾음, API Key 누락 등.

## Phase 4: 패키징 및 설치 (Distribution)
- [ ] `pyproject.toml`에 `[tool.poetry.scripts]` 설정으로 `cheater` 명령어 등록.
- [ ] 로컬 설치 테스트 (`pip install -e .`).
- [ ] 최종 사용성 테스트 (실제 코딩 시나리오 리허설).

## Phase 5: 문서화 및 마무리 (Finalize)
- [ ] `README.md` 작성 (설치법, 사용법, API Key 설정).
- [ ] 코드 정리 및 주석 보강.
