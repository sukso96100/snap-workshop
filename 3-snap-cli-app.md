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
