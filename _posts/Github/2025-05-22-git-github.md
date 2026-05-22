---
layout: post
title: "4. Git Repository Hosting Service"
categories: programming
tags: git
---

# 원격 호스팅 서비스

원격 호스팅 서비스에는 github, gitlab, burket이 있다.

대표적으로 github을 가장 많이 사용된다.

원격호스팅은 Git을 협업에서 사용하게 하기 위한 도구, 개발하는 프로젝트가 온라인으로 저장될 공간을 제공한다.

- 클라우드는 저장하면 덮어씌워지며 이전 작업을 없애 버릴 수 있지만 git의 원격 호스팅의 경우 이전 작업을 포함한 모든 이력을 저장한다.
- 최신 commit을 현재 로컬 git에 저장하지 않으면 자신의 작업 내용을 github에 업로드 할 수 없다.

## 로컬 디렉토리에 원격 호스팅 설정

[원격 저장소 설정](/programming/2025/05/18/git-first-setting/#remote)
여기서 이미 다루었다.
git remote add 후 별칭을 붙히는데, 별칭은 `origin`이외에도 자기 맘대로 만들어서 여러 원격 저장소를 연결 할 수 있다.

## 로컬 디렉토리 레포지토리 원격 호스팅 업로드 (push)

- `origin`이란 별칭으로 github을 연결했다는 가정으로 진행한다.

- git에서 작업하였던 Repository들을 원격 main Branch에 push 하는 명령어

```
git push origin main

git push -u origin main
```

- `-u`옵션 : 현재 브랜치와 명시된 원격브랜치를 default로 묶어서 연결한다.

- `-u`를 한번이라도 했다면, `git push`만 해도 된다.

### 업로드 시 주의 사항

내가 먼저 작업을 했더라도, 원격 호스팅 main branch에 우선적으로 올라온 commit 이 있다면, push가 되지 않는다.

아래와 같은 오류가 발생한다.

```
! [rejected]        main -> main (fetch first)
  error: failed to push some refs to 'https://github.com/...'
  hint: Updates were rejected because the remote contains work that
  you do
  hint: not have locally. Integrate the remote changes (e.g.,
  hint: 'git pull ...') before pushing again.
```

그렇기 때문에 내가 먼저 작업을 했더라도 원격에 push 하기 전에는 원격의 변경사항이 있는지 확인해야 한다.

## 원격 통신 다운로드

원격 repository의 데이터를 가져오기 위해서는 아래 방법 들이 있다.

1. DownloadZIP
2. git clone : 처음 가져오기, 원격저장소 -> 로컬로 전체 복사 (프로젝트 시작할 때)
3. git fetch : 원격 변경사항을 로컬에 가져오지만, 내 코드에는 적용하지 않음
4. git pull : fetch + rebase 또는 +merge : 원격 변경 사항을 내 코드 브랜치에 바로 반영

### git clone

- 원격 main branch에서 처음 로컬에 가져오거나, 로컬로 전체 복사하기 위한 명령어

```
git clone [원격 주소]
```

### git fetch

- 원격 저장소의 최신 커밋을 로컬로 가져오기만 한다.
- 다른 branch나 main branch에는 영향이 전혀 없다.

```
git fetch origin # 특정 원격 저장소

git fetch --all # 모든 원격 저장소
```

- git pull을 신중하게 하는 방법
  - git fetch origin
  - git checkout origin/main : 원격의 다른 branch라도 상관없음
  - 데이터 확인
  - git switch main : 다시 로컬의 main branch로 이동
  - git merge origin/main 또는 git rebase origin/main 을 해주면 된다.

### git pull

- Fetch기능과 함께 merge or rebase를 하는 명령어
- default로는 merge 방식을 채택한다.
- 원격 변경사항을 로컬에서 가져오는 명령어

```
git pull origin main
```

- git push 에서 upstream(-u)옵션을 사용했다면 git pull만 해도 된다.

## git pull 충돌

원격의 branch들은 로컬의 다른 branch들의 병합과 유사하다.  
branch들의 병합에는 충돌이 발생할 수도 있다.

### Merge 방식

이번에만 merge방식으로 하기 위해서는 아래와 같은 명령어를 사용해야 한다.

```
git pull --no-rebase
```

- 이때 만약 pull 방식을 명시하라는 명령어가 뜬다면 아래 명령어를 사용하면 됨
- default 설정이 아래와 같다. (merge 방식으로 pull을 진행한다.)

```
git config --global pull.rebase false
```

- 이후에는 branch에서 merge 충돌 해결 방법과 동일하게 해결하면 된다.
  [Branch merge 충돌](/programming/2025/05/21/git-branch/#merge-충돌)

### Rebase 방식

- 이번에만 rebase방식으로 하기 위해서는 아래와 같은 명령어를 사용해야 한다.

```
git pull --rebase origin main
```

- 만약 rebase를 기본 pull 방식으로 채택하고 싶다면

```
git config --global pull.rebase true
```

[Branch Rebase 충돌](/programming/2025/05/21/git-branch/#rebase-충돌)

# 로컬 Branch와 원격 Branch

## 로컬 Branch에서 원격 Branch

1. 로컬 Branch 생성 후 작업
   - git switch -c from-local
2. git push 에러 발생
   - from-local이라는 로컬 Branch가 원격의 어떠한 Branch와도 연결이 되어 있지 않음
   - 로컬의 main은 원격의 main과 git push -u origin main 의 upstream으로 연결이 되어 있음
3. from-local의 branch 와 원격 Branch 연결
   - `git push --set-upstream origin from-local`
     - `--set-upstream` 은 `-u`와 동일한 옵션
   - 연결되었다는 안내문과 원격에도 동일한 이름의 `from-local`이라는 branch가 생김
4. 원격의 branch조회
   - `git branch --all` 또는 `git branch -a`
   - 로컬과 원격의 branch 전부 조회 가능
   - 단 fetch 이후에 생긴 원격의 branch는 조회 안됨

## 원격 Branch에서 로컬 Branch

1. 원격에서 `from-remote` branch 생성
2. 로컬에서 원격 remote branch 가져오기
   - `git switch -t origin/from-remote`

## 원격 Branch 삭제

- 로컬 Branch 삭제

```
git branch -d [branch name]

git branch -d from-remote
git branch -d from-local
```

- 원격 Branch 삭제

```
git push origin --delete [branch name]

git push origin --delete from-remote
git push origin --delete from-local
```
