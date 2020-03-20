---
title: iTerm2 설치 및 커스터마이징
date: 2019-03-05 15:02:58
tags: Etc
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


![item2](/images/etc/iTerm2.jpg)

생활코딩의 지옥에서 온 git 강의를 듣다 보면 이고잉님의 터미널이 뭔가 특별한것을 보실 수 있습니다.
현재 path 및 branch 등도 표현되며 무엇보다 뭔가 있어 보였습니다.😁<br>
구글링을 통해 알아보니, macOS에서 사용하며 mac용 터미널인 iterm2를 설치한 후 여러 설치과정들을 통해 멋진 터미널을 만들 수 있다는 정보를 알게되었고..  [참고블로그 : Beomi's Tech Blog](https://beomi.github.io/2017/07/07/Beautify-ZSH/)     
mac을 사용하고 있고, 앞으로 터미널 이용이 더 많아질 예정이기 때문에.. 제 mac 터미널에 적용하기로 결정하였습니다.<br>
편리하고 다양하게 터미널을 사용하기위해 기본 Shell인 bash 대신에 zsh를 사용하고, 보다 깔끔하고 멋진 테마를 적용하는 방법에 대해 알아봅시다.
<!-- more -->
# iterm 설치 및 꾸미기

### iterm2 설치
![iTerm](/images/etc/iTerm01.png)
- [iTerm2 홈페이지](https://www.iterm2.com/downloads.html)에서 iTerm2를 설치한다.

![iTerm](/images/etc/iTerm02.png)
- 접근권한 설정이 필요한 경우 설정을 해준다.
<br><br>

### HomeBrew 설치하기
> HomeBrew란 우분투의 APT와 비슷하게 프로그램 패키지를 관리해 주는 프로그램.
brew라는 명령어로 패키지를 관리할 수 있다.

![iTerm](/images/etc/iTerm03.png)
![iTerm](/images/etc/iTerm04.png)
- [HomeBrew 홈페이지](https://brew.sh/) 이동 
- 해당 명령어 업력
<br><br>

### Zsh 설치하기
zsh은 bash에 추가적인 명령어를 추가하고 편의성을 개선한 새로운 쉘이다.   
한가지 예로 git 폴더 상태를 관리하고 터미널에 상태를 나타내준다.    
zsh는 위에서 설치한 HomeBrew를 통해 설치할 수 있다.
```
brew install zsh
```
- 상기 명령어 입력

```
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

```
- oh-my-zsh 설치하기 위해 상기 명령어를 입력

> oh-my-zsh는 zsh를 편리하게 사용하도록 도와주는 zsh플러그인이라고 보면된다.

### Oceanic Next iTerm 색 테마 입히기
![iTerm](/images/etc/iTerm05.png)
- [Oceanic Next iTerm](https://github.com/mhartington/oceanic-next-iterm) github 레퍼지토리 이동 후 zip파일 다운

![iTerm](/images/etc/iTerm06.png)
- master.zip 파일의 압축을 푼다.(상기 파일목록을 확인한다.)

![iTerm](/images/etc/iTerm07.png)
- iterm → preference

![iTerm](/images/etc/iTerm08.png)
- profiles → Default → Color presets... → import 상기파일 추가 후 적용
- iTerm2 재시작 시, 칼라적용
<br><br>

### Agnoster 테마 설치하기
![iTerm](/images/etc/iTerm09.png)
- .zshrc 파일 찾는다. (숨김파일 상태이다.)

![iTerm](/images/etc/iTerm10.png)
- 해당 피일을 열어 위와 같이 수정한다.
- 터미널 재시작 시, 제대로 실행은 되지만, 일부 폰트 깨짐
<br><br>

### Ubuntu Mono derivative Powerline 폰트 설치 & 설정하기
- [Ubuntu Mono derivative Powerline 폰트](https://beomi.github.io/others/Ubuntu_Mono_derivative_Powerline.ttf) 다운로드

![iTerm](/images/etc/iTerm12.png)
- 다운받은 폰트를 설치한다.

![iTerm](/images/etc/iTerm11.png)
- 칼라테마 적용고 같은방법으로 Color가 아닌 Text로 들어간다.

![iTerm](/images/etc/iTerm13.png)
- Change Font를 선택하고 위와같이 변경한다.
<br><br>
 
### zsh-syntax-highlighting 설치하기
>zsh-syntax-highlighting 시스템의 PATH에 등록된 명령어들을 자동으로 Syntax HighLighting 해준다.

```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
echo "source ${(q-)PWD}/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ${ZDOTDIR:-$HOME}/.zshrc
```
- 위 두줄의 명령어를 터미널에 입력한다.
- 터미널 재시작
<br><br>

### 완성
![iTerm](/images/etc/iTerm14.png)
<br><br>











            



