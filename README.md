# Agent Rules Router

**작업에 필요한 규칙과 스킬을 골라, 한 대화에서 이어서 일하기 위한 Codex 스킬.**

전역 `AGENTS.md`에 모바일 테스트, 배포, 문서 작성 규칙을 모두 넣으면 간단한 작업에도 불필요한 절차가 따라올 수 있습니다. 이 스킬은 원래 규칙의 조건과 출처를 확인하고, 현재 작업에 적용할 내용을 작은 Markdown 프로필로 정리합니다.

예를 들어 Android UI 테스트용 규칙은 웹 문구 수정에서는 조건이 맞지 않을 수 있습니다. 반대로 실제 기기 테스트를 요청하면 해당 탐색·검증 규칙을 포함합니다. 무조건 적용하라고 적힌 규칙을 임의로 삭제하지는 않습니다.

## 설치

Codex에서 다음처럼 요청하세요.

```text
$skill-installer https://github.com/meanit-dh/agent-rules-router/tree/main/skills/agent-rules-router 에 있는 스킬을 설치해줘.
```

또는 저장소를 내려받은 뒤 `skills/agent-rules-router` 폴더를 사용 중인 Codex의 사용자 스킬 경로에 복사하세요. [현재 공식 문서](https://learn.chatgpt.com/docs/build-skills)는 `~/.agents/skills`와 프로젝트의 `.agents/skills`를 안내합니다. 기존 설치가 `~/.codex/skills`를 사용한다면 그 환경의 경로를 확인하세요. 같은 스킬을 여러 경로에 중복 설치할 필요는 없습니다.

## 사용

**지금 어떤 규칙이 들어 있는지 확인**

```text
$agent-rules-router 내 AGENTS.md 실제 파일을 읽고 어떤 요청사항이 있는지 보여줘.
```

**작업 전에 필요한 규칙만 정리하고 바로 진행**

```text
$agent-rules-router 이번 작업은 웹사이트 버튼 문구 수정이야.
기존 규칙과 스킬을 확인해서 이번 작업용 규칙 파일을 만들고 수정까지 진행해줘.
```

**같은 대화에서 작업 종류가 바뀔 때**

```text
$agent-rules-router 이제 Android 로그인 흐름을 테스트할 거야.
현재 작업 프로필에서 필요한 규칙과 스킬을 다시 골라줘.
```

**다음 실행에 사용할 임시 규칙을 적용할 때**

```text
$agent-rules-router 이 프로젝트에 적용할 임시 AGENTS.override.md를 준비해줘.
적용 전 변경 내용을 보여줘.
```

적용을 요청하면 기존 override를 백업하고, 같은 파일이 다른 작업에서 바뀌지 않았는지 확인합니다. 이후 다음처럼 복원을 요청할 수 있습니다.

```text
$agent-rules-router 아까 적용한 임시 override를 저장해 둔 백업으로 복원해줘.
중간에 수정된 파일이 있으면 덮어쓰지 말고 알려줘.
```

## 실제로 하는 일

1. 현재 작업, 지침 파일의 범위, 사용 가능한 스킬 목록을 확인합니다.
2. 규칙을 `적용 / 조건 미충족 / 확인 필요`로 분류합니다.
3. 관련 스킬만 읽고, 출처를 남긴 작업용 Markdown을 만듭니다.
4. 요청된 본 작업을 같은 대화에서 계속합니다.
5. 별도 요청이 있을 때만 임시 override를 적용하거나 복원합니다.

작업 프로필과 백업은 개인 작업 산출물입니다. 이 공개 저장소에 올리지 않습니다. 저장소에는 재사용할 스킬과 사용법만 들어 있습니다.

## 알아둘 점

- 이미 대화에 들어온 지침을 삭제하거나 사용한 토큰을 되돌리는 기능은 아닙니다.
- 작업용 Markdown은 정리 문서입니다. 생성했다는 이유만으로 Codex가 자동으로 읽지는 않습니다.
- `AGENTS.override.md`는 같은 디렉터리의 기본 지침을 대신하는 방식이며, 다른 계층의 지침까지 제거하지 않습니다. 새 실행에서 지침을 다시 읽어야 합니다.
- 전역 override는 같은 Codex 홈을 쓰는 다른 새 실행에도 영향을 줍니다. 대화별로 전역 파일을 번갈아 덮어쓰는 방식에는 적합하지 않습니다.
- 설치만으로 매 작업 시작 시 실행되는 것은 아닙니다. 확실히 사용하려면 `$agent-rules-router`를 호출하세요. 자동 선택은 요청과 스킬 설명의 일치 여부에 달려 있습니다.
- 시스템 지침, 권한, 보안 요구사항을 우회하지 않습니다. 필요 없는 상시 규칙을 줄이려면 원본 규칙의 적용 범위를 명시적으로 조정해야 합니다.

Codex가 지침을 읽는 방식은 [OpenAI 공식 AGENTS.md 문서](https://learn.chatgpt.com/docs/agent-configuration/agents-md)를 참고하세요. 실행 중인 앱의 실제 동작이 다르면 그 환경을 확인한 뒤 적용합니다.

## English

Agent Rules Router inspects instruction files and skill metadata, selects applicable guidance, and writes a compact task profile with source references. It supports inspection, routing within the current chat, and explicitly requested temporary Codex overrides with backup and conflict-aware restoration.

It is an instruction-only skill: no dependencies, background process, automatic startup hook, or bundled MCP server. It does not erase already-loaded instructions or promise token savings. Conditional rules remain conditional; unconditional requirements require an authorized scope change before they can be narrowed.

Install the `skills/agent-rules-router` directory using Codex's skill installer, then invoke:

```text
$agent-rules-router Prepare the rules and skills needed for this task, then continue the work.
```

The override workflow is agent-operated, not a transactional file-locking utility. Keep shared instruction files free of concurrent writers during an apply or restore operation.

## 파일 구성

```text
skills/agent-rules-router/
  SKILL.md                          # 분류와 작업 프로필 생성
  agents/openai.yaml                # Codex 표시 정보
  references/temporary-overrides.md # 요청 시 적용·백업·복원
```

## License

[MIT](LICENSE). This project is independent of OpenAI and ARTEMIS. It does not redistribute personal instruction files or ARTEMIS's rule text.
