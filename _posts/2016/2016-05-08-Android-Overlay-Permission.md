---
layout: post
title: Android 윈도우(Overlay) 권한 획득하기
published: true
---

Android 6.0부터는 Overlay(윈도우 레이아웃) 사용을 권장하지 않고 있으며, 권한 획득을 요구하지 않을 경우 정상적인 Window(Overlay)을 사용할 수 없습니다.

이번 글에서는 Alert Window 권한 획득 방법과 권한을 적용하여 Overlay를 사용할 때의 문제점을 함께 살펴보겠습니다.

<br />

## Marshmallow 부터 변경된 사항

<hr>

API 23(Marshmallow)부터 직접적으로 [SYSTEM_ALERT_WINDOW](http://developer.android.com/intl/ko/reference/android/Manifest.permission.html#SYSTEM_ALERT_WINDOW) 권한 사용을 하지 않도록 권고하고 있습니다.

WindowManager의 type에 따라서 Marshmallow 권한 설정이 변경될 수 있으나, 6.0 이상에서는 Overlay 권한 승인 후 넘어가는 것이 좋습니다.

제조사/OS 버전에 따라서 Overlay 설정 메뉴가 다른 곳에 위치할 수 있지만 아래의 그림과 같이 설정 변경이 가능합니다.

![Screenshot_20160508-121059](/images/2016/2016-05-08-Android-Overlay-Permission/Screenshot_20160508-121059.png)

해당 부분을 눌러주면 아래와 같이 설정 변경이 가능합니다.

![Screenshot_20160508-121104](/images/2016/2016-05-08-Android-Overlay-Permission/Screenshot_20160508-121104.png)

<br />

## Overlay 권한으로의 문제

<hr>

대략 Overlay을 사용하는 앱들은 다음과 같을 것 같습니다.

- 전역 적으로 서비스를 제공하기 위한 앱(페이스북, 녹화 등)
- 색상 필터를 제공하기 위한 앱
- 앱 내에서 Overlay을 사용하기 위한 앱

위와 같은 앱들이 Overlay을 사용하게 됩니다.

이때 6.0 이상에서는 다음과 같은 일이 발생합니다.

- **다른 앱의 권한 설정이 불가능해집니다.**
- **쉐도우처리된 배경 색이 보이지 않습니다.**

첫 번째의 다른 앱 권한 설정이 불가능 해진다는 부분은 다음과 같습니다.

사용자의 동의를 얻어야 하는 권한(아래에서는 위치 정보)에서 동의를 눌렀을 경우 오른쪽 그림과 같이 Overlay detected라는 명령이 포함됩니다. 권한 설정하는 앱에 따라서 앱 사용이 불가능합니다.

![2016-05-08 03.47.00](/images/2016/2016-05-08-Android-Overlay-Permission/2016-05-08 03.47.00.png)

<hr>

활성화하기 위해서는 Overlay에 대한 설정을 변경 처리해주어야 하는데 사용자가 어느 앱에서 이러한 문제가 발생하고 있는지 알기는 어렵습니다.

**저 또한 저 창이 떠서 재부팅을 하기도 하였습니다.**

다만 저 창이 뜨는 것은 오류가 아닙니다. 사용자를 위해서 개발자가 주의하여 Overlay을 사용해 주어야 한다는 점입니다.(구글이 해주어야 하는데...)

**두 번째 사항인 쉐도우 처리** 부분을 다음과 같이 확인할 수 있습니다.

확연하게 다른 부분입니다. 바로 AlertDialog에서 반투명 배경이 들어갔을 때와 들어가지 않았을 때입니다.

개발 중에도 쉐도우 처리가 보이지 않을 수 있으니 주의해야겠습니다.

![2016-05-08 03.54.20](/images/2016/2016-05-08-Android-Overlay-Permission/2016-05-08 03.54.20.png)

이런 점이 6.0 Marshmallow 이상에서 적용될 사항으로 주의하여 개발이 필요합니다.

우선 6.0 대응을 위한 부분을 작성하였습니다.

<br />

## Marshmallow 대응

<hr>

여기에서 테스트한 예제는 아래 github 링크를 참고해주세요.

- [Android-Overlay-Permission-Example](https://github.com/taehwandev/BlogExample/tree/master/Android/2016-05-08-Android-Overlay-Permission-Example)

Target 23 이상일 경우 설정을 해주어야 하는데... 테스트를 해보니 View type [TYPE_SYSTEM_ALERT](http://developer.android.com/intl/ko/reference/android/view/WindowManager.LayoutParams.html#TYPE_SYSTEM_ALERT)인 경우에는 사용자의 권한 설정을 요구해야 합니다.

TYPE_SYSTEM_DIALOG을 처리하는 경우에는 권한 설정을 하지 않아도 동작합니다. 개발에 맞는 View 설정 type을 찾아서 정의하시면 되겠습니다.

아래 예제는 TYPE_SYSTEM_ALERT을 이용하게 됩니다.

<hr>

기존과 동일하게 Alert window 권한 설정을 합니다.

```xml
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
```

6.0 이상에서 구분을 하기 위해 다음과 같은 코드를 추가하였습니다.

android M 버전 이상을 체크하고, <a href="http://developer.android.com/intl/ko/reference/android/provider/Settings.html#canDrawOverlays(android.content.Context)">canDrawOverlays</a>를 통해 Overlay 사용이 가능한지 체크하여 권한을 요청합니다.

```java
public void startOverlayWindowService(Context context) {
   if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M
           && !Settings.canDrawOverlays(context)) {
       getView().onObtainingPermissionOverlayWindow();

   } else {
       getView().onStartOverlay();
   }
}
```

권한은 다음과 같이 요청하게 됩니다.

[Settings.ACTION_MANAGE_OVERLAY_PERMISSION](http://developer.android.com/intl/ko/reference/android/provider/Settings.html#ACTION_MANAGE_OVERLAY_PERMISSION)에 uri을 함께 넘겨서 권한을 요구합니다.

```java
@TargetApi(Build.VERSION_CODES.M)
@Override
public void onObtainingPermissionOverlayWindow() {
    Intent intent = new Intent(Settings.ACTION_MANAGE_OVERLAY_PERMISSION, Uri.parse("package:" + getPackageName()));
    startActivityForResult(intent, REQ_CODE_OVERLAY_PERMISSION);
}
```

실행하면 다음과 같은 화면이 표시되고, 사용자가 권한 설정을 한 다음 초기화를 진행하시면 됩니다.

저는 다음과 같이 초기화하였습니다.

```java
/**
 * Window View 를 초기화 한다. X, Y 좌표는 0, 0으로 지정한다.
 */
private void initWindowLayout(LayoutInflater layoutInflater) {
    windowView = layoutInflater.inflate(R.layout.window_view, null);
    windowViewLayoutParams = new WindowManager.LayoutParams(
            WindowManager.LayoutParams.WRAP_CONTENT, WindowManager.LayoutParams.WRAP_CONTENT,
            30, 30, // X, Y 좌표
            WindowManager.LayoutParams.TYPE_SYSTEM_ALERT,
            WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE | WindowManager.LayoutParams.FLAG_LAYOUT_IN_SCREEN,
            PixelFormat.TRANSLUCENT);
    windowViewLayoutParams.gravity = Gravity.TOP | Gravity.START;
    windowManager.addView(windowView, windowViewLayoutParams);
}
```

<br />

## 마무리

<hr>

Overlay가 적용되면 반투명 배경도 나오지 않을뿐더러 다른 앱에 영향을 미칠 수 있습니다.

그에 따라 적절한 대응과 사용자에게 요구하는 게 필요합니다.

페이스북은 다음과 같이 사용자에게 설명을 하고 설정을 하도록 만들었습니다.

![Screenshot_20160508-121133](/images/2016/2016-05-08-Android-Overlay-Permission/Screenshot_20160508-121133.png)

<hr>

WindowManager를 활용한 onTouchEvent 처리하기는 이전 글을 참고하시면 됩니다.

아래와 같은 결과물을 확인할 수 있습니다.

- [WindowManager의 onTouchEvent 처리하기](http://thdev.net/617)
- [Android-Overlay-Permission-Example](https://github.com/taehwandev/BlogExample/tree/master/Android/2016-05-08-Android-Overlay-Permission-Example)

![android-overlay-permission](/images/2016/2016-05-08-Android-Overlay-Permission/android-overlay-permission.gif)
