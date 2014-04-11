---
layout: default
title: Steam for Debian Sid
date: 2012-12-10
tags: steam linux debian
---

예전에 신청한 [Steam for Linux](http://steamcommunity.com/linux) 클로즈 베타에
추가 합격했습니다.
베타에 추가하는 기준을 잘 모르겠고
보너스로 받은 게임도 리눅스 지원이 아직 안 되는 Portal이라는 게 요상하지만..

우선 클라이언트를 설치해야 할텐데 이게 우분투 기준이다 보니
데비안 sid에서는 바로 되지가 않습니다.

    # gdebi steam.deb
    ...
    Wrong architecture 'i386'

데비안 sid x64에서는 더더욱 바로 되지가 않습니다.
검색해보니 `dpkg --add-architecture i386`를 해야 하더군요.

    # gdebi steam.deb
    ...
    Dependency is not satisfiable: multiarch-support (>= 2.15-0ubuntu10.2)

의존성이 안 맞는군요.
unstable 주제에 우분투보다 버전이 낮은 패키지가 꽤..
어떻게 할까 고민하다 일단 deb을 뜯어고치면서 무슨 패키지가 문제인지도 알아보기로 했습니다.
[deb 구조](http://tldp.org/HOWTO/html_single/Debian-Binary-Package-Building-HOWTO/)를 보니
그냥 ar 파일이라고 하네요.

    $ ar x steam.deb
    $ ls
    control.tar.gz  data.tar.gz  debian-binary  steam.deb
    $ tar xzvf control.tar.gz
    ./
    ./control
    ./md5sums
    ./postinst

패키지 의존성은 control 파일에서 관리합니다.
`vi control` 해서 몇 개 의존성을 멋대로 sid 버전으로 바꿨습니다.

* multiarch-support: 2.15-0ubuntu10.2 -> [2.13](http://packages.debian.org/sid/multiarch-support)
  ([experimental](http://packages.debian.org/experimental/multiarch-support)에
  버전 높은 게 있긴 했지만 일단 무시했습니다.)
* [libjpeg-turbo8](http://packages.ubuntu.com/quantal/libjpeg-turbo8):
  의존성 삭제 (데비안에 해당 패키지 없음)
* libc6: 2.15 -> 2.13
* [libpulse0](http://packages.ubuntu.com/quantal/libpulse0): 1:0.99.1 -> 2.0-6

libc6 같은 게 아마 문제 될 거 같지만 일단 진행해봤습니다.

    $ tar zcf control.tar.gz control md5sums postinst
    $ ar q steam.mod.deb debian-binary control.tar.gz data.tar.gz

(`ar q`할 때 debian-binary를 먼저 넣지 않으면
dpkg가 얘는 패키지가 아니라고 거부하는 현상을 볼 수 있습니다.)
그리고 `sudo gdebi steam.deb`를 했더니 별다른 오류는 없었고,
온갖 패키지를 i386 버전으로 새로 설치하길 요구하길래 순순히 yes를 했습니다.
설치가 끝나고..

    $ steam

그러자 우선 프로그램이 gksudo를 띄웠습니다.
[jockey-common](http://packages.ubuntu.com/quantal/jockey-common)를 설치하고 싶다고 하는데..
데비안엔 없습니다.

    (gksudo:18589): GLib-CRITICAL **: g_str_has_prefix: assertion `str != NULL' failed

별로 중요한 건 아닌 거 같으니 넘어갔습니다.
맨 처음에 실행할 때만 물어보더군요.
실제 문제는 그 다음입니다.

    $HOME/.local/share/Steam/ubuntu12_32/steam: /lib/i386-linux-gnu/i686/cmov/libc.so.6: version `GLIBC_2.15' not found (required by $HOME/.local/share/Steam/ubuntu12_32/steam)

예상대로 libc가 문제네요.
최신 라이브러리를 써야 하는건지
그냥 우분투 기반으로 작업해서 그런지는 모르겠지만,
[experimental](http://packages.debian.org/experimental/libc6)에 2.16이 있길래
[데비안 위키](http://wiki.debian.org/DebianExperimental) 따라 설치했습니다.

    $ steam

![처음 실행 모습](/images/steam-update.png)

성공! 게임 설치, 상점, 메신저 등 모두 작동합니다.

스팀 리눅스에 현재 지원되는 게임 목록은
[여기](http://store.steampowered.com/search/?os=linux)에서
볼 수 있습니다.
인디 게임이 많은데,
대부분이 [Humble Indie Bundle](https://humblebundle.com)에 올라온 적이 있어서
쉽게 작업할 수 있었을 겁니다.

다만 라이브러리에 있다고 해서 모두 실행 가능한 건 아닙니다.
LIMBO나 Braid의 경우 [스팀 리눅스 카탈로그](http://store.steampowered.com/search/?os=linux&category1=998&snr=1_230_linux__202)에 없는데,
아마 그 때문인지 설치를 시도하면
하드 용량이 얼마나 필요한지도 안 알려주고 그냥 설치를 진행하더니 곧 끝났다고 합니다.
([관련 버그](https://github.com/ValveSoftware/steam-for-linux/issues/58))

2013/01/08 추가: 예전엔 Splice도 안 되었는데 이제는 잘 되네요.

Cogs는 문제 없이 다운로드를 받고, 실행해 봤습니다.

![Cogs 실행 모습](/images/steam-cogs.png)

일단 동작은 하는데 심각하게 느리네요..
안드로이드에서도 잘 돌았던 녀석인데?

World of Goo를 해보려고 했는데..
목록에 없어서 확인해보니 Humble Bundle에서 사놓고서 스팀 키 등록을 잊었네요.
리눅스 클라이언트에서 등록을 시도했더니 잘 됩니다.
(다만 영수증 출력은 안 되네요.)
설치도 잘 되었고요.

근데 게임을 실행하려고 하니 `Failed to load module "canberra-gtk-module"`
아...

Big Picture로 실행을 해 보았습니다.
처음 밸브 로고 뜨는 데에 20초는 기다려야 할 정도로 느린데,
일단 그걸 넘어가면 내비게이션은 쾌적한 편입니다.
그래픽이 간간히 깨지는 게 보이긴 하지만요.

![Big Picture. 일부 그래픽이 깨져 보입니다.](/images/steam-big-picture.png)

Big Picture에서 World of Goo를 실행하니 문제없이 잘 실행되고,
쾌적하게 게임을 할 수 있을 정도로 성능이 나왔습니다.
아까는 왜 안된거지..

![World of Goo.](/images/steam-goo.png)

반대로 Big Picture에서 Cogs를 실행하니 Cogs가 그대로 창 모드로 뜨고,
Cogs를 종료하니 스팀 클라이언트가 뻗는 현상이 발생했습니다.

전반적인 감상은, 아직 게임이 많지 않은게 아쉬운데
그래도 리눅스 쪽에서 게임을 지원한 사례가 워낙 없다 보니
스팀 플랫폼 자체가 리눅스에 관심을 가진다는 것 자체가 기대가 되긴 합니다.

* Arch Linux는 우분투 deb 패키지를 가지고 직접
  [arch 패키지](https://wiki.archlinux.org/index.php/Steam)를 구성했습니다.
* 2013/01/08 추가: 현재는 스팀 리눅스가 오픈 베타로 전환되어 있습니다.
  버그 신고는 [깃헙](https://github.com/ValveSoftware/steam-for-linux)에서
  받고요.
