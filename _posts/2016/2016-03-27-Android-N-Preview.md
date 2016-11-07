---
layout: post
title: Android N Preview
published: true
---

 Google I/O가 시작되기 전에 안드로이드 N Preview 버전이 공개되었습니다.

 선공개로 인해 개발 대응도 빨라지고, I/O 전에 새로운 기능을 모두 만져볼 수 있는 기회도 생겼습니다. 올해는 I/O가 있기 전에 총 2회에 걸친 Preview가 계획되어 있습니다.

 마지막 한 번은 I/O 때 발표가 되겠지만 그때쯤이면 안정적인 N 버전을 만나볼 수 있으리라고 생각됩니다.

 안드로이드 M을 올리는 방법도 전보다 간단해졌습니다. 오늘은 Android N Preview 설치 방법과 달라지는 부분을 미리 살펴보겠습니다.

 ![Screenshot 2016-03-27 16.54.47.png]({{ site.baseurl }}/images/2016/2016-03-27-Android-N-Preview/Screenshot 2016-03-27 16.54.47.png)

<br />

## Android Preview 일정

Android Preview 일정은 다음과 같습니다.
![Screenshot 2016-03-27 16.58.45.png]({{ site.baseurl }}/images/2016/2016-03-27-Android-N-Preview/Screenshot 2016-03-27 16.58.45.png)
 3월에 첫 Preivew이면서 Alpha 버전이 공개되었습니다. 설정이 가장 많이 변했고, 멀티윈도를 지원합니다. 아직 안정적이지는 않고, API도 완성적이지 않은 정말 Alpha 같은 Preview 버전입니다.

 3월 : Alpha Preview
 4월 : beta
 5월 : beta(Google I/O 예정)
 6월 : final APIs and offical SDK, Pla publishing
 7월 : near-final system images
 3분기 : 정식 버전이 배포됩니다.

 5월 버전부터가 안정성이 가장 높다고 볼 수 있을 것 같습니다. Final API(6월 공개 예정)가 공개되기 직전이기도 합니다. 이후 버전부터는 높은 안정성을 가지고 가겠지만 완성도는 3분기까지 기다려야겠죠.

  생각보다 빠른 배포로 인해 제조사 역시 빠른 대응이 가능할 것이라고 생각됩니다. 최종 배포까지 오래 걸리지 않을 것 같지만 Mashmallow 버전이 업데이트 되는 속도를 보면 최소 6개월까지 걸릴 것이라고 생각됩니다.(초기 모델 제외)

<br />

## Android Preview 지원 단말기

 Android Preview 지원단말기는 아래와 같습니다.
 <br />
 **Phone** : Nexus 6, Nexus 5x, Nexus 6p, Android One
 <br />
 **Tablet** : Nexus 9, Nexus 9(4G), Pixel C
 <br />
 **TV** : Nexus Player

 위의 단말기에서 Android Preview를 살펴볼 수 있습니다.
정식 버전이 업데이트 될 때쯤에도 위의 단말기들만 M이 지원될 것으로 보입니다. Nexus 5, Nexus 7(2013)이 포함될 수도 있겠지만...

<br />

## OTA를 통한 업데이트

이번 버전은 OTA도 지원합니다. Updates over-the-air(OTA)를 지원하다 보니 기존 데이터를 날리지 않고도 올렸다가 내릴 수 있습니다.(rollback도 지원하고 있습니다.) 사용하는데 문제가 많다고 생각되면 언제든 rollback을 통해 기존 버전으로 돌릴 수 있습니다.

<br />

## OTA 방법

[Android N Preivew OTA](https://www.google.com/android/beta)
위의 링크를 통해 OTA를 신청할 수 있습니다. 로그인을 하여야 넘어갈 수 있습니다.

페이지에 접속하면 아래와 같은 단말기를 확인할 수 있습니다. 위에서 적었던 내용의 단말기에서만 OTA를 지원합니다.

![Screenshot 2016-03-27 17.07.41.png]({{ site.baseurl }}/images/2016/2016-03-27-Android-N-Preview/Screenshot 2016-03-27 17.07.41.png)

저는 총 4개의 제품이 Android N Preview를 테스트할 수 있습니다.
3월에 나온 버전은 일반 폰에서는 올리지 말기를 권장합니다.
제가 올렸을 때 동작하지 않는 앱이 더 많았습니다.

Alpha 버전이기에 올리지 않으시는 걸 추천드립니다. 4월에 나오는 beta 버전부터 안정성이 높아지리라고 생각됩니다.

OTA 방법은 ENROLL DEVICE 버튼을 눌러서 동의만 하면 간단합니다. 짧게는 수분 길게는 1일 이상이 소요되었습니다. Nexus 9 에서 등록하였는데 1일 이상이 걸려 OTA가 도착하였습니다.

UNENROLL DEVICE를 눌러서 언제든지 복구가 가능하다고 합니다. 역시 OTA 정도의 기간이 걸릴 수 있습니다.

도착한 OTA는 아래와 같습니다.
 ![2016-03-10 08.43.46.png]({{ site.baseurl }}/images/2016/2016-03-27-Android-N-Preview/2016-03-10 08.43.46.png)

 넥서스 6에서의 용량은 850MB이고 다른 단말기는 1GB가 조금 넘었습니다.
설치 후 기존 데이터가 초기화되지는 않습니다. 언제나 그랬든 동작하지 않는 앱이 있습니다.

<br />

## Devices image를 통한 업데이트

 OTA로 안될 경우에는 Devices image를 통해서 직접 올릴 수 있습니다.
 [Android m device image](http://developer.android.com/preview/download.html)
OTA로 하지 않으실 분은 위의 링크를 가서 zip을 다운로드하신 다음 업데이트 진행하시면 됩니다. 완전 초기화가 진행될 수 있습니다.

 이 방법에 대해서 자세한 설명은 Nexus Factory Image 올리는 방법을 참고하셔서 진행하시면 됩니다.
 [안드로이드 Factory Image를 통한 초기화](http://thdev.net/631)

<br />

## Android N 주요 변경사항

Android M의 주요 변경사항은 아래와 같습니다.
* 업그레이드 된 Doze 모드
  6(Mashmallow)에서는 Doze에 빠져도 CPU/Network를 최소한으로 사용할 수 있게 하였습니다.

 M부터는 2단계 Doze 모드를 사용하게 됩니다.
1단계에서는 네트워크 Job Scheduler, Sync 중지 및 연기되며 2단계 Doze 모드 진입시에는 Wakelock, Alarm, GPS/Wi-Fi 스캔 중지 및 연기가 추가되었습니다. Mashmallow에서 강조하였던 Doze 모드 접근 방법을 잘 지키면 된다고 합니다.
![Screenshot 2016-03-27 17.17.04.png]({{ site.baseurl }}/images/2016/2016-03-27-Android-N-Preview/Screenshot 2016-03-27 17.17.04.png)

* Permissions Changes
  GET_ACCOUNTS가 제거됩니다. 사용자 정보를 불러오던 GET_ACCOUNTS를 더 이상 사용할 수 없게 되었습니다.

  파일 시스템 접근 permission이 추가되었습니다. MODE_WORLD_READABLE and/or MODE_WORLD_WRITEABLE 권한이 추가되었습니다.

* NDK Apps Linking to Platform Libraries
   NDK에서 non-Public API 사용이 금지됩니다.

그 외 내용은 개발 블로그를 참고해주세요.
[Android N behavior-changes](http://developer.android.com/preview/behavior-changes.html)
