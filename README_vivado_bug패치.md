# ✅ Vivado Export Hardware 시 bitstream 포함 안내

## 📌 상황
Vivado에서 `.xsa` 내보내기 시 "Include bitstream" 옵션을 선택하면 다음 메시지가 뜹니다:

> **Bitstream out-of-date**  
> Implementation run is out of date. Exported hardware information may be inconsistent...

## ✅ 선택에 따른 의미

| 선택  | 의미                                               | 안전한가?               |
|-------|----------------------------------------------------|--------------------------|
| ✅ Yes | 오래된 bitstream을 포함한 채 .xsa 생성             | **예** (Petalinux FSBL용 OK) |
| ✅ No  | bitstream 없이 .xsa 생성                           | **예** (더 빠르고 깔끔함)    |
| ⚠️ Run Implementation 후 export | 최신 bitstream이 필요한 경우 (보드 다운로드용) | 필요 시만 실행          |

## ✅ 결론: 지금 "Yes" 눌러도 괜찮은가?

- **Yes 눌러도 전혀 문제 없습니다.**
- Petalinux는 `.xsa` 안의 **드라이버 정보만 사용**하기 때문에  
  오래된 bitstream이 들어 있어도 FSBL 빌드에는 영향 없습니다.

---

## 🧠 참고: bitstream이 필요한 경우

보드에 직접 `.bit`/`.bin`을 다운로드하려면 다음 절차 필요:

```bash
Vivado → Run Synthesis → Run Implementation → Generate Bitstream
```


---

# 🛠️ Vivado 커스텀 IP Makefile 버그 해결 가이드 (Markdown 목업)

```md
# 🛠️ Vivado 커스텀 IP Makefile 버그 해결 가이드

## 📌 문제 요약

Vivado가 생성한 커스텀 IP의 드라이버 Makefile에 다음과 같은 문법 오류가 들어있음:

```makefile
LIBSOURCES = (wildcard *.c)   # ❌ 쉘에서 에러 발생
/bin/sh: -c: syntax error near unexpected token `('
