---
layout: post
title: Android N 멀티윈도우 - 어떻게 대응해야 할까?
published: true
---

Edit.
Preivew 2가 나오면서 일부 API 이름이 변경되었습니다.

<br />

안드로이드 N부터 Multi window를 지원하게 됩니다.<br />
단순히 Multi window이고 구글 설명상 Activity lifecycle을 그대로 따를 것이라고 합니다.<br />
이번에는 예제를 통해서 이러한 변화가 어떻게 적용되는지 살펴보겠습니다.<br />
<br />
**지난 정리 글 :**
<br />
[Android N Multi Window 살펴보기](http://thdev.tech/Android-N-Preview-Multi-Window/)
<br />
[Android N Multi Window Freeform mode 에뮬레이터에 적용해보기](http://thdev.net/657)

**API 문서**
[Android N Multi Window API](http://developer.android.com/preview/features/multi-window.html)
<br />

## 안드로이드 멀티 윈도우

 멀티 윈도우 적용 시 달라지는 점을 확인하기 위해서 다음과 같은 테스트를 해보았습니다.

 - onResume/onPause 호출 시점
 - 창 크기 변경에 따른 lifecycle
 - 창 크기 변경 - configChange를 적용하였을 경우
 - 전체적인 정리

<br />
**onResume/onPause 호출 시점**<br />
 일반적으로는 Activity가 활성화되고, 비활성화 시에 호출되게 됩니다.<br />
 화면이 완전히 사라진다면 onStop이 호출되겠죠.<br />
 멀티 윈도우가 적용되는 N에서는 onResume/onPause가 생각보다 많이 호출된다고 합니다.<br />
 onResume/onPause가 호출되는 시점을 확인하기 위해서 다음을 테스트해보았습니다.<br />

**다음 테스트 결과는 위쪽에 최신 데이터를 가지게 됩니다.**
<br />

 ![Screenshot_20160404-234814.png]({{ site.baseurl }}/images/2016/2016-04-05-Android-Multi-Window-Example-One/Screenshot_20160404-234814.png)

 정상적으로 앱이 실행되었습니다.<br />
 onCreate -> onStart -> onResume 가 순서대로 동작합니다.<br />
 <br />
 Multi Window 간 화면 전환을 시도하였을 때 다음과 같은 동작이 추가되었습니다.<br />
 onPause -> onResume -> onPause -> ...<br />
 onResume(활성화) 상태에서 onPause(비활성화) 상태로의 변환이 계속적으로 일어남을 확인할 수 있습니다.<br />
 <br />

이를 그림으로 그리면 다음과 같습니다.<br />
창간의 전환이 일어나게 되면 빨간 부분의 onResume / onPause 가 계속적으로 호출되게 됩니다.<br />
특히나 동영상의 onPause에서 동영상 일시 정지를 하게 되는 경우가 많을 것 같습니다.<br />
이 경우에는 꼭 onPause가 아니라 onStop으로 이동이 필요하다고 생각됩니다.([구글 팁의 lifecycle에서 자세하게 나옵니다.](http://developer.android.com/preview/features/multi-window.html#lifecycle))

 ![Screen Shot 2016-04-05 at 2.13.03 PM.png]({{ site.baseurl }}/images/2016/2016-04-05-Android-Multi-Window-Example-One/Screen Shot 2016-04-05 at 2.13.03 PM.png)

 <br />
 이로 인해 onResume, onPause 간에서는 동작을 최소한으로 줄이는 노력이 필요해 보입니다.

 <br />
 **창 크기 변경에 따른 lifecycle**<br />
 창의 크기를 변경할 수 있습니다.<br />
 분활 된 창의 가운데 부분을 왼쪽/오른쪽, 상/하로 이동하게 되면 창이 커지고 줄어들게 됩니다.<br />
 이때의 창의 크기가 변하는데 실제 lifecycle은 어떻게 동작하는지 살펴보겠습니다.<br />

 ![Screenshot_20160404-235109.png]({{ site.baseurl }}/images/2016/2016-04-05-Android-Multi-Window-Example-One/Screenshot_20160404-235109.png)

 별다른 것 없어 보입니다. 실제로는 다음을 무한 루프 돌게 됩니다.<br />
 onCreate -> onStart -> onResume<br />
 <br />
 onResume 상태를 기준으로 전체적으로 다시 보게 되면<br />
 onResume -> onPause(창 크기 변경 시작) -> onStop -> onDestroy -> onCreate(창 크기 변경 종료) -> onStart -> onResume<br />
 <br />
 앱이 onCreate부터 처음부터 다시 시작하고 있습니다.<br />
 이동 중에는 아래의 flowchart처럼 동작하게 됩니다.

 ![Screen Shot 2016-04-05 at 2.25.10 PM.png]({{ site.baseurl }}/images/2016/2016-04-05-Android-Multi-Window-Example-One/Screen Shot 2016-04-05 at 2.25.10 PM.png)

 다시 정리하면 Android N 대응을 하지 않을 경우에는 onDestroy -> onCreate가 계속적으로 동작하게 됩니다.<br />

 <br />
 **창 크기 변경 - configChange를 적용하였을 경우**<br />
  [5 tips for preparing for Multi-Window in Android N](https://medium.com/google-developers/5-tips-for-preparing-for-multi-window-in-android-n-7bed803dda64#.x5dd4ku7n)에서 보았던 configChanges 코드를 적용해보겠습니다.<br />
  <br />
  configChanges를 적용하였을 경우 lifeCycle이 어떻게 변화되는지 살펴보겠습니다.<br />
  우선 xml에 다음의 코드를 추가하였습니다.<br />

```xml
android:configChanges="screenSize|smallestScreenSize|screenLayout|orientation"
```

java 코드도 추가했습니다.

```java
@Override
public void onConfigurationChanged(Configuration newConfig) {
    super.onConfigurationChanged(newConfig);

    // ...

}
```

 ![Screenshot_20160405-000516.png]({{ site.baseurl }}/images/2016/2016-04-05-Android-Multi-Window-Example-One/Screenshot_20160405-000516.png)


바로 위에서 보았던 이미지와는 많이 다름을 확인할 수 있습니다.<br />
크게 onPause -> onDestroy 가 호출되지 않았습니다.<br />
코드 상으로 onConfigurationChanged가 호출이 되고 있습니다.<br />
그림 상으로는 아래와 같이 표현 할 수 있겠습니다.

![Screen Shot 2016-04-05 at 2.34.57 PM.png]({{ site.baseurl }}/images/2016/2016-04-05-Android-Multi-Window-Example-One/Screen Shot 2016-04-05 at 2.34.57 PM.png)

그리고 숫자 1이 표시되고 있는데 이 값은 API의 [Configuration API 문서](http://developer.android.com/reference/android/content/res/Configuration.html)<br />

```java
Configuration.SCREENLAYOUT_SIZE_SMALL
```

 화면이 축소된 상태이므로 size small이 호출됩니다.<br />
<br />

 **전체적인 정리**<br />
 내용을 정리해보겠습니다.<br />
 멀티윈도우 간 화면 전환 시 호출되는 순서<br />
 onPause -> onResume -> onPause -> ...<br />
 <br />
 Android N 대응 존 앱에서 창 사이즈를 변경 시<br />
 onPause -> onStop -> onDestroy -> onCreate -> onStart -> onResume<br />
 이 경우에는 전체적인 lifeCycle이 다시 동작하게 됩니다.<br />
 <br />
 configure 설정을 하였을 경우<br />
 onResume인 상태에서 -> onConfigurationChanged<br />
 onConfigurationChanged만 호출되게 됩니다.<br />
 <br />

 창 사이즈가 줄어들기 전과 복구된 상태에서는 onConfigurationChanged에서 다음의 코드가 호출됩니다.<br />

```java
Configuration.SCREENLAYOUT_SIZE_NORMAL
Configuration.SCREENLAYOUT_SIZE_SMALL
```

 Size가 변경이 일어나게 되면 Normal -> small 이 호출됩니다.<br />
 inMultiWindow 상태도 false -> true로 변경되게 됩니다.<br />

![Screenshot_20160405-005032.png]({{ site.baseurl }}/images/2016/2016-04-05-Android-Multi-Window-Example-One/Screenshot_20160405-005032.png)


<br />

## 주요 코드

 xml에서는 다음의 코드를 적용하였습니다.<br />

  ***참고 : 반대로 동작하지 않을 경우***
  screenOrientation을 강제로 지정하는 경우(가로 고정/세로 고정)에는 Multi window가 동작하지 않습니다.

```xml
<!-- Multi window support example -->
<activity
    android:name=".multiwindow.MultiWindowActivity"
    android:resizeableActivity="true"
    android:configChanges="screenSize|smallestScreenSize|screenLayout|orientation"
    android:theme="@style/AppTheme.NoActionBar" />

<!-- Multi window freeform mode example -->
<!-- 테스트 하려고 넣었지만 실제 동작하지 않네요 -->
<activity
    android:name=".multiwindow.FreeformModeActivity"
    android:resizeableActivity="true"
    android:theme="@style/AppTheme.NoActionBar">
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

<br />

## Java 코드에서는

Activity 기반에서는 다음의 코드를 통해 Multi window 상태를 알 수 있습니다.<br />
아래 2개 코드가 존재하고 있는데 boolean 값을 return 해주고 있습니다.<br />

```java
/*
 * 멀티 윈도우 상태를 가져옵니다.
 */
// Preview 2 API 이름 변경
// boolean inMultiWindow()
boolean isInMultiWindowMode()
/*
 * 멀티 윈도우 상태를 변경합니다.
 * true -> 멀티윈도우로 전환합니다.
 * false -> 멀티윈도우에서 빠져나옵니다.
 */
// Preview 2 API 이름 변경
// void onMultiWindowChanged(boolean isMultiWindow)
void onMultiWindowModeChanged(boolean isMultiWindow)
```

제가 테스트 한 코드는 아래와 같습니다.

```java
multiWindowAdapter.addItem("onResume() isInMultiWindowMode " + isInMultiWindowMode(), true);
```

<br />

## 마무리

 멀티 윈도우에 대해서 전반적인 내용을 살펴보았습니다.<br />
 API 문서를 정리하였고, Freeform 모드를 적용해보기도 하였습니다.
<br />
 [Android N Multi Window 살펴보기](http://thdev.tech/Android-N-Preview-Multi-Window/)
<br />
 [Android N Multi Window Freeform mode 에뮬레이터에 적용해보기](http://thdev.net/657)
<br />
<br />
onResume/onPause와 configChanges에 대한 대응은 꼭 필요하다고 생각됩니다.<br />
configChanges를 적용하지 않으면 앱의 LifeCycle가 처음부터 다시 동작하게 됩니다.<br />
그것도.. 실시간으로 일어나게 되겠습니다. 손을 놓을 때마다...
<br />
<br />
다음의 문서는 정말 중요한 문서라고 생각됩니다.<br />
이 문서의 내용을 꼭 습득하시기 바랍니다.<br />
 [5 tips for preparing for Multi-Window in Android N](https://medium.com/google-developers/5-tips-for-preparing-for-multi-window-in-android-n-7bed803dda64#.x5dd4ku7n)

<br />

## 테스트 코드 주소

Preview 2. Android Studio 2.1 Beta에 맞게 수정한 내용을 적용하였습니다.

[Multi window example code - GitHub](https://github.com/taehwandev/BlogExample/tree/master/Android/2016-04-05-N-Preview-MultiWindow)
