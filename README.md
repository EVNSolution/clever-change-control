# clever-change-control

## 목적

이 저장소는 변경 요청, 승인 상태, 구현 연결, 롤아웃 및 롤백 근거를 한곳에서 관리하기 위한 저장소다.

## 관리하는 것

- 변경 요청 이슈와 롤백 요청 이슈
- 변경 단위별 검토 및 승인 기록
- 구현 PR과 대상 저장소/서비스 연결 정보
- 환경별 릴리스 묶음과 추적 기준

## 관리하지 않는 것

- 서비스 소스코드와 애플리케이션 구현
- 전사 규칙의 정본 문서
- 서버 배포 방법이나 운영 절차 상세

## change id 규칙

- change id는 `chg-YYYYMMDD-NNN` 형식을 사용한다.
- `YYYYMMDD`는 변경 요청을 등록한 날짜를 뜻한다.
- `NNN`은 같은 날짜 안에서 001부터 증가하는 세 자리 일련번호다.
- change request, 구현 PR, rollout 기록, rollback 요청은 같은 작업 단위라면 하나의 change id를 공유한다.
- change id는 변경 요청 이슈를 등록할 때 발급하고 이후 관련 산출물에서 그대로 재사용한다.

## 기본 흐름

1. 변경 요청을 등록한다.
2. 필요한 spec 또는 ui-spec을 `clever-context-monorepo`에 작성하거나 갱신한다.
3. 요청과 문서를 기준으로 승인한다.
4. 구현 PR을 대상 저장소와 서비스에 연결한다.
5. 결과를 환경별로 정리하고 필요하면 rollback 요청으로 되돌린다.

## clever-context-monorepo와의 관계

`clever-context-monorepo`는 규칙, 구조, 용어, 서비스 문서의 정본을 관리한다. 이 저장소는 그 정본을 바탕으로 변경 단위의 승인 흐름과 구현 연결, rollout/rollback 이력을 관리한다.
