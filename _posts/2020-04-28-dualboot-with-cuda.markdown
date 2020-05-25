---
layout: post
title:  "Windows 10 / Ubuntu 18.04 듀얼부팅 + CUDA 10.0 설치"
date:   2020-04-28 14:35:14 +0900
categories: develop
---

GPU가 있는 데스크탑에 멀티부팅을 할 일이 생겨 시도하던 중 윈도우가 리눅스를 6번쯤 잡아먹고 겨우 해내고 나니 이 과정을 글로 남기지 않으면 너무 억울할 것 같아 이렇게 글로 남기게 되었다.  
이 방법이 최선은 아니겠지만 CUDA와 함께 멀티부팅을 시도할 미래의 나나 많은 사람들에게 도움이 될 수 있기를..  
듀얼부팅을 시도하다가 리눅스 로그인 화면에서 마우스 / 키보드가 움직이지 않거나 느리게 움직이고 로그인을 시도해도 다시 로그인 화면으로 되돌아오는 이른바 **무한 로그인** 현상을 겪는 사람이라면 이 글의 방법을 시도해보길 바란다.  

## GPU Spec / Requirement
---
Spec

* RAM : 32GB
* CPU : Intel i7-8700 3.20GHz x 12
* GPU : GeForce GTX 1060 3GB
* SSD 2TB + SSD 500GB

Requirement
* Windows 10 Boot USB
* Ubuntu 18.04 Boot USB

## Dualboot
---
1. Windows 10 설치  
    윈도우 USB를 꼽고 컴퓨터를 킨다. Boot menu에 들어가서 (나의 경우에는 F2를 누르면 들어가졌는데 컴퓨터에 따라 다르니 찾아볼 것) 부팅 방법을 USB로 설정한다.  
    아마 USB(처럼 보이는 것)가 두 개 있을 것이다. UEFI와 Legacy 중 선택하는 것인데 UEFI로 깔도록 하자. Legacy로 깔면 우분투를 깔 때 추가적으로 설정을 해줘야 한다.  
    모든 파티션을 밀고 설치할 물리 디스크에 새 파티션 만들기? (이름이 정확히 기억이 안난다) 를 선택하고 특별한 설정 없이 예를 누르게 되면 파티션이 4개가 생길 것이다. 이 중 주 디스크를 누르고 윈도우를 설치한다.  
    나의 경우에는 물리 디스크가 2개라 하나는 윈도우를, 하나는 리눅스를 설치했다. 디스크 하나로 쓰는 경우에도 일단은 전체 디스크로 만들어주자.  

2. Fast Boot / Secure Boot 비활성화  
    듣기로는 비활성화하지 않으면 윈도우가 다른 OS의 Boot Manager까지 덮어쓴다고 한다.. 이걸 설정하는 걸 까먹어서 3번 정도 처음부터 깔았다.  

    * OS 내에서 비활성화  
        제어판 > 전원 옵션 에서 빠른 시작 (권장) 이 있을 것이다. 이를 해제해주자.  
    * BIOS 화면에서 비활성화  
        아마 Fast Boot는 비활성화가 이미 되어있을 수도 있는데
        Secure Boot와 Fast Boot를 찾아서 비활성화해주자. Boot Option을 잘 찾아보면 있다.  

3. 파티션 축소  
    디스크 하나로 쓰는 경우에는 파티션 관리에 들어가 리눅스를 쓸 만큼 파티션을 축소한다.  
    노트북에서 SSD 하나로 처음 듀얼부팅을 할 때에는 리눅스용으로 50GB를 할당했는데 꽤나 부족하다.. 윈도우에서 게임 등으로 프로그램을 많이 깔 게 아니라면 개발용으로는 리눅스 용량이 훨씬 필요할테니 참고해서 할당하자  

4. nouveau 비활성화  
    Ubuntu USB를 꼽고 마찬가지로 BIOS 화면으로 들어가 UEFI : Ubuntu 어쩌구를 제일 위로 올려준다.  
    이후 부팅하면 리눅스를 많이 써본 사람이라면 익숙한 grub 메뉴가 열릴텐데 **여기서 nouveau를 비활성화하는 것이 중요하다.**
    nouveau는 Ubuntu에 기본으로 내장된 오픈소스 그래픽 드라이버라고 한다. [참조](https://ottuging.tistory.com/10) 그런데 Nvidia Graphic Driver가 깔리게 되면 충돌이 일어나는 것처럼 보인다.
    따라서 비활성화를 하고 우분투를 설치한다.  
    (가지고 있는 GRAM 노트북으로 듀얼부팅은 별 문제 없이 한 경험이 있어 쉽게 해결할 수 있을 줄 알았으나 노트북은 GPU가 없어서 그래픽 드라이버를 따로 설치할 필요가 없어서 괜찮았던 것으로 생각된다.)  
    
    - install ubuntu로 커서를 옮기고 e를 누른다.
    - vi 화면이 열릴텐데 linux 라고 되어있는 줄의 끝에 `nouveau.modeset=0` 을 추가해준다.
    - F10을 누르면 자동으로 저장되고 부팅이 된다.

5. Ubuntu 18.04 설치  
    우분투 설치 자체가 처음이라면 [참조](https://jimnong.tistory.com/676)를 같이 보면서 따라오길 바란다.  
    우분투 설치는 대부분 건드릴 것 없이 넘어가면 되지만 파티션 설정은 꼭 해줘야 한다. 부트로더를 Windows Boot Manager가 있는 파티션으로 잡아줘야 하기 때문이다.  
    - 설치 형식을 정하는 창이 떴다면 기타를 누르고 (영어로는 뭐였는지 기억이 안난다. 맨 밑에 있는 걸 누르자) 계속을 누른다.
    - 여러 블로그를 찾아본 결과, 그리고 내가 듀얼부팅을 해본 결과 Ubuntu 18.04에 **swap 파티션은 필요하지 않다**.  
        설치할 디스크 (남은 공간)을 선택하고 + 를 누른다.
    - 마운트 위치에 /를 써주고 OK를 누른다.
    - 부트로더를 설치할 장치가 Windows Boot Manager가 있는 파티션이 되도록 한다. 나의 경우에는 /dev/sda2 였던 것으로 기억한다.
    - 이후에는 추가 설정 없이 쭉 설치하면 된다.
    - 마지막으로 재부팅을 할텐데 재부팅 후에도 grub에서 Ubuntu로 커서를 옮기고 (기본이 ubuntu이기 때문에 옮기지 않아도 된다.) e를 누른 뒤 nouveau가 꺼져 있는지 잘 확인한다.  
        install 시 써줬다면 기본으로 들어가 있을 것이고 without install로 설치한 경우에는 다시 써줘야 할 것이다.

6. 그래픽 드라이버 설치 (CUDA를 쓰지 않는 경우)  
    그래픽 드라이버가 없기 때문에 좋은 GPU가 있더라도 해상도가 매우 안 좋을 것이다.  
    CUDA를 쓰지 않는 사람이라면 Software Updater에 들어가서 Setting을 누른 뒤 Additional Drivers에 들어가서 자신에게 맞는 Driver를 설치해준다.  

7. CUDA 10.0 설치  
    CUDA를 설치하면 자동으로 적절한 그래픽 드라이버를 설치해준다. 6번을 분리한 이유도 NVIDIA Graphic Driver를 설치하고 다른 버전의 드라이버를 다시 설치하면 충돌이 일어날 수 있기 때문. (나의 능력이 부족하여 기존의 드라이버를 삭제하고 새로 설치하는 법은 알아내지 못했다.. 충돌이 일어나면 또 무한 로그인에 걸리게 될 수 있으니 조심하자)  
    ```
    sudo apt install gcc
    sudo apt update
    sudo apt upgrade

    # 혹시 모르니 기존의 CUDA와 그래픽 드라이버를 삭제
    sudo rm /etc/apt/source.list.d/cuda*
    sudo apt remove nvidia-cuda-toolkit
    sudo apt purge nvidia-*

    sudo apt update
    sudo apt-key adv --fetch-keys  http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
    sudo bash -c 'echo "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64 /" > /etc/apt/sources.list.d/cuda.list'
    sudo apt update
    sudo apt install cuda-10-0
    ```

8. 환경변수 설정  
    ```
    export PATH=/usr/local/cuda-10.0/bin${PATH:+:${PATH}}
    ```

9. nouveau 블랙리스트 설정  
    아마 안 해도 설치할 때 비활성화했으니 괜찮을 듯 하나 혹시 모르니 해줬다. 이 과정을 건너뛰어도 문제 없다면 건너뛰어도 될 것이다.  
    `sudo gedit /etc/modprobe.d/blacklist.conf` 실행 후 아래의 내용을 추가해준다.  
    ```
    blacklist nouveau
    blacklist lbm-nouveau
    options nouveau modeset=0
    alias nouveau off
    alias lbm-nouveau off
    ```
    이후 설정을 적용하기 위해 `sudo update-initramfs -u` 를 실행한다.

10. 설치 확인  
    재부팅 후 `nvcc --version` 을 실행했을 때 10.0이 나온다면 성공이다.  
    `nvidia-smi`를 했을 때에는 10.2로 표기될 수도 있으나 Runtime 시에는 CUDA 10.0이 사용된다는 의미이므로 안심하자.  