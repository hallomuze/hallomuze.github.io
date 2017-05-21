---
layout: post  
title:  swift snapshot4 install.   
date:   2017-5-20 10:29:00  
categories: swiftenv, swift  
---
#  snapshot4 install 하기 / fish 터미널에서 swiftenv 사용해서 install (x) , install pkg file(O)
아무것도 모른다.
fish는 터미널을 조금 예쁘게 보여주는 애플리케이션이다.
여튼 fish 가 설치되었다고 가정하고 시작한다
그후 부터 과정은 모두 삽질입니다.

*시간절약 팁*
* `Xcode 8.3` 이상으로 설치할 것.
* 삽집하기싫으면 그냥 애플사이트에서 `스위프트 4.0 snapshot` 설치할것


## swiftenv 바닥부터 설치하기(install swiftenv from scratch)
먼저 fish를 설치하자. 나는 pkg 를 다운로드받았다.
Alfred 로 실행하려고 하니 fish 가 검색이 안 되는 것이다. 이런.
아하.terminal 을 실행후에. 아래처럼 fish를 타이핑하니
짜잔 실행되네..근데 매번 이렇게 실행해야...???

```bash
eddie:~ word$ fish
word@eddie ~> 
```

`~>`가 나타내는 것이 바로 fish terminal 이다.

설치후 fish config 를 고쳐주자. 
```
word@eddie~> nano ~/.config/fish/config.fish
```
내 경우는 처음이라서 그런지 파일에 아무 내용이 없었다.
아래 내용을 복사해서 넣었다.
```
setenv SWIFTENV_ROOT "$HOME/.swiftenv"
setenv PATH "$SWIFTENV_ROOT/bin" $PATH
status --is-interactive; and . (swiftenv init -|psub)
```

nano에서 저장은 `ctrl + O` 이고 빠져나오려면 `ctrl + X `누르자.

```
word@begin ~> exit
begin:~ word$ fish
word@begin ~> swiftenv 
```

자 이제 swiftenv 를 이용해 swift snapshot 을 설치해보자.
snapshot 은 정식 swift 버전이 아니고 개발중인 버전이라 생각하면 된다.
보통 xcode  만 설치하면 3.0 같은 것이 깔릴 것이다.

아래는 현재 설치할 수 있는 swift  리스트를 보여준다.
```bash
word@begin ~> swiftenv install --list
2.2-dev
2.2
2.2.1
3.0-dev
3.0
3.0.1
3.0.2
3.1-dev
3.1
3.1.1
4.0-dev
```

## swiftenv 로 swift 설치 삽질기( 실패기 )
여기부터 swift 설치에 대한 삽질이 시작됩니다.
곧바로 정상적으로 쉽게 설치하려면 이 부분을 건너띄고 `pkg 로 설치하는 방법`으로 이동하셔서 읽으면 됩니다.

그럼 swift 를 설치해보자.
```
word@begin ~> swiftenv install 4.0-dev
Downloading swift (swift-4.0-branch) from git
Downloading swift-llvm (swift-4.0-branch) from git
Downloading swift-clang (swift-4.0-branch) from git
Downloading swift-lldb (swift-4.0-branch) from git
Downloading swift-cmark (swift-4.0-branch) from git
Downloading swift-llbuild (swift-4.0-branch) from git
Downloading swift-package-manager (swift-4.0-branch) from git
Downloading swift-compiler-rt (swift-4.0-branch) from git
Downloading swift-corelibs-xctest (swift-4.0-branch) from git
Downloading swift-corelibs-foundation (swift-4.0-branch) from git
Downloading swift-corelibs-libdispatch (swift-4.0-branch) from git
Building Swift
This may take a very long time...
Building Swift failed
Check out the logfile for more information: /Users/word/.swiftenv/tmp/swiftenv-build-4.0-dev/swiftenv-build.20170520072322.3916.log
You can inspect or delete the working tree at: /Users/word/.swiftenv/tmp/swiftenv-build-4.0-dev
word@begin ~> 
```
자 끝났다!!

그래 해보자.
```
word@begin ~> swiftenv versions
* system
  3.0.2
  2.0
```
아니 아무것도 없잖아??
```
word@begin ~> swiftenv global
swiftenv: version is not set
```
위 로그를 확인해보니까 `Building Swift failed` 이 있다. 그래서 해당 로그를 확인해보았다.
```
word@begin ~> cat /Users/word/.swiftenv/tmp/swiftenv-build-4.0-dev/swiftenv-build.20170520072322.3916.log
Cloning into 'swift'...
Checking out files: 100% (13044/13044), done.
Cloning into 'llvm'...
Checking out files: 100% (24153/24153), done.
Cloning into 'clang'...
Checking out files: 100% (13768/13768), done.
Cloning into 'lldb'...
Checking out files: 100% (6883/6883), done.
Cloning into 'cmark'...
Cloning into 'llbuild'...
Cloning into 'swiftpm'...
Cloning into 'compiler-rt'...
Cloning into 'swift-corelibs-xctest'...
Cloning into 'swift-corelibs-foundation'...
Cloning into 'swift-corelibs-libdispatch'...
./utils/build-script: fatal error: can't find CMake (please install CMake)
```

이런 CMake를 설치하라네.
brew 를 사용해 설치하면 된다는데, 내 MAC 에는 brew 가 없다. 그럼 brew 부터 설치하자
```bash
word@begin ~> brew
fish: Unknown command 'brew'
word@begin ~>  /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
$(...) is not supported. In fish, please use '(curl)'.
fish:  /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
                         ^
word@begin ~>  
```

fish shell에서 무슨 에러가 나서 설치가 안된다.
아이고 그냥 terminal 에서 설치하자

먼저 fish 에서 안되네 fish 에서 exit 해서 일반 터미널로 가자.
터미널창에서 아래를 타이핑해 `Homebrew`를 설치하자.
```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

그다음  CMake 를 설치해보자
```
word@begin ~> brew install cmake
```
성공했다~~

```bash
root@begin ~# swiftenv install 4.0-dev
Found swift, skipping download
Found llvm, skipping download
Found clang, skipping download
Found lldb, skipping download
Found cmark, skipping download
Found llbuild, skipping download
Found swiftpm, skipping download
Found compiler-rt, skipping download
Found swift-corelibs-xctest, skipping download
Found swift-corelibs-foundation, skipping download
Found swift-corelibs-libdispatch, skipping download
Building Swift
This may take a very long time...
Building Swift failed
Check out the logfile for more information: /Users/word/.swiftenv/tmp/swiftenv-build-4.0-dev/swiftenv-build.20170520074448.5500.log
You can inspect or delete the working tree at: /Users/word/.swiftenv/tmp/swiftenv-build-4.0-dev
root@begin ~# cat /Users/word/.swiftenv/tmp/swiftenv-build-4.0-dev/swiftenv-build.20170520074448.5500.log
+ mkdir -p /Users/word/.swiftenv/tmp/swiftenv-build-4.0-dev/build/Ninja-ReleaseAssert
./utils/build-script: fatal error: can't find source directory for ninja (tried /Users/word/.swiftenv/tmp/swiftenv-build-4.0-dev/ninja)
root@begin ~# exit
```
아..그런데 여기서 swift4 snapshot 이 계속 실패했다.  따라서 여기까지 삽질은 그만하고 그냥 애플사이트에서 스냅샷을 다운로드했다. (하나하나 읽으신분들 죄송합니다)

## pkg 로 설치하는 방법
애플사이트에 들어가보면 스냅샷 4.0에 해당하는 것을 다운로드하면 pkg 파일이 다운로드 폴더에 확인된다, 이걸 설치하고나서 다시 terminal  로 왔다.
다시 확인하니 잘 보인다.
```bash
begin:~ word$ fish

word@begin ~> swiftenv versions
* system
  4.0-DEVELOPMENT-SNAPSHOT-2017-05-17-a
2017-05-20 07:47:27.958 xcodebuild[5620:237029] [MT] PluginLoading: Required plug-in compatibility UUID E0A62D1F-3C18-4D74-BFE5-A4167D643966 for plug-in at path '~/Library/Application Support/Developer/Shared/Xcode/Plug-ins/Alcatraz.xcplugin' not present in DVTPlugInCompatibilityUUIDs
  3.0.2
  2.0
word@begin ~> swiftenv global
swiftenv: version is not set
word@begin ~> swiftenv global 4.0
swiftenv: version `4.0' is not installed
word@begin ~> swiftenv global 4.0-DEVELOPMENT-SNAPSHOT-2017-05-17-a
word@begin ~> swiftenv global

```

이제..xcode 를 실행하고 4.0 소스를 열어보았다.
아뿔사 xcode 버전이 8.2인것이다…멘붕…ㅜㅜ..
8.3로 업그레이드 합시다.!!





  
