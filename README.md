## DSONE-SDHC-KOREAN
DSONE-SDHC EOS FIX

DSONE-SDHC 한글 메뉴 수정.

Korean Language Menu fix.

마지막 실행 ROM 포커스 기능 추가.

Last Launched ROM Focus.

[Implemented using Codex GPT-5.5.]

---

<https://nds.flashcarts.net/card/dsone-sdhc>

카트리지 정보

---

## 상세 변경 사항
한국어 관련.
- 한국어가 메뉴 언어 항목으로 표시.
- 한국어 선택 상태 체크 표시가 정상 동작.
- 런타임 언어 전환 중 텍스트 렌더링이 `settings.language`에만 의존하지 않도록 수정.
- 렌더러는 현재 로드된 font object의 vtable을 보고 한국어 variable-font 경로를 선택.
- 이 방식으로 메뉴 선택값과 저장값은 즉시 바뀌어도, 렌더링은 실제 로드된 `local.fon` 형식에 맞춰 유지.

마지막 실행 ROM 포커스.
- 마지막 실행 ROM을 부팅 후 다시 포커스하는 helper를 추가
- helper는 원본 펌웨어가 이미 로드해 둔 global settings 구조체에서 `enterLastDirWhenBoot == 1`일 때만 활성화.
- gate는 global settings pointer `0x02079c9c`를 읽고 `settings + 0x12` 바이트를 확인.
- `enterLastDirWhenBoot=0` 또는 settings pointer 없음은 last-ROM 포커싱 비활성화로 처리.
- helper는 `_dsone/LASTSAVE.INI`를 읽음.
- 펌웨어가 기존에 저장하던 `[Dir Info] last dir` 값을 사용.
- 정상 브라우저 UI loop가 활성화된 뒤에만 실행.
- `/` 기준으로 경로를 나누고, 한 번에 한 path component만 처리.

Last Launched ROM Focus
- Added a boot-time focus helper for the last launched ROM.
- The helper is enabled only when the firmware's already-loaded global settings structure has `enterLastDirWhenBoot == 1`.
- The gate reads the global settings pointer at `0x02079c9c` and checks byte `settings + 0x12`.
- `enterLastDirWhenBoot=0` or a missing settings pointer leaves last-ROM focusing disabled.
- The helper reads `_dsone/LASTSAVE.INI`.
- It uses `[Dir Info] last dir`, matching the firmware's existing save path.
- It splits paths by `/` and processes one path component at a time.

Main Binary Patch Points
- Korean item8 menu hook:
  - hook file offset: `0x2fbaa`
  - code cave file offset: `0x798e8`
- Font-object renderer guard:
  - hook file offset: `0x36930`
  - code cave file offset: `0x79a20`
- Last-ROM focus UI-loop hooks:
  - first UI loop hook file offset: `0x00dab4`
  - state-9 UI loop hook file offset: `0x00dada`
  - auto-enter helper file offset: `0x079108`
  - focus wrapper file offset: `0x079a2c`


## 기본 커널에서 바뀐 부분
1. globalsettings.ini 에서 language=8, langDirectory=lang_kor로 변경.
2. _dsone\language\lang_kor\language.txt 에서 한글 번역 + cp949 인코딩 / 상황에 따라 ASCII만 쓰는 부분이 존재함.
3. 언어 선택 메뉴에 한국어 추가, 언어 선택 관련 버그 수정.
4. globalsettings.ini 에서 enterLastDirWhenBoot=1 추가, 마지막 실행 ROM 포커스 기능 추가.


## 사용 방법

<https://flashcard-archive.ds-homebrew.com/SuperCard/DSONE_SDHC_DSONEi/SuperCard_DSONE_SDHC_EOS_sp6_20121206.zip>

1. DSONE-SDHC 기본 커널파일을 받습니다.

<https://github.com/multi-vitamin/DSONE-SDHC-KOREAN/releases>

2. 위 링크의 zip 파일을 받고 압축해제.

3. 1번의 기본 커널 파일 압축을 해제하고 2번의 파일을 덮어씁니다.
