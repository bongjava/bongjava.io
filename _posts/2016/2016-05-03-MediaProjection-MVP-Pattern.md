---
layout: post
title: MediaProjection 예제에 MVP 패턴을 적용해보았다.
published: true
---

지난번에 작성하였던 MediaProjection 예제를 수정하였습니다.

- [안드로이드 화면 캡쳐 API - MediaProjection 사용해보기](http://thdev.tech/Android-MediaProjection-Exmple/)
- [GitHub Repository : MediaProjectionExample ](https://github.com/taehwandev/MediaProjectionExample)

Model View Presenter를 구분하여 리팩토링을 진행하였고, 다음의 자료를 참고하여 MVP 패턴을 적용해보았습니다.

- [GDE 정승욱 : Adapter, 누구냐 넌? - Data? View?](https://medium.com/@jsuch2362/adapter-%EB%88%84%EA%B5%AC%EB%83%90-%EB%84%8C-data-view-2db7eff11c20#.o4dguvga7)

<br />

## MVC(Model View Controller)

MVP를 소개하기 앞서 MVC에 대해서 간단하게 정리해보았습니다.

MVC는 가장 흔하게 사용되는 하나의 패턴으로 Model View Controller를 분리하여 개발되게 됩니다.

안드로이드에서는 Activity/Adapter(View)에서 Model과 Controller을 함께 사용하기도 합니다.

예를 들면 **Activity > Adapter > Activity** 순으로 Activity가 다시 받아 처리하게 되죠.

MVC를 표현하면 다음과 같습니다.

![MVC](/images/2016/2016-05-03-MediaProjection-MVP-Pattern/MVC.png)

<br />

## MVP(Model View Presenter)

MVC로 하면 Activity에서 너무 많은 코드가 들어갑니다. Model, Controller의 구분이 모호하게 개발되기도 합니다. 그래서 MVP 패턴을 적용해보게 되었습니다.

MVP는 Model View Presenter을 말하게 됩니다.

**View는** 화면을 갱신하는 역할을 수행하고, **Presenter는** View의 이벤트와 Data 처리를 담당하게 됩니다. **Model은** Data를 처리하는 역할만 수행하게 됩니다.

그림으로는 다음과 같이 표현할 수 있습니다.

![MVP](/images/2016/2016-05-03-MediaProjection-MVP-Pattern/MVP.png)

정승욱님이 작성한 다음글에서 MVP를 소개하고 있습니다.

[GDE 정승욱 : Adapter, 누구냐 넌? - Data? View?](https://medium.com/@jsuch2362/adapter-%EB%88%84%EA%B5%AC%EB%83%90-%EB%84%8C-data-view-2db7eff11c20#.o4dguvga7)

Adapter를 View의 역할만 하도록 정의하고, Presenter를 통해 Data와 데이터 처리를 하고 나서 View에게 최종 갱신을 하도록 예제를 작성해두었으니 추가로 보고 싶으신 분은 위의 링크를 보시는 것을 추천드립니다.

**View(Button Click Event) > Presenter > Data > Presenter > View(Activity/Adapter)**

<br />

## 주요 코드

저는 MediaProjection 예제에 MVP를 적용해보았습니다. MediaProjectionLibrary와 MediaProjection 모두에 MVP 패턴을 적용하였지만 이번에는 MediaProjectionLibrary에 대해서만 짧게 작성하였습니다.

MVP와 유사하게 View와 Presenter를 구분하였습니다.

View는 MediaProjection의 권한을 획득하도록 하였고, Presenter에서 이벤트를 받아 MediaProjection을 생성하고, isStart를 관리하도록 만들었습니다.

제가 작성한 MediaProjection 예제는 다음의 링크를 참고하시면 됩니다.

[MediaProjection Example](https://github.com/taehwandev/MediaProjectionExample)

<br />

### Presenter/View - MediaProjectionAccessPresenterView

interface로 처리하고, View(Activity)에서 상속받아 사용합니다.

MediaProjection에 대한 Access(권한 획득) 과정이 끝난 후 처리 상태를 AccessStatus() 값을 전달하는 View를 정의하였습니다.

```java
void onMediaProjectionAccessStatus(@MediaProjectionConstant.ProjectionStatusType int type)
```

Callback으로 전달되는 값은 다음과 같습니다.

```java
/**
 * Projection의 정상적인 시작
 */
public static final int PROJECTION_TYPE_STARTED = 1000;

/**
 * Projection의 정지
 */
public static final int PROJECTION_TYPE_STOPPED = 2000;

/**
 * Projection의 사용자 취소
 */
public static final int PROJECTION_TYPE_REJECT = 4000;

/**
 * Projection의 fail(오류))
 */
public static final int PROJECTION_TYPE_FAIL = 3000;
```

<br />

### View - MediaProjectionAccessActivity

추상 클래스로 작성된 Activity입니다. MediaProjectionAccessActivity를 상속받아 구현해주시면 MediaProjection Access를 쉽게 접근할 수 있도록 작성하였습니다.

MediaProjectionAccessPresenterView를 상속받았고, 기존의 코드들을 Presenter로 보내도록 만들었습니다.

주요 일은 Projection의 권한을 요청하고, Projection의 권한을 설정하는 Presenter를 호출하게 됩니다.

```java
MediaProjectionAccessPresenter mediaProjectionAccessPresenter
```

MediaProjection의 권한을 요청할 수 있도록 다음과 같이 작성하였습니다.

MediaProjection에서 사용할 Params(Surface, Projection Name)을 초기화하면 아래 startActivityForResult를 호출하게 됩니다.

```java
public void createMediaProjection(MediaProjectionControllerParams controllerParams) {
    mediaProjectionAccessPresenter.setControllerParams(controllerParams);
    startActivityForResult(mediaProjectionManager.createScreenCaptureIntent(), REQ_CODE_MEDIA_PROJECTION);
}
```

사용자의 Access 이벤트가 끝이 나면 onActivityResult를 전달받게 됩니다.

기존에는 다음과 같이 onActivityResult에서 모든 처리를 하였고, Projection에서 생성된 객체를 모두 Activity가 가지고 있었습니다.

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
  if (requestCode == REQUEST_CODE) {
    // 사용자가 권한을 허용해주었는지에 대한 처리
    if (resultCode != RESULT_OK) {
      // 사용자가 권한을 허용해주지 않았습니다.
      return;
    }
    ... Projection 생성
  }
  super.onActivityResult(requestCode, resultCode, data);
}
```

Presenter를 사용하여 분리한 코드는 다음과 같습니다.

위에서 처리하던 코드는 Presenter로 넘겨서 처리하도록 하여 다음과 같이 간단한 코드로 변경되었습니다.

뭐 별도 함수로 빼서 처리해도 좋으나 Presenter로 옮겨서 작성하였습니다.

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    switch (requestCode) {
        case REQ_CODE_MEDIA_PROJECTION:
            mediaProjectionAccessPresenter.initMediaProjection(resultCode, data);
            break;

        default:
            super.onActivityResult(requestCode, resultCode, data);
            break;
    }
}
```

<br />

### Presenter - MediaProjectionAccessPresenter

Presenter는 PresenterView를 가지게 되고, View에서의 Event에 대하여 Presenter에서 처리하고 나면 다시 PresenterView에 값을 넘겨 UI를 갱신하도록 만들었습니다.

MediaProjectionAccessPresenter에서 사용하는 초기값은 다음과 같습니다.

```java
// Activity(MediaProjection에서 사용)
Activity activity
// MediaProjection에 대한 View
MediaProjectionAccessView view
// Projection manager
MediaProjectionManager mediaProjectionManager
```

onActivityResult에서 넘겨받은 resultCode, data를 받아 Projection을 초기화하게 됩니다.

Projection 생성, 실패, 사용자 취소를 하게 되면 View의 onMediaProjectionAccessStatus을 이용하여 Callback을 하도록 합니다.

```java
if (resultCode != Activity.RESULT_OK) {
    view.onMediaProjectionAccessStatus(MediaProjectionConstant.PROJECTION_TYPE_REJECT);
    return;
}

// Projection 생성
mediaProjection = mediaProjectionManager.getMediaProjection(resultCode, data);
if (mediaProjection != null) {
    // Projection 생성

    // Projection View callback
    view.onMediaProjectionAccessStatus(MediaProjectionConstant.PROJECTION_TYPE_STARTED);

} else {
    // Projection View callback
    view.onMediaProjectionAccessStatus(MediaProjectionConstant.PROJECTION_TYPE_FAIL);
}
```

MediaProjection Destroy를 처리하게 됩니다.

역시 onMediaProjectionAccessStatus를 통해 Callback을 처리하게 됩니다.

```java
public void destroyMediaProjection() {
    if (mediaProjection != null) {
        // Release
    }

    if (virtualDisplay != null) {
        // Release
    }

    view.onMediaProjectionAccessStatus(MediaProjectionConstant.PROJECTION_TYPE_STOPPED);
}
```

<br />

## 마무리

MediaProjection을 MVP 패턴을 적용해보았습니다.

Projection Access를 요청하면 Presenter에서 Projection을 생성하고, PresenterView에서 이를 전달받아 UI를 처리하도록 만들었습니다.

감사합니다.
