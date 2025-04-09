# Xilinx Zynq 7000 시리즈 개발보드 AX7010

## 개발보드 소개

### 개발보드 개요
이 개발보드는 Xilinx의 Zynq7000 시리즈 칩을 사용하며, 모델명은 XC7Z010-1CLG400I이고 400핀 FBGA 패키지입니다. ZYNQ7000 칩은 프로세서 시스템(PS, Processor System)과 프로그래머블 로직(PL, Programmable Logic) 두 부분으로 나뉩니다. AX7010 개발보드는 ZYNQ7000의 PS와 PL 부분 모두에 풍부한 외부 인터페이스와 주변장치를 갖추고 있어 사용자 기능 구현 및 검증에 편리합니다. 또한, 보드 내에 Xilinx USB Cable 다운로드 회로가 내장되어 있어, USB 케이블 하나로 개발보드의 프로그램 다운로드 및 디버깅이 가능합니다.

### 주요 특징
1. +5V 전원 입력 지원, 최대 2A 전류 보호회로 탑재
2. Xilinx ARM+FPGA 칩 Zynq-7000 XC7Z010-1CLG400C 탑재
3. 대용량 고속 DDR3 SDRAM 2개 (각 2Gbit, 총 4Gbit), 데이터 캐시 및 운영체제 메모리로 사용 가능
4. 256Mbit QSPI FLASH, 시스템 파일과 사용자 데이터 저장에 사용 가능
5. 10/100M/1000M 이더넷 RJ-45 인터페이스 1개, 컴퓨터 및 기타 네트워크 장비와의 데이터 통신 가능
6. HDMI 영상 입력 및 출력 인터페이스 1개, 최대 1080P 해상도 지원
7. 고속 USB2.0 HOST 인터페이스 1개, 마우스, 키보드, USB 저장장치 연결 가능
8. 고속 USB2.0 OTG 인터페이스 1개, PC 및 USB 장치와 OTG 통신 가능
9. USB UART 인터페이스 1개, PC 및 외부 장치와 시리얼 통신 지원
10. RTC 실시간 시계 1개 (배터리 홀더 포함, 배터리 모델: CR1220)
11. IIC 인터페이스 EEPROM 24LC04 1개
12. 사용자 LED 6개 (PS 제어 2개, PL 제어 4개)
13. 버튼 7개 (CPU 리셋 1개, PS 제어 2개, PL 제어 4개)
14. PS 시스템용 33.333MHz 액티브 크리스탈 오실레이터 및 PL 로직용 50MHz 크리스탈 오실레이터 제공
15. ZYNQ PL의 IO 확장을 위한 40핀 확장 포트 2개 (2.54mm 핀 간격), 7인치 TFT 모듈, 카메라, AD/DA 모듈 등 연결 가능
16. ZYNQ PS의 MIO 확장을 위한 12핀 확장 포트 1개 (2.54mm 핀 간격)
17. USB JTAG 포트 1개 (USB 케이블과 온보드 JTAG 회로로 디버깅 및 다운로드 가능)
18. Micro SD 카드 홀더 1개 (보드 후면), OS 이미지 및 파일시스템 저장 가능

---

## AX7010 문서 및 튜토리얼 링크

> **주의**: 문서 하단에서 중국어와 영어 간 전환이 가능합니다.

---

# AX7010 예제 사용법

## 예제 설명
본 프로젝트는 AX7010 개발보드의 기본(팩토리) 예제로, 보드 내 대부분의 주변장치를 지원합니다.

## 개발 환경 및 요구사항
- Vivado 2023.1 이상
- AX7010 개발보드
 
---

## Vivado 프로젝트 생성 방법
1. 최신 ZIP 패키지를 다운로드합니다.
2. 프로젝트를 저장할 새 폴더를 생성합니다.
3. 다운로드한 ZIP 파일을 이 폴더에 압축 해제합니다.

Vivado 프로젝트는 두 가지 방법으로 생성할 수 있습니다.

---

### 방법 1: Vivado TCL 콘솔을 이용한 프로젝트 생성
1. Vivado 소프트웨어를 실행한 뒤 TCL 콘솔에서 **cd** 명령어를 통해 "**auto_create_project**" 디렉토리로 이동합니다.

```bash 
cd \<archive extracted location\>/vivado/auto_create_project
```
2. 아래의 명령을 입력하여 프로젝트를 생성합니다.
```
source ./create_project.tcl
```

### Windows에서 배치(.bat) 파일을 이용한 프로젝트 생성
1. "auto_create_project" 폴더에 있는 "create_project.bat" 파일을 마우스 오른쪽 버튼으로 클릭한 뒤 편집하여 본인의 Vivado 설치 경로로 수정하고 저장합니다.
```
CALL E:\XilinxVitis\Vivado\2023.1\bin\vivado.bat -mode batch -source create_project.tcl
PAUSE
```
2. 수정한 "create_project.bat" 파일을 더블클릭하여 Vivado 프로젝트를 생성합니다.


For more information, please visit [ALINX website](https://www.alinx.com)
