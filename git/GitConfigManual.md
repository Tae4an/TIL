# Git Config 사용법

## 설정 파일 위치
- `--global` : 전역 설정 파일 사용
    - ex: `git config --global user.name "Your Name"` // 사용자 이름 설정
- `--system` : 시스템 설정 파일 사용
    - ex: `git config --system core.editor vim`
- `--local` : 저장소 설정 파일 사용
    - ex: `git config --local user.email "your.email@example.com"`
- `--worktree` : 작업 트리별 설정 파일 사용
    - ex: `git config --worktree core.autocrlf true`
- `-f, --file <file>` : 지정된 설정 파일 사용
    - ex: `git config -f ./config user.name "Your Name"`
- `--blob <blob-id>` : 지정된 blob 객체에서 설정 읽기
    - ex: `git config --blob 1234567 user.email "your.email@example.com"`

## 동작
- `--get` : 값 가져오기: `이름` [값 패턴]
    - ex: `git config --get user.name`
- `--get-all` : 모든 값 가져오기: `키` [값 패턴]
    - ex: `git config --get-all user.email`
- `--get-regexp` : 정규 표현식으로 값 가져오기: `이름-정규식` [값 패턴]
    - ex: `git config --get-regexp 'user.*'`
- `--get-urlmatch` : URL에 특정한 값 가져오기: `섹션[.변수] URL`
    - ex: `git config --get-urlmatch 'remote.origin.url' https://github.com`
- `--replace-all` : 일치하는 모든 변수 대체: `이름 값` [값 패턴]
    - ex: `git config --replace-all user.name "New Name"`
- `--add` : 새로운 변수 추가: `이름 값`
    - ex: `git config --add alias.co checkout`
- `--unset` : 변수 제거: `이름` [값 패턴]
    - ex: `git config --unset user.name`
- `--unset-all` : 모든 일치 항목 제거: `이름` [값 패턴]
    - ex: `git config --unset-all user.email`
- `--rename-section` : 섹션 이름 변경: `이전-이름 새-이름`
    - ex: `git config --rename-section old-section new-section`
- `--remove-section` : 섹션 제거: `이름`
    - ex: `git config --remove-section user`
- `-l, --list` : 모두 나열
    - ex: `git config --list`
- `--fixed-value` : 값 패턴과 비교할 때 문자열 동일성 사용
    - ex: `git config --fixed-value --get user.name`
- `-e, --edit` : 편집기 열기
    - ex: `git config --edit`
- `--get-color` : 설정된 색상 찾기: `슬롯` [기본값]
    - ex: `git config --get-color status.header blue`
- `--get-colorbool` : 색상 설정 찾기: `슬롯` [stdout이 tty일 때]
    - ex: `git config --get-colorbool color.ui`

## 타입
- `-t, --type <type>` : 값이 주어진 타입으로 설정됨
    - ex: `git config -t bool user.active true`
- `--bool` : 값이 "true" 또는 "false"
    - ex: `git config --bool user.active true`
- `--int` : 값이 십진수
    - ex: `git config --int core.pager 42`
- `--bool-or-int` : 값이 `--bool` 또는 `--int`
    - ex: `git config --bool-or-int core.logallrefupdates true`
- `--bool-or-str` : 값이 `--bool` 또는 문자열
    - ex: `git config --bool-or-str core.logallrefupdates "auto"`
- `--path` : 값이 경로 (파일 또는 디렉토리 이름)
    - ex: `git config --path core.excludesfile ~/.gitignore`
- `--expiry-date` : 값이 만료 날짜
    - ex: `git config --expiry-date gc.pruneexpire "now"`

## 기타 옵션
- `-z, --null` : 값을 NUL 바이트로 종료
    - ex: `git config -z --list`
- `--name-only` : 변수 이름만 표시
    - ex: `git config --name-only --list`
- `--includes` : 조회 시 include 지시문 준수
    - ex: `git config --includes --get-all user.name`
- `--show-origin` : 설정의 원본 표시 (파일, 표준 입력, blob, 명령줄)
    - ex: `git config --show-origin --list`
- `--show-scope` : 설정의 범위 표시 (작업 트리, 로컬, 전역, 시스템, 명령)
    - ex: `git config --show-scope --list`
- `--default <value>` : `--get`와 함께 사용 시, 항목이 없을 때 기본 값 사용
    - ex: `git config --default "default value" --get user.name`
