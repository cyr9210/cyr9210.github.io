---
title: OS별 유니코드 정규화
toc: true
widgets:
  - type: toc
    position: right
  - type: tagcloud
    position: right
sidebar:
  right:
    sticky: true
date: 2020-06-19 08:32:51
tags: Java
---
![java](/images/javaimage.png)
<!-- more -->

## 유니코드 정규화

- MacOS 에서 만든 한글이름 파일을 윈도우에서 사용 시, 자모 분리가 될 때가 있다.
- 유니코드를 쓰지만 서로 다른 유니코드 정규화 방식을 쓴다.
  - MacOS : NFD
  - Windows : NFC

### 차이점

- NFD
  - 코들를 정준 분해한다.
- NFC
  - 코드를 정준 분해 후, 다시 정주 결합한다.

**JDK에서 Unicode Normalization 지원한다.**

### 사용법

```java
public static String normalizeNfc(String unNormalMailBoxName) {
    if (!Normalizer.isNormalized(unNormalMailBoxName, Normalizer.Form.NFC)) {
        return Normalizer.normalize(unNormalMailBoxName, Normalizer.Form.NFC);
    }
    return unNormalMailBoxName;
}
```

- NFC가 아니면 NFC로 Normarize 한다.

### 참고

- https://www.hungrydiver.co.kr/bbs/detail/develop?id=68&scroll=comment



