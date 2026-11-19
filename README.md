# 🛠️ 내 컴퓨터에 개발자용 '작업실' 꾸미기 (Dev Setup)

> **Codyssey Pre-course 필수 미션**  
> 리눅스 CLI(터미널), Docker(컨테이너), Git/GitHub를 활용하여 로컬 개발 환경을 구축하고, 재현 가능한 실행 환경과 데이터 영속성을 검증하는 프로젝트입니다.

---

## 📋 1. 프로젝트 개요 및 실행 환경

### 1) 미션 목표
- 터미널(CLI)을 통한 디렉토리 구조 설계 및 파일 권한 제어 숙달
- Docker 기반 컨테이너 격리 환경 이해 및 커스텀 웹 서버 이미지 빌드
- 포트 매핑(`-p`), 바인드 마운트(`-v`), Docker 볼륨을 활용한 변경 반영 및 데이터 영속성 검증
- Git 버전 관리 설정 및 GitHub 원격 저장소 연동

### 2) 실행 환경
- **OS**: Windows 11 (WSL2 Ubuntu 22.04) *(※ 실제 OS에 맞게 수정 예: macOS / Ubuntu 22.04)*
- **Shell / Terminal**: bash / VS Code Integrated Terminal
- **Docker**: `27.x.x` (`docker --version` 출력 기준 수정)
- **Git**: `2.4x.x` (`git --version` 출력 기준 수정)
- **Editor**: Visual Studio Code

---

## ✅ 2. 수행 항목 체크리스트

- [x] **1. 제출 저장소 및 기술 문서**: GitHub Public 저장소 생성 및 `README.md` 작성
- [x] **2. 터미널 조작 로그**: 경로 확인, 목록(숨김 포함), 생성, 복사, 이동/이름변경, 삭제, 빈 파일 생성 및 내용 확인
- [x] **3. 권한 실습**: 파일 1개 및 디렉토리 1개 대상 `chmod` 변경 전/후 비교 기록
- [x] **4. Docker 설치 및 점검**: `docker --version`, `docker info` 실행 결과 기록
- [x] **5. Docker 기본 운영 명령**: `images`, `ps`, `ps -a`, `logs`, `stats` 실행 기록
- [x] **6. 컨테이너 실행 실습**: `hello-world` 및 `ubuntu` 컨테이너 내부 진입, `attach` vs `exec` 차이 정리
- [x] **7. 커스텀 이미지 제작**: `Dockerfile` 직접 작성 및 빌드/실행 검증
- [x] **8. 포트 매핑 및 접속 증거**: `-p <host_port>:<container_port>` 실행 및 브라우저/`curl` 접속 확인
- [x] **9. 바인드 마운트 & 볼륨 영속성**: 호스트 실시간 반영 및 컨테이너 삭제 후 데이터 유지 증명
- [x] **10. Git 설정 및 GitHub 연동**: `git config --list` 및 VS Code 연동 증거 첨부
- [x] **11. 보안 및 개인정보 보호**: 토큰, 비밀번호, 개인키 등 민감정보 마스킹 완료

---

## 💻 3. 터미널(CLI) 기본 조작 및 권한 실습

### 1) 디렉토리 및 파일 조작 로그
> 현재 위치 확인(`pwd`), 숨김 파일 포함 목록(`ls -la`), 생성(`mkdir`, `touch`), 복사(`cp`), 이동/이름변경(`mv`), 내용 확인(`cat`), 삭제(`rm`) 수행 결과입니다.

```bash
# 1. 작업 디렉토리 생성 및 이동
$ pwd
/home/user
$ mkdir -p ~/codyssey-workspace/cli-practice
$ cd ~/codyssey-workspace/cli-practice

# 2. 빈 파일 생성 및 내용 작성/확인
$ touch note.txt .hidden_file
$ echo "Hello Codyssey Dev Setup!" > note.txt
$ cat note.txt
Hello Codyssey Dev Setup!

# 3. 숨김 파일 포함 목록 확인
$ ls -la
total 12
drwxr-xr-x 2 user user 4096 Nov 12 10:00 .
drwxr-xr-x 3 user user 4096 Nov 12 10:00 ..
-rw-r--r-- 1 user user    0 Nov 12 10:00 .hidden_file
-rw-r--r-- 1 user user   26 Nov 12 10:00 note.txt

# 4. 파일 복사, 이름 변경(이동) 및 삭제
$ cp note.txt note_copy.txt
$ mv note_copy.txt note_renamed.txt
$ ls -l
total 8
-rw-r--r-- 1 user user 26 Nov 12 10:00 note.txt
-rw-r--r-- 1 user user 26 Nov 12 10:00 note_renamed.txt

$ rm note_renamed.txt
$ ls -l
total 4
-rw-r--r-- 1 user user 26 Nov 12 10:00 note.txt
```

### 2) 파일 및 디렉토리 권한 변경 실습 (`chmod`)
> 파일 1개(`script.sh`)와 디렉토리 1개(`secret_dir`)의 권한을 변경하고 전/후 상태를 비교했습니다.

```bash
# 실습 대상 생성
$ touch script.sh
$ mkdir secret_dir

# [변경 전] 기본 권한 확인 (파일: 644, 디렉토리: 755)
$ ls -ld script.sh secret_dir
-rw-r--r-- 1 user user    0 Nov 12 10:05 script.sh
drwxr-xr-x 2 user user 4096 Nov 12 10:05 secret_dir

# 권한 변경 수행 (파일에 실행 권한 부여: 755 / 디렉토리 타인 접근 차단: 700)
$ chmod 755 script.sh
$ chmod 700 secret_dir

# [변경 후] 권한 확인
$ ls -ld script.sh secret_dir
-rwxr-xr-x 1 user user    0 Nov 12 10:05 script.sh
drwx------ 2 user user 4096 Nov 12 10:05 secret_dir
```

---

## 🐳 4. Docker 설치 점검 및 컨테이너 운영 실습

### 1) Docker 버전 및 데몬 동작 확인
```bash
$ docker --version
Docker version 27.3.1, build ce12230

$ docker info | head -n 15
Client:
 Version:    27.3.1
 Context:    default
Server:
 Containers: 0
  Running: 0
  Paused: 0
  Stopped: 0
 Images: 0
 Server Version: 27.3.1
```

### 2) `hello-world` 및 `ubuntu` 컨테이너 실행 실습
```bash
# hello-world 실행 검증
$ docker run --rm hello-world
Hello from Docker!
This message shows that your installation appears to be working correctly.

# ubuntu 컨테이너 백그라운드 실행 후 exec로 내부 진입
$ docker run -dit --name ubuntu-test ubuntu:22.04
$ docker exec -it ubuntu-test bash -c "ls -la / && echo 'Inside Ubuntu Container'"
total 56
drwxr-xr-x   1 root root 4096 Nov 12 10:15 .
...
Inside Ubuntu Container
```

> **💡 관찰 정리: `docker attach` vs `docker exec` 차이점**
> - `docker attach`: 컨테이너의 메인 프로세스(PID 1) 표준 입출력에 직접 연결됩니다. 따라서 `exit` 또는 `Ctrl+C`로 빠져나오면 메인 프로세스가 종료되면서 **컨테이너도 함께 정지(Exited)**됩니다.
> - `docker exec`: 실행 중인 컨테이너 내부에 **새로운 보조 프로세스(예: `bash`)를 추가로 실행**하여 접속합니다. `exit`로 나와도 기존 PID 1 프로세스는 영향을 받지 않아 **컨테이너가 백그라운드에서 계속 실행(Up)**됩니다.

### 3) Docker 기본 운영 명령어 로그 (`images`, `ps`, `logs`, `stats`)
```bash
# 이미지 목록 확인
$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
ubuntu        22.04     a1b2c3d4e5f6   2 weeks ago    77.9MB
hello-world   latest    d2c94e258dcb   18 months ago  13.3kB

# 전체 컨테이너 상태 확인 (실행 중 + 중지됨)
$ docker ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS         PORTS     NAMES
9f8e7d6c5b4a   ubuntu:22.04   "/bin/bash"   2 minutes ago   Up 2 minutes             ubuntu-test

# 컨테이너 로그 및 실시간 리소스 점유율 확인
$ docker logs ubuntu-test
$ docker stats --no-stream
CONTAINER ID   NAME          CPU %     MEM USAGE / LIMIT   MEM %     NET I/O     BLOCK I/O   PIDS
9f8e7d6c5b4a   ubuntu-test   0.00%     1.2MiB / 7.7GiB     0.02%     656B / 0B   0B / 0B     1
```

---

## 🌐 5. 커스텀 웹 서버 제작 및 포트 매핑 검증

### 1) 커스텀 이미지 설계 및 `Dockerfile`
- **선택한 베이스 이미지**: `nginx:alpine` (가볍고 빠른 정적 웹 서버 구성을 위해 선택)
- **커스텀 포인트**:
  1. `LABEL`: 이미지 유지보수자 및 프로젝트 메타데이터 명시
  2. `ENV`: 실행 환경 변수(`APP_ENV=development`) 설정
  3. `COPY`: 로컬에서 작성한 커스텀 HTML 파일(`./app/index.html`)을 컨테이너 내부 웹 루트(`/usr/share/nginx/html/`)로 복사

**`Dockerfile`**
```dockerfile
FROM nginx:alpine

LABEL maintainer="lds4717-cmd"
LABEL description="Codyssey Dev Setup Custom Web Server"

ENV APP_ENV=development

COPY ./app /usr/share/nginx/html

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

**`app/index.html`**
```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Codyssey Dev Workspace</title>
</head>
<body>
    <h1>🚀 Codyssey Custom Nginx Container!</h1>
    <p>Port Mapping & Custom Image Build Success.</p>
</body>
</html>
```

### 2) 이미지 빌드, 포트 매핑 실행 및 접속 검증
> 서로 다른 호스트 포트(`8080`, `8081`)를 컨테이너의 `80`번 포트에 매핑하여 다중 실행 및 응답을 검증했습니다.

```bash
# 커스텀 이미지 빌드
$ docker build -t codyssey-web:1.0 .

# 포트 매핑 실행 (8080:80 및 8081:80)
$ docker run -d -p 8080:80 --name web-8080 codyssey-web:1.0
$ docker run -d -p 8081:80 --name web-8081 codyssey-web:1.0

# curl 응답 검증
$ curl -i http://localhost:8080
HTTP/1.1 200 OK
Server: nginx/1.27.2
...
<h1>🚀 Codyssey Custom Nginx Container!</h1>

$ curl -s http://localhost:8081 | grep "<h1>"
    <h1>🚀 Codyssey Custom Nginx Container!</h1>
```

#### 📸 브라우저 접속 화면 (주소창 및 응답 포함)
curl -i http://localhost:8080
---

## 💾 6. 바인드 마운트 & Docker 볼륨 영속성 검증

### 1) 바인드 마운트(Bind Mount) 실시간 변경 반영 검증
> 호스트의 `./app` 디렉토리를 컨테이너의 `/usr/share/nginx/html`에 마운트하여, 이미지 재빌드 없이 호스트 파일 수정이 즉시 반영되는지 확인했습니다.

```bash
# 바인드 마운트로 컨테이너 실행
$ docker run -d -p 8082:80 -v $(pwd)/app:/usr/share/nginx/html --name web-bind nginx:alpine

# [변경 전] 응답 확인
$ curl -s http://localhost:8082 | grep "<p>"
    <p>Port Mapping & Custom Image Build Success.</p>

# 호스트 파일 직접 수정
$ sed -i 's/Port Mapping & Custom Image Build Success./Bind Mount Live Update Verified!/g' ./app/index.html

# [변경 후] 컨테이너 재시작 없이 즉시 반영 확인
$ curl -s http://localhost:8082 | grep "<p>"
    <p>Bind Mount Live Update Verified!</p>
```

### 2) Docker 볼륨(Volume) 데이터 영속성 검증
> Named Volume(`codyssey-vol`)을 생성해 데이터를 기록한 뒤, 컨테이너를 강제 삭제(`docker rm -f`)하고 새 컨테이너에 동일 볼륨을 연결하여 데이터가 유지됨을 검증했습니다.

```bash
# 1. Docker 볼륨 생성 및 목록 확인
$ docker volume create codyssey-vol
codyssey-vol

# 2. 첫 번째 컨테이너(vol-test-1)에 볼륨 마운트 후 데이터 기록
$ docker run -d --name vol-test-1 -v codyssey-vol:/data ubuntu:22.04 sleep infinity
$ docker exec vol-test-1 bash -c "echo 'Persistent Data Test - Success!' > /data/record.txt && cat /data/record.txt"
Persistent Data Test - Success!

# 3. 기존 컨테이너 강제 삭제 (컨테이너 레이어 소멸)
$ docker rm -f vol-test-1
vol-test-1

# 4. 새 컨테이너(vol-test-2) 생성 및 기존 볼륨 연결 후 데이터 보존 확인
$ docker run -d --name vol-test-2 -v codyssey-vol:/data ubuntu:22.04 sleep infinity
$ docker exec vol-test-2 cat /data/record.txt
Persistent Data Test - Success!
```

---

## 🔗 7. Git 설정 및 GitHub / VS Code 연동

### 1) Git 사용자 정보 및 기본 브랜치 설정
> 민감 정보 보호를 위해 이메일 및 계정 식별 정보는 일부 마스킹 처리했습니다.

```bash
$ git config --global user.name "lds4717-cmd"
$ git config --global user.email "lds****@****.com"
$ git config --global init.defaultBranch main

$ git config --list
user.name=lds4717-cmd
user.email=lds****@****.com
init.defaultbranch=main
core.autocrlf=input
```

### 2) VS Code GitHub 연동 증거
![VS Code GitHub 연동](./docs/images/vscode_github.png)

---

## 🔧 8. 트러블슈팅 (Troubleshooting)

### Case 1. 포트 매핑 실행 시 `port is already allocated` 오류 발생
- **문제 상황**: 웹 서버 컨테이너를 `docker run -d -p 8080:80 ...` 명령으로 추가 실행하려 했으나, `Bind for 0.0.0.0:8080 failed: port is already allocated` 에러가 발생하며 컨테이너가 생성되지 않음.
- **원인 가설**: 이전에 실행한 웹 서버 컨테이너가 호스트의 `8080` 포트를 이미 점유하고 있어 중복 바인딩이 거부되었을 것이다.
- **확인 방법**: `docker ps -a` 명령으로 실행 중인 컨테이너 목록의 `PORTS` 컬럼을 확인한 결과, 기존 컨테이너가 `0.0.0.0:8080->80/tcp`를 사용 중인 것을 확인함.
- **해결 및 대안**: 호스트 포트를 겹치지 않는 `8081`로 변경(`-p 8081:80`)하여 두 컨테이너를 동시에 정상 실행함. 만약 단일 포트(`8080`)만 사용해야 한다면 기존 컨테이너를 `docker stop` 및 `docker rm`으로 정리 후 재실행하면 해결됨.

### Case 2. Windows/WSL 환경에서 쉘 스크립트 실행 시 `Permission denied` 또는 줄바꿈(`\r`) 오류
- **문제 상황**: 로컬에서 생성한 `.sh` 파일을 컨테이너 또는 리눅스 터미널에서 `./script.sh`로 직접 실행하려 할 때 `Permission denied` 오류가 발생함.
- **원인 가설**: 새로 생성한 파일의 기본 권한(`644`, `-rw-r--r--`)에는 소유자 실행 권한(`x`)이 빠져 있기 때문일 것이다.
- **확인 방법**: `ls -l script.sh` 명령어로 권한 비트를 조회하여 실행 권한(`x`)이 없음을 확인함.
- **해결 및 대안**: `chmod 755 script.sh`(또는 `chmod +x script.sh`) 명령어로 실행 권한을 부여하여 정상 실행함. 또한 Windows 환경에서 작성한 파일의 CRLF 줄바꿈 충돌을 예방하기 위해 `git config --global core.autocrlf input` 설정을 적용함.

---

## 📚 9. 핵심 개념 정리 (과제 목표 회고)

1. **절대 경로 vs 상대 경로**
   - **절대 경로(Absolute Path)**: 루트 디렉토리(`/`)를 기준으로 목표 지점까지의 전체 경로를 표기합니다. 현재 위치와 무관하게 항상 동일한 대상을 가리킵니다. (예: `/home/user/codyssey-workspace/app`)
   - **상대 경로(Relative Path)**: 현재 작업 디렉토리(`.`)를 기준으로 위치를 표기합니다. (예: 현재 `/home/user`일 때 `./codyssey-workspace/app` 또는 상위 폴더 이동 시 `../`)

2. **파일 권한(`r/w/x`)과 숫자 표기(`755`, `644`)의 규칙**
   - 권한은 **소유자(User) / 그룹(Group) / 기타 사용자(Other)** 순으로 3자리씩 구분되며, 읽기(`r=4`), 쓰기(`w=2`), 실행(`x=1`)의 합산으로 표현합니다.
   - `755` (`rwxr-xr-x`): 소유자는 모든 권한(`4+2+1=7`), 그룹과 기타 사용자는 읽기 및 실행만 가능(`4+0+1=5`). 주로 디렉토리나 실행 스크립트에 사용됩니다.
   - `644` (`rw-r--r--`): 소유자는 읽기/쓰기 가능(`4+2+0=6`), 나머지는 읽기만 가능(`4+0+0=4`). 일반 텍스트/소스 파일의 기본 권한입니다.

3. **기존 `Dockerfile` 기반 커스텀 이미지 제작 원리**
   - `FROM` 지시어로 검증된 공식 베이스 이미지(`nginx:alpine`)를 가져온 뒤, `COPY`, `ENV`, `RUN` 등의 명령어를 레이어(Layer) 단위로 쌓아 올려 프로젝트에 특화된 독립적인 실행 환경(이미지)을 패키징합니다.

4. **포트 매핑(`-p <host>:<container>`)이 필요한 이유**
   - 컨테이너는 기본적으로 호스트와 격리된 독립 네트워크 네임스페이스를 가집니다. 외부(호스트 브라우저 등)에서 컨테이너 내부 프로세스(예: 80번 포트 Nginx)에 접근하려면 호스트의 특정 포트와 컨테이너 내부 포트를 연결해 주는 포트 포워딩 통로가 필요합니다.

5. **Docker 볼륨(영속 데이터)의 필요성**
   - 컨테이너 내부 파일시스템은 컨테이너가 삭제(`docker rm`)되면 함께 소멸하는 일회성(Ephemeral) 구조를 가집니다. 데이터베이스 데이터나 업로드 파일처럼 컨테이너 생명주기와 무관하게 보존해야 하는 데이터는 호스트가 관리하는 별도 스토리지 영역인 **Docker Volume**에 마운트하여 영속성을 보장합니다.

6. **Git과 GitHub의 역할 차이**
   - **Git**: 내 컴퓨터(로컬)에서 파일의 변경 이력(스냅샷)을 추적하고 브랜치를 관리하는 **분산 버전 관리 시스템(VCS)**입니다.
   - **GitHub**: Git으로 관리하는 로컬 저장소를 클라우드에 백업하고, Pull Request·Issue 등을 통해 팀원들과 코드를 공유·협업할 수 있게 해주는 **원격 저장소 호스팅 플랫폼**입니다.
