# github-timestamp-tag

## 설명

- timestamp 기반 tag 생성
- prefix 와 생성된 tag 기반으로 이전 tag 조회
- 이전 tag 와 생성된 tag 기반으로 [release note](https://docs.github.com/en/repositories/releasing-projects-on-github/automatically-generated-release-notes) 생성

## Usecase

- 모노레포에서 각 프로젝트별로 release 생성하는 경우 github release의 [Generate release notes](https://docs.github.com/en/repositories/releasing-projects-on-github/automatically-generated-release-notes) feature 를 사용할 수 없음.
  - 해당 기능은 timestamp 기반으로 이전 tag 를 가져오기 때문
- 따라서 prefix로 release tag를 구분하고 해당 prefix로 이전 tag를 조회하여 release note를 생성

## 사용법

- .github/workflows/test.yml 참고
- 기존 tag를 찾기 위해서는 checkout 시 fetch-depth: 0 으로 설정해야 함
