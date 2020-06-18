---
title: GitLab Slack 연동
toc: true
widgets:
  - type: toc
    position: right
  - type: tagcloud
    position: right
sidebar:
  right:
    sticky: true
date: 2020-06-19 08:16:36
tags: Etc
---
  ![slack_gitlab](/images/slack_gitlab/slack_gitlab_plus.png)  

<!-- more -->
# slack gitlab 연동
- 현재 사내에서 사용중인 프로젝트 소스관리 : GitLab
- 커뮤니케이션 : slack
- CodeReview : GitLab MergeRequest 및 해당 내용을 가지고 주1회 오프라인 리뷰 진행
- 본인이 올린 소스코드 외 다른 사람이 올린 MR에 대해서 관심이 있지 않으면 주니어들은 다른사람의 MR을 확인하기 쉽지 않다.
  - MR이 많거나 이미 Merged 된 요청에대해서 확인하기 쉽지않다.
- 오프라인 코드리뷰 전에 한번은 해당 코드에대해서 보고 들어갈 수 있도록 slack에 연동하여 알림을 받을 수 있고 싶었다.# slack gitlab 연동                                                         

## slack 설정

- Details -> Add Apps

  ![slack_gitlab1](/images/slack_gitlab/slack_gitlab1.png)

- View App Directory

  ![slack_gitlab2](/images/slack_gitlab/slack_gitlab2.png)

- Incoming WebHooks 검색

  ![slack_gitlab3](/images/slack_gitlab/slack_gitlab3.png)

- Add to Slack

  ![slack_gitlab4](/images/slack_gitlab/slack_gitlab4.png)

- 채널 선택

  ![slack_gitlab5](/images/slack_gitlab/slack_gitlab5.png)

- 설정

  ![slack_gitlab7](/images/slack_gitlab/slack_gitlab7.png)

  - Webhook URL 내용을 복사해둔다.
  - 메세지에 같이올 문구 및 아이콘등을 설정가능

## GitLab 설정

- Maintainer 권한임을 확인한다.

- settings -> Integration

  ![slack_gitlab8](/images/slack_gitlab/slack_gitlab8.png)

- Slack notifications 선택

  ![slack_gitlab9](/images/slack_gitlab/slack_gitlab9.png)

- 설정

  ![slack_gitlab10](/images/slack_gitlab/slack_gitlab10.png)

  - Acitive(활성화) 및 이벤트 트리거를 선택 가능하다.
    - 본인의경우 MergeRequest만 적용

- 설정 완료 시, 테스트 메세지가 전달된다.

  ![slack_gitlab11](/images/slack_gitlab/slack_gitlab11.png)

## 참고

- [https://medium.com/@Rando209/how-to-integrate-gitlab-with-slack-slack%EA%B3%BC-gitlab-%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0-8d4958d7e57f](https://medium.com/@Rando209/how-to-integrate-gitlab-with-slack-slack과-gitlab-연동하기-8d4958d7e57f)


