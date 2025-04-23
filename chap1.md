해당 시험에선 세가지의 상황을 제시하고 이에 맞게 대응을 하는 것을 확인한다고 한다.
1. root 암호 잃어버린 경우 -> rd.break 모드에서 root 암호 복구를 통해 해결할 것
2. root 암호를 제시한 경우이며 정상 접속이 되는 경우
3. root 암호를 제시한 경우지만 정상 부팅이 되지 않는 경우 -> emergency 복구 모드를 통해 해결할 것
------------------
# root 암호를 잃어버린 경우 rd.break 모드에서 해결하는 방법


1. GRUB 부팅 화면에서 e를 눌러 편집
<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F3sRkX%2FbtsNwLgGUjV%2FeWTKj2MqmUuKkB67kmoKAK%2Fimg.png" style="width:100%; height:auto;"/>
</p>

2. 아래 그림과 같이 linux 마지막 줄에 re.break 매개변수 추가
<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcFLttB%2FbtsNt9aNzvM%2F7W7T9qPv4LdDbkaraCfVwK%2Fimg.png" style="width:100%; height:auto;"/>
</p>
3. ctrl + x 을 눌러서 변경된 매개 변수를 사용해 시스템을 부팅한다. 

- rd.break는 초기 RAM 디스크의 쉘 단계에서 부팅을 멈추게 하는 옵션이다
- 여기서 루트 시스템은 /sysroot에 마운트됨
- ctrl + x or F10 을 누르면 루트 비밀번호 없이 쉘에 접속할 수 있게 됨(initramfs 환경)

<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F2s0bN%2FbtsNwYswXFt%2FMYqd94m1ShlHtsRDds2Tdk%2Fimg.png" style="width:100%; height:auto;"/>
</p>

4. mount -o remount, rw /sysroot
- 읽기 모드인 /sysroot를 읽기,쓰기 모드로 remount



5. chroot /sysroot

- 실제 OS에 있는 것처럼 명령어 실행을 위한 실제 시스템 루트로 진입

6. passwd
7. 비밀번호 변경 입력
<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcRsNDE%2FbtsNvFBvAZU%2F6tTqfKTxFJV5wKr7H6w5GK%2Fimg.png" style="width:100%; height:auto;"/>
</p>

8. touch /.autorelabel

- SELinux 설정을 위한 re-label 예약 명령어
- SELinux가 있는 시스템의 경우 해당 명령어 입력이 없으면 로그인 후에 권한 오류가 발생할 수 있음
- SELinux는 리눅스 시스템에서 보안을 강화하기 위해 만든 보안 정책 강화 시스템



9. mount -o remount, ro /

- 루트 파일시스템을 다시 읽기 전용으로 마운트
- 진행하지 않아도 괜찮지만 마무리 정리 차원에서 권장

10. exit로 chroot 모드 빠져나오기

11. su , sudo root 명령어로 해당 root 비밀번호가 변경됐는지 확인한다. 

-----------------------
<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F3sRkX%2FbtsNwLgGUjV%2FeWTKj2MqmUuKkB67kmoKAK%2Fimg.png" style="width:100%; height:auto;"/>
</p>
1. GRUB 부팅 화면에서 e를 눌러 편집

<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FDF8yY%2FbtsNtQWX3VK%2FuXF8ppBKvEfRkPHPNWgaR1%2Fimg.png" style="width:100%; height:auto;"/>
</p>
2. 위의 그림과 같이 linux 줄에 ro를 rw로 변경 (read only -> read write 가능 권한으로 변경)
3. linux 마지막 줄에 init=/bin/bash 추가

- 시스템 부팅 후 bash만 실행하고 멈추게 하는 파라미터


4. ctrl + x 누르기 (안 먹히면 F10)
<p align="center">
  <img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdFoeif%2FbtsNwOKDEBL%2FLqE7gDaKEbHEBvKPN8kqQk%2Fimg.png" style="width:100%; height:auto;"/>
</p>
5. passwd 
6. 비밀번호 변경
7. touch /.autorelabel
8. exec /sbin/init

- 위에 init=/bin/bash로 멈춰둔 실행을 exec 명령어를 통해 평소처럼 정상 부팅  상태로 넘어가게 해줌
- exec 명령어의 경우엔 지금 쉘 프로세스를 대체하겠다는 의미로 위의 명령어의 경우엔 bash 프로세스가 끝난 뒤 init이 실행됨

## rd.break VS init=/bin/bash
rd.break init=/bin/bash

| 항목         | `rd.break`                | `init=/bin/bash`             |
|--------------|---------------------------|-----------------------------|
| **유연성**    | 더 시스템 친화적           | 더 단순하고 빠름            |
| **SELinux 처리** | 자동 고려됨                | 수동으로 `touch /.autorelabel` 해야 함 |
| **환경**      | `/sysroot` 진입 필요      | 곧장 `/` 루트               |
| **추천 상황** | RHCSA 시험용, 표준 복구    | 빨리 root shell 필요할 때  |

-------------------------------------------------------------
2. emergency mode

https://docs.redhat.com/ko/documentation/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/proc_booting-to-emergency-mode_assembly_making-temporary-changes-to-the-grub-menu#proc_booting-to-emergency-mode_assembly_making-temporary-changes-to-the-grub-menu


6.4. 긴급 모드로 부팅 | Red Hat Product Documentation

형식멀티 페이지단일 페이지모든 문서를 PDF로 표시

docs.redhat.com


