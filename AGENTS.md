# 이 저장소의 작업 지침

- 공용 기본 지침, 스킬, agent의 원본은 `.skillshare/`에서 수정한다.
- 변경 후 `skillshare sync -p --all --dry-run`으로 대상과 충돌을 확인하고 동기화한다.
- `.skillshare/config.yaml`과 `.local-backups/`는 이 컴퓨터의 개인 설정과 백업이므로 Git에 추가하지 않는다.
- 다른 프로젝트의 전용 지침은 해당 프로젝트 루트의 `AGENTS.md`에 둔다. 그 프로젝트의 Gemini와 Claude 연결 파일도 함께 확인한다.
