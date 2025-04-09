# Vivado FPGA 프로젝트 및 비트스트림 생성 종합 가이드

이 문서는 Xilinx Vivado를 사용하여 FPGA 프로젝트를 생성하고 비트스트림(Bitstream)을 만드는 전체 과정을 단계별로 상세히 안내합니다.

---

## 🔧 사용 환경

| 항목 | 사용 환경 |
|------|----------|
| Vivado 버전 | 2024.2 |
| 운영체제 | Windows 11 또는 Ubuntu 22.04 |
| FPGA 보드 | AX7010 (Zynq-7000 XC7Z010-1CLG400C) |

---

## 🚀 1단계: Vivado 프로젝트 생성

다음 순서로 Vivado에서 프로젝트를 생성합니다:

1. Vivado 실행 후 **Create Project** 클릭
2. 프로젝트 이름과 위치 설정

   예시:
   ```
   프로젝트 이름: led_example
   위치: D:\AX7010_projects\led_example
   ```

3. 프로젝트 유형에서 `RTL Project` 선택
4. 타겟 디바이스로 **XC7Z010-1CLG400C** 선택 (AX7010 보드)

---

## 📂 2단계: 소스 파일 추가

프로젝트가 생성되면 다음과 같이 소스파일을 추가합니다:

- **RTL 소스파일 추가**:
    - Verilog (`.v`, `.vh`) 또는 VHDL (`.vhd`) 소스 파일

- **제약조건 (Constraints) 파일 추가**:
    - 핀 할당 및 클럭 제약 조건 등을 설정한 `.xdc` 파일

소스파일 추가는 Vivado의 **Sources** 탭 → `Add Sources`에서 수행합니다.

---

## 🔨 3단계: Synthesis 및 Implementation 수행

소스파일 추가 후 아래 순서대로 진행합니다:

1. 좌측 Flow Navigator에서 **Run Synthesis** 클릭하여 합성 수행
2. 합성 후 나타나는 경고나 오류를 확인하고 수정
3. 합성 완료 후 **Run Implementation** 클릭하여 구현 수행

모든 과정이 완료되면, 하드웨어 구현이 정상적으로 이루어집니다.

---

## 📥 4단계: Bitstream 생성

Implementation 완료 후 **Generate Bitstream**을 클릭하여 FPGA에 프로그래밍 가능한 비트스트림 파일을 생성합니다.

- 생성된 비트스트림 파일(`*.bit`)은 다음 경로에 저장됩니다:

```
<프로젝트 경로>/<프로젝트명>.runs/impl_1/*.bit
```

```

## 📌 5단계: FPGA 보드 프로그래밍 (하드웨어 매니저 사용)

Vivado 하드웨어 매니저를 사용하여 FPGA 보드에 비트스트림을 프로그래밍합니다.

1. FPGA 보드와 컴퓨터를 USB(JTAG) 케이블로 연결
2. Vivado 상단 메뉴의 **Open Hardware Manager** 선택
3. 하드웨어 매니저에서 **Open Target → Auto Connect** 선택
4. **Program Device** 클릭 후 생성된 `.bit` 파일 선택 및 프로그래밍

정상적으로 프로그래밍되면 FPGA 보드가 바로 동작합니다.

---

## 📦 6단계: 하드웨어(XSA) 파일 추출 (PetaLinux 용)

Vivado에서 PetaLinux 연동을 위해 필요한 XSA 파일을 다음과 같이 추출합니다.

1. Vivado 메뉴에서 **File → Export → Export Hardware** 선택
2. **Include bitstream** 옵션 체크 후 내보내기 실행
3. 추출된 `.xsa` 파일은 아래 위치에 저장됩니다:

```
<프로젝트 경로>/<프로젝트명>.sdk/*.xsa
```

---

## 📚 추가 참고 자료

- [Xilinx Vivado 공식 매뉴얼](https://docs.xilinx.com/r/en-US/ug910-vivado-getting-started)
- [Xilinx FPGA Support 포럼](https://support.xilinx.com)

---

🎉 **축하합니다! FPGA 프로젝트 생성 및 비트스트림 생성 과정을 성공적으로 완료하셨습니다.**