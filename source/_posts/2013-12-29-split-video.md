---
layout: post
title: "비디오 동영상 쪼개기"
date: 2013-12-29 07:31:32 +0900
comments: true
categories: video tools
author: 최효성
---

맥 환경에서는 `homebrew`를 이용하면 `ffmpeg`를 쉽게 설치할 수 있습니다.
<!--more-->
```
$ brew install ffmpeg
```

제법 시간이 걸리지만 기다릴만 합니다.

동영상을 쪼개는 command line 명령과 옵션은 아래와 같습니다.

```
$ ffmpeg -v quiet -y
  -i [original file name]
  -vcodec copy -acodec copy -ss 00:00:00 -t 00:25:00 -sn [split1 file name]
  -vcodec copy -acodec copy -ss 00:25:00 -t 00:54:25 -sn [split2 file name]
```

아래에 이를 이용한 예를 소개 합니다.

ex 1) 1시간 44분 25초 크기의 remotty_3rd_hangout.mp4 동영상 파일을 50분 크기로 쪼개고자 할 때.

```
$ ffmpeg -v quiet -y -i remotty_3rd_hangout.mp4 -vcodec copy -acodec copy -ss 00:00:00 -t 00:50:00 -sn remotty3_1.mp4 -vcodec copy -acodec copy -ss 00:50:00 -t 01:44:25 -sn remotty3_2.mp4
```

ex 2) 50분짜리 remotty3_1.mp4 동영상 파일을 25분 크기로 쪼개고자 할 때

```
$ ffmpeg -v quiet -y -i remotty3_1.mp4 -vcodec copy -acodec copy -ss 00:00:00 -t 00:25:00 -sn remotty3_part1.mp4 -vcodec copy -acodec copy -ss 00:25:00 -t 00:50:00 -sn remotty3_part2.mp4
```

ex 3) 54분 25초 크기의 remotty3_2.mp4 동영상 파일을 25분 크기로 쪼개고자 할 때

```
$ ffmpeg -v quiet -y -i remotty3_2.mp4 -vcodec copy -acodec copy -ss 00:00:00 -t 00:25:00 -sn remotty3_part3.mp4 -vcodec copy -acodec copy -ss 00:25:00 -t 00:54:25 -sn remotty3_part4.mp4
```


#### 정말 순식간에 re-rendering 과정없이 동영상 파일이 쪼개지는군요. ㅎㅎㅎ


레퍼런스 : http://stackoverflow.com/a/19300561/1217633
