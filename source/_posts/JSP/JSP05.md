---
title: 게시판 페이징처리, 파일 업로드/다운로드, 썸네일 이미지 게시판
date: 2019-01-07 03:47:33
tags: JSP
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

# KOSTA DAY43
## JSP
![JSP](/images/JSP_logo.png)
<!-- more -->
### 게시판 페이징 처리
mvc 패턴의 비지니스로직을 처리하기 위해 Service객체를 생성하여 Service 객체에서 비지니스로직을 처리하는 법에 대해 알아보자.

- 기존 : Client → Controller → Action → DAO → mybatis → DB
- Service 객체 추가 : Client → Controller → Action → Service → DAO →mybatis → DB

Service 객체를 추가하여 페이징 처리 로직과 함께 게시판 리스트를 출력하는 예제를 살펴보자.

![JSP](/images/jsp/JSP05-01.png)
- 싱글톤방식을 사용하여 dao객체 및 Service 객체를 생성한다.

![JSP](/images/jsp/JSP05-02.png)
- pageSize = 게시글에 출력될 게시글의 수
- countBoard메소드 실행하여 총 게시물의 갯수를 구한다.
- totalPageCount를 구한다. 
총갯수 / 출력될 게시글 → 나머지가 있을 시 +1
- startPage/ endPage를 구한다.
startPage : 현재페이지-(현재페이지-1)%페이징수 (1~5 →1, 6~10 →6..)
(starPage +5)가 총페이지수 보다 크면 endPage = 총페이지수
- startRow = 게시글의 시작 번호
→ (현재페이지 -1) * 페이지당 글 갯수
- listBoard메소드에 startRow값을 넣어 list를 구한다.
- RowBounds(int start, PAGE_SIZE) : 페이징처리 getMapper 메소드
- 검색된 결과에 관하여 페이징 처리를 위하여 다음과 같은 처리를 한다.
searchKey(검색문자)가 null아 아니라면, search라는 값의 세션을 비운고 해당 조건 및 검색문을 set한다.
serarchKey값이 null이지만 search라는 session이 존재할 경우, 생성한 search 객체에 session값을 넣어준다.
- 페이징처리 정보와 list객체를 필드로 가지는 listModel객체를 리턴한다.
리턴한 값을 action에서 setAttribute 하고, path와 forward타입(dispatcher)을 정하여 컨트롤러에 리턴한다.
컨트롤러에서 dispatcher한다.

![JSP](/images/jsp/JSP05-03.png)
- 이전아이콘 처리
- 다음아이콘 처리
- 페이지 넘버처리
- 현재페이지 <b\>태그 처리
<br><br>

### 파일 업로드 / 다운로드
#### JSP 파일 업로드를 위한 기본적인 조건
- 파일을 업로드 할 때, form태그의 method = post 여야 한다.
- enctype : 데이터를 전송하게 될 타입을 지정하는 부분
- form태그에 enctype이 명시되면 서블릿에서 request객체를 이용해서 값을 가져오지 못한다.(하나의 스트림을 통해 전송되기 때문에)
스트림으로 넘어온 데이터를 getInputStream() 메소드를 사용해서 사용자가 전송한 스트림을 받아 각각 구분자를 이용하여 잘라내어 사용해야 하는데, 복잡하고 어렵다.
→ MultipartRequest 를 사용한다.

#### MultipartRequest
- cos.jar 라이브러리 추가가 필요하다.
- MultipartRequest 객체 알아보기

![JSP](/images/jsp/JSP05-04.png)

![JSP](/images/jsp/JSP05-05.png)

#### 예제

![JSP](/images/jsp/JSP05-06.png)
- form태그에 enctype=”multipart/form-data 설정
- input type= “file”로 설정

![JSP](/images/jsp/JSP05-07.png)
- Board객체에 filename이 들어가 변수를 추가한다.

![JSP](/images/jsp/JSP05-08.png)
- request.getRealPath(“upload”) 를 통해 저장될 경로지정
upload의 절대경로
- File size지정
- MultipartRequst객체 multi 생성
- DefaultFileRenamePolicy() : 같은 이름의 파일명 방지 처리
- 파일이 있으면 파일명을 보드에 담은 후 insert

다음은 다운로드 방법에 대해 알아보자.

![JSP](/images/jsp/JSP05-09.png)
- a태그를 통해 다운로드를 위한 페이지로 이동 filename을 get타입으로 가지고 간다.

![JSP](/images/jsp/JSP05-10.png)
- filename을 받아온 후 인코딩 한다.
- 경로를 지정한다.
- 다운로드 알림창이 뜨도록 하기 위해서 ContentType을 8비트 바이너리로 설정한다.
response.setContentType(“application/octet-stream”);
- 저장파일이름을 설정한다.
response.setHeader(“content-Disposition”, “attatchment;filename=\“” + filename2 + “/””);
- no-cache 설정

> **JSP에서 No-Cache 설정하는 방법**
웹개발 하다보면 캐쉬된 페이지때문에 가끔 웹브라우저가 재시동하거나, 웹서버를 재시동하는 경우가 있었을 것이다. 그런 경우 캐쉬에서 불러오는 것이 아니라 항상 최신의 페이지를 보여주도록 하는 방법입니다. 데이터가 넘어가는 경우에만 ‘만료된 페이지입니다’ 라는 메시지를 보여주게 된다.

![JSP](/images/jsp/JSP05-11.png)
- 버퍼크기 설정
byte[] data = new byte[8096\];
- 파일을 남아있으면 읽어서 data에 저장
- is, os 닫아준다. 서블릿도 닫아준다.
<br><br>

### 썸네일 이미지 게시판

![JSP](/images/jsp/JSP05-12.png)
- 이미지 파일임을 파악하기 위해 fname으로부터 “.”을 기준으로 분리
- uploadPath/fname 으로 경로지정
- imagePath를 사용하여 File객체로 이미지를 저장
- 썸네일 이미지를 위해 썸네일이미지 파일을 위한 경로를 지정한다.
- 파일형식이 gif 일때 ImageUtil의 resize 메소드 실행
- 썸네일 이미지로 크기를 줄여 객체를 이미지파일을 저장한다.

![JSP](/images/jsp/JSP05-13.png)
- src파일객체를을 FileInputStream객체로 만든다.
- 오버로딩된 resize메소드 호출
- 썸네일 크기 지정
- BufferedImage 객체를 사용하여 지정한 크기 및 이미지타입설정
- 생성한 BufferedImage 객체사용하여 2D graphics 생성

![JSP](/images/jsp/JSP05-14.png)
- head = .jpg 등 형식을 제외한 파일명
- pattern = 형식 (ex> jpg, gif등)
- <img src= “upload(파일위치)/썸네일 파일명\>으로 썸네일 이미지 출력
<br><br>