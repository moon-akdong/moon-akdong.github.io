---
layout: post
title: "3. Git Branch"
categories: programming
tags: git
---

# Branch란?

서비스를 운영하다 보면, 새로운 기능 개발이나 긴급 버그 수정 등 다양한 코드 작업이 필요하다.
이런 작업을 운영 중인 main Branch에 직접 하면 서비스에 문제가 생길 수 있다.

그래서 main Branch에 직접 작업하지 않고, Branch를 따로 생성하여 작업한 뒤 안정적으로 코드를 합치기 위해 사용한다.

## Branch 생성(branch, switch)

```git
git brance [이름]

git branch sub_branch
```

기본적으로 branch를 생성하는 방법은 위의 명령어로 생성할 수 있다.  
생성 후 해당 branch로 이동하여 작업을 시작해야 하는데,  
branch 이동은 아래 명령어로 이동이 가능하다.

```git
git switch [이름]

git switch sub_branch
```

위 두개의 명령어의 기능을 한번에 하는 명령어도 있다.  
생성하면서, branch로 이동하는 명령어다.

```git
git switch -c [이름]

git switch -c sub_branch
```

## Branch 조회

branch 이미 있는지 또는 몇개가 있는지 확인하는 명령어도 있다.

```
git branch
```

위 명령어를 사용하면, 로컬에 있는 branch를 조회 할 수 있다.

## Branch 삭제

Branch는 앞에서도 언급을 했지만 작업 공간을 다로 마련해서 작업을 하고, main에 안정적으로 합치기 위한 작업 공간이다.
따라서, branch에 작업을 한 내용이 있다면 main에 merge(병합)을 하지 않고는 삭제가 되지 않는다.

병합을 하였거나, 작업 내용이 없다면 삭제가 가능하다.

```
git branch -d [이름]

git branch -d sub_branch
```

하지만 강제로 삭제할 수 있는 명령어도 있긴 하다.

```
git branch -D [이름]

git branch -D sub_branch
```

## git branch 추가 옵션

branch 이름 변경

```
git branch -m [현재 이름] [변경할 이름]

git branch -m sub_branch sub_branch2
```

# Branch 병합(merge, rebase)

Branch를 main에 합치는 것을 병합이라고 한다.
병합 기능에는 두가지로 나눌 수 있다.

1. merge : 히스토리를 합친다. Branch History가 잔가지 처럼 남는다.
2. rebase : 히스토리를 재정렬한다. Branch History가 없다.

## merge (3-way merge)

- 장점
  - 협업시 안전함
  - 히스토리 보존
  - 협업 충돌 적음
- 단점
  - 히스토리가 너무 많아지면 지저분 해질 수 있음

```
git merge [이름] # main Branch 환경에서

(main)git merge sub_branch
```

### 이해를 돕기 위한 예제 : merge 사용법

1. branch 생성(sub_branch) 및 이동
   - `git switch -c sub_branch`
2. 코드 작업
3. merge를 위해 운영중인 서비스 branch로 이동
   - `git switch main`
4. merge
   - `git merge sub_branch`

## rebase(fast-forward)

- 장점
  - 히스토리가 깔끔(linear history)
- 단점
  - 히스토리 재작성 -> 협업에서 위험

```
git rebase main  # branch 내에서

(sub_branch)git rebase main
```

### 이해를 돕기 위한 예제 : rebase 사용법

1. branch 생성(sub_branch) 및 이동
   - `git switch -c sub_branch`
2. 코드 작업
3. rebase
   - `git rebase main`
4. main을 rebase의 위치에 HEAD 이동
   - `git switch main`
   - `git merge sub_branch`
   - main, sub_branch 가 최신 commit 위치로 이동 됨

## 병합 충돌

Branch 작업을 한 후 merge든 rebase든 충돌이 발생할 수 있다.  
[commit-revert 충돌](/programming/2025/05/20/git-commit/#git-revert-충돌)에서처럼 해결책은 두 가지가 있다.

1. 우선 회피 후 오류 수정
2. 충돌 난 부분을 보면서 수정

우선 회피의 경우 merge, rebase 옵션이 동일하다.

```
git merge --abort

git rebase --abort
```

### merge 충돌

충돌이 발생한 부분이 적어 `--abort`를 사용하지 않고, 당장 수정을 하고 merge를 완료하려면

1. 충돌이 발생한 파일로 들어간다.
   - IDE에서 제공해 주는 기능을 사용하면 편하다.
2. 충돌이 발생한 부분에서 어떤 branch의 코드를 선택할지 나온다.
   - HEAD 선택하면 현재 Branch(main)의 내용을 선택
   - InComing Change 선택하면 merge 하려는 branch의 내용이다.
   - 다 지우고, 새로운 내요을 입력 해도 된다.
3. 해결이 되었다면,`git add`를 해준 뒤 `git commit`을 해준다.
4. 3번 과정을 끝내면 merge된 commit 하나가 생긴다.

### rebase 충돌

rebase는 merge와는 조금 다르다.
rebase는 커밋 단위로 하나씩 적용하기 때문에, 커밋마다 충돌이 발생할 수 있다.

1. 충돌이 발생한 파일로 들어간다.
   - IDE에서 제공해 주는 기능을 사용하면 편하다.
2. 충돌이 발생한 부분에서 어떤 branch의 코드를 선택할지 나온다.
   - HEAD 선택하면 현재 Branch(main)의 내용을 선택
   - InComing Change 선택하면 merge 하려는 branch의 내용이다.
   - 다 지우고, 새로운 내요을 입력 해도 된다.
3. 여기서 달라지데, 2번에서 어떻게 선택하는지에 따라 commit이 달라질 수는 있다.
4. 충돌이 발생한 부분을 수정하였다면 `git add` 후 `git rebase --continue`을 실행하면된다.

### merge와 rebase 충돌 차이점

| **구분**  | **merge**        | **rebase**            |
| --------- | ---------------- | --------------------- |
| 비교 대상 | branch vs branch | main vs branch commit |
| incoming  | sub_branch 전체  | sub_branch의 커밋 1개 |
| 충돌 횟수 | 1번              | 여러 번               |

# 협업을 위한 브랜치 전략

Branch를 어떻게 사용하는지에 대한 전략에는 아래 세가지가 있다.

1. Git Flow
2. Github Flow
3. Trunk-based Development(TBD)

## Git Flow

명확한 branch구조를 가진 전통적인 전략, 예전에는 많이 사용됨

- 릴리즈 주기가 길고 대규모 프로젝트에 적합
- 안정적인 버전 관리가 가능하나 브랜치 관리가 복잡함

| **브랜치 이름** | **성격** | **주요 역할**                                                    |
| --------------- | -------- | ---------------------------------------------------------------- |
| **main**        | 메인     | 제품 출시 버전 관리, 항상 배포 가능한 상태                       |
| **develop**     | 메인     | 다음 출시 버전을 위한 개발 통합 브랜치                           |
| **feature**     | 임시     | 개별 기능 구현 (develop에서 생성 후 다시 병합)                   |
| **release**     | 임시     | 배포 준비 및 버그 수정 (develop에서 생성 후 master/develop 병합) |
| **hotfix**      | 임시     | 출시 버전(master)에서 발생한 긴급 버그 수정                      |

![git flow](/assets/post/github/gitflow.png)\_
_출처: [medium.com/@yanminthwin](https://medium.com/@yanminthwin/understanding-github-flow-and-git-flow-957bc6e12220)_

## Github flow

Main branch 하나를 중심으로 운영되는 단순한 구조

- 상시 배포(CD) 체계가 갖춰진 웹 서비스에 최적화
- Pull Request를 통한 코드 리뷰 및 피드백 중심
- PR에서 자동화가 연결되어 바로 소프트웨어가 배포 된다.

  ![alt text](/assets/post/github/githubflow.png)
  _출처: [medium.com/@yanminthwin](https://medium.com/@yanminthwin/understanding-github-flow-and-git-flow-957bc6e12220)_

## Trunk-based Development(TBD)

모든 개발자가 하나의 main branch에 매일 자주 병합

- 코드 병합 시 발생하는 충돌 최소화 및 개발 속도 극대화
- 높은 수준의 자동화 테스트와 기능 토글(Feature Flag)활용 필수

| **전략명**      | **관리 브랜치 종류**                      | **핵심 가치**         |
| --------------- | ----------------------------------------- | --------------------- |
| **Git Flow**    | master, develop, feature, release, hotfix | 엄격한 관리 및 안정성 |
| **GitHub Flow** | main, topic branches (feature)            | 단순함과 빠른 배포    |
| **TBD**         | main (Trunk)                              | 지속적 통합과 속도    |
