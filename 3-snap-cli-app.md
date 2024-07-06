# Snapcraft 로 간단한 CLI 앱 패키징 하기

앞서 Snap 을 한번 사용 해 보았으니, 이제 본격적으로 Snapcraft로 패키징을 해 볼 차례입니다. 우선 간단한 CLI 앱을 패키징 해 보는 것으로 시작 해 봅시다.
여기서는 [hledger](https://hledger.org) 라는 CLI 회계 프로그램을 한번 패키징 해 보겠습니다.

## 패키징할 앱 어떻게 빌드 하는지 먼저 알아보기
우선 패키징 할 프로그램을 어떻게 빌드하고 설치 하는지 확인 해 보는것이 먼저일 것 입니다. hledger의 경우 아래 링크에서 그 방법을 제공하고 있는데 먼저 확인 해 봅시다.

https://hledger.org/install.html#build-from-source

## Snapcraft 및 LXD 설치
Snap 패키징을 위해 Snapcraft 를 설치하고, Snapcraft 에서 Snap 빌드에 필요한 환경을 구성할 용도로 쓰는 LXD도 설치 해 줍니다.

```bash
sudo snap install snapcraft --classic
```

LXD 는 아래 방법으로 설치 및 초기화 합니다.

```bash
sudo snap install lxd # 설치
sudo usermod -a -G lxd $USER # sudo 없이 lxd 명령 사용할 수 있도록 현재 사용자를 lxd 그룹에 추가
```

로그아웃 및 다시 로그인 하면 lxd 그룹 설정 덕에 sudo 없이 lxd 사용이 가능합니다. 이제 아래 명령으로 lxd 환경을 초기화 합니다.
```bash
lxd init --minimal
```

## snapcraft.yaml 생성 및 빌드 해 보기
필요한 빌드 도구를 설치 하였으니, snapcraft.yaml 을 생성 해 보고 빌드 해 봅시다.

아래 명령으로 작업 디렉토리를 하나 만들고, 그 안에서 snapcraft.yaml을 생성 합니다.
```bash
mkdir hledger-snap
cd hledger-snap
snapcraft init
```

그러면 아래와 같은 출력이 나오면서 snapcraft.yaml 이 생성 되었음을 확인할 수 있습니다.

```bash
$ snapcraft init
Created 'snap/snapcraft.yaml'.                                                 
Go to https://docs.snapcraft.io/the-snapcraft-format/8337 for more information about the snapcraft.yaml format.                                                
$
``` 

빌드도 시도 해 봅시다. 작업 디렉토리에서 `snapcraft`를 실행 하시면 됩니다.
```bash
$ snapcraft
Generated snap metadata                                                                 
Created snap package my-snap-name_0.1_amd64.snap
$ 
```

## snapcraft.yaml 구조 살펴보기

https://snapcraft.io/docs/snapcraft-schema 에 발문하여 각 부분에 대한 설명도 확인 해 봅시다.

```yaml
name: my-snap-name # you probably want to 'snapcraft register <name>'
base: core22 # the base snap is the execution environment for this snap
version: '0.1' # just for humans, typically '1.2+git' or '1.3.2'
summary: Single-line elevator pitch for your amazing snap # 79 char long summary
description: |
  This is my-snap's description. You have a paragraph or two to tell the
  most important story about your snap. Keep it under 100 words though,
  we live in tweetspace and your description wants to look good in the snap
  store.

grade: devel # must be 'stable' to release into candidate/stable channels
confinement: devmode # use 'strict' once you have the right plugs and slots

parts:
  my-part:
    # See 'snapcraft plugins'
    plugin: nil
```

## snapcraft.yaml 수정하기

### Metadata 수정

snapcraft.yaml 내용의 상단을 보면 name, version, summary 등의 metadata를 정의 한 부분을 보실 수 있습니다. 워크샵에서 패키징할 hledger 에 맞춰 아래와 같이 수정 해 봅시다.
https://github.com/simonmichael/hledger 의 README.md 를 참고하여 작성 한 내용입니다.

```yaml
name: hledger # you probably want to 'snapcraft register <name>'
base: core22 # the base snap is the execution environment for this snap
version: '1.34' # just for humans, typically '1.2+git' or '1.3.2'
summary: Robust, intuitive plain text accounting # 79 char long summary
description: |
  hledger is lightweight, cross platform, multi-currency, double-entry accounting software. 
  It lets you track money, investments, cryptocurrencies, invoices, time, inventory and more, 
  in a safe, future-proof plain text data format with full version control and privacy.

# grade, confinement 는 그대로 유지 합니다.
grade: devel # must be 'stable' to release into candidate/stable channels
confinement: devmode # use 'strict' once you have the right plugs and slots
```

### parts 정의하기

Snapcraft 파일에서는 패키지 빌드가 어떻게 되어야 하는지 parts 섹션에서 정의하고 있습니다. 하위에는 하나 이상의 parts 가 들어갈 수 있고, 빌드할 프로그램 숫자가 여러개 이거나 사전에 빌드해야 하는 의존성이 있다면 여러개의 parts 를 정의 하기도 합니다.

parts 부분에 아래와 같이 hledger part 를 정의 해 줍시다.

```yaml
parts:
  hledger:
    # See 'snapcraft plugins'
    plugin: nil
```

먼저 빌드 할 소스를 정의해야 하는데요. 이 워크샵 에서는 hledger 의 GitHub 저장소에서 끌어오도록 정의 해 보겠습니다. 그러면 아래와 같이 작성할 수 있습니다.

```yaml
parts:
  hledger:
    # See 'snapcraft plugins'
    plugin: nil # 플러그인 없음
    source: https://github.com/simonmichael/hledger # hledger git 저장소에서 소스 가져오기
    source-tag: "1.34" # 빌드할 Git Tag 지정
    source-type: git # 소스 유형 지정
```

그 다음으로는 빌드 방법을 정의 해 줍시다. 먼저 빌드에 필요한 의존성 패키지를 정의 해 줍니다. hledger 문서에 의하면, Ubuntu 환경에서 빌드 할 때는 `libgmp-dev`, `libtinfo-dev`, `zlib1g-dev` 가 요구 되는데, 아래와 같이 `build-packages` 부분에 정의할 수 있습니다.

```yaml
parts:
  hledger:
    # See 'snapcraft plugins'
    plugin: nil # 플러그인 없음
    source: https://github.com/simonmichael/hledger # hledger git 저장소에서 소스 가져오기
    source-tag: "1.34" # 빌드할 Git Tag 지정
    source-type: git # 소스 유형 지정
    build-packages: # 빌드할 떄 필요한 의존성
    - libgmp-dev 
    - libtinfo-dev 
    - zlib1g-dev
```

이 워크샵에서는 Haskell 의 Stack 이라는 도구로 빌드를 할 것인데, 이 도구는 현재 우분투 main 저장소에서 패키지로 제공되지 않습니다. 그래서 빌드 단계에서 별도 스크립트로 설치 되도록 할 것입니다. 이를 위해 build 과정에서 실행되는 명령을 `override-build` 로 재정의 합니다.

```yaml
parts:
  hledger:
    # See 'snapcraft plugins'
    plugin: nil # 플러그인 없음
    source: https://github.com/simonmichael/hledger # hledger git 저장소에서 소스 가져오기
    source-tag: "1.34" # 빌드할 Git Tag 지정
    source-type: git # 소스 유형 지정
    build-packages: # 빌드할 떄 필요한 의존성
    - libgmp-dev 
    - libtinfo-dev 
    - zlib1g-dev
    - curl # Stack 설치 시 curl 필요하므로 추가
    override-build: | 
      curl -sSL https://get.haskellstack.org/ | sh # Stack 설치
    
```
Stack 에서는 빌드한 프로그램을 `/root/local/.bin` 에 설치 하는데, Haskell 앱 빌드에 필요한 GHC 등도 없으면 이 경로에 설치 하므로 `PATH` 환경변수를 설정 해 주어야 합니다.
아래처럼 `build-environment` 를 활용해 설정할 수 있습니다. 

```yaml
parts:
  hledger:
    # See 'snapcraft plugins'
    plugin: nil # 플러그인 없음
    source: https://github.com/simonmichael/hledger # hledger git 저장소에서 소스 가져오기
    source-tag: "1.34" # 빌드할 Git Tag 지정
    source-type: git # 소스 유형 지정
    build-environment:
    - PATH: "/root/.local/bin:$PATH" # PATH 설정
    build-packages: # 빌드할 떄 필요한 의존성
    - libgmp-dev 
    - libtinfo-dev 
    - zlib1g-dev
    - curl # Stack 설치 시 curl 필요하므로 추가
    override-build: | 
      curl -sSL https://get.haskellstack.org/ | sh # Stack 설치
```

빌드에 필요한 명령도 넣어 줍니다.

```yaml
parts:
  hledger:
    # See 'snapcraft plugins'
    plugin: nil # 플러그인 없음
    source: https://github.com/simonmichael/hledger # hledger git 저장소에서 소스 가져오기
    source-tag: "1.34" # 빌드할 Git Tag 지정
    source-type: git # 소스 유형 지정
    build-environment:
    - PATH: "/root/.local/bin:$PATH" # PATH 설정
    build-packages: # 빌드할 떄 필요한 의존성
    - libgmp-dev 
    - libtinfo-dev 
    - zlib1g-dev
    - curl # Stack 설치 시 curl 필요하므로 추가
    override-build: | 
      curl -sSL https://get.haskellstack.org/ | sh # Stack 설치
      stack update
      stack build
```

마지막으로 빌드한 파일을 설치하는 명령도 넣어 주는데, 경로를 지정해서 빌드 환경이 아닌 패키지 될 파일 경로로 지정해 주어야 합니다.
이때 Snapcraft 빌드 환경에서 생성되는 환경 변수인 `CRAFT_PART_INSTALL` 를 사용할 수 있습니다. `CRAFT_PART_INSTALL`는 빌드 단계에서 나온 결과물을 보관할 경로로, 여기에 저장된 파일이 추후 패키지에 포함 됩니다.

이렇게 빌드 중 생성되는 Snapcraft 환경변수 정보는 https://snapcraft.io/docs/parts-environment-variables 에서 자세히 확인 가능합니다.

```yaml
parts:
  hledger:
    # See 'snapcraft plugins'
    plugin: nil # 플러그인 없음
    source: https://github.com/simonmichael/hledger # hledger git 저장소에서 소스 가져오기
    source-tag: "1.34" # 빌드할 Git Tag 지정
    source-type: git # 소스 유형 지정
    build-environment:
    - PATH: "/root/.local/bin:$PATH" # PATH 설정
    build-packages: # 빌드할 떄 필요한 의존성
    - libgmp-dev 
    - libtinfo-dev 
    - zlib1g-dev
    - curl # Stack 설치 시 curl 필요하므로 추가
    override-build: | 
      curl -sSL https://get.haskellstack.org/ | sh # Stack 설치
      stack update
      stack build
      stack install --local-bin-path $CRAFT_PART_INSTALL/bin/
```

### apps 정의하기
빌드 방법을 정의 했다면, 이제 패키지에 어떤 프로그램이 있고 어떻게 실행해야 하는지 정의해 주어야 합니다. 이 작업은 `apps` 섹션에서 지정하게 됩니다.

아래와 같이 지정할 수 있는데요, `apps` 섹션 아래에 프로그램 실행 시 어떤 명령으로 실행 되어야 하는지 이름을 정의하고, 실행할 파일 등을 지정하는 형태 입니다.
여기서는 우선, `bin/hledger`를 `hledger` 라는 명령으로 실행 되도록 지정합시다. 아래처럼 작성할 수 있습니다.

```yaml
apps:
  hledger:
    command: bin/hledger
```

이렇게 지정한 apps 아래 항목의 이름이 패키지 이름과 일치하면, 추후 패키징 한 것을 설치 했을 떄 해당 이름 만으로 명령을 실행 할 수 있게 됩니다. 예를 들어 여기서는 apps 아래 hledger 가 패키지 이름인 hledger 와 일치하므로, 설치 후 `hledger` 명령으로 실행할 수 있습니다.

패키지에 포함된 프로그램이 더 있다면 추가로 지정할 수 있습니다. `hledger`의 경우, `hledger-ui`, `hledger-web`도 있는데, 아래와 같이 추가할 수 있습니다.

```yaml
apps:
  hledger:
    command: bin/hledger
  ui:
    command: bin/hledger-ui
  web:
    command: bin/hledger-web
```
이렇게 추가한 항목은 패키지 이름과 일치하지 않기 떄문에 `<패키지 이름>.<App 이름>` 형식으로 실행하게 됩니다. 예를 들어 `hledger.ui` 혹은 `hledger.web` 명령으로 실행하게 되는 것 입니다.

### 빌드, 설치, 실행 해보기

패키지의 기본적인 부분은 어느정도 작성이 되었는데요. 이제 빌드하고 설치하여 실행 해 보겠습니다. 아래 명령을 순서대로 실행 해 보세요.

```yaml
snapcraft
sudo snap install --dangerous --devmode <패키지 파일 이름>.snap
```

Launchpad build firm 을 활용하여 원격 환경에서 빌드 하려면 `remote-build` 를 사용할 수 있습니다 (Launchpad 계정 필요)
```yaml
snapcraft remote-build
```

설치 후, `hledger`를 실행해서 정상적으로 실행 가능한지 확인 해 보세요

### Confinement 수정 및 Interface 지정