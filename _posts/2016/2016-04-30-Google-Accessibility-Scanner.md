---
layout: post
title: Google MediaProjection을 이용한 Accessibility Scanner
published: true
---

최근 Play store에 구글의 새로운 앱이 하나 등록되었습니다.

Android MediaProjection을 이용하여 개발한 Accessibility Scanner입니다.

MediaProjection으로 화면을 캡쳐하고 이를 분석해 일부 UI의 속성 정보를 분석하여 제공합니다.

일부 UI를 분석하여 보여주는데 Button, TextView 등이 해당됩니다. 많은 UI를 분석해주지는 못하더군요.

<br />

## 다운로드
[Accessibility Scanner](https://play.google.com/store/apps/details?id=com.google.android.apps.accessibility.auditor)

<br />

## 어떻게 동작할까?

간단하게 동작 방식이 어떨지 .. 제가 아는 한도에서 정리해보면

- 시스템의 스크린을 캡쳐한다.(MediaProjection API를 이용합니다)
- UI 상 Button, TextView의 UI 정보를 분석합니다.
- 분석한 내용을 출력해주는데 텍스트 사이즈, 색상 정보 등을 확인할 수 있습니다.

<br />

## MediaProjection

[MediaProjection 살표보기](http://thdev.tech/Android-MediaProjection-Exmple/)

위의 글을 보시면 되겠지만 MediaProjection은 다음과 같은 환경에서 동작합니다.

- Android 5.0(Lollipop) - API 21 이상
- 실시간으로 화면을 가져오는데 디스플레이에 출력되는 FPS(현재 기기상 최대 60FPS) 만큼을 가져올 수 있습니다.

동작하지 않는 경우 :

- 안드로이드 5.0 이상을 강제로 올린 기기(커스텀 롬)
- 제조사 기기에서 정상 동작하지 않을 수 있음(CTS 통과된 기기라면 문제없습니다.)

<br />

## Scanner

구글에서 만든 Scanner을 살펴보겠습니다.

MediaProjection을 사용하였고, Accessibility(접근성)에 등록되어 사용이 가능한 앱입니다.

실행하면 다음과 같은 화면이 표시됩니다.

바로 접근성에 등록이 되며, 접근성에서 Accessibility Scanner을 활성화해주어야 합니다.

![2016-04-17 05.32.36-1]({{ site.baseurl }}/images/2016/2016-04-30-Google-Accessibility-Scanner/2016-04-17 05.32.36-1.png)

<br />

최초에는 다음과 같이 알림 팝업이 뜨지만 이후에는 다음으로 접근하여 활성화해주어야 합니다.

- 시스템 설정 -> 접근성(Accessibility) -> Accessibility Scanner On/OFF 활성화

아래와 같이 Off 상태에서 On를 전환해주시면 됩니다.

![2016-04-17 05.32.36-2]({{ site.baseurl }}/images/2016/2016-04-30-Google-Accessibility-Scanner/2016-04-17 05.32.36-2.png)

<br />

접근성 On이 완료되면 MediaProjection을 사용할 수 있도록 사용자 권한을 획득해야 합니다.

아래와 같이 start capture... 창에서 시작을 눌러주면 됩니다.

![2016-04-17 05.33.01]({{ site.baseurl }}/images/2016/2016-04-30-Google-Accessibility-Scanner/2016-04-17 05.33.01.png)

<br />

최초 실행 시에는 다음과 같은 화면을 볼 수 있습니다.

스캔이 완료된 목록이 차곡차곡 쌓이게 될 겁니다.

최초 앱 실행 후 이 화면을 다시 열더라도 스캔을 다시 시작할 수 있는 창은 제공되지 않고, 완료된 목록을 볼 수 있습니다.

![2016-04-17 05.33.09]({{ site.baseurl }}/images/2016/2016-04-30-Google-Accessibility-Scanner/2016-04-17 05.33.09.png)

<br />

실행된 Scanner은 아래와 같이 파란색 체크 버튼 하나가 생기게 됩니다.

길게 눌러야 이동이 가능하고, 터치하면 실행이 되게 됩니다.

종료/시작이 쉽지는 않습니다. 접근성을 통해서 시작/종료를 할 수 있으니깐요.

![2016-04-17 05.33.41]({{ site.baseurl }}/images/2016/2016-04-30-Google-Accessibility-Scanner/2016-04-17 05.33.41.png)

<br />

캡쳐는 아래와 같이 진행됩니다.

구글 CTO 테스트 중에는 MediaProjection을 이용하여 Image로 저장하는 예제가 담겨 있습니다.

이를 이용하면 다음과 같은 화면 구성이 가능합니다.

다만 UI 분석이... 어렵겠죠...

![2016-04-17 05.33.44]({{ site.baseurl }}/images/2016/2016-04-30-Google-Accessibility-Scanner/2016-04-17 05.33.44.png)

UI 분석이 완료되면 다음과 같은 화면을 볼 수 있습니다.

2개를 합쳐두었는데요. 왼쪽과 같이 특정 화면 UI 정보를 볼수도 있고, 오른쪽처럼 전체의 목록을 한 번에 볼 수도 있습니다.

처음에 말씀드렸듯이 모든 화면의 UI를 분석해주지는 못하고, 일부 UI의 화면을 분석합니다.

아직은... 분석하는데 수준이 낮은 것이죠.

![2016-04-17 05.34.29]({{ site.baseurl }}/images/2016/2016-04-30-Google-Accessibility-Scanner/2016-04-17 05.34.29.png)

<br />

## 마무리

그래서 어느 정도를 분석할까요?

예제를 하나 만들어서 돌려보았습니다.

그냥 이 정도로만 보여주네요. 예제라서 그런지 버튼은 찾아주지 못하고 있습니다.

![device-2016-04-30-114740]({{ site.baseurl }}/images/2016/2016-04-30-Google-Accessibility-Scanner/device-2016-04-30-114740.png)


**시작과 종료**

처음에 실행 시에 표시되는 Scanner On/Off 하는 방법입니다. 이 툴팁은 최초에 한 번만 실행됩니다.

설정 > 접근성 > Accessibility Scanner 메뉴로 접근하여 On/Off 할 수 있습니다.

![2016-04-17 05.33.14]({{ site.baseurl }}/images/2016/2016-04-30-Google-Accessibility-Scanner/2016-04-17 05.33.14.png)


시작과 종료가 불편하네요.

구글이 MediaProjection을 이용하여 만들었다는 점이 중요한것이니...

Android Studio의 Device Manager의 dump를 이용하여 UI를 분석할 수 있습니다.

![Screenshot 2016-04-30 11.38.33]({{ site.baseurl }}/images/2016/2016-04-30-Google-Accessibility-Scanner/Screenshot 2016-04-30 11.38.33.png)

<br />

## 다운로드
[Accessibility Scanner](https://play.google.com/store/apps/details?id=com.google.android.apps.accessibility.auditor)
