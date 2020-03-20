---
title: Git - status, init, commit, config, log, reset, revert
date: 2019-04-19 16:15:26
tags: Git
toc: true
widgets:
  - type: toc
    position: right
  - type: tagcloud
    position: right
sidebar:
  right:
    sticky: true
---
# Git 
![gitlogo](/images/git_logo.png)
<!-- more -->
개발공부를 시작하면서.. git에대한 이야기를 참 많이 들었습니다.
커밋, 푸쉬, 풀등... 알 수 없는 이야기들을 하고 나중에 그것이 git과 관련된 명령어였다는 사실을 알게되었습니다.
회사에 일을 시작하기전에 알아야 할 필수적인 내용이라고 듣게되어 좀 자세히 알아보려고 합니다.
git을 사용하는 방법에는 gui방식의 툴(소스트리, 크라켄)을 사용하는 방법도 있지만 보다 본질적으로 이해하고 싶어 명령어에 대해 공부하게 되었습니다.

### Git 명령어01
#### Git
- 컴퓨터 파일의 변경사항을 추적하고 여러 명의 사용자들 간에 해당 파일들의 작업을 조율하기 위한 분산 버전 관리 시스템(DVCS)이다.

#### git \--version
- git 버전확인

#### git config
- 환경설정
- git config \--global \--list
    - 현재 설정정보를 조회
    - global 명령어는 전역설정에 대한 옵션
- git config \--global user.name "사용자명"
    - 필수
    - 원격 저장소 같도록하는것이 좋다. 
- git config \--global user.email "이메일주소"
    - 필수 
    - 원격 저장소 같도록하는것이 좋다.
    

#### git init
![git](/images/git/git01-1.png)
- 현재 디렉토리에 git 저장소를 생성한다.
- git init 명령어를 사용하면 현재 디렉토리에 .git이라는 디렉토리가 생성되며 버전관리를 시작할 수 있습니다.
    - .git 폴더에는 버전에 대한 정보를 가지고 있다. (삭제하면 안됩니다.)

#### git status
- git의 저장소는 3단계로 이루어져 있다.
    - 작업 디렉토리 (unstaging)
    - 인덱스 (staging)
    - HEAD (최종 확정본 commit)

>HEAD는 현재 브랜치의 가장 최신커밋을 의미한다.

- git status
    - 커밋되지 않은 변경사항을 조회합니다.
    ![git](/images/git/git01-3.png)

#### git add
- 스테이징 영역에 올라간 파일만이 commit 즉, 버전등록이 가능하다.
    ![git](/images/git/git01-2.png)

- git add <파일이름>
    ![git](/images/git/git01-4.png)
    - untracked file(버전관리를 하지않는 파일)을 버전관리 시작해주며 스테이징 영역으로 올려준다. 
    - 버전관리를 하고 있으나 스테이징영역에 올라기지 않은 파일을 스테이징 영역으로 올려준다. 
- git add .
    - untracked 및 unstaged 파일을 모두 add 한다.
    ![git](/images/git/git01-5.png)
    
- git add -i
    - 대화식으로 추가 가능하다.
    ![git](/images/git/git01-12.png)
     
#### git commit
- 버전을 만드는 작업
- git commit
    ![git](/images/git/git01-6.png)
    - 버전이름을 정의하여 현재 staging된 파일들을 버전으로 등록한다.
    ![git](/images/git/git01-7.png)
        - i 입력 후 메세지 입력
        - 메세지 입력 완료후 esc 
        - :wq 로 쓰고 나가기(write, quit)

- git commit -m "<커밋메세지>"
    - 커밋메세지와 함께 명령할 수 있다.

- git commit -C HEAD -a \--amend
    - 최근 커밋을 덮어쓴다.
        - HEAD를 commit 로그로 변경하면 해당 commit의 메세지로 커밋이 덮어써진다.
    - -c 사용 시, 커멧메세지를 변경 할 수 있다.
    
#### git diff
- 사전 조건(test03.txt)
    ![git](/images/git/git01-8.png)

- git diff
    - 스테이징 영역과 작업 디렉토리의 차이정을 알려준다.
    ![git](/images/git/git01-9.png)
- git diff -cached
    - 스테이징 영역과 저장소
    ![git](/images/git/git01-10.png)
- git diff HEAD
    - 작업디렉토리, 스테이징영역, 저장소 모두 볼 수 있다.
    ![git](/images/git/git01-11.png)

#### git log
- git log
    -  커밋로그 조회
    - -1, -2 등을 붙여 조회로그 갯수를 지정할 수 있다.
    - -p 옵션을 주면 변경사항도 같이 조회된다.
    - \--decorate \--graph  옵션을 주면 브랜치 트리를 볼 수 있다.
- git blame
    - 갈 줄 앞에 커밋명과 커밋한 사람등의 정보를 볼 수 있습니다.

### reset 과 revert
- git reset 
    - 과거 버전으로 돌아가기
    - \--hard : 돌아가려는 이력이후의 모든 내용을 지운다.
    - \--soft : 돌아가려 했던 이력으로 되돌아 갔지만, 이후의 내용이 지워지지 않고, 해당 내용의 인덱스(또는 스테이지)도 그대로 
    - \--mixed : 역시 이력은 되돌려집니다. 이후에 변경된 내용에 대해서는 남아있지만, 인덱스는 초기화 됩니다.
     
- git revert
    - 기존의 커밋에서 변경한 내용을 취소해서 새로운 커밋을 만듭니다. 
    
- 차이점 : https://www.popit.kr/%EA%B0%9C%EB%B0%9C%EB%B0%94%EB%B3%B4%EB%93%A4-git-back-to-the-future/

---
다음 블로깅에서는 branch와 관련된 내용에 관하여 정리하겠습니다. 
<br><br>
    



    



    


 
