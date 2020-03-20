---
title: Git - merge, rebase
date: 2019-05-29 10:48:07
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
### merge
![merge](/images/git/git04-1.png)
- 브랜치를 병합한다.
- 3wayMerge를 통해 병렬로 병합한다.

#### fast forward
- 자동 병합이 가능한 상태 커밋까지 해준다.

#### conflict
- 자동병합이 안되고 충돌이 난 상태
- git status를 통해 충돌 파일을 확인하고 직접 수정해주고 커밋해주어야 한다.

#### 명령어
- 합쳐질 대상에서 명령어 입력
- git merge 대상
<br><br>

### rebase
![merge](/images/git/git04-2.png)
- 브랜치 병합방법중 하나이다.

#### merge와 차이점
- 장점
    - 병렬로 저장되는 merge와 다르게 일렬로 저장되어 history를 보다 정확하게 확인할 수 있다.
- 단점 
    - 어렵고 위험하다.
    
#### 주의사항
- 다른사람과 공유하지 않은 커밋에 대해서만 해야한다. (git pull 하고나서 하지 말 것.)

#### 명령어
- git rebase 대상
    - git rebase --abort : rebase 중단
    - git rebase --continue : 진행 대상이없으면 중단
    - git rebase --skip : 해당 커밋을 rebase 하지 않고 반영하지 않는다.
