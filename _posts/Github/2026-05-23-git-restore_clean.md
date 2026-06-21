---
layout: post
title: "5. Git restore, clean, reset"
categories: programming
tags: git
---

# 변경 사항 폐기

## git restore

### Working Directory

Git Tracked 파일 중 선택된 파일을 마지막 최신 커밋 시점의 내용으로 복원된다.  
즉, 수정 내용은 사라지고 파일은 삭제 되지 않고 최신 커밋 시점의 내용이 된다.

이렇게 **사라진 수정 내용은 복구할 수 없다.**

```
git restore [파일 명]

git restore test.txt
git restore . : 현재 디렉토리의 모든 Tracked 파일
```

### Staging Area

Staging Area(git add)에 있는 파일을 Working Directory로 이동시킨다.
Working Directory에 파일은 그대로 존재한다.

```
git restore --staged [파일명]

git restore --staged test.txt
git restore --staged . # 모든 파일
```

### 특정 Commit 상태로 되돌리기

```
git restore --source=[커밋해시] [파일명]
```

## git clean

Untracked 파일을 삭제하는 명령어

```
git clean -fd # 디렉토리까지 포함해서 삭제
```

- `-n` : 미리 삭제될 파일들 보여주기
- `-i` : 인터렉티브 모드 시작
- `-d` : 폴더 포함
- `-f` : 강제로 바로 삭제
- `-x` : .gitignore에 등록된 파일들도 삭제

git restore 과 마찬가지로 삭제하면 **복구 할 수 없다.**

## git restore vs. clean 비교

|      | git restore                             | git clean                       |
| ---- | --------------------------------------- | ------------------------------- |
| 대상 | Tracked 파일                            | Untracked 파일                  |
| 동작 | 수정 내용을 되돌림                      | 파일 자체를 삭제                |
| 예시 | 기존 파일을 수정했는데 되돌리고 싶을 때 | 새로 만든 파일을 지우고 싶을 때 |

## git reset

이전에 했던 git reset도 동일한 기능을 할 수 있다.
[Git reset](/programming/2025/05/20/git-commit/#git-reset)

git reset에서도 `git restore --staged [파일]` 와 같은 Staging Area에서 working directory로 내리는 명령어가 있었다.
`git reset HEAD [파일]`

restore는 원래 reset과 checkout에 섞여 있던 파일 복원 기능을 분리해서 만든 명령어이기 때문이다.
유사항 명령어들이 있다.

### 차이점

git reset은 커밋 자체를 이동할 수 있다. (git restore은 안됨 )

```
git reset --soft HEAD~1    # 커밋만 취소, 파일은 Staging에 유지
git reset --mixed HEAD~1   # 커밋 취소 + Staging에서 내림
git reset --hard HEAD~1    # 커밋 취소 + 파일 수정 내용도 삭제
```

단 이동 한다면 이동 된 이후 commit 은 삭제 될 수 있다.

git restore는 특정 파일만 골라서 되돌리기에 더 적합하다.

```
git restore --source=abc123 file.txt
```
