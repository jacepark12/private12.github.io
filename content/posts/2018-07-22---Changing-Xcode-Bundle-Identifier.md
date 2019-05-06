---
title: xcode bundle identifier 변경하기
date: "2018-07-22T23:46:37.121Z"
template: "post"
draft: false
slug: "/posts/change-xcode-bundle-identifier/"
category: "IOS Development"
tags:
  - "Xcode"
  - "IOS"
  - "React Natvie"
description: "React Native로 IOS개발을 진행하던 도중 Xcode에서 Bundle Identifier를 변경해야 provisioning profile를 생성할 수 있다는 오류 해결"
---

xcode bundle identifier 변경
react-native 혹은, ios를 github 저장소에서 받아서 프로젝트를 archive하거나 release를 하려다 보면 Bundler Identifier를 변경해야 provisioning profile을 생성할 수 있다는 에러가 뜬다. 이러한 경우에는 Bundle Identifier를 변경해야 한다. react-native의 ios 폴더안에 있는 xcode workspace 파일을 xcode로 열고 아래의 순서를 따른다.
1. Target -> Build Settings -> product bundle 검색 -> product bundle identifier
2. 더블 클릭 후 변경