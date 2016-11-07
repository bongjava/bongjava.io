---
layout: post
title: Mac OS 터미널에 ZSH 설치하기
published: true
---

Mac OS x에서 zsh를 설치해보았습니다.

터미널을 유용하게 사용할 수 있다고 하여 설치하였는데 테마도 이쁘게 적용됩니다.

최종적으로 다음과 같은 결과물을 만들어보겠습니다.

![Screenshot 2016-05-01 12.30.14](/images/2016/2016-05-01-Mac-ZSH-Install/Screenshot 2016-04-30 12.30.14.png)

<br />

## Brew 설치하기

zsh를 설치하기 전에 brew를 설치해주어야 합니다. brew를 이용하여 mac에서 제공하지 않는 패키지들을 추가로 설치할 수 있는 툴입니다.

Brew 설치하는 방법은 다음 글에 잘 설명이 되어 있는데 다음과 같은 과정으로 설치합니다.

우선 Terminal을 열어줍니다.

![Screenshot 2016-05-01 12.46.25](/images/2016/2016-05-01-Mac-ZSH-Install/Screenshot 2016-04-30 12.46.25.png)

실행된 터미널에 다음의 명령어를 입력해줍니다.
입력을 해주시면 설치가 진행됩니다.

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

brew 사용법은 다음과 같다는군요

```
brew install wget
```

Brew 설치가 끝났으니 ZSH 설치로 돌아가겠습니다.

<br />

## ZSH 설치하기

Brew 설치가 완료되면 zsh 설치가 가능해집니다. oh my zsh를 통해 zsh를 사용할 수 있습니다.

- [oh my zsh](http://ohmyz.sh/)
- [oh my zsh github](https://github.com/robbyrussell/oh-my-zsh)
- [zsh](http://www.zsh.org/)

맥에서는 기본으로 bash를 사용하고 있으며 zsh 역시 설치는 되어 있습니다.

터미널의 테마를 변경하여 색상을 변경하는 것도 가능하고 brew를 사용하여 다양한 패키지 설치도 가능합니다. 하지만 이쁘진 않죠. 그래서 zsh를 설치해보기로 했습니다.

oh my zsh의 경우 커뮤니티 활동이 활발하여 많은 자료를 가지고 있다고 합니다. 테마, 플러그인 모두 많은 수를 제공하고 있습니다.(검색시 찾기도 쉽더군요)

터미널에서 다음을 실행하시면 지금 Mac에서 제공하는 zsh 버전을 알 수 있습니다.

```
zsh --version
```
 
저는 맥 최신 버전에서 5.0.8 버전이 설치되어 있었고, brew를 이용하여 최신? 버전을 설치합니다.

```
brew install zsh
```

**진행 중 비밀번호를 입력하게 되는데, 맥 로그인 비밀번호를 입력하시면 됩니다.**

다음 명령어를 입력하여 oh-my-zsh를 설치합니다.

```
curl -L https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh | sh
```

설치된 oh-my-zsh을 사용하도록 설정을 하면 zsh를 사용하기 위한 모든 작업이 종료됩니다.

```
chsh -s /usr/local/bin/zsh
```

명령어를 입력하면 다음과 같이 Changing shell for name. 이름과 함게 암호를 넣어주시면 됩니다.

![Screenshot 2016-05-01 13.39.58](/images/2016/2016-05-01-Mac-ZSH-Install/Screenshot 2016-04-30 13.39.58.png)


터미널을 종료하고 다시 실행하시면 다음과 같습니다.

![Screenshot 2016-05-01 13.42.17](/images/2016/2016-05-01-Mac-ZSH-Install/Screenshot 2016-04-30 13.42.17.png)

<br />

## ZSH Theme 설정하기

ZSH에 이제 Theme를 설정해주어야 합니다.

Oh my zsh에서 사용할 수 있는 theme 목록들이 나열되어 있습니다.

맘에 드는 Theme를 선택해서 설정을 할 수 있습니다.

[oh my zsh themes](https://github.com/robbyrussell/oh-my-zsh/wiki/External-themes)

터미널에서 다음의 명령어를 입력하여 설정을 변경합니다.

vim에 익숙하실 경우에는 다음 명령어를 입력하여 수정하시면 되고,

```
vim ~/.zshrc
```

vim 명령어 사용이 어려우신 분은 다음 명령어를 입력해주세요.

```
open ~/.zshrc
```

파일을 열면 다음의 문구를 볼 수 있습니다.

**ZSH_THEME="테마명"** 을 입력해주시면 됩니다. 만약 어떤 것을 선택해야 할지 모르신다면

**"random"을** 입력하여 실행시마다 다른 테마를 맛볼 수 있다고 합니다.

```
# Set name of the theme to load.
# Look in ~/.oh-my-zsh/themes/
# Optionally, if you set this to "random", it'll load a random theme each
# time that oh-my-zsh is loaded.
ZSH_THEME="robbyrussell"
```

참고로 사용자의 이름을 지정하지 않으면 ~ 앞에 사용자 이름이 표시됩니다.

.zshrc에 추가하려면 다음과 같이 추가해주시면 됩니다.

```
DEFAULT_USER="$USER"
```

저는 **agnoster** 을 사용하였습니다.

저장하고 터미널을 다시 실행합니다.

짜잔...

![Screenshot 2016-05-01 13.42.17](/images/2016/2016-05-01-Mac-ZSH-Install/Screenshot 2016-04-30 13.42.17.png)

테마에서 보던 것과 많이 다릅니다.

ZSH_THEME는 커맨드 라인에서 사용할 색상을 뜻합니다.(텍스트의 색상 정보))

텍스트에 대한 색상 값 정의를 마쳤으니 이제 실제 터미널의 프로파일을 설정해주어야 이쁘게 보이게 됩니다.

사용자마다 선택하는 터미널의 종류가 2가지가 있습니다.

- Mac의 기본 터미널
- [iTerm2](https://www.iterm2.com/)(GPL v2의 오픈소스 터미널)

<br />

### 맥/iTerm2에 테마 설정하기

제가 사용하는 테마는 **agnoster** 을 사용하게 되는데 다음 글을 참고하여 작성합니다.

- [iterm2-solarized](https://gist.github.com/kevin-smets/8568070)

터미널에서도 동일하기에 설명을 하겠습니다.

iTerm2와 터미널에서 사용할 테마는 다음 링크를 통해 다운로드하시면 됩니다.

- [iTerm2 Themes](http://iterm2colorschemes.com/)

다운로드해 압축을 풀어주세요.

### 터미널에서 설정하기

터미널의 설정을 열어줍니다.

```
cmd + ,
```

프로파일로 이동하고 하단의 설정 아이콘을 눌러 Import... 을 눌러줍니다.

![Screenshot 2016-05-01 13.59.32](/images/2016/2016-05-01-Mac-ZSH-Install/Screenshot 2016-04-30 13.59.32.png)

시스템의 터미널에 적용하기 위한 테마는 **terminal** 폴더에 존재합니다.

제가 선택한 **agnoster** 은 Solarized dark 테마를 사용하는 게 이쁘더군요.

해당 테마를 찾아서 import 해주고, import 한 테마를 Default로 설정합니다.

![Screenshot 2016-05-01 14.00.13](/images/2016/2016-05-01-Mac-ZSH-Install/Screenshot 2016-04-30 14.00.13.png)

그런 다음 터미널을 다시 실행합니다.

적용된 것을 확인할 수 있습니다. 하지만? 표시가 보입니다.

이건 아래쪽에서 설명하죠.

![Screenshot 2016-05-01 14.04.45](/images/2016/2016-05-01-Mac-ZSH-Install/Screenshot 2016-04-30 14.04.45.png)


### iTerm2에서 설정하기

우선 iTerm2를 사용하고 싶으신 분은 아래 링크를 통해 다운로드하시면 됩니다.

[iTerm2 다운로드](https://www.iterm2.com/)

iTerm2는 GPL v2를 따르는 오픈소스입니다.

iTerm2도 터미널과 똑같이 생겼습니다. 이제 터미널의 테마 설정을 추가해주어야겠습니다.

![Screenshot 2016-05-01 14.08.17](/images/2016/2016-05-01-Mac-ZSH-Install/Screenshot 2016-04-30 14.08.17.png)

설정을 실행합니다.

```
cmd + ,
```

설정의 프로파일 탭으로 이동하고, Default 테마에서 컬러 탭으로 이동합니다.

별도 Profile을 추가해주셔도 되고, 복사해서 사용하셔도 됩니다.

- 복사 방법은 하단의 Other Actions... 의 부분에서 복사할 수 있습니다.

![Screenshot 2016-05-01 14.13.09](/images/2016/2016-05-01-Mac-ZSH-Install/Screenshot 2016-04-30 14.13.09.png)

Colors 탭에서 하단의 Load Presets... 을 눌러 import해줍니다.

iTerm2에서 사용할 컬러는 다음 폴더에 있습니다.

**schemes** 폴더에 존재하고 있으며 저는 Solarized Dark.itemcolors를 추가하였습니다.

![Screenshot 2016-05-01 14.22.29](/images/2016/2016-05-01-Mac-ZSH-Install/Screenshot 2016-04-30 14.22.29.png)

import를 하고 나서 **Load Presets...** 을 한번 더 눌러 추가된 테마를 선택해주시면 아래와 같이 적용된 모습을 확인할 수 있습니다.

![Screenshot 2016-05-01 14.23.21](/images/2016/2016-05-01-Mac-ZSH-Install/Screenshot 2016-04-30 14.23.21.png)


<br />

## 폰트 깨짐 확인하기

Theme에 따라서 폰트가 물음표로 나오는 경우가 있습니다.

[agnoster.zsh-theme에 폰트 설정](https://gist.github.com/agnoster/3712874)

터미널에서 다음 명령어를 입력합니다.

```
echo "\ue0b0 \u00b1 \ue0a0 \u27a6 \u2718 \u26a1 \u2699"
```

위의 명령어를 입력하면 아래 그림과 같이 물음표 표시되는게 있습니다.

![Screenshot 2016-05-01 14.29.41](/images/2016/2016-05-01-Mac-ZSH-Install/Screenshot 2016-04-30 14.29.41.png)

폰트 설정 글에는 zsh-theme에 폰트 설정을 추가한 zsh-theme를 다운로드해 덥어쒸우거나, 다른 폰트를 정의하고 있는데 전 다운로드해 폰트를 변경했습니다.

다음을 다운로드해 설치합니다.

[Mesol font 다운로드](https://github.com/powerline/fonts/blob/master/Meslo/Meslo%20LG%20M%20DZ%20Regular%20for%20Powerline.otf)

<br />

### 터미널에서 폰트 설정

프로파일 설정에서 사용하고 있는 테마인 Solarized Dark를 선택합니다.

Font 부분의 Change... 을 눌러 Meslo을 선택하여 줍니다.

![Screenshot 2016-05-01 14.34.45](/images/2016/2016-05-01-Mac-ZSH-Install/Screenshot 2016-04-30 14.34.45.png)

그럼 echo 명령을 다시 확인하면 아래와 같습니다.

![Screenshot 2016-05-01 14.35.35](/images/2016/2016-05-01-Mac-ZSH-Install/Screenshot 2016-04-30 14.35.35.png)

<br />

### iTerm2에서 폰트 설정

iTerm2의 프로파일에서 Text의 Change Font를 설정하면 됩니다.

Regual Font와 Non-ASCII Font 2개 모두 설정을 변경합니다.

![Screenshot 2016-05-01 14.38.54](/images/2016/2016-05-01-Mac-ZSH-Install/Screenshot 2016-04-30 14.38.54.png)

iTerm2에서도 적용되면 아래와 같이 정상적인 텍스트가 보입니다.

![Screenshot 2016-05-01 14.40.41](/images/2016/2016-05-01-Mac-ZSH-Install/Screenshot 2016-04-30 14.40.41.png)

<br />

## ZSH Path 설정하기

**ZSH에서 Path를 설정하지 않고, .bash_profile에 적용된 내용도 그대로 사용이 가능합니다.**

ZSH에서 Path를 설정할 수 있습니다.

다음 글 대로 해주면 좋지만...

[Where to place $PATH variable assertions in zsh?](http://stackoverflow.com/questions/10574684/where-to-place-path-variable-assertions-in-zsh)

저는 zsh를 계속 사용할 것이라서 다음과 같이 설정하였습니다.

우선 .zshrc를 열어줍니다.

```
vim ~/.zshrc
OR
open ~/.zshrc
```

다음과 같이 추가해주시면 됩니다.

```
export aaa=file path
export PATH=${PATH}:${aaa}
```

종료하고 다시 실행하면 동작하는데 iTerm2도 함께 적용되니 별도 설정하실 것은 없습니다.

<br />

## 참고 자료

- [OS X Brew 설치하기](http://brew.sh/index_ko.html)
- [ZSH 설치하기](http://sourabhbajaj.com/mac-setup/iTerm/zsh.html)
- [iterm2-solarized - Solarized 테마 설치하기](https://gist.github.com/kevin-smets/8568070)
- [agnoster.zsh-theme - 테마 적용](https://gist.github.com/agnoster/3712874)
- [터미널 초보의 필수품인 Oh My ZSH!를 사용하자](https://nolboo.github.io/blog/2015/08/21/oh-my-zsh/)
