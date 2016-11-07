---
layout: post
title: Android N Preview 멀티 윈도우 살펴보기
published: true
---

Edit. 2016. 04. 14
Preview 2 버전이 나오면서 변경된 사항을 업데이트 합니다.

 - 일부 API 이름 변경

<br />

안드로이드 N에서 정식으로 Multi Window를 제공합니다. N에 숨겨진 멀티 윈도 기능도 있는데 그 기능을 사용하는 방법도 함께 적어보겠습니다.

Google [lan Lake](https://medium.com/@ianhlake)가 medium에 작성한 "[5 tips for preparing for Multi-Window in Android N](https://medium.com/google-developers/5-tips-for-preparing-for-multi-window-in-android-n-7bed803dda64#.x5dd4ku7n)"
을 참고하시면 좋을 것 같습니다. 저는 주요 코드와 설명을 작성하도록 하겠습니다.(5가지 팁 중에 중요한 내용 포함...)

<br />

## Android N Multi Window

Android N에 포함된 Multi Window는 Andoid M에도 있었습니다. 초기 버전에만 존재하는데 Nexus 6p에서만 확인이 되었던 적이 있습니다. 개발자 옵션에 잠시 포함되었다가 11월 패치쯤에 해당 기능이 사라졌었습니다.

동작 방식은 태스크에 표시된 미리 보기 창에 멀티윈도우 옵션을 제공하고, 이를 동작하는 정도였습니다.

이러한 기능이 좀 더 안정적으로 Android N에 정식으로 포함되었습니다. Android N에서는 폰과 Tablet에 제공하게 되는데 Tablet에서는 좀 더 PC처럼 사용할 수 있는 기능이 OFF로 숨겨져 있습니다. ***(이 기능은 아래쪽에 설명하도록 하겠습니다.)***

<br />

## Multi Window Support

Preview 문서 : [Multi Window Support](http://developer.android.com/preview/features/multi-window.html)

**스마트폰** : 스마트폰에서는 상/하 모드로 Split-Screen mode를 지원합니다.
구글 문서에 나와 있는 사진입니다. 아래와 같이 화면 분할이 되고, 기존 Overview screen(태스크?) 모양이 2개의 네모로 분리됩니다.
![Screenshot 2016-03-28 22.18.59.png]({{ site.baseurl }}/images/2016/2016-03-29-Android-N-Preview-Multi-Window/Screenshot 2016-03-28 22.18.59.png)

Tablet은 별도의 설명이 없습니다. 기본 기능은 모두 스마트폰과 동일하게 동작합니다. 다만 태블릿에서는 화면 회전을 지원하지 않을 경우 분할 모드를 지원하지 않습니다.

SDK를 통해 멀티윈도우를 제어할 수 있습니다. 창의 최소 사이즈를 정할 수 있고, 멀티 윈도우를 사용하지 않도록 지정할 수 있습니다.

<br />

**TV Device** : Picture-in-picture(PIP) 모드를 지원합니다.

TV Device에서 사용할 수 있는 Picture-in-picture(PIP)는 영상을 시청 중에 다른 행동을 할 수 있습니다. 영상을 틀어놓고 사진을 볼 수 있는데 일반적인 TV/모니터의 PIP 기능이라고 볼 수 있겠습니다.(팝업 플레이어? 정도라고 생각할 수도 있겠네요)
[Picture-in-picture](http://developer.android.com/preview/features/picture-in-picture.html)
구글 문서에 나와있는 사진입니다.
![Screenshot 2016-03-28 22.17.54.png]({{ site.baseurl }}/images/2016/2016-03-29-Android-N-Preview-Multi-Window/Screenshot 2016-03-28 22.17.54.png)

<br />

## Activity Lifecycle

Multi-window mode를 지원한다고 해서 별도로 달라지는 Lifecycle은 없다고 합니다. 기본 [Android Activity의 Lifecycle](http://developer.android.com/training/basics/activity-lifecycle/index.html)을 따른다고 합니다.

그래서 현재 동영상을 시청하는데 있어서 멀티윈도우가 정상 동작하지 않습니다. 멀티윈도우 상태에서 동영상을 시청하려고 틀어놓고 다른 행동을 하려고 시도하였는데 이렇게 동작하였습니다.(문서상 내용도 비슷합니다.)

- 동영상 APP을 실행하여 동영상을 플레이합니다.
- 멀티 윈도우의 다른 화면을 눌러줍니다.
- 동영상은 재생이 멈춥니다.

하나씩 Android Lifecycle대로 설명하면 다음과 같습니다.
- 동영상을 정상으로 Player 하였습니다.(onResume 상태입니다.)
- **멀티윈도우의 다른 화면을 눌러줍니다.** 이때 동영상의 화면은 onPause()를 타게 됩니다.
- 선택한 다른 화면이 onResume()를 타고 정상적으로 사용 가능해집니다.

**Google Note.**

화면이 보이기 때문에 onStop()이 불리지는 않습니다. 문서의 Note에는 다음과 같은 Tip을 제공하고 있습니다.
- Player 하기 위해서 onPause()에서는 일시정지/정지 하지 않도록 한다.
- onStop()으로 일시정지/정지하도록 한다

위와 같이 처리해주면 onPause()가 되더라도 동영상 플레이는 유지되고, 화면이 보이는 상태이기 때문에 onStop()이 호출되지는 않습니다.
다시 돌아왔을 때도 onResume()에서 동영상 재생을 하지 않고, onStart()에서만 start를 하도록 해주면 onPause와 onResume이 왔다 갔다 하는 상항에서도 문제없이 플레이가 가능해집니다.

아직은.. Google App도 이렇게 동작하지 않아서 확인은 불가능합니다.. 저도 아직 직접 확인해보지 않은 내용이라서... 일단 문서상...

[Handling Runtime Changes](http://developer.android.com/guide/topics/resources/runtime-changes.html)에 관한 내용도 담고 있습니다. 자세한 내용은 문서를 참조해주세요.

<br />

## Configuring Your App for Multi-Window Mode

Multi-Window 모드는 개발자가 직접 컨트롤 할 수 있습니다. 멀티윈도우를 지원하지 않을 수도 있고(현재 Preview에서는 모두 다 지원하도록 되어 있어서 정식 버전에 어떻게 적용될지 아직은 모르겠습니다.) 지원하면서 최소 사이즈를 지정할 수 있습니다.

AndroidManifest.xml에서 다음을 정의하면 Multi-Window를 지원/해제 할 수 있습니다.

```java
android:resizeableActivity=["true" | "false"]
```

이 코드를 `<actiity>` 또는 `<application>`에 각각 넣을 수 있습니다.
application이 false 이면 해당 앱은 false가 기본으로 처리되어 멀티 윈도우가 동작하지 않습니다.
activity 기반으로도 각각 페이지별 true/false를 적용하게 되면 동작이 다르게 되는 것이죠.

그래서 저는 이런 방법을 시도해보았습니다.
- A Activity에서는 true
- B Activity에서는 false

아직 Preview라서 그런지 첫 번째 Activity에 맞게 B Activity가 동작을 하였습니다. 다시 말하면 B는 true/false이 든 상관없이 동작하였습니다.

저런 식의 화면 구성을 하고 싶어 하시는 분이 있을 것 같습니다. 특정 화면은 집중을 요해서 전체 화면으로 구성을 하고 싶은 경우입니다. 그런데 이 경우는 별로 추천하지는 않을 것 같지만.. 현재 `<activity>`에 true/false를 지원하는 것을 보아 가능할 것 같습니다.

Picture-in-picture 역시 아래와 같습니다.

```java
android:supportsPictureInPicture=["true" | "false"]
```

**화면 사이즈 정의**

화면 사이즈 구성이 가능합니다. 아래와 같은 설정이 가능합니다.
Google 문서상 freeform mode라는 이름으로 다음을 정의하고 있습니다.

```xml
android:defaultWidth
android:defaultHeight
android:gravity
android:minimalHeight
android:minimalWidth

<!-- Preview 2 change -->
<!-- Remove API Name : android:minimalSize -->
<!-- Add API Name : android:minimalWidth -->
<!-- Add API Name : android:minimalHeight -->
```

AndroidManifest.xml에 다음과 같이 정의합니다.

```xml
<activity android:name=".MyActivity">
   <layout
       android:defaultHeight="500dp"
       android:defaultWidth="600dp"
       android:gravity="top|end"
       android:minimalHeight="450dp"
       android:minimalWidth="300dp" />

   <!-- Preview 2 change -->
   <!-- Remove API Name : android:minimalSize -->
   <!-- Add API Name : android:minimalWidth -->
   <!-- Add API Name : android:minimalHeight -->
</activity>
```

freeform mode는 화면 자체를 자유롭게 이동할 수 있는 모드입니다. 사실 이 옵션(freeform mode)은 OFF되어 있습니다. xml 정의는 제공하고 있는 것 같습니다. 이 모드는 아래에서 설명을 하겠습니다.

<br />

## Disable features in multi-window mode

다음의 경우는 Multi-window를 지원하지 않습니다. 저도 위에서 언급하였는데 screenOrientation을 설정하였을 경우에는 multi-window를 제공하지 않는다고 합니다.

<br />

## Multi-window change notification and querying

 Multi-window 모드 API가 있습니다. 저도 아직 자세하게 알아보지는 않았는데 API 문서 링크는 아래와 같습니다.
 [Android N Preview API](http://developer.android.com/preview/setup-sdk.html#docs-dl)

현재 모드가 멀티 윈도우인지 확인할 수 있습니다.

```java
// Preview 2 API 이름 변경
// Activity.inMultiWindow()
Activity.isInMultiWindowMode()
```

<br />

PictureInPicutre 모드인지 확인합니다.
**Note**: isInPictureInPictureMode() 모드가 true라면 isInMultiWindowMode() 역시 true가 return 된다고 합니다.

```java
// Preview 2 API 이름 변경
// Activity.inPictureInPicture()
Activity.isInPictureInPictureMode()
```

<br />

true 일 경우 현재 MultiWindow 모드가 활성화 되고, false라면 MultiWindow에서 일반 모드로 변경됩니다.

```java
// Preview 2 API 이름 변경
// Activity.onMultiWindowChanged()
Activity.onMultiWindowModeChanged()
```

<br />

onMultiWindowChanged와 기본 동작은 동일합니다.

```java
// Preview 2 API 이름 변경
// Activity.onPictureInPictureChanged()
Activity.onPictureInPictureModeChanged()
```

추가로 Fragment에서도 동일하며 Fragment.isInMultiWindowMode()입니다.

<br />

## Etc

// Flag 이름 변경 FLAG_ACTIVITY_LAUNCH_ADJACENT -> FLAG_ACTIVITY_LAUNCH_TO_ADJACENT
앱을 실행과 동시에 Multi-Window 모드를 사용할 수 있습니다. Intent.FLAG_ACTIVITY_LAUNCH_TO_ADJACENT 을 통해서 Multi-window를 실행할 수 있습니다. [자세한 내용은 API를 참고해주세요.](http://developer.android.com/preview/features/multi-window.html)

<br />

## drag and drop 모드 지원

 삼성 멀티윈도우와 같은 파일 복사? 기능을 제공합니다. Multi-window 간 퍼미션을 요청하여 URI을 전달하는 drag and drop 모드입니다.

<br />

## Tip 5

[5 tips for preparing for Multi-Window in Android N](https://medium.com/google-developers/5-tips-for-preparing-for-multi-window-in-android-n-7bed803dda64#.x5dd4ku7n)
에 소개되어 있는 5개 Tip 중에 가장 중요하다고 생각되는 부분을 소개하려고 합니다.
나머지 내용은 원문을 참고해주세요^^;;<br />
Pro-tip 1 번에 해당하는 내용입니다. Google에서도 다음과 같이 하라고 하더군요.<br />

>Loading the proper resources requires the proper Context. If you’re using the Activity context for inflating your layouts, retrieving resources, etc, then you’re good to go.
However, if you’re using your Application context for anything UI related, you’ll find that the resources that are loaded are blissfully unaware of multi-window. Besides issues with not using your Activity’s theme, you may be loading the wrong resources entirely! Best to keep your UI stuff with the Activity Context.

원문은 위와 같습니다.
Multi-window의 지원으로 주의해야 할 부분이 context 사용 방법입니다. UI를 다룰 경우에는 Application context가 아닌 Activity의 context를 사용하라고 합니다. Application context를 사용하였을 경우 Activity 기반의 theme가 아니라 원하지 않는 theme가 나올 수 있으니 Application context가 아니라 Activity context을 사용하라고 합니다.

onResume()에서 너무 많은 처리를 하지 말 것을 강조합니다. 화면의 이동 간 onResume() onPause()는 매번 호출되게 됩니다. 멀티윈도우 사이즈가 변경되었을 때도 onResume()이 호출될 수 있으니 아래의 코드를 사용하여 데이터 처리를 하도록 하는 게 좋다고 합니다.

```xml
<activity
 android:name=".MyActivity"
 android:configChanges="screenSize|smallestScreenSize
     |screenLayout|orientation"
/>
```

<br />

## Multi-window의 숨겨진 기능

[ganachoco(Young-Ho Cha)님이 Facebook GDG Korea Android](https://www.facebook.com/groups/gdg.korea.android/permalink/1747068618855262/)(가입 필요)에 공유해주신 FreeForm Window 기능을 활성화하는 방법입니다. 위에 API 상으로는 FreeForm이라는 이름이 계속적으로 나오지만 실제 동작은 어떻게 되는지는 알 수 없었습니다. 다음의 방법을 통하면 아래 Remix OS와 같은 화면을 Android N Preview에서도 볼 수 있게 됩니다.

Freeform mode는 [Remix OS](http://www.jide.com/en/remixos)와 유사하게 보입니다.
Remix OS의 멀티 윈도우
![Screenshot 2016-03-28 23.33.03.png]({{ site.baseurl }}/images/2016/2016-03-29-Android-N-Preview-Multi-Window/Screenshot 2016-03-28 23.33.03.png)

다음 글에 Android N Feature FreeForm을 활성화 하는 방법을 담고 있습니다.
자세한 설명은 다음글을 참고하시면 되고, 적용 방법은 크게 2가지가 있습니다.
실물 기기에서는 TWRP 등의 커스텀롬 리커버리 모드를 이용하시거나
에뮬레이터를 통한 적용 방법입니다.
[Android N Feature FreeForm Window](http://www.androidpolice.com/2016/03/21/android-n-feature-spotlight-freeform-window-mode-offers-true-windowed-multitasking-but-its-disabled-by-default/)

Android N의 FreeForm Window 모드 적용 모습(androidpolice에서 따온 이미지입니다.)
![Screenshot 2016-03-28 23.35.50.png]({{ site.baseurl }}/images/2016/2016-03-29-Android-N-Preview-Multi-Window/Screenshot 2016-03-28 23.35.50.png)

<br />

## 마무리

Android N Preview의 멀티 윈도우를 살펴보았습니다. 멀티 윈도우에 동일한 UI를 적용하기 위해서는 Application Context가 아닌 Activity Context를 이용할 것과, 멀티 윈도우 지원 조건을 살펴보았습니다.

짧은 영어 실력으로 다 변환하지는 못하였고... 제가 이해한 부분만 글로 담았습니다. 자세한 내용은 원문 글을(당연하지만) 참고해주시는 게 더 좋을듯합니다...^^;;;

언제 노출될지 모르는 FreeForm Window는 이미 API는 모두 지원하는 듯합니다. 10인치 이상의 태블릿에 이 모드가 적용되거나, 차후 태블릿에서 이 모드가 적용될 것이라고 생각됩니다.(제조사는 별도...)

실제 테스트했던 부분은 문서상 적용 안되는 경우에 해당이 되어서... 별로 한건 없네요. 크게 변경되지 않고 Multi-Window를 지원받을 수 있으리라고 생각되는데 정식 버전에서도 기본 값이 true 인지 false가 될지는 아직 알 수 없어 보입니다. 하지만 true가 기본일 것 같은...

true가 기본이라면 아이패드보다 더 많은 멀티 윈도우를 맛볼 수 있으리라고 생각되며, 그에 따른 동영상 재생 문제도 해결을 위해서 onPause()에서 stop() 코드를 제거하는 게 좋을 것 같습니다.(안정성 테스트는 해야겠죠...)


## MultiWindow 다루기

MultiWindow를 적용하는데 필요한 내용을 정리한 글입니다.
[Android N 멀티윈도우 - 어떻게 대응해야 할까?](http://thdev.tech/Android-Multi-Window-Example-One/)
