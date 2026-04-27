# clever-change-control

## 목적

이 저장소는 변경 요청, 승인 상태, 구현 연결, 롤아웃 및 롤백 근거를 한곳에서 관리하기 위한 저장소다.

## 작업 시작 규칙

일반적인 CLEVER 작업 시작은 이 저장소가 아니라 `clever-agent-project`에서 한다.

즉 아래처럼 구분한다.

- 작업 라인과 대상 저장소가 아직 정해지지 않은 일반 시작: `clever-agent-project`
- 현재 `clever-change-control` 자체를 수정하는 작업: 여기서 계속

시작 전에 먼저 아래 명령으로 로컬 3저장소, `gh auth status`, GitHub 계정, org membership, 원격 접근, issue/PR/ruleset 조회 가능 여부를 확인한다.

```bash
python3 ../clever-agent-project/scripts/bootstrap_clever_work.py --cwd "$PWD" --preflight --json
```

현재 저장소 자체를 직접 수정하는 작업이면 아래처럼 유지보수 모드로 확인한다.

```bash
python3 ../clever-agent-project/scripts/bootstrap_clever_work.py --cwd "$PWD" --preflight --current-repo-maintenance --json
```

`preflight_check.ready=false`이면 시작 절차로 내려가지 않고 실패한 check를 먼저 해결한다.
`true`이면 내부 `workspace_check.agent_action`을 아래처럼 해석한다.

- `proceed-with-hard-gate`: 로컬 상태가 정상이며 시작 절차를 진행할 수 있음
- `current-repo-maintenance`: 현재 저장소 자체를 수정하는 세션이므로 여기서 계속
- `switch-to-clever-agent-project`: 일반 시작이므로 `clever-agent-project`로 이동
- `stop-and-fix-workspace`: 필요한 로컬 저장소 구성이 부족하므로 먼저 보완

즉 이 저장소는 승인과 추적용 저장소이지, 일반 시작의 기본 위치는 아니다.

## 관리하는 것

- `project-start` root issue와 그 승인 상태
- 변경 요청 이슈와 롤백 요청 이슈
- 변경 단위별 검토 및 승인 기록
- 구현 PR과 대상 저장소/서비스 연결 정보
- 환경별 릴리스 묶음과 추적 기준

## 관리하지 않는 것

- 서비스 소스코드와 애플리케이션 구현
- 전사 규칙의 정본 문서
- 서버 배포 방법이나 운영 절차 상세

## main 브랜치 운영 규칙

이 repo는 public으로 운영하며, `main`은 GitHub ruleset `CLEVER protect main`으로 보호한다.

- `main` direct push 금지
- `main` 삭제 금지
- force push 금지
- `main` 변경은 PR 필수
- PR 필수지만 승인 수는 0명
- merge/write는 repo admin 권한자만 수행
- admin bypass는 `pull_request` 모드만 허용

`main` merge commit 제목은 PR merge임이 드러나게 아래 형식을 쓴다.

```text
Merge pull request #<pr-number> from <owner>/<source-branch>
```

이 저장소 자체를 수정할 때도 역할 접두사 branch에서 작업하고 PR로 올린다.

### PR 완료 후 branch 정리

PR이 merge됐거나 source branch를 버리기로 하고 closed 처리된 뒤에는 task
branch를 정리한다. 단, 해당 branch가 아직 open PR, 후속 issue, child branch,
active release/hotfix에 쓰이면 삭제하지 않는다.

기본 명령은 아래 순서다.

```bash
git switch main
git pull --ff-only origin main
git branch -d <source-branch>
git push origin --delete <source-branch>
git fetch --prune origin
```

- `main`과 `dev`는 삭제 대상이 아니다.
- 기본은 `git branch -d <source-branch>`를 쓴다.
- merge 없이 닫은 branch를 폐기해야 할 때만 사용자 확인 후 `git branch -D <source-branch>`를 쓴다.
- remote branch가 GitHub에서 이미 삭제됐더라도 `git fetch --prune origin`으로 로컬 추적 branch를 정리한다.

## project-start root 규칙

모든 작업의 시작점은 `project-start` root issue다.

- root canonical identifier는 생성된 `project-start issue #`다.
- intake 단계에서는 `business intent`, `constraints`, `expected outcome`, `candidate template lineage`, `candidate target repo/service`를 먼저 기록한다.
- 일반 intake에서는 `target service` 확정값이나 `change id`를 강제하지 않는다.
- root issue 승인 후, 실행 scope가 고정되면 scoped change request와 `change id`로 내려간다.

## change id 규칙

`project-start` intake의 root canonical identifier는 생성된 issue 번호다.

`change id`는 그 이후 실제 change request, rollout, rollback 묶음을 추적할 때 쓰는 보조 식별자다.

- change id는 `chg-YYYYMMDD-NNN` 형식을 사용한다.
- `YYYYMMDD`는 변경 요청을 등록한 날짜를 뜻한다.
- `NNN`은 같은 날짜 안에서 001부터 증가하는 세 자리 일련번호다.
- post-project-start change request, 구현 PR, rollout 기록, rollback 요청은 같은 작업 단위라면 하나의 change id를 공유한다.
- change id는 실행 scope가 고정된 change request에서 발급하고 이후 관련 산출물에서 그대로 재사용한다.

## 개발 타입 규칙

변경 요청 이슈는 요청 유형 외에도 개발 타입을 상위/하위 2단으로 기록한다.

### 상위 타입

- `MSA/SaaS`
- `일반 개발`

### 하위 타입

#### `MSA/SaaS`

- `복제`
- `수정`
- `변경`
- `신규`

#### `일반 개발`

- `신규 개발`
- `수정`
- `변경`
- `리팩토링`

### 제목 규칙

변경 요청 이슈 제목은 아래 형식을 따른다.

```text
[상위 타입][하위 타입] 짧은 제목
```

예:

- `[MSA/SaaS][복제] 배차 서비스 고객사 배포 분기 추가`
- `[일반 개발][리팩토링] bootstrap packet 구조 정리`

### 본문 규칙

이슈 본문에도 아래 값을 구조적으로 남긴다.

```text
work_type_group: MSA/SaaS
work_type_detail: 복제
```

에이전트는 제목과 본문의 타입 값을 항상 일치시켜야 한다.

## 템플릿 하네스 메타 규칙

템플릿 기반 신규 시작이나 유지보수 추적이 필요한 변경 요청은 아래 메타도 함께 남긴다.

```text
template_id: <template-id>
template_version: <version>
deploy_profile: <deploy-profile>
override_scope: <override-scope-if-relevant>
lifecycle_action: <adopt|modify|migrate|retire>
```

`override_scope`는 form field로 강제하지 않더라도, customer-specific divergence 경계를 같이 남겨야 하는 경우 issue body 메모로 추가한다.

이 값은 `clever-context-monorepo`의 template lineage와 bootstrap 선택 결과를 mirror 하는 용도다. 이 저장소가 별도의 정본이 되는 것은 아니다.

## 기본 흐름

1. `project-start` root issue를 등록하거나 갱신한다.
2. root issue 기준으로 작업 목적, 제약, candidate lineage, candidate repo/service를 승인한다.
3. 실행 scope가 고정되면 scoped change request를 만들고 `change id`를 발급한다.
4. 필요한 spec 또는 ui-spec을 `clever-context-monorepo`에 작성하거나 갱신한다.
5. 구현 PR을 대상 저장소와 서비스에 연결한다.
6. 결과를 환경별로 정리하고 필요하면 rollback 요청으로 되돌린다.

## clever-context-monorepo와의 관계

`clever-context-monorepo`는 규칙, 구조, 용어, 서비스 문서의 정본을 관리한다. 이 저장소는 그 정본을 바탕으로 변경 단위의 승인 흐름과 구현 연결, rollout/rollback 이력을 관리한다.
