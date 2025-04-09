## 사용된 소프트웨어 버전

- Vivado 버전: 2024.2
- PetaLinux 버전: 2024.2
- OS 환경: Ubuntu 22.04 권장

## 보드 DIP 스위치 설정 (AX7010 보드)

정상적으로 SD카드에서 부팅하려면 반드시 보드의 DIP 스위치를 다음과 같이 설정해야 합니다:

| MIO[5:3] | DIP 스위치 | 설정 |
|----------|------------|------|
| 010      | SW6        | SD Card 부팅 모드 ✅ |

---

# AX7010 PetaLinux 프로젝트 빌드 및 SD 카드 부팅 가이드

본 가이드는 Xilinx Vivado에서 생성한 `.xsa` 파일을 사용하여 PetaLinux 프로젝트를 구성하고, SD카드(EXT4 파티션)로 부팅하는 방법을 안내합니다.

---

## 준비물

- Vivado에서 생성된 `.xsa` 파일 (예: `design_1_wrapper.xsa`)
- SD 카드 (최소 8GB 권장)
- 리눅스 환경 (Ubuntu 권장)
- PetaLinux가 설치된 환경

---

## 1. XSA 파일 업로드

생성된 XSA 파일을 리눅스 환경에 업로드합니다.

예시 경로:
```
/home/mrdev/design_1_wrapper.xsa
```

---

## 2. 하드웨어(XSA) 파일을 프로젝트에 가져오기

```bash
cd /home/mrdev/ax7010_petalinux
petalinux-config --get-hw-description=/home/mrdev/design_1_wrapper.xsa
# 창이 열리면 바로 저장(Exit) → Yes 선택
```

---

## 3. 루트 파일시스템을 SD 카드(EXT4)로 설정하기

다음 명령어로 설정 창에 진입하여 루트 파일 시스템을 EXT4(SD)로 선택합니다.

```bash
petalinux-config
```

메뉴에서 다음과 같이 변경합니다.
```
→ Image Packaging Configuration
  └─ Root filesystem type
      └─ SD card (ext4)
```

⚠️ **중요 추가 설정** (부팅 안 되는 문제 방지)

다음 파일을 편집하여 부팅 옵션을 설정합니다.
```bash
vim project-spec/meta-user/conf/petalinuxbsp.conf
```

파일 끝에 아래 내용 추가:
```bash
EXTRA_BOOTARGS = "earlycon console=ttyPS0,115200 root=/dev/mmcblk0p2 rw rootwait"
```

---

## 4. 프로젝트 빌드

```bash
petalinux-build -x mrproper   # (옵션) 이전 빌드 초기화
petalinux-build
```

---

## 5. BOOT.BIN 및 부팅 이미지 생성

빌드 완료 후 다음 명령어로 부팅 파일을 생성합니다.

```bash
petalinux-package --boot --fsbl images/linux/zynq_fsbl.elf \
                  --fpga images/linux/system.bit \
                  --u-boot \
                  --force
```

---

## 6. 생성된 이미지 파일 확인

다음 경로에서 생성된 파일을 확인합니다.

```
ax7010_petalinux/images/linux/
├── BOOT.BIN
├── image.ub
└── rootfs.tar.gz
```

---

## 7. SD 카드 준비 및 파일 복사

SD 카드를 시스템에 삽입한 후 파티션 생성 및 파일 복사를 진행합니다.

### (1) SD 카드 파티션 생성

```bash
sudo fdisk /dev/sdb
```

다음과 같이 입력하여 파티션을 생성합니다.

|명령어|설명|
|---|---|
|`o`|기존 파티션 삭제 및 초기화|
|`n`, `p`, `1`, Enter, `+500M`|FAT32 파티션 생성|
|`t`, `c`|FAT32 파티션 타입 변경|
|`n`, `p`, `2`, Enter, Enter|나머지 공간을 EXT4 파티션 생성|
|`w`|저장 및 종료|

### (2) 파티션 포맷하기

```bash
sudo mkfs.vfat -F 32 -n BOOT /dev/sdb1
sudo mkfs.ext4 -L rootfs /dev/sdb2
```

---

## 8. 이미지 파일 SD카드에 복사하기

### (1) FAT32 파티션에 부팅 파일 복사

```bash
sudo mkdir -p /mnt/boot
sudo mount /dev/sdb1 /mnt/boot

sudo cp images/linux/BOOT.BIN /mnt/boot/
sudo cp images/linux/image.ub /mnt/boot/

sync
sudo umount /mnt/boot
```

### (2) EXT4 파티션에 루트 파일시스템 복사

```bash
sudo mkdir -p /mnt/rootfs
sudo mount /dev/sdb2 /mnt/rootfs

sudo tar xzf images/linux/rootfs.tar.gz -C /mnt/rootfs

sync
sudo umount /mnt/rootfs
```

---

## 9. 파일 최종 확인 (선택사항)

```bash
sudo mount /dev/sdb1 /mnt/boot
ls -al /mnt/boot
sudo umount /mnt/boot

sudo mount /dev/sdb2 /mnt/rootfs
ls -al /mnt/rootfs
sudo umount /mnt/rootfs
```

---

## 10. 보드 부팅

SD 카드를 보드에 장착하고 전원을 켜서 부팅을 진행하면 정상적으로 동작됩니다.

정상 부팅 확인 메시지:
```
U-Boot 202X.X

reading BOOT.BIN
reading image.ub

Booting kernel from SD card...
```

---

## 추가 참고 자료

- [Xilinx 공식 PetaLinux 설치 가이드](https://docs.xilinx.com/r/en-US/ug1144-petalinux-tools-reference-guide)
- [AX7010 보드 제조사(ALINX) 공식 자료](https://www.alinx.com)