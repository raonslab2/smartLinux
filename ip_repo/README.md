# Vivado 프로젝트 Git 관리 및 커스텀 IP 적용 가이드

이 가이드는 Vivado 프로젝트를 Git으로 관리하고, 커스텀 IP를 추가하여 사용하는 전체 과정을 안내합니다.

---

## 1. Vivado 프로젝트 Git 관리 설정

아래 구조를 권장합니다:

```
smartLinux
├── auto_create_project
│   ├── create_project.tcl
│   └── create_project.bat
│
├── ip_repo
│   └── axi_add2_1_0
│       ├── bd
│       ├── drivers
│       ├── example_designs
│       ├── hdl
│       ├── xgui
│       └── component.xml
│
└── linux_base
    └── linux_base.srcs
```

### `.gitignore` 설정

프로젝트 루트 디렉터리에 `.gitignore` 파일을 생성하여 아래 내용을 추가합니다.

```gitignore
*.jou
*.log
*.backup.jou
*.backup.log
*.cache/
*.hw/
*.runs/
*.sim/
*.gen/
*.tmp/
.ip_user_files/
.Xil/
*.str
*.xpr
```

### Git 원격 저장소 연결

```bash
git init
git remote add origin <GitHub Repository URL>
git add .
git commit -m "Vivado 프로젝트 최초 구성"
git branch -M main
git push -u origin main
```

---

## 2. 커스텀 IP를 Vivado에 추가하는 방법

### Vivado IP Repository 추가

Vivado를 열고:

```
Tools → Settings → IP → Repository
```

- `[+]` 버튼을 눌러 IP 경로 추가:
```
D:\git\smartLinux\ip_repo
```
- **Refresh** 버튼을 클릭해 IP 목록 갱신

### Vivado 프로젝트에서 IP 사용하기

- Block Design에서 IP 추가:
    - **[+]** 버튼 클릭 후 `axi_add2` 검색 및 선택
    - IP의 AXI 인터페이스 연결 및 구성
- Block Design 저장 및 Validate 수행

### 빌드 과정

```
Generate Bitstream → Export Hardware(.xsa) → (필요시 Petalinux로 이동)
```

---

## 3. 새로운 환경에서 프로젝트 복제 및 생성 방법

새로운 환경에서는 다음과 같이 프로젝트를 복제하여 생성합니다.

```bash
git clone <GitHub Repository URL>
cd smartLinux\auto_create_project
create_project.bat
```

Vivado가 실행되며 자동으로 프로젝트가 생성됩니다.

---

이제 Vivado 프로젝트가 깔끔하게 Git으로 관리되고, 커스텀 IP도 쉽게 추가하여 관리할 수 있습니다.