# PetaLinux 프로젝트 빌드 및 SD 카드 부팅 이미지 제작 가이드

이 문서는 Vivado로부터 생성한 XSA 파일을 사용하여 PetaLinux 프로젝트를 구성하고, 이를 빌드하여 SD 카드 부팅 이미지를 제작하는 전체 과정을 설명합니다.

---

## 1. XSA 및 Bitstream 업로드

- **XSA 파일**: `/home/mrdev/design_1_wrapper.xsa`
- **Bitstream 파일**:  
  로컬에서:
  ```
  D:\vivadotest\linux\linux_base\linux_base.runs\impl_1\design_1_wrapper.bit
  ```
  업로드 대상:
  ```
  /home/mrdev/ax7010_petalinux/images/linux/design_1_wrapper.bit
  ```

---

## 2. 하드웨어(XSA) 파일을 PetaLinux 프로젝트에 가져오기

```bash
cd /home/mrdev/ax7010_petalinux
petalinux-build -x mrproper
petalinux-config --get-hw-description=/home/mrdev
# 메뉴 저장 후 종료(Exit → Yes)
```

---

## 3. 루트 파일시스템을 SD 카드(ext4)로 설정

```bash
petalinux-config
```

메뉴 설정:
```
→ Image Packaging Configuration
    → Root filesystem type
        → SD card (ext4)
```

설정 후 저장 및 종료(Exit → Yes)

---

## 4. AXI 인터페이스를 위한 UIO 드라이버 세팅(생량,pass)

```bash
petalinux-build -c kernel -x menuconfig
```

- UIO 드라이버 활성화 후 빌드하여 다음과 같이 확인:
```bash
ls /dev/uio*
```

---

## 5. 프로젝트 빌드

```bash
petalinux-build
```

---

## 6. BOOT.BIN 및 부팅 이미지 생성

```bash
petalinux-package --boot \
  --fsbl images/linux/zynq_fsbl.elf \
  --fpga images/linux/design_1_wrapper.bit \
  --u-boot \
  --force
```

---

## 7. 생성된 이미지 파일 확인

다음 경로에서 생성된 파일 확인:

```
/home/mrdev/ax7010_petalinux/images/linux/
├── BOOT.BIN
├── image.ub
└── rootfs.tar.gz
```

---

## 8. SD 카드 파티션 및 포맷

SD 카드가 `/dev/sdb`라고 가정하고 다음 작업 수행:

```bash
sudo fdisk /dev/sdb
# 기존 파티션 삭제 및 초기화: o

# FAT32 부팅 파티션 (500MB)
n → p → 1 → [Enter] → +500M
t → c

# EXT4 루트파일시스템 파티션
n → p → 2 → [Enter] → [Enter]

# 저장 후 종료
w
```

파티션 포맷:
```bash
sudo mkfs.vfat -F 32 -n BOOT /dev/sdb1
sudo mkfs.ext4 -L rootfs /dev/sdb2
```

---

## 9. 생성된 이미지 파일을 SD 카드에 복사

BOOT 파티션:
```bash
sudo mount /dev/sdb1 /mnt/boot
sudo cp images/linux/BOOT.BIN /mnt/boot/
sudo cp images/linux/image.ub /mnt/boot/
sync
sudo umount /mnt/boot
```

루트파일시스템 파티션:
```bash
sudo mount /dev/sdb2 /mnt/rootfs
sudo tar xzf images/linux/rootfs.tar.gz -C /mnt/rootfs
sync
sudo umount /mnt/rootfs
```

---

## 10. 최종 확인

```bash
sudo mount /dev/sdb1 /mnt/boot
ls -al /mnt/boot
sudo umount /mnt/boot

sudo mount /dev/sdb2 /mnt/rootfs
ls -al /mnt/rootfs
sudo umount /mnt/rootfs
```

---

## 11. 부팅 후 계정 설정

로그인:
```
ax7010_petalinux login: petalinux
# 최초 로그인 시 비밀번호 변경 요구됨
```

계정 추가 등 필요한 추가 작업 수행 가능.

---

이제 PetaLinux 빌드 과정과 SD 카드 부팅 이미지 제작이 성공적으로 완료되었습니다.

