# Snap (snapd) 설치하고 사용 해 보기

Snapcraft 로 Snap 패키지를 만들어 보기 전에, Snap 패키지를 설치해서 먼저 사용해 보면 좋을 것입니다. 이번 장에서는 Snapd 를 설치하고 Snap Store 에 있는 패키지를 설치해 사용 해 봅니다. Snap의 Revision 기능과 Interface 기능도 사용 해 보면서 어떻게 작동 하는지 간단히 살펴봅시다.

## Snapd 설치
Ubuntu의 경우 보통 Snapd 는 이미 설치되어 있습니다. 사용중인 배포판에 Snapd 가 설치되어 있지 않다면, 아래 링크를 참고하여 설치할 수 있습니다.

https://snapcraft.io/docs/installing-snapd

## 패키지 검색

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

패키지에 대한 세부 정보를 보려면 `snap info`를 이용할 수 있습니다.

```bash
~$ snap info firefox
name:      firefox
summary:   Mozilla Firefox web browser
publisher: Mozilla✓
store-url: https://snapcraft.io/firefox
contact:   https://support.mozilla.org/kb/file-bug-report-or-feature-request-mozilla
license:   unset
description: |
  Firefox is a powerful, extensible web browser with support for modern web application
  technologies.
commands:
  - firefox
  - firefox.geckodriver
snap-id:      3wdHCAVyZEmYsCMFDE9qt92UV8rC8Wdk
tracking:     latest/stable/ubuntu-24.04
refresh-date: 7 days ago, at 19:19 KST
channels:
  latest/stable:    127.0.2-1     2024-06-26 (4483) 281MB -
  latest/candidate: 128.0-2       2024-07-04 (4539) 280MB -
  latest/beta:      128.0b9-1     2024-06-28 (4502) 280MB -
  latest/edge:      129.0a1       2024-07-05 (4542) 300MB -
  esr/stable:       115.12.0esr-1 2024-06-11 (4373) 256MB -
  esr/candidate:    115.13.0esr-3 2024-07-04 (4532) 256MB -
  esr/beta:         ↑                                     
  esr/edge:         ↑                                     
installed:          127.0.2-1                (4483) 281MB -
~$ 
```

## 패키지 설치

설치 하려면 다른 패키지 관리자 처럼 `snap install`을 이용 합니다.

```bash
sudo snap install firefox
```

Snap에는 채널(Channel)기능이 있는데, 이를 통해 출시 후보나 테스트 단계의 패키지도 배포가 가능하고 선택적으로 설치가 가능합니다.
예를 들어 개발 단계의 버전을 이용 해 보려면 `edge` 채널을 이용 하는데, 아래와 같이 `--channel`옵션을 이용하면 됩니다.
```bash
sudo snap install --channel=edge firefox
```

설치 후 채널을 변경하려면 `switch`를 이용합니다.
```bash
sudo snap switch --channel=stable firefox
```