---
layout: post
title: "2. Git Commit Message 작성법"
categories: programming
tags: git
---

# Commit Message

Staging Area에서 Commit 할 때 메세지를 작성한다. 이 메세지를 작성 할때는 하나의 원자적인 단위의 기능 or 독립적인 작업을 commit 해야 한다.

그렇게 하지 않으면 이력 추적과 추후 리팩토링에서 시간과 작업량이 늘어 날 수 있다.
보통은 아래와 같은 형식으로 작성이 된다.

```command
<type>(<scope>): <subject>
<BLANK LINE> // 빈 줄
<body>
<BLANK LINE> // 빈 줄
<footer>
```

- **Type** : 어떤 종류의 작업을 했는지 나타낸다.

| **type** | **의미**                        |
| -------- | ------------------------------- |
| feat     | 새로운 기능                     |
| fix      | 버그 수정                       |
| docs     | 문서 수정                       |
| style    | 코드 포맷 변경 (로직 변화 없음) |
| refactor | 리팩토링                        |
| test     | 테스트 코드                     |
| chore    | 빌드, 설정, 패키지 관리         |
| perf     | 성능 개선                       |

- **Scope** : 어떤 영역을 수정하였는지 나타낸다. (선택 사항이라 생략을 많이 한다.)
  - `feat(auth): JWT 로그인 구현`
  - `fix(api): 주문 조회 오류 수정`
- **Subject** : 50자 이내 권장, 마침표 X, 명령형 사용
  - `feat: add login API` (O)
  - `feat: added login API` (X, 과거형)
- **Body** : 왜 변경하였는지, 문제상황, 해결 방법, 설계 이유 등
- **Footer** : 추가적인 메타 정보, 이슈 번호, Breaking change, co-author
  - `Closes #42`
  - `BREAKING CHANGE: remove session authentication`

## 예시

```
feat(auth): JWT 기반 로그인 구현

세션 인증 방식에서 JWT로 전환한다.
서버 확장 시 세션 공유 문제를 해결하기 위함.

Closes #42
```
