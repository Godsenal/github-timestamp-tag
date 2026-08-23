# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

GitHub Composite Action that generates timestamp-based git tags with prefix support. Designed for monorepo environments where GitHub's built-in release note generation doesn't work per-project (since it uses timestamp-based previous tag lookup).

## How It Works

The action (`action.yml`) is a composite action with three steps:
1. **Create version** — generates a tag in format `{prefix}YYYYMMDDHHmm` (KST, UTC+9) using `actions/github-script`
2. **Get previous tag** — finds the most recent existing tag matching the prefix via `WyriHaximus/github-action-get-previous-tag`
3. **Generate release note** — calls GitHub's `generateReleaseNotes` API between previous and current tags; supports a `configuration_file_path` input for customizing release note generation

### Inputs/Outputs

- **Inputs**: `prefix` (tag prefix), `configuration_file_path` (release notes config)
- **Outputs**: `previousTag`, `currentTag`, `releaseNote`

## GitHub Action Development

이 프로젝트는 **Composite Action** (`runs.using: "composite"`)으로 구성됨. Docker나 JavaScript action이 아닌, 여러 step을 조합하는 방식.

### action.yml 수정 시 주의사항
- `actions/github-script`의 `script` 블록 안에서 inputs 참조는 `${{ inputs.xxx }}` 문법 사용
- composite action의 각 step에서 `shell: bash`를 명시해야 함 (run step)
- step 간 데이터 전달은 `steps.<id>.outputs` 사용

### 릴리스/배포
- git tag로 버전 관리: `v0.0.1`, `v0.0.2` 등
- 소비자는 `uses: godsenal/github-timestamp-tag@v0.0.1` 형태로 참조
- 새 버전 배포 시: `git tag v0.0.X && git push origin v0.0.X`
- major 버전 태그(`v1` 등)를 floating tag로 유지하면 소비자가 `@v1`로 최신 패치를 자동 수신 가능

### 의존 액션
- `actions/github-script@v6` — JS 스크립트 실행
- `WyriHaximus/github-action-get-previous-tag@v1` — prefix 기반 이전 태그 조회

## Testing

로컬 테스트 스위트 없음. `.github/workflows/test.yml`에서 push/workflow_dispatch 시 CI로 테스트. 로컬 테스트가 필요하면 [act](https://github.com/nektos/act) 사용.

## Key Requirement

소비자는 `actions/checkout`에서 `fetch-depth: 0`을 설정해야 전체 태그 히스토리를 가져올 수 있음. 그렇지 않으면 이전 태그 조회 실패.
