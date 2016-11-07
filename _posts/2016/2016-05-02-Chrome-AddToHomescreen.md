---
layout: post
title: 모바일 크롬에서 단축 아이콘 생성 - WebApp 만들기?
published: true
---

Chrome 31 버전부터 웹앱을 만들 수 있는 Shortcut을 제공하고 있었다고 합니다.

그간 웹의 성능이 많이 좋아지면서 많은 변화가 있었고, 크롬 브라우저를 통한 웹앱생성이 가능하다고 합니다.

지난주 [Google Developers Summit](http://googledevkr.blogspot.kr/2016/04/google-developers-summit.html)에 참여하여 Introduction to Progressive Web App ([Robert Nyman](https://robertnyman.com/), Google)의 주제를 듣고나서 알게되었습니다.

크롬 개발문서에는 [Add to Homescreen](https://developer.chrome.com/multidevice/android/installtohomescreen) 문서가 제공중입니다.

오늘은 Add to Homescreen을 적용하는 방법을 간단하게 살펴보겠습니다.

**현재는 안드로이드 용 크롬 브라우저에서만 지원하고 있습니다.**

<br />

## 홈스크린 앱 만들기

Chrome 31부터 지원하였지만 39버전 부터는 JSON-based manifest 파일을 생성하여 간단하게 웹앱을 생성할 수 있다고 합니다.

크롬 브라우저에 단축 아이콘 생성을 하게 되면 마치 네이티브앱과 동일하게 동작하게 됩니다.

Robert Nyman이 소개한 페이지를 안드로이드 크롬에서 접속하시면 단축 아이콘 생성 유도가 됩니다.(39버전 이상)

단축 아이콘을 생성하게 되면 [Flipkart](flipkart.com)이 네이티브앱 처럼 동작하게 됩니다.

<br />

## 단축 아이콘을 지원하기

네이티브 앱처럼 동작하는 단축 아이콘을 만들기 위해서는 몇가지 meta tag와 json 관련된 부분을 추가해주면 됩니다.

메인 페이지에 다음을 추가해줍니다.(저는 github 블로그에 추가할탠데 index.html이 됩니다.)

```html
<link rel="manifest" href="manifest.json">
```

이제 json을 생성해주게 됩니다.

json에는 화면의 회전 상태를 강제로 지정할 수 있고, 단축아이콘으로 사용할 이미지의 단축 아이콘을 정의할 수 있습니다.

```json
{
  "name": "Web Application Manifest Sample",
  "icons": [
    {
      "src": "launcher-icon-0-75x.png",
      "sizes": "36x36",
      "type": "image/png",
      "density": 0.75
    },
    {
      "src": "launcher-icon-1x.png",
      "sizes": "48x48",
      "type": "image/png",
      "density": 1.0
    },
    {
      "src": "launcher-icon-1-5x.png",
      "sizes": "72x72",
      "type": "image/png",
      "density": 1.5
    },
    {
      "src": "launcher-icon-2x.png",
      "sizes": "96x96",
      "type": "image/png",
      "density": 2.0
    },
    {
      "src": "launcher-icon-3x.png",
      "sizes": "144x144",
      "type": "image/png",
      "density": 3.0
    },
    {
      "src": "launcher-icon-4x.png",
      "sizes": "192x192",
      "type": "image/png",
      "density": 4.0
    }
  ],
  "start_url": "index.html",
  "display": "standalone",
  "orientation": "landscape"
}
```

모질라 개발 부분에 각 Members의 상세한 설명이 달려있습니다.

[Mozilla Developers](https://developer.mozilla.org/en-US/docs/Web/Manifest)

### background_color

위에는 없지만 앱이 실행될때 보여질 백그라운드 색상을 지정할 수 있습니다.

```json
"background_color": "red"
```

### display

화면의 상태를 지정할 수 있습니다. 브라우저 형태로 뜨게 할것인지 전체화면을 적용하여 앱처럼 뜨게 할 것인지 지정이 가능합니다.

총 4개의 옵션을 제공합니다.

- fullscreen : 전체화면을 사용하게 됩니다.
- standalone : 일반 앱처럼 동작하게 됩니다.
- minimal-ui : 최소한의 WebUI를 포함합니다.
- browser : 그냥 크롬 웹에 표시되는것과 동일하며 이 값이 기본값입니다.

### icons

구글에서 제공하는 예제에는 density가 포함되어 있지만 mozilla에서 제공하는 예제에는 포함되어 있지 않습니다. 아이콘이 뜨지 않아 해당 코드를 제거했더니 아이콘이 정상으로 표시됩니다.

- size : 아이콘의 사이즈를 정의합니다.(모질라 예제에서는 멀티 사이즈 지정도 가능하네요)
- src : 이미지의 경로를 정의합니다.
- type : 이미지의 타입을 지정합니다.

### description

웹앱에 대한 설명을 정의합니다.

### start_url

앱이 실행될 위치 페이지를 지정합니다.

```json
"start_url": "index.html"
```

### short_name

단축 아이콘을 생성할 이름을 지정합니다.

```json
"short_name": "Shortcut name"
```

### theme_color

앱이 실행될때 표시할 앱 색상을 지정합니다.

```json
"theme_color": "black"
```

### orientation

화면 회전을 지정할 수 있습니다. 강제로 가로/세로를 고정할수도 있고, any 명령으로 자유롭게 둘 수도 있습니다.

```json
"orientation": "any"
```

<br />

## Example

제 Github 블로그에 적용해본 결과물입니다.

[manifest.json](https://raw.githubusercontent.com/taehwandev/taehwandev.github.io/master/manifest.json)에 background_color 검정색으로 정의하였습니다.

![chrome-web-app-example](/images/2016/2016-05-02-Chrome-Add-To-Homescreen-Example/chrome-web-app-example.gif)
