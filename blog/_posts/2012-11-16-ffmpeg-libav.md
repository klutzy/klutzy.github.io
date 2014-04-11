---
layout: default
date: 2012-11-16
mod_date: 2013-01-25
title: FFmpeg-libav 분쟁 기록
tags: history foss ffmpeg libav
---

작년의 [FFmpeg](http://ffmpeg.org/)과 [libav](http://libav.org/) 간의 분쟁은
꽤 시끌벅적한 뉴스였지만,
구체적으로 어떤 일들이 있었는지 파악하는 건 꽤 어렵습니다.
메일링 리스트 링크도 죄다 깨져 있는 상태고요.
현재 남아있는 문서들을 기반으로, 무슨 일이 있었던 것인지 정리해봅니다.

FFmpeg는 [Fabrice Bellard](http://www.bellard.org/)가 2000년 즈음에 만들었습니다.
(이 사람은 qemu를 만들기도 했습니다.)
이후 2004년부터 Michael Niedermayer가 FFmpeg의 중심 메인테이너를 맡아왔습니다.
문제는 Michael이라는 사람이 독선적으로 행동할 때가 많았고,
다른 개발자들과 자주 싸우기도 했다는 점입니다.

2011년 1월에 FFmpeg는 svn에서 git으로 옮기는데,
새 주소는 "git://git.videolan.org/ffmpeg"였습니다.
(ffmpeg는 mplayer 메일링 리스트를 쓰고 있었지만,
Michael은 mplayer에서 준 서버 권한이 [마음에 안 들어서](http://lists.mplayerhq.hu/pipermail/mplayer-dev-eng/2011-March/067913.html) vlc 측에서 git 서버를 제공해줬다고 합니다.)
이 즈음, FFmpeg의 주요 개발자들은 메인테이너를 갈아치우겠다고
[선언합니다](http://article.gmane.org/gmane.comp.video.ffmpeg.devel/123868).
선언문을 보면 git 주소가 "git://git.ffmpeg.org/ffmpeg.git"로 되어 있는 것을 볼 수 있습니다. 현재 이 주소는 동작하지 않죠.

2월에는 Mans를 대표로 하는 새 진영과 기존 Michael 진영의 말다툼이 이어집니다.
가령 Michael은 7인 체제를 해산하고 모두에게 커밋 권한을 주자는 투표를
[메일링에 올립니다](http://lists.mplayerhq.hu/pipermail/ffmpeg-devel/2011-February/102966.html).
이건 그냥 다투자는 거죠.

2011년 3월, Michael은 ffmpeg.org 도메인 주인인 Fabrice에게 연락을 취합니다.
Fabrcie는 이 '반란'을 끝내기 위해
[ffmpeg.org DNS를 변경하여](http://article.gmane.org/gmane.comp.video.ffmpeg.devel/129721)
Michael이 미리 준비해둔 서버로 연결합니다.
이렇게 쿠테타는 실패합니다.

며칠 후, 반란팀은 프로젝트 이름을
[libav로 변경한다고 공지합니다](http://article.gmane.org/gmane.comp.video.ffmpeg.devel/129579).
libav 홈페이지도 이 즈음 생성되었습니다.
하지만 정작 FFmpeg 홈페이지는 그에 대한 공지가 전혀 없었고,
사람들은 libav가 FFmpeg의 새 이름인지 포크인지 혼란스러운 상황이었습니다.
당시 두 사이트는 거의 동일한 레이아웃/로고를 갖고 있어서 더욱 헷갈렸고요.
(wayback machine:
[FFmpeg 2011/05/11](http://web.archive.org/web/20110511074952/http://www.ffmpeg.org/),
[libav 2011/05/03](http://web.archive.org/web/20110503201254/http://libav.org/))

정확한 사실은 이렇습니다. libav는 제3자가 보기에는 FFmpeg에서 갈라져 나온 포크가 맞지만,
libav 팀은 자신들이 포크라는 '다운스트림' 위치가 아니라 'FFmpeg의 정식 후예'라는 위치에 서고 싶었던 것입니다.
그래서 저런 괴상한 공지가 나온 것이고요.
코드 상에서 FFmpeg를 libav로 바꾸었다가
이를 [Michael이 revert](https://github.com/FFmpeg/FFmpeg/commit/c0b1bc8d1797355c4f65ae4c488705b98cf9c8e9)한 적도 있습니다.

실제로 libav에서는 자신들을 포크로 부르지 않고,
반대로 FFmpeg을 [downstream 취급합니다](https://lists.libav.org/pipermail/libav-devel/2012-May/028461.html).
[libav의 history 문서](http://libav.org/about.html)에서도 ffmpeg 메인테이너에 대한 불만을 느낄 수 있습니다.
[The FFmpeg/libav situation](http://blog.pkh.me/p/13-the-FFmpeg-libav-situation.html) 글에 따르면
FFmpeg에서 구현한 내용을 libav 쪽에서 가져오지 않고 아예 따로 구현한다고도 하고요.
한편 FFmpeg는 libav 패치를 모두 가져와 적용하는 한편 이전에 거부했던 패치도 모두 다시 받아들이면서
[경쟁심에 몸집을 불린다](http://codecs.multimedia.cx/?p=370)는 이야기도 있습니다.

한편 libav가 갈라선 뒤로 FFmpeg 로고가 3d 모양으로 바뀌었는데,
FFmpeg의 트레이드마크는 Fabrice가 가지고 있지만
원래 쓰던 2d 로고는 libav 팀으로 건너간 Mans Rullgard가 만들었습니다.
2011년 5월에 Mans는 FFmpeg 팀에 로고에 대한 저작권 침해를 [알리고](http://pastebin.com/qNYPHWTh)
이에 따라 FFmpeg에서는 로고를 다른 것으로 교체합니다.
원래 로고는 libav에서 쓰고 있고요.

5월, 우분투/데비안은
[FFmpeg을 libav로 대체합니다](https://lists.ubuntu.com/archives/technical-board/2011-May/000891.html).
해당 메인테이너는 libav 쪽에 속해 있었고요.
커맨드 라인에서 `ffmpeg`를 실행하면
`This program is not developed anymore and is only provided for compatibility. Use avconv instead`
라는 메시지를 띄웠던 적도 있습니다.
엄밀히 말하면 ffmpeg는 FFmpeg 프로젝트가 제공하는 프로그램이고
libav에서 제공하는 이름은 avconv이니까 아예 말이 안 되는 것도 아니지만..
(이후 [수정되었다고는 합니다](https://bugs.launchpad.net/ubuntu/+source/libav/+bug/939863).)

# 현재

현재 상황은 그리 변하지 않았습니다.
FFmpeg와 libav는 각자 개발을 계속하고 있고,
사용자 입장에서는 둘 중 무엇이 나은지 선택하기가 쉽지 않습니다.
그저 배포판에서 기본으로 쓰는 걸 쓸 수밖에요.

* 현재 우분투는 여전히 libav를 기본으로 사용하고 있고,
ffmpeg를 쓰고 싶다면 [ppa](https://launchpad.net/~jon-severinsson/+archive/ffmpeg)를 등록해야 합니다.
* 젠투 상황을 찾아보니 최근 흥미로운 토론이 있네요.
  [ffmpeg에서 libav로 기본 코덱을 바꾸려는](http://blogs.gentoo.org/scarabeus/2013/01/15/libav-going-to-be-default-provider-for-your-codec-experience/) 토론이 있습니다.
  [follow-up 1](http://aballier.wordpress.com/2013/01/18/ffmpeg-vs-libav-a-distribution-maintainer-point-of-view-almost-two-years-after-the-split/),
  [follow-up 2](http://blogs.gentoo.org/lu_zero/2013/01/18/the-case-of-defaults-libav-vs-ffmpeg/).

[git-videolan-patch]: http://patches.libav.org/patch/497/
