# Prompt Cheater

Natural Language → XML Prompt → Claude Code 변환 CLI 도구

## Build/Test Commands

```bash
# 실행
cheater              # 기본 (싱글라인 입력)
cheater -m           # 멀티라인 입력
cheater -p           # XML 미리보기
cheater -n           # 드라이런 (전송 안함)

# 코드 품질
ruff check prompt_cheater/       # 린팅
ruff format prompt_cheater/      # 포맷팅
mypy prompt_cheater/             # 타입 체크
bandit -r prompt_cheater/ -c pyproject.toml  # 보안 검사

# 테스트
pytest --cov=prompt_cheater
```

## Code Style

- **Python**: 3.10+
- **Formatter**: Ruff (line-length=88, double quotes)
- **Type Hints**: 필수 (mypy 검사)
- **Imports**: isort 스타일 (Ruff로 정렬)
- **UI Colors**: Nord 팔레트 (`ui.py:COLORS`)

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                    main.py                          │
│  CLI entry point, argparse, main loop               │
└──────────────┬──────────────────┬───────────────────┘
               │                  │
       ┌───────▼───────┐  ┌───────▼───────┐
       │    ai.py      │  │    ui.py      │
       │ Gemini API    │  │ Rich TUI      │
       │ XML 생성      │  │ 입력/출력     │
       └───────────────┘  └───────────────┘
               │
       ┌───────▼───────┐
       │   tmux.py     │
       │ Pane 탐색     │
       │ 텍스트 주입   │
       └───────────────┘
```

## Key Files

| 파일 | 역할 |
|------|------|
| `prompt_cheater/main.py` | CLI 진입점, 메인 루프 |
| `prompt_cheater/ai.py` | Gemini API 래퍼, XML 생성 |
| `prompt_cheater/tmux.py` | Tmux pane 탐색 및 텍스트 주입 |
| `prompt_cheater/ui.py` | Rich TUI 헬퍼, 입력/출력 |
| `pyproject.toml` | 의존성, 도구 설정 |
| `.pre-commit-config.yaml` | Git hooks 설정 |

## Functions Reference

### main.py
| 함수 | 설명 |
|------|------|
| `main()` | CLI 진입점, argparse 설정, 메인 루프 |
| `app()` | Poetry script 호환 래퍼 |

### ai.py
| 클래스/함수 | 설명 |
|------------|------|
| `GeminiClient` | Gemini API 클라이언트 |
| `GeminiClient.__init__(api_key)` | API 키로 초기화 |
| `GeminiClient.generate_xml_prompt(user_input)` | 자연어 → XML 변환 |
| `META_PROMPT` | Gemini에게 보내는 메타 프롬프트 |

### tmux.py
| 클래스/함수 | 설명 |
|------------|------|
| `TmuxError` | 기본 Tmux 예외 |
| `NotInTmuxError` | Tmux 세션 외부 실행 시 |
| `NoPaneFoundError` | 타겟 pane 없음 |
| `get_current_pane_id()` | 현재 pane ID 반환 |
| `get_server()` | libtmux Server 객체 |
| `get_current_window()` | 현재 window 객체 |
| `get_target_pane(explicit_pane_id)` | 타겟 pane 자동 탐색 |
| `send_to_pane(pane, text, enter)` | pane에 텍스트 전송 (버퍼 방식) |
| `send_text_to_other_pane(text, enter)` | 다른 pane에 텍스트 전송 |

### ui.py
| 함수 | 설명 |
|------|------|
| `console` | 전역 Rich Console 인스턴스 |
| `COLORS` | Nord 색상 팔레트 dict |
| `PROMPT_STYLE` | prompt_toolkit 스타일 (Nord 팔레트) |
| `print_banner()` | 앱 배너 출력 |
| `print_info(message)` | 정보 메시지 (›) |
| `print_success(message, elapsed)` | 성공 메시지 (✓) + 시간 |
| `print_error(message)` | 에러 메시지 (✗) |
| `print_warning(message)` | 경고 메시지 (!) |
| `get_multiline_input()` | 멀티라인 입력 (prompt_toolkit, Enter 2회로 전송) |
| `get_single_line_input(prompt)` | 싱글라인 입력 (prompt_toolkit) |
| `spinner(message)` | 로딩 스피너 context manager |
| `show_xml_preview(xml_text)` | XML 구문 강조 미리보기 |
| `confirm(message, default)` | Y/n 확인 프롬프트 (prompt_toolkit) |
| `print_separator()` | 구분선 출력 |

## Environment Variables

| 변수 | 필수 | 설명 |
|------|------|------|
| `GEMINI_API_KEY` | Yes | Google Gemini API 키 |
| `CHEATER_TARGET_PANE` | No | 명시적 타겟 pane ID (예: %1) |
| `TMUX_PANE` | Auto | Tmux가 자동 설정 |

## Dependencies

| 패키지 | 용도 |
|--------|------|
| `google-generativeai` | Gemini API SDK |
| `libtmux` | Tmux 세션/pane 제어 |
| `rich` | 터미널 UI (색상, 패널, 스피너) |
| `prompt-toolkit` | 터미널 입력 (한글 등 wide character 지원) |
| `python-dotenv` | .env 파일 로드 |

## Error Handling

```
NotInTmuxError     → Tmux 세션에서 실행 필요
NoPaneFoundError   → 2개 이상의 pane 필요
TmuxError          → 버퍼 작업 실패
ValueError         → API 키 누락
```

## Key Implementation Details

1. **텍스트 주입 방식**: `send_keys()` 대신 `load-buffer` + `paste-buffer` 사용 (긴 텍스트 지원)
2. **입력 처리**: prompt_toolkit 사용 (한글 등 wide character 정상 지원, wcwidth 기반 폭 계산)
3. **XML 생성**: META_PROMPT로 Gemini에게 XML 전용 출력 지시
4. **Pane 탐색**: 현재 window에서 자신이 아닌 첫 번째 pane 자동 선택
