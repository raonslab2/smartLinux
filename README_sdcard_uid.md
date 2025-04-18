# PetaLinux AXI UIO 드라이버 설정 가이드

이 문서는 PetaLinux 환경에서 AXI 기반의 IP를 user-space에서 접근하기 위한 UIO 드라이버 설정 방법을 안내합니다.

---

## 1. 필수 설정 항목

| 설정 항목 | 목적 |
|-----------|------|
| system-user.dtsi UIO 노드 추가 | AXI IP를 커널에서 인식 가능하게 함 |
| CONFIG_UIO_PDRV_GENIRQ 옵션 활성화 | generic-uio 드라이버를 부팅 시 자동 로딩 |
| /dev/uio0 생성 및 mmap() 접근 | AXI IP에 Segfault 없이 접근 가능 |

---

## 2. device-tree (`system-user.dtsi`) 설정

`system-user.dtsi` 파일에 AXI IP 노드를 추가해야 합니다.

```bash
cd ~/ax7010_petalinux/project-spec/meta-user/recipes-bsp/device-tree/files
nano system-user.dtsi
```

**주의**: 최신 PetaLinux (2023.x 이후)에서는 인터럽트 노드 경로를 정확히 입력해야 합니다.

올바른 예시:

```dts
/include/ "system-conf.dtsi"
/ {
    axi {
        axi_add2_0@43c20000 {
            compatible = "generic-uio";
            reg = <0x43C20000 0x10000>;
            interrupt-parent = <&{/axi/interrupt-controller@f8f01000}>;
            interrupts = <0 93 4>; /* dout[2] → IRQ_F2P[2] → GIC ID 93 */
        };
    };
};
```

---

## 3. 커널에 UIO 드라이버 포함하기

PetaLinux 커널 메뉴로 설정합니다:

```bash
petalinux-build -c kernel -x menuconfig
```

설정 위치:

```
Device Drivers → Userspace I/O drivers (UIO)
├── <*> Userspace I/O driver support
└── <*> Userspace I/O platform driver with generic IRQ handling (UIO_PDRV_GENIRQ)
```

반드시 `<*>`로 설정하여 부팅 시 자동으로 `/dev/uio0`가 생성되도록 합니다.

---

## 4. 커널 빌드

설정 후 아래 명령으로 빌드를 진행합니다.

```bash
petalinux-build
```

---

## 5. UIO 드라이버 로딩 확인

빌드 후 타겟 보드에서 부팅 시 `/dev/uio0`가 생성되었는지 확인합니다:

```bash
ls /dev/uio*
```

아래 명령으로 UIO 드라이버 상태 확인 가능:

```bash
cat /sys/class/uio/uio0/name
```

---

## 6. User-space에서 UIO 장치 사용 예시 (C 코드)

UIO 장치를 User-space에서 접근할 때:

```c
#include <fcntl.h>
#include <sys/mman.h>

int fd = open("/dev/uio0", O_RDWR);
void *base = mmap(NULL, size, PROT_READ|PROT_WRITE, MAP_SHARED, fd, 0);
```

Segfault 없이 정상 작동하면 성공입니다.

---

이 가이드를 통해 AXI 인터페이스의 UIO 드라이버를 PetaLinux 환경에 완벽히 설정할 수 있습니다.