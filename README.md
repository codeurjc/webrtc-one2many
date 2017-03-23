[![][CodeUrjc Logo]][CodeUrjc]

Copyright © 2017 [CodeUrjc]. Licensed under [Apache 2.0 License].

webrtc-one2many
===============

This is a benchmark application aimed to assess the performance of Kurento Media Server. It has been implemented using two main components:

* *Spring-Boot* : it allows to create stand-alone Spring based web applications
* *KurentoClient* : Java client to control Kurento Media Server (KMS)

The application has beed designed as a WebRTC video broadcasting, i.e. a WebRTC-compliant browser (e.g. Chrome, Firefox) acting as presenter sends its user media to N viewers. One of those viewers is required to be another real WebRTC-compliant browser. The main feature implemented in this application is that the rest of N-1 viewers are going to be *fake*. Instead of more real browsers acting as viewers in the video broadcasting, internaly the application uses another instance of KMS to add more and more *fake clients*. This is done simply adding new *WebRtcEndpoints* (KMS media elements implementing the WebRTC stack) to the WebRTC session. This allows to scale a KMS benchmark very easily. For that reason, the application consumes two KMS URLs:

| Parameter                   | Default Value                | Description                                                                                                                                                                                                                       |
|-----------------------------|------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| kms.ws.uri                  | ws://127.0.0.1:8888/kurento  | KMS (under test) WebSocket URL                                                                                                                                                                                                    |
| fake.kms.ws.uri             | ws://127.0.0.1:8888/kurento  | KMS (for fake clients) websocket URL. Several instances of KMS can be used for fake clients, simply separating the URLs with the symbol ",", e.g: fake.kms.ws.uri=ws://192.168.0.101:8888/kurento,ws://192.168.0.102:8888/kurento |

Therefore, we can start the application using the following command. Typically we are going to use at least two different KMS's (one for the KMS under test, another for supporting the *fake clients*):

```bash
mvn spring-boot:run -Dkms.ws.uri=ws://192.168.0.100:8888/kurento -Dfake.kms.ws.uri=ws://192.168.0.101:8888/kurento
```

The application GUI has different configuration parameters to tune the behavior of the application.

- *Session number*: The app can ben **multi-session**, meaning that there can be different simultaneous sessions. Each session is identified by the field *Session number* in the GUI.
- *Bandwidth (kbps)*: Bandwidth to configure all the WebRtcEndpoints elements handled by the app.
- *Number of fake clients*: As depicted before, this application supports **fake clients**. It has been designed to be consumed always by two real browsers per session: one acting as presenter and other acting as viewer. This is the case of a one2one video call. In order to convert the session in a one2many, the GUI parameter *Number of fake clients* (in GUI) should be greater that 0. In that case, the application becomes in a one2many, in which N-1 viewers are fake (i.e., they are not browsers consuming the media, but WebRtcEndpoint provided by another instance of KMS). If this value is zero, the application behaves as a **one2one** or **one2many** WebRTC video call. All in all, and depending how the GUI is configured, the topology of the application is one of the following:

![](https://raw.githubusercontent.com/codeurjc/webrtc-one2many/master/src/main/resources/static/img/topology.png)

- *Rate between clients (milliseconds)*: Time among a fake viewer and the next one.
- *Remove fake clients*: Boolean value that indicates whether or not the fake clients should be removed after a given time (next value).
- *Time with all fake clients together (seconds)*: If the previous value is true, this field sets the time in which all the fake clients are consuming media. When this time expires, the fake clients are removed using the same shrinking time used for inclusion.
- *Number of fake clients per KMS instance*: We can use several KMS URL to create fake clients. As depicted in the tabla before, each URL is separated by `,`. This value is useful to establish when use a new KMS (i.e., create a new KurentoClient) for fake viewers. The following commands use two KMS's for generating fake clients: 

```bash
mvn spring-boot:run -Dkms.ws.uri=ws://192.168.0.100:8888/kurento -Dfake.kms.ws.uri=ws://192.168.0.101:8888/kurento,ws://192.168.0.102:8888/kurento
```

Once the application is up and running, we should click on the *Presenter* button to start the WebRTC session. In another browser, we should click on the *Viewer* button to connect the real viewers and also all the fake clients as configured in the field *Number of fake clients* (by default this value is 10). During the time of the session (i.e. until we click on *Stop*) we can monitor the machine hosting the KMS under test and so on.

News
----

Follow us on Twitter @[CodeUrjc Twitter].

Licensing and distribution
--------------------------

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

  http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.


[Apache 2.0 License]: http://www.apache.org/licenses/LICENSE-2.0
[CodeUrjc Logo]: https://raw.githubusercontent.com/codeurjc/webrtc-one2many/master/src/main/resources/static/img/code.png
[CodeUrjc Twitter]: https://twitter.com/codeurjc
[CodeUrjc]: http://www.code.etsii.urjc.es/
