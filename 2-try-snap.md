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

## 업데이트, 이전 버전으로 복구

패키지를 업데이트 하려면 `refresh` 를 사용합니다.
```bash
sudo snap refresh firefox
```

Snap 은 기본적으로 자동 업데이트 이므로, 업데이트를 지연시키는 것도 가능합니다. `--hold` 를 이용하면 됩니다.
```bash
snap refresh --hold=24h firefox
```

Snap의 트랜젝션 업데이트 덕에 업데이트 한 버전을 이전 버전으로 되돌리는 것 또한 가능합니다. `revert` 로 직전 버전이나 특정 버전으로 되돌리기가 가능합니다.
```bash
sudo snap revert firefox
```

특정 revision 으로 되돌리려면 `--revision` 옵션을 사용합니다.
```bash
snap revert firefox --revision 4480
```

## 인터페이스 연결 관리
Snap 에서 제공하는 인터페이스 기능으로 시스템 자원 접근을 허용 하거나 제한 해 봅니다.

먼저 `connections` 명령으로 패키지에 연결된 인터페이스를 조회 해 봅니다.
```bash
~$ snap connections firefox
Interface               Plug                            Slot                            Notes
alsa                    firefox:alsa                    -                               -
audio-playback          firefox:audio-playback          :audio-playback                 -
audio-record            firefox:audio-record            :audio-record                   -
avahi-observe           firefox:avahi-observe           :avahi-observe                  -
browser-support         firefox:browser-sandbox         :browser-support                -
camera                  firefox:camera                  :camera                         -
content[gnome-42-2204]  firefox:gnome-42-2204           gnome-42-2204:gnome-42-2204     -
content[gtk-3-themes]   firefox:gtk-3-themes            gtk-common-themes:gtk-3-themes  -
content[icon-themes]    firefox:icon-themes             gtk-common-themes:icon-themes   -
content[sound-themes]   firefox:sound-themes            gtk-common-themes:sound-themes  -
cups-control            firefox:cups-control            :cups-control                   -
dbus                    -                               firefox:dbus-daemon             -
desktop                 firefox:desktop                 :desktop                        -
desktop-legacy          firefox:desktop-legacy          :desktop-legacy                 -
gsettings               firefox:gsettings               :gsettings                      -
hardware-observe        firefox:hardware-observe        :hardware-observe               -
home                    firefox:home                    :home                           -
joystick                firefox:joystick                :joystick                       -
login-session-observe   firefox:login-session-observe   :login-session-observe          -
mount-control           firefox:host-hunspell           :mount-control                  -
mpris                   -                               firefox:mpris                   -
network                 firefox:network                 :network                        -
network-bind            firefox:network-bind            :network-bind                   -
network-observe         firefox:network-observe         -                               -
opengl                  firefox:opengl                  :opengl                         -
personal-files          firefox:dot-mozilla-firefox     :personal-files                 -
removable-media         firefox:removable-media         :removable-media                -
screen-inhibit-control  firefox:screen-inhibit-control  :screen-inhibit-control         -
system-files            firefox:etc-firefox             :system-files                   -
system-packages-doc     firefox:system-packages-doc     :system-packages-doc            -
u2f-devices             firefox:u2f-devices             :u2f-devices                    -
unity7                  firefox:unity7                  :unity7                         -
upower-observe          firefox:upower-observe          :upower-observe                 -
wayland                 firefox:wayland                 :wayland                        -
x11                     firefox:x11                     :x11                            -
~$ 
```

`connect` 와 `disconnect`로 인터페이스 연결/해제가 가능합니다. 여기서는 `firefox` 패키지에 대해 `wayland` 인터페이스를 연결/해제 해 보고 테스트 해 보겠습니다.

먼저 `wayland` 인터페이스 연결 해제 후 `firefox`를 실행 해 봅니다. Wayland 사용하는 데스크톱 이라면 이제 Wayland 접근이 불가하여 앱 실행이 불가 한 것을 확인할 수 있습니다.
```bash
sudo snap disconnect firefox:wayland
firefox
```

다시 연결 후 실행하면, 앱이 정상적으로 실행되는 것을 볼 수 있습니다.
```bash
sudo snap connect firefox:wayland
firefox
```