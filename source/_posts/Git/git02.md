---
title: Git - branch, stash 
date: 2019-04-19 19:04:32
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
### Git branch
- branch는 가지라는 뜻으로 작업을 분기해서 처리하는 경우에 대한 시적인 표현입니다.

#### git branch
- git branch
    - 브랜치 조회

- git branch "생성할 브랜치 이름"
    - 브랜치가 생성된다.
    
- git branch -d <브랜치이름\>
    - 브랜치 삭제 (병합된것만 가능하다.)
    
- git branch -D <브랜치이름\>
    - 병합되지 않은 브랜치 강제 삭제
    ![git](/images/git/git02-3.png)

#### git checkout
- git checkout <전환하려는 브랜치 이름\>
    - 브랜치를 전환 
    - 커밋로그 입력 시, 해당 커밋으로 체크아웃된다.

- git checkout -b <생성하고 전환할 브랜치 이름\>
    - 브랜치를 생성하고 전환까지 할 때
    ![git](/images/git/git02-2.png)
    

#### 브랜치 정보확인
- git log : 현재 브랜치 로그 정보 조회
- git log "브랜치명1".."브랜치명2" : 브랜치 로그 비교
- git log \--branches : 모든 브렌치의 commit 정보를보여준다. 
- git log \--branches \--decorate \--graph : 모든 브렌치의 commit 관계를 그래프형태로 보여준다.
    ![git](/images/git/git02-1.png)
- git log \--branches \--decorate \--graph \--oneline:  모든 브렌치의 commit 관계를 한줄로 관계를 보여준다.

#### merge(병합)
- A 브랜치로 B 브랜치를 병합할 때 (A ← B)
    - git checkout A
    - git merge B
    - 반드시 A상태에서 B를 merge 해야한다.

####  fast forward와 merge commit
- fast forward
    - 빨리감기라는 뜻이다.
    - 어떠한 새로운 커밋도 발생하지 않은 상태로 다른 브랜치의 커밋과 병합을 하게되면 가르키는 커밋의 방향만 바꿔준다.
    - 기존의 브랜치를 지운다.
    - 별도의 커밋을 생성하지 않는다.

- merge commit
    - 두 개의 브랜치가 합쳐졌다는 정보를 가진 새로운 커밋이 발생한다.
    - 합쳐진 두 브랜치의 정보가 담겨있다.

#### git stash
- git stash
    - 현재 작업내용을 스테이징 파일에 올리지않고 stash라는 새로운 공간에 저장한다.
    - git status 확인 시, 변경내용 없음으로 표시된다. (변경사항들이 stash에 저장됨)
    ![git](/images/git/git02-4.png)
    
- git stash list
    - 저장된 stash 조회
- git stash apply
    - 최근에 저장됬던 stash를 적용한다.
    - 변경사항이 적용된다.
    - stash는 삭제되지 않는다. (최근 stash가 남아있음)
- git stash drop
    - 최근의 stash를 삭제한다. 
    
- git stash apply; git stash drop;
    - 동시 수행된다.
    - ** git stash pop**으로 대체할 수 있다. (같은역할)
    ![git](/images/git/git02-5.png)
    
- **stash는 버전관리를 하고있는 파일에 한해서 동작한다.**(즉, untracked파일은 적용 되지않는다.)

---
다음 git 블로깅은 github 원격저장소와 관련된 내용 및 ssh 설정 방법 .gitingore 설정까지 할 예정입니다. 

<br><br>

 