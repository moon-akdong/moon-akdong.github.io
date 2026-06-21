---
layout: post
title: "0. Git Setting"
categories: programming
tags: git
---

# Git Settings

## local 설정

기본적으로 github(원격저장소)에서 repository를 만들면, 아래와 같은 명령어를 작성하라고 알려준다.

```
echo "# test" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/moon-akdong/test.git
git push -u origin main
```

### init

로컬 디렉토리에서 git 저장소를 새로 시작할 때 사용하는 명령어다.
아래 명령어를 실행하면, 작업 폴더로 이동한 후 아래 명령어를 실행하면, 해당 폴더가 git이 관리하는 저장소가 된다.

```
git init
```

### branch

예전에는 기본 브랜치가 master 였지만, 해당 간어가 인종차별적 문제가 발생하면서, main을 기본 브랜치 명으로 사용한다.

그렇기 때문에, branch를 변경해주어야 하기 때문에, 아래 명령어를 사용한다.

```
git branch -M main
```

매 로컬 디렉토리에서 입력하기 힘들다면 아래 글을 참고하면 된다.

[global branch 설정](#default-branch-변경)

### remote

로컬 저장소와 원격 저장소(github)을 연결할 때 사용한다.
로컬에서만 작업하면 내 컴퓨터에만 이력이 남지만, remote를 연결해야 다른 사람과 협업할 수 있다.

#### 원격 저장소 확인

```
git remote -v
```

현재 로컬 저장소에 연결된 원격 저장소의 이름과 URL을 보여준다.
연결이 잘 되었는지 확인할 때 쓴다.

#### 원격 저장소 연결

```
git remote add [원격 저장소 별칭] [원격 저장소 URL]

git remote add origin https://github.com/username/repository.git
```

- `origin`은 원격 저장소 별칭으로 관례적으로 사용하는 이름

#### 원격 저장소 변경

```
git remote set-url [원격 저장소 별칭] [변경할 URL]

git remote set-url origin https://github.com/username/new-repository.git
```

#### 원격 저장소 해제

```
git remote remove [원격 저장소 별칭]

git remote remove origin
```

## global(전역) 설정

### 조회

- 우선 global 설정을 볼 수 있는 명령어

```
git config --global --list

# 또는

git config --global -l
```

- vi로 실행되기 때문에, **q를 눌러 종료**

### 이름, 아이디 설정

- 대략적으로 코드 작업 기록과 github 연결을 위한 설정인 줄 알았다.
- **github 연결을 위한 설정은 아니고**, 이 설정을 하는 이유는 코드 작업(커밋)에 작성자 정보를 기록하기 위한 설정이다.

```
git config --global user.name [본인 이름]
git config --global user.email [본인 이메일]

git config --global user.name Jaeyun_Moon
git config --global user.email problemy0913@gmail.com
```

### Windows / Linux,Max 줄바꿈 오류 방지

다른 운영체제를 가진 컴퓨터에서 같은 원격저장소(github)에 작업할 때 발생했다.

리눅스,맥 과 Windows 에는 줄바꿈 문자의 차이 때문에 발생하는 에러  
협업 시 OS가 다른 사람들끼리 불필요한 줄바꿈 diff가 생기는 걸 방지하는 용도

- Windows: CRLF (\r\n2바이트)
- Mac/Linux: LF (\n1바이트)

```
# Mac/Linux
git config --global core.autocrlf input

# Windows
git config --global core.autocrlf true
```

| 설정              | 커밋 시    | 체크아웃 시 | 대상      |
| ----------------- | ---------- | ----------- | --------- |
| input (Mac/Linux) | CRLF -> LF | 변환 없음   | Mac/Linux |
| true (Windows)    | CRLF -> LF | LF -> CRLF  | Windows   |

### Default Branch 변경

위에서 보았듯 작업 폴더에서 git 을 시작할 때 `git init`후 `git branch -M main`을 실행 해 준다.

매번 입력하기 번거롭다면 아래와 같이 global 설정을 해주면, git init 할 때 자동으로 main이 기본 브랜치가 된다.

```
git config --global init.defaultBranch main
```

# 그 외 설정

## gitignore

보통 세팅값이나 보안적으로 이슈가 있는 파일/폴더들을 넣어 둔다.
github에서 기본적으로 세팅 되어 있는 값들을 제공하는 경우도 있다. AI를 활용해도 좋다.

### 1. 기본 파일 무시 (파일명 정확히 일치)

```
config.json    # 정확히 'config.json' 파일만 무시
README.txt     # 정확히 'README.txt' 파일만 무시
```

### 2. 확장자별 무시 (와일드카드 `*`)

```
*.log    # 모든 .log 파일 무시
*.tmp    # 모든 .tmp 파일 무시
```

### 3. 디렉토리 무시 (끝에 `/` 붙임)

```
node_modules/    # node_modules 디렉토리와 그 안의 모든 내용
build/           # build 디렉토리 전체
```

### 4. 특정 경로의 파일/폴더

```
src/config.json    # src 폴더 안의 config.json만 무시
```

### 5. 패턴 매칭 (와일드카드)

```
temp*           # temp로 시작하는 모든 파일/폴더
*temp*          # temp가 포함된 모든 파일/폴더
temp*.*         # temp로 시작하고 확장자가 있는 파일들
config*.json    # config로 시작하고 .json으로 끝나는 파일들
```

### 6. 중첩 디렉토리 패턴 (`**`)

```
**/logs/        # 모든 위치의 logs 디렉토리
logs/**         # logs 디렉토리 안의 모든 파일과 하위 디렉토리
src/**/temp/    # src 하위 어느 위치든 temp 디렉토리
```

### 7. 단일 문자 매칭 (`?`)

```
temp?.txt    # temp1.txt, tempA.txt 등 (한 글자만)
log?.log     # log1.log, logA.log 등
```

### 8. 문자 집합 매칭 (`[]`)

```
log[0-9].txt      # log0.txt ~ log9.txt
temp[abc].log     # tempa.log, tempb.log, tempc.log
file[!0-9].txt    # file 뒤에 숫자가 아닌 한 글자가 오는 파일
```

### 9. 예외 처리 (`!`)

```
*.log              # 모든 .log 파일 무시
!important.log     # 하지만 important.log는 추적
!logs/keep.log     # logs 폴더의 keep.log는 추적
```

### 10. 복합 예외도 가능하다.

```
build/              # build 디렉토리 전체 무시
!build/README.md    # 하지만 build/README.md는 추적
!build/docs/        # build/docs 디렉토리는 추적
build/docs/*.tmp    # 단, build/docs/*.tmp 파일들은 무시
```
