# 파이썬 가상 환경 설정 및 관리 방법

## 1. 가상 환경(virtualenv) 사용 이유
가상 환경은 여러 파이썬 프로젝트가 하나의 컴퓨터에서 충돌 없이 존재할 수 있도록 도와준다. 가상 환경을 사용하면 각 프로젝트별로 라이브러리 버전을 독립적으로 관리할 수 있으며, 여러 개발 환경을 독립적으로 설치하고 실행할 수 있다. 이를 통해 프로젝트 간의 의존성 문제를 방지할 수 있다.

## 2. venv 명령어로 가상 환경 만들기
```bash
c:\python>python -m venv c:\python\myenv
```
## 3 가상 환경 활성화 및 비활성화
- cmd 창에서 가상 환경 활성화
```bash
c:\python>python -m venv myPython
C:\python>python\myPython\Scripts\activate
```
## VS Code에서 가상 환경 사용하기
- VS Code 터미널에서 가상 환경을 만들고자 하는 폴더를 생성한다.
```bash
PS c::\my_python\2024_06\python -m venv 가상_환경_이름
```
Include, Lib, Scripts 폴더와 pyvenv.cfg 파일이 생성된 것을 볼 수 있다. pip install을 통해 설치할 라이브러리들은 Lib 폴더에 설치된다. src 폴더는 앞으로 작성할 프로그램들을 저장하기 위해 직접 만들어야 한다.

## 오류가 발생하는 경우
파이참이나 VS Code의 경우 터미널을 실행했을 때 명령(cmd) 창이 아닌 파워셸(Powershell) 창이 나타날 수 있다. 이런 경우 환경 변수 등이 제대로 적용되지 않아 오류가 발생할 수 있으므로 파워셸 창이 아닌 cmd 창이 나타나도록 다음과 같이 수정한다.

## VS Code 터미널에서 가상 환경 활성화
```bash
C:\my_Python\2024_06\myenv\Scripts>activate
```
## 가상 환경 비활성화
``` bash
C:\my_Python\2024_06\myenv\Scripts>deactivate
``` 
## 파이참에서 가상 환경 사용하기
- 파이참 설정에서 Settings → Terminal → Shell path → cmd.exe로 변경한다.

## 가상 환경 삭제하기
```bash
cd c::\my_python\2024_06\
rmdir /s /q myenv
```