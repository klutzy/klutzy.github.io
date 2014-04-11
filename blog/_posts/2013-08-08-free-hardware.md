---
layout: default
title: 자유로운 하드웨어 구입하기
date: 2013-08-08
tags: linux foss free hardware
---

최근에 새로운 컴퓨터를 사려고 이리저리 견적을 내보았는데,
non-free [binary blob][wp-binary-blob]/[firmware][debian-firmware]가
하나도 필요하지 않도록 구입해보자는 생각이 들어 한번 시도해 보았습니다.
그런데 이거 생각보다 쉬운 일이 아니더군요..
직접 조립하고 언제나처럼 Debian Sid를 설치했습니다.

Debian/Ubuntu에는 [vrms][] 프로그램이 있는데,
컴퓨터에 설치된 패키지를 검사해서 non-free가 있는지를 알려줍니다.
이게 정확히는 [FSF 가이드라인][gnu-free-system-guideline]이 아니라
데비안 자유 소프트웨어 가이드라인(DFSG)을 따르는데,
가령 데비안에서는 GFDL도 경우에 따라 non-free로 구분하는 등의 차이가 있습니다.
그런 면에서는 "vrms"라는 이름이 조금 [잘못된 감][libreplanet-vrms]이 있죠.
어쨌거나 저는 Debian 기준에서의 free를 목표로 했으니 실행해 보았습니다.

    $ vrms
    No non-free or contrib packages installed on napa!  rms would be proud.

성공! 여기까지 도달하는 데에 겪은 것들과 기타 참고 자료를 정리합니다.

# GPU

저는 그래픽 카드를 당장은 안 사고 일단 하스웰의 내장 그래픽을 사용하기로 했습니다.
드라이버가 최신이 아니라서 Gnome 3가 fallback 모드로 동작했는데,
[experimental 드라이버][debian-intel-experimental]를 설치하니 잘 동작합니다.

사실 그래픽 카드는 FOSS 입장에서 가장 고르기 어려운 부분입니다.
위키백과에 [현황이 정리][wp-gpu-foss]되어 있는데, 읽어보면 꽤 복잡하죠.

특히 Nvidia는 예전부터 리눅스 드라이버 지원이 엉망이기로 악명이 높았습니다.
이에 대해 Linus Torvalds가 Nvidia에게 [비판을 하기도 했고요][torvalds-nvidia].
리누스 덕분인지 최근 들어 다시 리눅스 드라이버 개발이 계속 이루어지는 모양이지만,
오픈소스 nouveau 드라이버를 지원하는 건 아니고 non-free 드라이버입니다.
[최근의 Phoronix 벤치마크][phoronix-nvidia-bench]도 참고하세요.

ATI는 Nvidia에 비교하면 그나마 오픈 소스를 잘 지원하는 편입니다:
[최근의 Phoronix 벤치마크][phoronix-ati-bench]를 보니
Gallium3D 드라이버가 독점 드라이버에 가까운 성능을 내는 것 같네요.
하지만 ATI 장비를 사용하려면 non-free 펌웨어가 [필요할 수 있습니다][debian-firmware-linux-nonfree].

인텔 그래픽은 오래 전부터 오픈 소스를 잘 지원해 온 편이긴 하지만,
[Intel GMA][wp-intel-gma-linux] 같은 예외가 존재하기 때문에 주의해야 합니다.

# 네트워크

시중에서 판매하는 대부분의 메인보드에서는
이더넷 통신에 Realtek Gigabit Ethernet (RTL8111/RTL8168) 칩을 사용하고 있었습니다.
사실 조금 난감했는데.. 본래 이 칩은 [non-free 펌웨어를 필요로 합니다][firmware-realtek].
조금 찾아보니 펌웨어가 없어도 사용에는 큰 문제가 없다는 글들을 접하여
한번 그들을 믿어보고 Realtek 8111G가 달린 보드를 구매했습니다.

    r8169 0000:03:00.0: firmware: agent aborted loading rtl_nic/rtl8168g-2.fw (not found?)
    r8169 0000:03:00.0 eth0: unable to load firmware patch rtl_nic/rtl8168g-2.fw (-2)

역시 "rtl8168g-2.fw" [펌웨어][linux-firmware-realtek]가 없다는 경고가 뜨네요.
([드라이버 소스][realtek-driver])
다행히 펌웨어가 없어도 잘 동작합니다.

Wi-Fi의 경우는 더 어려운 게,
특히 802.11n부터는 상당수 제품이 binary blob을 요구합니다.
[데비안 WiFi 위키][debian-wiki-wifi]와
[위키백과 비교 표][wp-foss-wifi]를 참고해서 구매해야겠죠..

2013-12-06 추가: Atheros의 AR9271/AR7010 칩은 [ath9k_htc][] 드라이버를 사용하며,
여기에 들어가는 펌웨어가 [오픈 소스로 공개되어 있습니다][ath9k_htc-fw].
이 칩은 FSF의 [Respect Your Freedom][fsf-ryf]에서 인증을 받기도 했습니다.
(RYF의 심사 대상에 펌웨어의 자유도 여부가 있습니다.)
다행히, 해당 칩을 사용하는 제품을 그리 어렵지 않게 구할 수 있었습니다.
다만 데비안에는 아직 공개 펌웨어가 올라오지 않아서.. 직접 빌드했습니다.
(펌웨어를 빌드하기 위해 먼저 크로스 컴파일러부터 빌드하는데,
gcc 4.8로 gcc 4.7을 빌드하다가 에러가 나더군요.
[툴체인 업그레이드 픽스][ath9k_htc-fw-fix]를 적용했습니다.)
펌웨어를 설치한 후 [이런저런][elinux-rpi] 설정을 하여 현재 AP로 사용하고 있습니다. :)

# 노트북 / 소형 기기

노트북이나 핸드폰의 경우 사실상 완제품으로밖에 구할 수 없기 때문에,
non-free binary blob을 피하기가 굉장히 어렵습니다.
WiFi 같은 건 별도 하드웨어를 구매하면 되겠지만 아무래도 돈이 아까운 느낌이 있죠.

시중 노트북 중에 완전히 free한 게 얼마나 있는지는 모르겠습니다.
[libreplanet의 목록][libreplanet-laptops]에는 쉽게 구할 수 없는 것밖에 없는데..
저 목록에는 Lemote Yeelong 기기가 소개되어 있는데,
이게 바로 [Richard Stallman이][rms-uses] [쓰는 기기][rms-uses-this]입니다.

Android는 오픈 소스라고 알려져 있지만,
실제로는 코드를 빌드하여 기기에 실행하려면
그래픽 등의 [독점 바이너리가 필수적입니다][android-build-binary].
(게다가 바이너리조차도 [제때 올라오지 못하는 경우가 있고요][nexus-7-jbq].
이것 때문에 오늘 aosp 메인테이너인 JBQ가 [그만두겠다고 밝혔습니다][jbq-gp].)
Nexus 제품도 [바이너리][android-nexus-binary]가 별도로 제공되고요.
[Replicant][replicant]라는 프로젝트는 안드로이드에서 non-free 의존성을 빼는
프로젝트입니다.
현재 [Replicant 4.0][replicant-4.0]이 진행중이네요.

[Raspberry Pi][rpi]의 경우에도 ARM 기반 SoC이고,
당연히 [펌웨어][rpi-firmware]가 필요합니다.
(코덱 칩을 쓰려면 [코덱 라이선스][rpi-codecs]도 구매해야 합니다.)

# 참고 링크

FSF에서 요구하는 [Free System Distribution][gnu-free-system-guideline] 조건을 만족하는
리눅스 배포판이 [몇 가지 있습니다][gnu-free-distro].

위에서 소개한 것 외에도 다음의 자료에서 도움을 받았습니다.

-   [h-node.org][h-node]
-   [LibrePlanet][libreplanet]
-   [Ubuntu: WirelessCardsSupported][ubuntu-wifi-supported]
-   [wireless.kernel.org][kernel-wireless]
-   [FSFe: Liberate Your Device!][fsfe-android-liberate]

[wp-intel-gma-linux]: http://en.wikipedia.org/wiki/Intel_GMA#Linux
[vrms]: http://packages.debian.org/sid/vrms
[debian-intel-experimental]: http://packages.debian.org/experimental/xserver-xorg-video-intel
[wp-gpu-foss]: http://en.wikipedia.org/wiki/Graphics_hardware_and_FOSS
[torvalds-nvidia]: http://www.youtube.com/watch?v=MShbP3OpASA&feature=youtu.be&hd=1&t=48m9s
[phoronix-nvidia-bench]: http://www.phoronix.com/scan.php?page=article&item=nvidia_windows8_geforce&num=1
[wp-firmware]: http://en.wikipedia.org/wiki/Firmware
[debian-firmware]: https://wiki.debian.org/Firmware
[phoronix-ati-bench]: http://www.phoronix.com/scan.php?page=article&item=amd_win8_ubuntu13&num=1
[firmware-realtek]: http://packages.debian.org/sid/firmware-realtek
[fsf-ryf]: http://www.fsf.org/resources/hw/endorsement/respects-your-freedom
[debian-wiki-wifi]: https://wiki.debian.org/WiFi#Install_Driver
[android-nexus-binary]: https://developers.google.com/android/nexus/drivers
[android-build-binary]: http://source.android.com/source/building-devices.html#obtaining-proprietary-binaries
[fsfe-android-liberate]: https://fsfe.org/campaigns/android/liberate.en.html
[replicant]: http://replicant.us/
[replicant-4.0]: http://redmine.replicant.us/projects/replicant/wiki/ReplicantStatus#Replicant-40
[kernel-wireless]: http://wireless.kernel.org/
[wp-foss-wifi]: http://en.wikipedia.org/wiki/Comparison_of_open-source_wireless_drivers
[ubuntu-wifi-supported]: https://help.ubuntu.com/community/WifiDocs/WirelessCardsSupported
[h-node]: http://h-node.org/
[libreplanet-laptops]: http://libreplanet.org/wiki/Group:Hardware/Freest#Laptops
[rms-uses]: http://stallman.org/stallman-computing.html
[rms-uses-this]: http://richard.stallman.usesthis.com/
[libreplanet]: http://libreplanet.org/wiki/LibrePlanet:About
[wp-binary-blob]: http://en.wikipedia.org/wiki/Binary_blob
[rpi]: http://www.raspberrypi.org/
[rpi-firmware]: https://github.com/raspberrypi/firmware
[rpi-codecs]: http://www.raspberrypi.com/
[nexus-7-jbq]: http://phandroid.com/2013/08/07/jbq-quits-aosp-qualcomm-to-blame/
[jbq-gp]: https://plus.google.com/112218872649456413744/posts/9HHRURorE7g
[linux-firmware-realtek]: http://git.kernel.org/cgit/linux/kernel/git/firmware/linux-firmware.git/tree/rtl_nic
[realtek-driver]: https://github.com/torvalds/linux/blob/master/drivers/net/ethernet/realtek/r8169.c#L52
[gnu-free-system-guideline]: http://www.gnu.org/distros/free-system-distribution-guidelines.html
[libreplanet-vrms]: http://libreplanet.org/wiki/List_of_software_that_does_not_respect_the_Free_System_Distribution_Guidelines#vrms
[debian-firmware-linux-nonfree]: http://packages.debian.org/sid/firmware-linux-nonfree
[gnu-free-distro]: http://www.gnu.org/distros/free-distros.html
[ath9k_htc]: http://wireless.kernel.org/en/users/Drivers/ath9k_htc
[ath9k_htc-fw]: https://github.com/qca/open-ath9k-htc-firmware
[elinux-rpi]: http://elinux.org/RPI-Wireless-Hotspot
[ath9k_htc-fw-fix]: https://github.com/qca/open-ath9k-htc-firmware/pull/29
