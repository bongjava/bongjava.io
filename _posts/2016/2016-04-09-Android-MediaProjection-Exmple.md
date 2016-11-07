---
layout: post
title: 안드로이드 화면 캡쳐 API - MediaProjection 사용해보기
published: true
---

안드로이드 5.0(Lollipop : API 21)부터 Screen 데이터를 가져올 수 있는 API가 추가되었습니다.

- [ScreenCapture](http://developer.android.com/about/versions/lollipop.html#ScreenCapture)

이 같은 API를 이용하여 구글에서도 게임 녹화 앱을 만들었습니다.<br />
 **[Youtue Game app(미국에서만 다운로드 가능합니다)](https://play.google.com/store/apps/details?id=com.google.android.apps.youtube.gaming&hl=en)**

화면 캡처를 할 수 있는 API의 이름은 MediaProjection입니다. 이 API는 안드로이드 5.0(API 20)을 minSdk로 설정하면 사용할 수 있습니다.

<br />

## Android MediaProjection

 MediaProjection에 대한 문서가 다른 API처럼 예제를 제공하지는 않습니다. 별도로 검색을 해서 찾으면 나오긴 하지만 문서상에는 잘 나오지 않습니다.

MediaProjection을 이용하는 전체 API는 다음 링크에 정리되어 있습니다.<br />
[MediaProjection API 문서](http://developer.android.com/reference/android/media/projection/package-summary.html)

<br />
API 문서 페이지에는 3개의 API 링크를 담고 있습니다.

- [MediaProjection](http://developer.android.com/reference/android/media/projection/MediaProjection.html)
  - A token granting applications the ability to capture screen contents and/or record system audio.
  - 화면의 데이터를 가져오거나 음성 데이터를 가지고 가져오기 위한 API입니다. 실제 동작은 영상만 가능합니다.
- [MediaProjection.Callback](http://developer.android.com/reference/android/media/projection/MediaProjection.Callback.html)
  - Callbacks for the projection session.
  - MediaProjection 처리에 대한 Callback을 받을 수 있습니다.(Stop 등)
- [MediaProjectionManager](http://developer.android.com/reference/android/media/projection/MediaProjectionManager.html)
  - Manages the retrieval of certain types of MediaProjection tokens.
  - MediaProjection에 대한 권한을 처리합니다.

<br />
위의 3개 API를 통해서 MediaProjection의 사용자 권한을 가져와 캡처를 할 수 있습니다.<br />
구글에 검색해보면 MediaProjectionDemo를 쉽게 구할 수 있습니다. 다음 예제는 참고하시면 됩니다.<br />

- [MediaProjectionDemo](https://android.googlesource.com/platform/development/+/master/samples/ApiDemos/src/com/example/android/apis/media/projection/MediaProjectionDemo.java)
  - MediaProjection을 640x360, 960x540, 1366x768, 1600x900으로 테스트하는 MediaProjection 예제입니다.
  - 사이즈는 캡처한 화면의 크기를 말합니다.

<br />
이번 글에서는 제가 직접 작성한 예제를 통해서 자세하게 살펴보겠습니다.

<br />

## MediaProjection API 사용법

세부 API를 살펴보기 전에 전체 사용법을 살펴보겠습니다.<br />
2단계를 통해 정리하였습니다.<br /><br />

**1단계**<br />
MediaProjectionManager는 getSystemService를 통해 service를 생성하고, 사용자에게 권한을 요구하게 됩니다.

```java
// 시스템의 Projection service를 획득합니다.
MediaProjectionManager mpm = (MediaProjectionManager) getSystemService(Context.MEDIA_PROJECTION_SERVICE);

// 실제 권한을 사용자에게 통보하고 권한을 요구하게 됩니다.
startActivityForResult(mpm.createScreenCaptureIntent(), REQUEST_CODE);
```

<br />
**2단계**<br />
사용자에게 권한을 획득 받음 다음 onActivityResult를 통해 MediaProjection을 생성하여 사용을 할 수 있습니다.<br />

 - Callback을 처리
 - VirtualDisplay 생성

```java
// 종료와 시작을 정의하기 위해서 전역으로 정의해주었습니다.
/**
 * MediaProjection을 사용하기 위한 객체입니다.
 */
private MediaProjection mediaProjection;

/**
 * 실제 화면의 크기를 정하고, Surface를 통해 화면을 그리게 됩니다
 */
private VirtualDisplay virtualDisplay

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
  if (requestCode == REQUEST_CODE) {

    // 사용자가 권한을 허용해주었는지에 대한 처리
    if (resultCode != RESULT_OK) {
      // 사용자가 권한을 허용해주지 않았습니다.
      return;
    }

    // 사용자가 권한을 허용해주었기에 mediaProjection을 사용할 수 있는 권한이 생기게 됩니다.
    mediaProjection = projectionManager.getMediaProjection(resultCode, data);
    if (mediaProjection != null) {
      // MediaProjection에 대한 Event 정보를 받으려면 아래와 같이 적용하시면 됩니다.
      mediaProjection.registerCallback(new MediaProjectionCallback(), null);
      virtualDisplay = mediaProjection.createVirtualDisplay("VirtualDisplay name", 화면 넓이, 화면 높이, 화면 density, flag, surface(화면을 출력할 곳), null /* Callbacks */, null /* Handler */);
    }
  }
  super.onActivityResult(requestCode, resultCode, data);
}
```

<br />
위와 같은 방법으로 MediaProjection을 사용할 수 있습니다.<br />

<br />
## 세부 API 살펴보기

간단하게 코드를 알아보았으니 API 별로 세부 내용을 알아보겠습니다.<br />
API를 사용하는 순서대로 다음의 순서대로 설명하도록 하겠고, 간단한 예제에서 보았던 VirtualDisplay를 추가하였습니다.

a. [MediaProjectionManager](http://developer.android.com/reference/android/media/projection/MediaProjectionManager.html) : 권한 획득을 위함<br />
b. [MediaProjection](http://developer.android.com/reference/android/media/projection/MediaProjection.html) : 실제 MediaProjection을 사용하기 위한 객체<br />
c. [MediaProjection.Callback](http://developer.android.com/reference/android/media/projection/MediaProjection.Callback.html) : MediaProjection을 사용하는 중 발생한 Callback<br />
d. [VirtualDisplay](http://developer.android.com/reference/android/hardware/display/VirtualDisplay.html) : MediaProjection을 생성된 화면의 정보를 가집니다.

<br />
**a. MediaProjectionManager**

MediaProjectionManager는 system의 Service를 생성하고, 사용자에게 권한을 요구하기 위한 API입니다.<br />
사용자에게 권한을 요구하기 전에 SystemService를 생성해주어야 합니다.<br />
Context.[MEDIA_PROJECTION_SERVICE](http://developer.android.com/reference/android/content/Context.html#MEDIA_PROJECTION_SERVICE)를 이용하여 MediaProjectionManager를 생성하게 됩니다.
<br />

```java
MediaProjectionManager getSystemService(Context.MEDIA_PROJECTION_SERVICE);
```

<br />
MediaProjectionManager가 생성되면 사용자에게 통보하여 권한 획득을 요청하는 단계가 필요합니다.

```java
// API는 아래와 같습니다.
Intent createScreenCaptureIntent()

// 실제 요청은 다음의 코드를 이용하게 됩니다.
startActivityForResult(mpm.createScreenCaptureIntent(), REQUEST_CODE);
```

<br />
사용자는 다음과 같은 창이 표시되었을 때 시작/취소를 할 수 있고 이 창을 다시 표시하지 않을 수 있습니다.<br />

![device-2016-04-09-201521.png]({{ site.baseurl }}/images/2016/2016-04-09-Android-MediaProjection-Exmple/device-2016-04-09-201521.png)

사용자가 동의하면 resultCode는 RESULT_OK가 떨어지고 아니라면 그 외의 값이 떨어지게 됩니다. 그에 대한 처리는 다음과 같이 할 수 있습니다.

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
  if (requestCode == REQUEST_CODE) {

    // 사용자가 권한을 허용해주었는지에 대한 처리
    if (resultCode != RESULT_OK) {
      // 사용자가 권한을 허용해주지 않았습니다.
      return;
    }

    // ...
  }
  super.onActivityResult(requestCode, resultCode, data);
}
```

<br />
위와 같이 REUSLT_OK가 처리되었다면 다음의 코드를 통해 MediaProjection을 생성해야 합니다.<br />
onActivityResult에서 전달받은 resultCode와 Intent data를 세팅하면 됩니다.

```java
MediaProjection getMediaProjection(int resultCode, Intent resultData)
```

<br />
MediaProjectionManager를 통해 부여받은 권한으로 MediaProjection이 생성됩니다.

<br />
**b. [MediaProjection](http://developer.android.com/reference/android/media/projection/MediaProjection.html)**

MediaProjection으로 가상의 화면을 생성하고, MediaProjection Callback을 처리하도록 세팅을 해줄 수 있습니다.<br />

Callback을 처리합니다. Handler는 굳이 필요하지 않다면 null 처리하시면 됩니다.

```java
// MediaProjection.Callback을 셋팅하고 처리합니다.
void registerCallback(MediaProjection.Callback callback, Handler handler)

// MediaProjection.Callback을 해제 합니다.
void unregisterCallback(MediaProjection.Callback callback)
```

<br />
VirtualDisplay를 생성합니다.<br />

- name : 가상의 화면에 사용할 이름입니다. 임의로 지정 가능하지만 empty 상태로 설정하면 안 됩니다.<br />
- width : 캡처할 화면의 넓이(Pixels)<br />
- height : 캡처할 화면의 높이(Pixels)<br />
- dpi : 캡처할 화면의 dpi 사이즈(dpi) : DisplayMertics를 통해 얻어온 값을 사용하면 됩니다.<br />

```java
DisplayMetrics metrics = new DisplayMetrics();
getWindowManager().getDefaultDisplay().getMetrics(metrics);
int screenDensity = metrics.densityDpi;
```

- flag : 가상 화면을 생성하는데 필요한 flag입니다. 이 flag의 선언에 따라서 다른 가상 화면을 허용할지 말지를 결정할 수 있습니다. 전체 리스트는  [DisplayManager](http://developer.android.com/reference/android/hardware/display/DisplayManager.html)를 참고하세요.
  - DisplayManager.VIRTUAL_DISPLAY_FLAG_AUTO_MIRROR
    - 사용자 동의를 얻어 처리합니다.
  - DisplayManager.VIRTUAL_DISPLAY_FLAG_OWN_CONTENT_ONLY
    - 동의된 사용자에게만 미러링을 제공합니다.
  - DisplayManager.VIRTUAL_DISPLAY_FLAG_PRESENTATION
    - Presentation display을 생성합니다.
  - DisplayManager.VIRTUAL_DISPLAY_FLAG_PUBLIC
    - Public display을 생성합니다.
  - DisplayManager.VIRTUAL_DISPLAY_FLAG_SECURE
    - Secure display을 생성합니다.
    - 대략적으로 설명하면 다음과 같습니다.
    - A 앱에서 미러링을 사용하고, B에서도 사용을 하게 해줄 것인가에 대한 여부를 결정합니다. VIRTUAL_DISPLAY_FLAG_OWN_CONTENT_ONLY로 선언하게 되면 허용하지 않게됨으로 MediaProjection이 중지될 수 있습니다.
- surface : 캡쳐한 가상 화면을 출력할 Surface를 셋팅합니다.(오늘 예제에서 확인이 가능합니다.)
- callback : MediaProjection.Callback 과는 다르며, [VirtualDisplay.Callback](http://developer.android.com/reference/android/hardware/display/VirtualDisplay.Callback.html)을 세팅합니다.<br />
  - onPaused() : 시스템이나 Surface가 detached 되었을 경우 호출됩니다.(setSurface(null))
  - onResumed() : 시작되었을 경우
  - onStopped() : 시스템에서 정지되었을 경우(완전 종료에 해당)
- handler : Handler를 세팅합니다.

전체적인 변수 설명은 위와 같으며, 메서드는 아래와 같습니다.

```java
VirtualDisplay createVirtualDisplay(String name, int width, int height, int dpi, int flags, Surface surface, VirtualDisplay.Callback callback, Handler handler)

// 다음과 같이 사용합니다.
mediaProjection.createVirtualDisplay()
```

<br />
**c. [MediaProjection.Callback](http://developer.android.com/reference/android/media/projection/MediaProjection.Callback.html)**

MediaProjection.Callback은 MediaProjection을 사용하는 중에 호출됩니다.<br />
onStop()에 대한 이벤트를 제공합니다.

```java
void onStop()
```

<br />
**d. [VirtualDisplay](http://developer.android.com/reference/android/hardware/display/VirtualDisplay.html)**

MediaProjection의 createVirtualDisplay를 통해서 생성된 VirtualDisplay입니다.<br />
VirtualDisplay는 다음의 API를 제공합니다.

```java
// 가상 display를 return 합니다.
Display getDisplay()
// 가상 display에서 사용하는 surface를 return 합니다.
Surface getSurface()
// destroy
void release()
// size 변경
void resize(int width, int height, int densityDpi)
// Surface를 셋팅합니다.
void setSurface(Surface surface)
```

<br />

## Example

MediaProjection을 테스트하기 위해 작성한 예제입니다.<br />
Android 4.0(API 14)에 적용된 [TextureView](http://developer.android.com/reference/android/view/TextureView.html)를 활용하였습니다.<br />

예제 코드는 위에 간단한 API 살펴보기에서 살펴보았고, 예제를 통해 작성한 부분은 다음과 같습니다.

- Service를 통해 TextureView 생성
- Activity에서 MediaProjection 권한을 획득합니다.
- 권한이 획득되면 Service의 TextureView의 Surface를 통해 초기화합니다.

![Screenshot 2016-04-09 21.30.38.png]({{ site.baseurl }}/images/2016/2016-04-09-Android-MediaProjection-Exmple/Screenshot 2016-04-09 21.30.38.png)

적용이 완료된 전체 화면이 다음과 같습니다.

MediaProjection이 동작을 하게 되면 테스크의 쉐어링 아이콘이 표시되고, 아래와 같이 특정 영역이 그려지게 됩니다.

![device-2016-04-09-201635.png]({{ site.baseurl }}/images/2016/2016-04-09-Android-MediaProjection-Exmple/device-2016-04-09-201635.png)


예제가 완벽하지 않아서 Android의 화면 일부를 가져와 출력해주고 있습니다. 이를 전체를 다 보게 하고 싶으시다면 "createVirtualDisplay"의 화면 크기를 변경해주시면 됩니다.

대략 createVirtualDisplay에서 생성한 화면과 Surface의 크기가 1:1이라면 가장 이상적이겠으나 createVirtualDisplay의 크기 변경은 쉬비 않을 겁니다.

**가장 쉽게 접근**<br />

1. Projection의 크기를 일정 크기로 지정한다.(구글의 샘플을 참고하셔서 적용하시면 됩니다.)
2. Surface에 실제 그려질 텍스처 크기를 지정할 수 있도록 개발한다.

   - 동영상에서 텍스처 그리는 방법을 접근하셔도 좋을 것 같습니다.
   - 구글 CTS 코드에 MediaCodec을 함께 사용하는 예제가 있습니다. 이를 참고하시면 좋을 것 같습니다.

<br />

## 테스트 코드 주소

GitHub Example : [MediaProjection example](https://github.com/taehwandev/MediaProjectionExample)
