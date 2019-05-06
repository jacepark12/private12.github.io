---
title: react native 'third party' 관련 빌드 에러 해결방법
date: "2018-07-22T22:40:32.169Z"
template: "post"
draft: false
slug: "/posts/fix-react-native-third-party-error/"
category: "React Native Development"
tags:
  - "Xcode"
  - "IOS"
  - "React Natvie"
description: "React Native로 개발할때, 빌드시 발생하는 ‘third-party’와 관련된 에러를 해결해본다."
---

최근 react-native으로 어플을 개발하게 되면서 관심있는 여러 프로젝트들의 저장소를 내려받아 실행해보고자 하였다. 그러나 의외로 다양한 에러들이 발생하며 빌드에 실패하였다.
그 중 가장 대표적인 에러가 빌드시 발생하는 ‘third-party’와 관련된 에러였다. 에러 발생 로그를 자세히 살펴보니 third-party 라이브러리를 정상적으로 읽을 수 없거나 불러오지 못하는 문제였다.
구글링 결과 react-native에서는 개발환경을 위해 여러 라이브러리를 사용하며 빌드 스크립트를 뜯어보면 github에서 라이브러리 릴리즈 파일을 받도록 되어있다. 그러나 빌드 도중 취소되거나, 네트워크 지연 상의 이유로 github으로 부터 정상적으로 third-party 라이브러리를 받아오지 못하는 경우가 있다. 그러한 경우에는 특정 라이브러리를 삭제하고 수작업으로 라이브러리를 받아서 디렉토리에 저장하던가, 디렉토리 전체를 삭제하고 안정적인 환경에서 빌드를 다시 시작하는 방법이 있다.

```
#!/bin/bash
#boostdir="`node ./node_modules/boost-lib/bin/boost-lib download -V 1.63`"
#cd "$boostdir"
#./bootstrap.sh
#./b2 headers
set -e
cachedir="$HOME/.rncache"
mkdir -p "$cachedir"
function fetch_and_unpack () {
    file=$1
    url=$2
    cmd=$3
if [ ! -f "$cachedir/$file" ]; then
        (cd "$cachedir"; curl -J -L -O "$url")
    fi
dir=$(basename "$file" .tar.gz)
    if [ ! -d "third-party/$dir" ]; then
        (cd third-party;
         echo Unpacking "$cachedir/$file"...
         tar zxf "$cachedir/$file"
cd "$dir"
         eval "${cmd:-true}")
    fi
}
mkdir -p third-party
SCRIPTDIR=$(dirname "$0")
fetch_and_unpack glog-0.3.4.tar.gz https://github.com/google/glog/archive/v0.3.4.tar.gz "$SCRIPTDIR/ios-configure-glog.sh"
fetch_and_unpack double-conversion-1.1.5.tar.gz https://github.com/google/double-conversion/archive/v1.1.5.tar.gz
fetch_and_unpack boost_1_63_0.tar.gz https://github.com/react-native-community/boost-for-react-native/releases/download/v1.63.0-0/boost_1_63_0.tar.gz
fetch_and_unpack folly-2016.09.26.00.tar.gz https://github.com/facebook/folly/archive/v2016.09.26.00.tar.gz
```
위에서 보이는 것과 같이 빌드 스크립트에서 해당 github 주소가 보인다. 위 주소를 통해 직접 다운로드를 하거나, 아래와 같은 절차를 따른다.
1. ~/.rncache 디렉토리 삭제
2. react-native run-ios 명령어를 통해 curl: Saved to filename 로그가 정상적으로 나오는지 확인한다.
