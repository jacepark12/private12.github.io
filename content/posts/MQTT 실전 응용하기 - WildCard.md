---
title: MQTT 실전 응용하기 - WildCard
date: "2018-05-06T22:40:32.169Z"
template: "post"
draft: false
slug: "/posts/mqtt-wildcard/"
category: "Server Development"
tags:
  - "MQTT"
  - "IoT"
  - "server"
description: "MQTT의 WildCard 사용하기"
---
# MQTT 실전 응용하기 - WildCard

MQTT를 활용하여 실제 개발을 진행하려 하니 몇가지 궁금한점과 고려해야할 사항이 생겼다. MQTT를 소개한 글에서 언급한 바와 같이 MQTT는 IoT 프로젝트 통신에 적합하기에 아두이노에서 여러 센서들의 데이터를 받아오는 gateway를 만들어보고자 하였다. 아두이노쪽에서 publish하게 된다면, 클라이언트측에서 해당 topic에 subscribe를 하게 되는데, 구조는 다음과 같이 생각해볼 수 있다. 

![](/media/mqtt-wildcard/mqtt-tmp.001.jpeg)

그렇다면 아두이노 기기를 추가하고, 센서를 추가하거나 변경할때마다 topic 관리가 복잡해질것으로 생각되었다. 그래서 생각해본 것이 http서버를 만들때 처럼 동적 url(여기서는 topic)을 사용하는 것이다. 

`/Arduino1/sensor/{sensor_name}`

위와 같이 topic을 설정할 수 있다면, 관리가 더욱 용이할 것으로 생각되었고, mqtt에서는 이를 `WildCard`를 통해서 구현할 수 있었다.

MQTT에서 WildCard는 크게 두가지 종류가 있다. 

1. Single Level : + 
Single Level + 는 이름에서 알 수 있다시피 topic에서 한단계의 문자열을 대체하는 문자이다.

아래 예시를 살펴보자
![](/media/mqtt-wildcard/topic_wildcard_plus.png)

![](/media/mqtt-wildcard/topic_wildcard_plus_example.png)

`Myhome/groundfloor/livingroom/temperature`, `Myhome/groundfloor/kitchen/temperature`와 같이 + 문자의 자리에는 하나의 문자열이 들어간다. 

다만, `Myhome/groundfloor/kitchen/fridge/temperature`에서와 같이, `/kitchen/fridge`처럼 여러 level은 해당되지 않는다.

2. Multi Level : #
Multi Level은 여러 단계의 topic을 subcribe할 수 있도록 한다. 

![](/media/mqtt-wildcard/topic_wildcard_hash.png)

![](/media/mqtt-wildcard/topic_wildcard_hash_example.png)

1번 예시를 활용해보자면,
`myhome/groundfloor/#/temperature`의 경우에는
Single Level과는 다르게 `myhome/groundfloor/kitchen/fridge/temperature`이 정상적으로 subscribe됨을 확인할 수 있다.

WildCard를 사용해서 MQTT-Explorer와 Python 코드에서 직접 예시를 실행해보자. 그러면 동적으로 다량의 기기와 센서데이터를 관리할때 훨씬 편하게 작업할 수 있음을 확인할 수 있을것이다. 

## Reference
예시 자료 : [MQTT Essentials Part 5: MQTT Topics & Best Practices](https://www.hivemq.com/blog/mqtt-essentials-part-5-mqtt-topics-best-practices/)


