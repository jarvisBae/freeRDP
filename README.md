# freeRDP
freeRDP 개발기록
# 목차

* [cmake설치](#cmake설치)
    * [macOS homebrew로 설치](#macOS-homebrew로-설치)
* [FreeRDP프로젝트 다운로드](#FreeRDP-프로젝트-다운로드)
    * [git clone](#FreeRDP-git-clone)
    * [프로젝트 폴더 루트로 이동](#프로젝트-폴더-루트로-이동)
* [openssl 설치](#Openssl-설치)
    * [openssl 스크립트 실행](#openssl-스크립트-실행)
    * [스크립트 파일 수정](#스크립트-파일-수정)
    * [스크립트 실행](#스크립트-실행)
* [cmake로 xcode 실행파일 생성](#Cmake-Xcode-실행파일-생성)
* [Xcode 빌드이슈 해결](#Xcode-빌드이슈-해결)
* [Windows 접속정보](#Windows-접속정보)
* [iFreeRDP 앱실행 및 접속정보 설정](#iFreeRDP-앱실행-및-접속정보-설정)


## cmake설치
cmake를 통해 오픈소스인 FreeRDP iOS, MacOS, Linux, Android 등 샘플소스 프로젝트를 생성 할 수 있다. 본문에서는 iOS 샘플 프로젝트 생성 및 빌드방법을 안내한다.

OS : macOS Big sur, 11.0.1
Xcode version : 12.2
xcode 12.4버전에서도 정상작동

### macOS homebrew로 설치
brew를 통해 cmake를 설치한다. 작성일 기준 21.03.04에 3.19.6버전 설치
```
brew install cmake
```

cmake version 확인
```
cmake -version
```

cmake 설치 위치 확인
```
which cmake
```

iOS cmake 다운로드 사이트 : http://code.google.com/p/ios-cmake/
iOS cmake 사용법 링크 : https://code.google.com/archive/p/ios-cmake/wikis/HowTo.wiki


## FreeRDP 프로젝트 다운로드
오픈소스로 제공되는 FreeRDP githib에서 설치한다.
링크 : https://github.com/FreeRDP/FreeRDP

### FreeRDP git clone
```
mkdir ~/git/FreeRDP
cd ~/git/FreeRDP
git clone git://github.com/FreeRDP/FreeRDP.git
```

### 프로젝트 폴더 루트로 이동
이후 진행된 명령어는 프로젝트가 다운로드된 폴더의 루트에서 실행한다.
iOS 개발환경 구축은 아래 문서를 참고하여 진행한다.
https://github.com/FreeRDP/FreeRDP/blob/master/docs/README.ios
```
cd /Users/사용자/FreeRDP
```

## Openssl 설치
FreeRDP를 사용하려면 openssl이 필요하다.
오픈소스에서 친절하게 openssl 설치를 지원한다.

### openssl 스크립트 실행
루트폴더 기준으로 /script/OpenSSL-DownloadAndBuild.command
파일을 실행시킨다.

```
현재위치 확인
pwd
$ /Users/사용자/FreeRDP

프로젝트 루트로 이동
cd /Users/사용자/FreeRDP

openssl 설치 
./scripts/OpenSSL-DownloadAndBuild.command
```

정상적으로 빌드된다면 
/external/openssl/lib 위치에 파일이 생성되야한다.
또한 터미널에서 아래와 같은 형태의 로그를 확인 할 수 있다.
```
jinkibae@JINKIui-iMac FreeRDP % ./scripts/OpenSSL-DownloadAndBuild.command
Unpacking OpenSSL ...

Building openssl-1.0.2q for iPhoneOS 14.2 armv7 (min SDK set: 10.0)
 Please wait ... 빌드 진행 : armv7
 Done. Build log saved in BuildLog.darwin-armv7.txt
Building openssl-1.0.2q for iPhoneOS 14.2 armv7s (min SDK set: 10.0)
 Please wait ... 빌드 진행 : armv7s
 Done. Build log saved in BuildLog.darwin-armv7s.txt
Building openssl-1.0.2q for iPhoneOS 14.2 arm64 (min SDK set: 10.0)
 Please wait ... 빌드 진행 : arm64
 Done. Build log saved in BuildLog.darwin-arm64.txt
Copying header files ...

Combining to unversal binary
Finished. Please verify the contens of the openssl folder in "external"
```

하지만 이 문서를 작성한 이유는 정상적으로 진행되지 않기 때문이다.
x86_64 아키텍처 빌드중에 에러가 발생한다. 
자세한 로그는 /FreeRDP/external/openssl/openssltmp 폴더에
BuildLog.darwin-아키텍처.txt에서 확인 할 수 있다.

### 스크립트 파일 수정
프로젝트 루트 기준 /scripts/OpenSSL-DownloadAndBuild.command
파일에 실제 디바이스 구동이 가능하도록 수정한다

```
vi ./scripts/OpenSSL-DownloadAndBuild.command

기존
ARCHS="i386 x86_64 armv7 armv7s arm64"

수정
#ARCHS="armv7 armv7s arm64"

:wq
```

### 스크립트 실행
```
./scripts/OpenSSL-DownloadAndBuild.command
```

## Cmake Xcode 실행파일 생성
오픈소스에서 설정된 iOSToolchain.cmake 파일을 실행
iOS 도큐먼트 문서상에는 아래와 같이 나오지만 완료되지 않는다.
이유는 openssl 폴더를 찾지못해서 

```
도큐먼트 문서에서 제시한 방법
cmake -DCMAKE_TOOLCHAIN_FILE=cmake/iOSToolchain.cmake -GXcode

수정한 방법
cmake -DOPENSSL_ROOT_DIR=external/openssl -DOPENSSL_LIBRARIES=external/openssl/lib -DCMAKE_TOOLCHAIN_FILE=cmake/iOSToolchain.cmake -DCMAKE_SYSTEM_NAME=iOS -GXcode
```

정상적으로 완료되면 
프로젝트 루트폴더에 FreeRDP.xcodeproj 가 생성된다.


## Xcode-빌드이슈-해결
루트에 생성된 FreeRDP.xcodeproj를 클릭하여 프로젝트를 실행한다.
실제 디바이스에서 구동하려면 아래와 같이 세팅을 변경한다.

1. 개발용 인증서 세팅
2. Bundle identifier 세팅
3. Enable bitcode = no
4. Architectures = arm64로 변경


## Windows 접속정보
실제 디바이스에 앱이 설치되고 테스트할 윈도우 pc 접속 정보

접속주소 : 183.109.117.57:52056
id : inuc
pw : 비번은 비밀입니다.

## iFreeRDP 앱실행 및 접속정보 설정
접속 정보를 세팅해도 원격 데스크톱에 접속되지 않는다면
환경설정을 수정해야한다.
Performance > RemoteFX, 등 다른 옵션으로 변경
Advanced > 옵션값 변경 



<br/><br/><br/>
참고문서
1. 첨부파일(FreeRDP-Developer-Menual.pdf)
2. 링크 : https://github.com/awakecoding/FreeRDP-Manuals/blob/master/Developer/FreeRDP-Developer-Manual.markdown
3. OpenCV 빌드 및 설치 : https://www.leelab.co.kr/bbs/board.php?bo_table=project&wr_id=14&page=1
4. freeRDP 적용 샘플 url : https://pds0819.tistory.com/entry/ios-freeRDP-적용시키기-절차
5. iOS 도큐먼트 : https://github.com/FreeRDP/FreeRDP/blob/master/docs/README.ios
