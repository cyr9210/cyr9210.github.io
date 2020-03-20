---
title: Git - 원격저장소(Github), gitignore설정, ssh설정
date: 2019-04-19 23:03:39
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
### Github
- github은 git이라는 버전관리시스템에 저장소를 빌려주는 호스팅 사이트이다.
- 호스팅 사이트로는 github외에도 GitLab, BitBucket등이 있다.

#### 원격저장소
- git clone <주소(https/ssh)>
    - 현재 디렉토리(local)에 github의 소스코드를 가져온다.

- git fetch
    - 원격저장소의 변경사항을 가져와서 원격 브랜치를 갱신한다.

- git pull
    - fetch와 master에 자동으로 merge가된다.
    
- git push
    -  로컬의 변경내용을 원격저장소에 업데이트한다.
    - tag를 달았을 경우, 기본적으로 푸싱하지 않기 때문에 아래와 같이 사용해야한다. 
        - git push origin <태그명>
        - git push origin --tags

- git remote
    - 원격저장소 목록 조회

- git remote add <이름> 저장주소
    - 지역저장소(local)에 원격 저장소를 추가한다.(연결)
    - 기본적으로 origin이라는 이름을 대부분 사용한다.

### gitignore
- git에서 버전관리하지 않는것을 원할 때, .gitignore로 설정 할 수 있다.
- gitignore파일 생성
    - touch  \.gitignore

- gitignore 설정
    - \.gitignore파일안에 해야할 설정을 모를 때, 아래 사이트를 통해서 쉽게 설정 할 수 있다.
    - http://www.gitignore.io
        - 사용하는 언어, IDE등을 입력한다.
        - 만들어준 내용을 .gitignore파일에 입력한다.
        
- 이미 github 파일을 올렸을 경우, 아래와같은 설정을 해줘야한다.
    - 전체 캐쉬 내용 삭제
        - git rm -r \--cached \.
    - 다시 전체 내용 업로드
        - git add .
        - git commit -m "커밋메세지"
        - git push origin <branch\>

### SecureShell
- 터미널에서 ssh 를 생성한다.
    - ssh-keygen
        - 다른 컴퓨터로 접속할 수 있는 비밀번호가 생긴다.
        - 기계적으로 굉장히 복잡한 비밀번호로 뚫을 수 없다.
        - ~/.ssh 디렉토리에 생성된다.(숨김)
            - id_rsa
                - private key
                
            - id_rsa.pub
                - public key
                - 접속하고자 하는 컴퓨터에 public key를 옮겨주어야한다.
- github 사이트에서 settings → SSH Keys → New SSH Key → id_rsa.pub 안의 내용을 넣어준다. 
- ssh 로 접속할 수 있게된다.
    - 처음 접속시 ssh로 접속하겠냐는 질문에대해 yes를 입력해주어야한다.

- github이 아니고 다른 컴퓨터에 접속할때도 사용할 수 있다.
    - 그때는 원격으로 비밀번호로 접속후에 .ssh디렉토리에 위치한다.
    - id_rsa.pub를 복사한다.
        - scp <id_rsa.pub위치\> id@<접속주소\>:복사위치
        ex)  scp $HOME/.ssh/id_rsa.pub egoing@egoing.net:id_rsa.pub
    - id_rsa.pub 키를 authorized_keys에 등록한다.
        - cat 위치/id_rsa_pub >> 위치/authorized_keys
        ex)  cat $HOME/id_rsa.pub >> $HOME/.ssh/authorized_keys
            - 여기서 >> 는 뒤에 붙여넣는것이다.(append)
            - \>는 덮어쓰기가된다.
    - ssh 접속하기
        - ssh <주소\>
        ex) ssh egoing.net
        -  id_rsa 파일을 $HOME/.ssh/id_rsa에 만들지 않고 다른 디렉토리에 만들었다면 -i 옵션을 사용
            - ssh -i <파일위치\> <주소\>
            ex)  ssh -i $HOME/auth egoing.net
    - debug모드로 접속하기 위해서는 -v옵션을 줄수있다. (좀 더 자세한 내용을 원할때는 -vv, -vvv까지 가능 )
    ex)  ssh -v egoing.net
    
- 참고 강좌 : https://opentutorials.org/module/432/3742

