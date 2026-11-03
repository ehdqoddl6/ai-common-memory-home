# 공용 AI 설정

이 저장소의 `.skillshare/`를 Claude Code, Codex, Gemini CLI에서 함께 쓰는 설정 원본으로 사용합니다. [Skillshare](https://github.com/runkids/skillshare)의 프로젝트 모드로 관리하며, 개인별 대상 경로가 담긴 `.skillshare/config.yaml`은 Git에 포함하지 않습니다. 공유용 설정 예시는 `.skillshare/config.example.yaml`입니다.

## 구성

- `.skillshare/extras/instructions/AGENTS.md`: 공통 기본 지침. Codex에는 `AGENTS.md`로 복사하고, Claude의 기존 `CLAUDE.md` 앞에는 관리 블록으로 넣으며, Gemini에는 `GEMINI.md`로 복사합니다.
- `.skillshare/skills/`: 세 도구에 배포할 공용 스킬 원본. 기존 `~/.agents/skills`와 사용 가능한 Gemini 스킬에서 가져왔습니다.
- `.skillshare/agents/`: Claude Code용 Markdown agent 원본.
- `.skillshare/extras/codex-agents/`: Codex용 TOML agent 원본.
- `.skillshare/extras/gemini-agents/`: Gemini CLI용 Markdown agent 원본.

도구별 agent 형식이 달라서 agent 파일은 각각의 원본을 유지합니다. 기존 대상에 같은 이름의 파일이 있으면 Skillshare의 `merge` 모드는 그 파일을 유지합니다. 원본을 우선 적용하려면 미리보기와 백업을 확인한 후 `--force`를 사용하세요.

## 이 컴퓨터에서 동기화

```sh
skillshare sync -p --all --dry-run
skillshare sync -p --all
skillshare status -p
```

`--all`은 스킬, agent, 기본 지침을 함께 동기화합니다. 이 저장소의 개인 설정에는 `~/.claude`, `~/.codex`, `~/.gemini`가 대상으로 등록돼 있습니다. 기존 파일을 원본으로 바꿔야 할 때만 `skillshare sync -p --all --force`를 실행하세요.

## 다른 컴퓨터에서 시작

```sh
brew install skillshare
cp .skillshare/config.example.yaml .skillshare/config.yaml
skillshare sync -p --all --dry-run
```

필요하면 `.skillshare/config.yaml`에서 각 대상 경로를 이 컴퓨터의 설치 위치에 맞게 수정합니다. 미리보기와 기존 파일 백업을 확인한 뒤 `skillshare sync -p --all`을 실행합니다. 같은 이름의 기존 파일을 원본으로 바꿀 때만 `--force`를 붙입니다. 개인 설정 파일은 Git에서 제외됩니다.

## 프로젝트별 설정 추가

각 프로젝트에서는 별도로 `skillshare init -p`를 실행합니다. 해당 프로젝트의 `.skillshare/skills/<스킬명>/SKILL.md`나 `.skillshare/agents/<agent명>.md`를 만들고 `skillshare sync -p --all`을 실행하면 프로젝트 범위에만 추가됩니다.

프로젝트별 **기본 지침**은 프로젝트 루트의 `AGENTS.md`에 작성합니다. Codex는 `~/.codex/AGENTS.md`를 전역 지침으로 읽고, 그 뒤에 프로젝트 루트부터 현재 작업 디렉터리까지의 `AGENTS.md`를 순서대로 읽습니다. 따라서 프로젝트의 `.codex/AGENTS.md`에만 적힌 내용은 프로젝트 루트에서 작업할 때 자동으로 적용되지 않습니다. 그런 파일이 있다면 프로젝트 루트 `AGENTS.md`로 옮기세요. 하위 폴더에만 적용할 규칙은 그 폴더의 `AGENTS.md` 또는 `AGENTS.override.md`에 둡니다.

Gemini CLI는 프로젝트의 `GEMINI.md`를 읽으므로, `AGENTS.md`만 만들면 프로젝트 지침을 놓칩니다. 이 저장소처럼 프로젝트 루트에 `GEMINI.md -> AGENTS.md` 심볼릭 링크를 함께 두세요. Claude Code는 루트 `CLAUDE.md`에 `@AGENTS.md`를 넣어 같은 지침을 불러옵니다. 이미 각 도구의 전용 지침 파일에 별도 내용이 있다면 그 내용을 보존한 채 연결을 추가하세요. 새 프로젝트에서는 Skillshare의 프로젝트 화면(`skillshare ui -p`)에서도 연결 상태를 확인하고 Gemini 링크와 Claude 가져오기를 만들 수 있습니다.

새 프로젝트에 `AGENTS.md`를 만든 뒤 Gemini 연결 파일은 `ln -s AGENTS.md GEMINI.md`로 만들 수 있습니다. 기존 `GEMINI.md`가 있으면 그 내용을 먼저 살펴보고, Gemini CLI가 지원하는 `@./AGENTS.md` 가져오기를 추가하는 방법을 사용하세요.

프로젝트의 `.codex/skills/<이름>/SKILL.md`에 스킬을 두었다면 Codex의 공식 프로젝트 스킬 위치인 `.agents/skills/<이름>/SKILL.md`로 옮기는 편이 확실합니다. 세 도구가 함께 쓸 스킬은 그 프로젝트의 `.skillshare/skills/`에 넣고 프로젝트 모드로 동기화하세요. 이 저장소의 전역 공용 스킬은 각 프로젝트의 전용 스킬과 함께 사용할 수 있습니다.

## 되돌리기

최초 변경 전 홈 디렉터리 파일은 `.local-backups/ai-common-memory-home-before-skillshare-20261031.tgz`에 보관합니다. 이 파일은 Git에서 제외됩니다. 필요한 경로를 확인한 뒤 해당 파일만 복원하세요.

```sh
tar -tzf .local-backups/ai-common-memory-home-before-skillshare-20261031.tgz
```

Skillshare가 기본 지침 파일을 바꿀 때 만든 개별 백업은 `skillshare backup files list -p`로도 확인할 수 있습니다.
