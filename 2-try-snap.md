# Snap (snapd) 설치하고 사용 해 보기

Snapcraft 로 Snap 패키지를 만들어 보기 전에, Snap 패키지를 설치해서 먼저 사용해 보면 좋을 것입니다. 이번 장에서는 Snapd 를 설치하고 Snap Store 에 있는 패키지를 설치해 사용 해 봅니다. Snap의 Revision 기능과 Interface 기능도 사용 해 보면서 어떻게 작동 하는지 간단히 살펴봅시다.

## Snapd 설치
Ubuntu의 경우 보통 Snapd 는 이미 설치되어 있습니다. 사용중인 배포판에 Snapd 가 설치되어 있지 않다면, 아래 링크를 참고하여 설치할 수 있습니다.

https://snapcraft.io/docs/installing-snapd

## 패키지 설치 및 실행 해 보기

먼저 로컬에 설치된 Snap 패키지를 조회 해 봅시다.

```bash
snap list
```

이 워크샵에서는 `firefox`를 설치해 봅시다. 먼저 `firefox` 패키지가 Snap Store 에 있는지 검색 해 봅니다.
```bash
~$ snap find firefox
이름                         버전                  발행인             노트   요약
firefox                    127.0.2-1           mozilla✓        -    Mozilla Firefox web browser
firefox-kiosk              0.1                 scout208        -    firefox example kiosk
ultimate-media-downloader  5.0                 keshavnrj✪      -    Online video & audio downloader for Linux, 1450+ websites support
red-app                    9.0                 keshavnrj✪      -    Complete Youtube Desktop Applications
joplin-desktop             v3.0.12             james-carroll✪  -    A free, private note taking and to-do app!
simple-pwgen               1.1.2+git1.47b1d83  hsbasu          -    Very simple Python3-based GUI application to generate secure and random passwords.
joplin-arnatious           1.0.224             arnatious       -    Joplin is a free, open source note taking and to-do application.
downloadaccelerator        3.2                 messey-bilal    -    A free tool to download files.
tncc-script                0+git.4192108       guilhem         -    wrapper for openconnect and pulse
~$ 
```

필요에 따라, 검색 시 패키지 설명이나 카테고리 기준으로 검색을 원한다면 아래와 같이 실행할 수 있습니다.

> 패키지 이름 또는 설명으로 검색 `snap find "검색 키워드"`
```bash
snap find "web browser"
```

> 검색 카테고리 지정 `--section=`
> 카테고리 목록: https://snapcraft.io/docs/quickstart-tour#section-categories
```bash
snap find firefox --section=productivity
```