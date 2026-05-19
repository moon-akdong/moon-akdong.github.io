---
layout: post
title: "2. Git Commit"
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

# reset, revert, checkout

Repository안에 있는 commit들을 이동하거나 수정할 수 있는 명령어들

1. git reset

- 특정 commit 으로 되돌리는 명령어, HEAD가 가리키는 위치를 강제로 이동 시키며, 특정 commit 이후의 히스토리는 사라진다.

2. git revert

- 특정 commit의 변경 내용을 되돌리는 새로운 commit 을 생성하는 명령어, 특정 commit의 내용을 취소하는 commit을 새로 만든다.

3. git checkout

- 특정 브랜치, commit 또는 파일의 상태로 작업 디렉토리를 변경하는 명령어, 히스토리를 변경하지 않고 단순히 그 시점의 상태를 보거나 이동하는데 사용

| 명령어     | 히스토리 변경        | 주요 용도                              |
| ---------- | -------------------- | -------------------------------------- |
| `reset`    | O (이후 commit 제거) | 로컬에서 commit을 되돌리거나 합칠 때   |
| `revert`   | X (새 commit 추가)   | push된 commit을 안전하게 취소할 때     |
| `checkout` | X (상태만 전환)      | 특정 시점 확인, 브랜치 이동, 파일 복원 |

## git reset

git reset은 이전 commit 으로 돌아가는 명령어다.
git reset에는 3가지 옵션이 존재한다.

```
git reset --hard [해시 또는 HEAD]
git reset --mixed [해시 또는 HEAD] (default)
git reset --soft [해시 또는 HEAD]
```

- `--hard` : 해시 Commit 으로 돌아간다. Staging , Working 초기화
- `--soft` : 해시 Commit으로 돌아가되, 이전 변경 사항은 Staging Area에 남겨둠
- `--mixed` : 해시 Commit으로 돌아가되, 이전 변경 사항은 Working Directory에 남겨둠

### 옵션들 예시

```
HEAD/main -> c3
Repository: c1 -> c2 -> c3
```

- 최신 commit 인 c3가 HEAD인 상태

#### hard

`git reset --hard c1` 을 한다면,  
staging , working directory가 c1의 상태가 된다. c2, c3 의 내용이 다 사라지는 결과가 발생한다. (협업 시 주의가 필요)

#### Soft

`git reset --soft c1` 를 한다면 c2,c3의 작업들(변화들)이 Staging 영역으로 이동되어 진다.

```
Repository: c1   (브랜치 기준으로는 여기)
Staging:    c3 상태
Working:    c3 상태
```

- 이런 상태가 된다.

#### Mixed (Default)

`git reset --mixed c1` 을 한다면 c2,c3의 작업들이 Working Directory 영역으로 이동되어 진다.

- `--mixed`는 default이기 때문에, 생략해도 된다.

```
Repository: c1   (브랜치 기준으로는 여기)
Staging:    c1 상태 (비워진 상태)
Working:    c3 상태
```

## git revert

이전 commit 에서 수정할 것이 있다면, 이 명령어를 사용하면 된다.  
commit을 최소 기능 단위로 작성해야 하는 이유다.최소 기능 단위로 commit 을 작성한다면,  
협업시에도 안정적으로 git revert를 사용하여, 코드 관리하기가 쉽다.

```
git revert [해시 값]

git revert --no-commit [해시 값]
```

- `--no-commit` : 커밋은 하지 않고 revert작업만 진행 된다. 다른 추가 수정이 가능

### 사용 예시

```
c1-> c2 -> c3

git revert c1

c1 -> c2 -> c3 -> c1.revert
```

`revert.c1` 처럼 새로운 커밋이 생긴다. 새로운 커밋이 생기면서, c1에서 했던 작업들의 정 반대의 작업들이 진행이 된다.

- 생성 했던 작업들은 삭제되고, 삭제 된 작업들은 다시 생성 된다.

물론 작업 중에 충돌이 발생할 수 있다.

### git revert 충돌

이후 commit 과 연결된 코드라던지, revert시 현재 코드와 에러가 발생한다 든지, 여러 경우 충돌이 발생할 수 있다.

이런 충돌들을 해결하는 방법이다.

1. 충돌 회피(임시)

```
 git revert --abort
```

- 충돌이 발생 되었고, 그 양이 많다면 우선 revert을 피하고 수정을 해야 한다면 위 명령을 사용하여 revert를 하기 전으로 돌아갈 수 있다.

2. 충돌 해결
   충돌이 발생하였다면, IDE에서 지원을 하는 경우가 있다.
   충돌 파일을 하나씩 보면서 수정을 할 수 있게 해준다.
   수정을 하나씩 수정해 나아가면서, 아래 명령어를 실행하면 된다.

```
git revert --continue
```

- 충돌이 발생되는 부분이 모두 해결 되었다면, revert.c1 이 생기고 종료 된다.

## checkout

checkout은 위의 두개와 달리 상태를 그대로 가져온다. 즉, 히스토리를 변경하지 ㅇ낳고 단순히 그 시점의 상태를 보거나 이동하는데 사용된다

또한 branch 이동 , 특정 파일만 되돌려서 확인이 가능하다.

```
git checkout [해시값 or branch]

git checkout [해시값] --[파일 경로]

```

이렇게 이동한 경우 deteched HEAD 상태가 된다. 이 상태에서 코드 확인 및 테스트는 가능하지만 이 상태에서 commit을 쌓으면 branch에 연결되지 않아 참조하기 어려워 진다.

그렇기 때문에 보통은 작업할 것이 있다면, 새 branch를 만들어 작업 한다.

```
git checkout -b [new branch name]
```

# 이전 Commit 명 수정, 삭제, 병합 (rebase)

# git reset 되돌리기 (reflog)
