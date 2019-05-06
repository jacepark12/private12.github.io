---
title: MQTT란?
date: "2019-05-06T22:40:32.169Z"
template: "post"
draft: false
slug: "/posts/mqtt-basic/"
category: "Server Development"
tags:
  - "MQTT"
  - "IoT"
  - "server"
description: "MQTT의 기본 공부하기"
---

MQTT는 M2M, IOT를 위한 프로토콜로서, 최소한의 전력과 패킷량으로 통신하는 프로토콜입니다. 따라서 IOT와 모바일 어플리케이션 등의 통신에 매우 적합한 프로토콜입니다. 

- [ ] MQTT는 HTTP, TCP등의 통신과 같이 클라이언트-서버 구조로 이루어지는 것이 아닌, Broker, Publisher, Subscriber 구조로 이루어집니다. 


![](/media/mqtt-basic/s213hU6yIRQ6qHiYBgip1kg.png)

Publisher는 Topic을 발행(publish) 하고, Subscriber는 Topic에 구독(subscribe)합니다. Broker는 이들을 중계하는 역할을 하며, 단일 Topic에 여러 Subscriber가 구독할 수 있기 때문에, 1:N 통신 구축에도 매우 유용합니다. 

MQTT에서 Topic은 /를 사용해서 구성된다. 
![](/media/mqtt-basic/Example-of-MQTT-topic-tree.png)

따라서 위와 같이 계층을 구성한다면, IOT 센서와 같은 데이터를 관리하기에 매우 용이합니다.

MQTT는 QoS(Quality of Service)를 제공하는데, 총 3단계로 나뉘어져 있습니다. 

* 0 : 메세지는 한번만 전달되며, 전달이후의 수신과정을 체크하지 않는다. 
* 1 : 메세지는 한번 이상 전달되고, 핸드셰이킹 과정을 추적하나, 엄격하게 추적하지 않기 때문에 중복수신의 가능성이 있다.
* 2 : 메세지는 한번만 전달되고, 핸드셰이킹의 모든 과정을 체크한다. 

QoS의 단계가 높아질 수록 통신의 품질은 향상되지만, 그에 따라 성능 저하의 가능성이 있으므로. MQTT의 QoS는 프로젝트의 특성에 따라 결정되어야 합니다.

## MQTT 브로커 구동하기
MQTT 프로토콜을 구현하는 브로커들은 아래와 같이 여러 것들이 있습니다. 

* Mosquitto
* HiveMQ
* mosca
* ActiveMQ
* RabbitMQ (Plug-in 형태로 지원)

그 중에서도 유명한 브로커 중 하나인 Mosquitto를 사용해봅시다. 

간편하게 실행하기 위해 docker로 실행해보도록 하겠습니다!
공식 이미지 링크 : https://hub.docker.com/_/eclipse-mosquitto?tab=description

docker을 실행하고, 아래와 같이 실행합니다.
```
$ docker run -it -p 1883:1883 -p 9001:9001 -v /mosquitto/data -v /mosquitto/log eclipse-mosquitto
```

![](/media/mqtt-basic/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202019-05-03%20%E1%84%8B%E1%85%A9%E1%84%8C%E1%85%A5%E1%86%AB%203.17.31.png)
(도커로 Mosquitto 구동 결과)

MQTT-Explorer 라는 툴을 이용해 topic에 publish하고, Python에서 subscribe하여 데이터를 가져오는 예제를 실행해보도록 하곘습니다. 

[MQTT Explorer | An all-round MQTT client that provides a structured topic overview](https://mqtt-explorer.com)


![](/media/mqtt-basic/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202019-05-03%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%202.15.26.png)
MQTT Explorer로 도커로 구동한 브로커 127.0.0.1:1833에 접속하였고, /test/1 topic에 데이터를 publish합니다.

Python 코드
``` python
import paho.mqtt.client as mqtt

# The callback for when the client receives a CONNACK response from the server.
def on_connect(client, userdata, flags, rc):
    print(“Connected with result code “+str(rc))

    # Subscribing in on_connect() means that if we lose the connection and
    # reconnect then subscriptions will be renewed.
    client.subscribe(“/test/1”) # Topic /seoul/yuokok을 구독한다.

# The callback for when a PUBLISH message is received from the server.
def on_message(client, userdata, msg):
    print(msg.topic+” “+str(msg.payload))

client = mqtt.Client()
client.on_connect = on_connect
client.on_message = on_message

client.connect(“127.0.0.1”) # - 서버 IP ‘테스트를 위해 test.mosquitto.org’로 지정

# Blocking call that processes network traffic, dispatches callbacks and
# handles reconnecting.
# Other loop*() functions are available that give a threaded interface and a
# manual interface.
client.loop_forever()
```

Python에서는 Paho 패키지를 사용하여 MQTT 브로커에 접속할 수 있고, /test/1 topic에 subscribe를 하였습니다. 

![](/media/mqtt-basic/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202019-05-03%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%202.17.11.png)
(MQTT Explorer에서 publish한 결과) 

![](/media/mqtt-basic/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202019-05-03%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%202.17.19.png)
(Python에서 subscribe한 데이터를 가져온 결과)

