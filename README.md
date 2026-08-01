# 개발 워크스테이션 구축 미션

## 1) 프로젝트 개요

서울캠퍼스 개발 워크스테이션 구축 미션으로, 터미널(리눅스 CLI), Docker(OrbStack), Git/GitHub을 직접 손으로 다뤄보며 재현 가능한 개발 환경을 세팅하는 것을 목표로 한다. 터미널 기초 조작과 파일 권한 실습 → Docker 설치 및 컨테이너 운영 → 커스텀 Dockerfile로 웹서버 컨테이너화 → 포트 매핑/바인드 마운트/볼륨 실습 → Git/GitHub 연동 순으로 진행했다.

## 2) 실행 환경

- OS: macOS
- Shell: zsh
- 컨테이너 엔진: OrbStack (Docker Desktop 대체, 서울캠퍼스 sudo 권한 제한 대응)
- Docker: 28.5.2 (build ecc6942)
- Git: 2.53.0

## 3) 수행 체크리스트

- [x] 터미널 기본 조작 및 폴더 구성 (절대/상대 경로, 생성/복사/이동/삭제)
- [x] 파일/디렉토리 권한 변경 실습 (644 → 755, 755 → 700)
- [x] Docker 설치/점검 (`docker --version`, `docker info`)
- [x] hello-world 실행
- [x] ubuntu 컨테이너 진입 실습
- [x] 컨테이너 종료(exit) vs 유지(exec) 차이 관찰
- [x] 커스텀 Dockerfile 빌드/실행 (nginx:alpine 베이스)
- [x] 포트 매핑 접속 (2회: 8080, 8081)
- [x] 바인드 마운트 반영 확인
- [x] Docker 볼륨 영속성 검증
- [x] Git 설정 + GitHub 연동 (add/commit/pull/push)

---

## 4) 터미널 기초 실습

### 4-1. 절대 경로 / 상대 경로

```bash
$ pwd
/Users/song0423ricky4374/Codyssey_Mission1/practice
# (절대 경로: / 부터 시작하는 완전한 주소)

$ cd ..
# (상대 경로로 한 단계 위로 이동)

$ cd practice
# (상대 경로로 다시 진입)
```
<명령어 정리>  
pwd  
Print Working Directory의 약자, 현재 폴더를 절대 경로로 화면에 출력     

cd  
Change Directory의 약자,뒤에 이동할 경로를 붙여서 사용

### 4-2. 파일/폴더 기본 조작

```bash
$ ls -la
total 0
drwxr-xr-x  2 song0423ricky4374  song0423ricky4374   64  7 30 17:16 .
drwxr-xr-x  5 song0423ricky4374  song0423ricky4374  160  7 30 17:16 ..

# 빈 파일 생성
$ touch memo.txt

# 파일에 내용 작성
$ echo "hello" > memo.txt

# 파일 내용 확인
$ cat memo.txt
hello

# 복사
$ cp memo.txt memo_copy.txt
$ ls -la
(memo.txt, memo_copy.txt 둘 다 존재 확인)

# 이름 변경/이동
$ mv memo_copy.txt memo_renamed.txt

# 삭제
$ rm memo_renamed.txt
$ ls -la
total 8
drwxr-xr-x  3 song0423ricky4374  song0423ricky4374   96  7 30 17:40 .
drwxr-xr-x  5 song0423ricky4374  song0423ricky4374  160  7 30 17:16 ..
-rw-r--r--  1 song0423ricky4374  song0423ricky4374    6  7 30 17:36 memo.txt
(memo.txt만 남음 확인)
```

### 4-3. 파일 권한 실습 (r/w/x, 644 → 755)

```bash
$ ls -l memo.txt
-rw-r--r--  1 song0423ricky4374  song0423ricky4374  6  7 30 17:36 memo.txt
# (변경 전, 644 = 소유자 rw- / 그룹 r-- / 기타 r--)

$ chmod 755 memo.txt
$ ls -l memo.txt
-rwxr-xr-x  1 song0423ricky4374  song0423ricky4374  6  7 30 17:36 memo.txt
# (변경 후, 755 = 소유자 rwx / 그룹 r-x / 기타 r-x)
```

### 4-4. 디렉토리 권한 실습 (755 → 700)

```bash
$ mkdir test_dir
$ ls -ld test_dir
drwxr-xr-x  2 song0423ricky4374  song0423ricky4374  64  7 30 17:49 test_dir
# (변경 전, 755)

$ chmod 700 test_dir
$ ls -ld test_dir
drwx------  2 song0423ricky4374  song0423ricky4374  64  7 30 17:49 test_dir
# (변경 후, 700 = 소유자만 접근 가능)
```

---

## 5) Docker(OrbStack) 설치 및 점검

```bash
# Docker CLI 버전 확인
$ docker --version
Docker version 28.5.2, build ecc6942

# Docker 데몬(OrbStack 엔진) 동작 확인
$ docker info
Client:
 Context:    orbstack
Server:
 Server Version: 28.5.2
 Containers: 0
 Images: 0
 Operating System: OrbStack
 ...
```
`Context: orbstack`, `Operating System: OrbStack`을 통해 OrbStack 엔진으로 Docker CLI가 정상 연결되어 있음을 확인했다.

---

## 6) 컨테이너 실행 실습

### 6-1. 기본 명령 확인

```bash
$ docker images          # 다운로드된 이미지 목록 확인
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
ubuntu        latest    de7345b16e94   2 weeks ago    100MB
hello-world   latest    e2ac70e7319a   4 months ago   10.1kB

$ docker ps               # 실행 중인 컨테이너 목록 확인
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
(실행 중인 컨테이너 없음)

$ docker ps -a             # 멈춘 것 포함 전체 컨테이너 목록 확인
CONTAINER ID   IMAGE         COMMAND    CREATED         STATUS                     PORTS     NAMES
104f2e839d87   ubuntu        "bash"     8 minutes ago   Exited (0) 5 minutes ago             my-ubuntu
4367b013568c   hello-world   "/hello"   9 minutes ago   Exited (0) 9 minutes ago             naughty_williamson
```

### 6-2. hello-world 실행

```bash
$ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
Status: Downloaded newer image for hello-world:latest
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

### 6-3. ubuntu 컨테이너 진입 실습

```bash
$ docker run -it --name my-ubuntu-v2 ubuntu bash
root@f19eafa3998d:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@f19eafa3998d:/# pwd
/
root@f19eafa3998d:/# echo "hello from container"
hello from container
root@f19eafa3998d:/# exit
```

### 6-4. 로그 / 리소스 확인

```bash
$ docker ps -a
CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                      PORTS     NAMES
f19eafa3998d   ubuntu        "bash"     4 minutes ago    Exited (0) 4 minutes ago              my-ubuntu-v2
4367b013568c   hello-world   "/hello"   22 minutes ago   Exited (0) 22 minutes ago             naughty_williamson

$ docker logs my-ubuntu-v2
root@f19eafa3998d:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@f19eafa3998d:/# pwd
/
root@f19eafa3998d:/# echo "hello from container"
hello from container
root@f19eafa3998d:/# exit

$ docker stats --no-stream
CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT   MEM %     NET I/O   BLOCK I/O   PIDS
(실행 중인 컨테이너가 없어 표시할 내용 없음)
```

### 6-5. 종료(exit) vs 유지(exec) 차이 관찰

- `exit`로 나가면: 컨테이너의 메인 프로세스(bash)가 끝나버려서 컨테이너 자체가 정지(Exited) 상태가 됨
- `docker exec -it`: 컨테이너가 백그라운드에서 계속 돌아가는 상태에서, 그 안에 "추가로" 새 세션을 열어서 들어가는 것. 이 세션에서 나가도(exit) 컨테이너 자체는 계속 실행 중
- `docker attach`: 실행 중인 컨테이너의 메인 프로세스에 다시 붙는 것. 여기서 exit하면 메인 프로세스가 끝나 컨테이너도 같이 멈춤

```bash
# 백그라운드로 계속 살아있게 실행 (메인 프로세스: sleep infinity)
$ docker run -d --name keep-alive ubuntu sleep infinity

$ docker ps
CONTAINER ID   IMAGE     COMMAND            CREATED          STATUS          PORTS     NAMES
2ff82e583cde   ubuntu    "sleep infinity"   12 seconds ago   Up 11 seconds             keep-alive

# 실행 중인 컨테이너에 추가 세션으로 진입
$ docker exec -it keep-alive bash
root@2ff82e583cde:/# echo "still running"
still running
root@2ff82e583cde:/# exit

# exit 했음에도 keep-alive는 여전히 Up 상태
$ docker ps
CONTAINER ID   IMAGE     COMMAND            CREATED              STATUS              PORTS     NAMES
2ff82e583cde   ubuntu    "sleep infinity"   About a minute ago   Up About a minute             keep-alive
```

**관찰 정리**: `my-ubuntu-v2`에서는 메인 프로세스(bash)를 직접 실행했기 때문에 `exit` 시 컨테이너가 함께 `Exited` 상태가 되었다. 반면 `keep-alive`는 메인 프로세스가 `sleep infinity`이고 `exec`는 그 위에 추가로 연 세션이었으므로, 그 세션에서 `exit`해도 메인 프로세스는 살아있어 컨테이너가 계속 `Up` 상태를 유지했다.

---

## 7) 커스텀 Dockerfile 기반 웹서버

- 선택한 방식: (A) 웹서버 베이스 이미지 활용 — `nginx:alpine` + 정적 콘텐츠 교체

### 7-1. 파일 구성

```bash
$ mkdir -p webserver/site

# index.html 생성 (한글 깨짐 방지를 위해 charset UTF-8 메타태그 포함)
$ cat > site/index.html << 'EOF'
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>My Custom Server</title>
</head>
<body>
  <h1>안녕하세요! 제가 만든 커스텀 nginx 이미지입니다 🐳</h1>
</body>
</html>
EOF

# Dockerfile 생성
$ cat > Dockerfile << 'EOF'
# nginx:alpine을 베이스 이미지로 사용 (가볍고 빠른 웹서버)
FROM nginx:alpine
# 이미지에 이름표(메타데이터) 붙이기
LABEL org.opencontainers.image.title="my-custom-nginx"
# 개발 환경임을 나타내는 환경변수 설정
ENV APP_ENV=dev
# 내가 만든 site 폴더의 정적 페이지로 nginx 기본 페이지를 교체
COPY site/ /usr/share/nginx/html/
EOF
```

**커스텀 포인트 요약**
- 베이스: `nginx:alpine` (경량 웹서버 이미지)
- `LABEL`: 이미지 메타데이터(제목) 부여
- `ENV APP_ENV=dev`: 개발 환경 구분용 환경변수
- `COPY site/ ...`: 직접 작성한 정적 페이지로 nginx 기본 페이지 교체

### 7-2. 빌드

```bash
$ docker build -t my-web:1.0 .
[+] Building 6.4s (7/7) FINISHED
 => [1/2] FROM docker.io/library/nginx:alpine ...
 => [2/2] COPY site/ /usr/share/nginx/html/
 => exporting to image
 => => naming to docker.io/library/my-web:1.0

$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
my-web        1.0       d9f3fde493d6   5 minutes ago   62.4MB
```

### 7-3. 포트 매핑 접속 (2회)

```bash
# 1차: 8080 포트
$ docker run -d -p 8080:80 --name my-web-8080 my-web:1.0
$ curl http://localhost:8080
(한글 정상 출력 확인)

# 2차: 8081 포트 (같은 이미지로 독립된 컨테이너 하나 더 실행)
$ docker run -d -p 8081:80 --name my-web-8081 my-web:1.0
$ curl http://localhost:8081
(한글 정상 출력 확인)
```
동일한 이미지(`my-web:1.0`)로 서로 다른 포트에 독립된 컨테이너 두 개를 동시에 실행할 수 있음을 확인했다.

---

## 8) 바인드 마운트 실습 (변경 반영 확인)

```bash
# 맥의 site 폴더를 컨테이너의 nginx html 폴더와 실시간 연결
$ docker run -d -p 8082:80 --name my-web-bind -v $(pwd)/site:/usr/share/nginx/html my-web:1.0

$ curl http://localhost:8082
# (변경 전)
<h1>안녕하세요! 제가 만든 커스텀 nginx 이미지입니다 🐳</h1>

# --- 맥에서 site/index.html 내용 수정 ---

$ curl http://localhost:8082
# (변경 후 - 재빌드/재시작 없이 즉시 반영됨)
<h1>수정된 페이지입니다! 바인드 마운트가 잘 동작하네요 🎉</h1>
```

**관찰 정리**: 바인드 마운트로 연결하면 컨테이너를 재시작하거나 이미지를 재빌드하지 않아도, 호스트(맥)에서 파일을 수정하는 즉시 컨테이너 내부에도 변경이 반영된다. 이는 Dockerfile의 `COPY`처럼 이미지 빌드 시점에 파일을 고정하는 방식과 달리, 실시간 개발에 적합하다.

---

## 9) Docker 볼륨 영속성 검증

```bash
# 데이터를 영구적으로 저장할 볼륨 생성
$ docker volume create mydata

# mydata 볼륨을 컨테이너의 /data 경로에 연결해서 실행
$ docker run -d --name vol-test -v mydata:/data ubuntu sleep infinity

# 컨테이너 내부에서 /data(=볼륨)에 hello.txt 파일을 만들고 내용 확인
$ docker exec -it vol-test bash -c "echo hi > /data/hello.txt && cat /data/hello.txt"
hi

# 컨테이너를 완전히 삭제 (볼륨 자체는 삭제되지 않음)
$ docker rm -f vol-test
vol-test

# 같은 볼륨(mydata)을 새 컨테이너에 다시 연결해서 실행
$ docker run -d --name vol-test2 -v mydata:/data ubuntu sleep infinity

# 새 컨테이너에서도 이전 데이터가 남아있는지 확인 → 삭제 전과 동일하게 "hi" 출력됨
$ docker exec -it vol-test2 bash -c "cat /data/hello.txt"
hi
```

**관찰 정리**: `vol-test` 컨테이너를 `docker rm -f`로 완전히 삭제한 뒤, 같은 볼륨(`mydata`)을 연결한 새 컨테이너 `vol-test2`를 실행해 확인한 결과, 이전에 저장했던 `hello.txt` 파일과 그 내용(`hi`)이 그대로 남아있었다. 컨테이너는 삭제되어도 볼륨에 저장된 데이터는 독립적으로 유지된다는 것을 확인했다.

---

## 10) Git 설정 및 GitHub 연동

```bash
# Git 버전 확인
$ git --version
git version 2.53.0

# 커밋에 기록될 사용자 이름/이메일 설정
$ git config --global user.name "song0423ricky"
$ git config --global user.email "song0423ricky@gmail.com"

# 전체 설정 확인 (원격 저장소 연결 상태 포함)
$ git config --list
user.name=song0423ricky
user.email=song0423ricky@gmail.com
remote.origin.url=https://github.com/song0423ricky/Codyssey_Mission1.git
branch.main.remote=origin
branch.main.merge=refs/heads/main

# 작업 폴더 상태 확인
$ git status
추적하지 않는 파일:
        practice/
        webserver/

# 변경사항 추가 및 커밋
$ git add .
$ git commit -m "터미널 실습 및 커스텀 nginx Dockerfile 추가"
[main db99cc9] 터미널 실습 및 커스텀 nginx Dockerfile 추가
 3 files changed, 19 insertions(+)

# GitHub에 업로드 시도 → 원격에 로컬에 없는 커밋이 있어 거부됨
$ git push origin main
 ! [rejected]        main -> main (fetch first)

# 병합 방식 지정 후 원격 변경사항 받아오기
$ git config pull.rebase false
$ git pull origin main
error: there was a problem with the editor 'vi'

# 편집기 오류로 자동 커밋이 안 되어 수동으로 병합 커밋 완료
$ git commit -m "Merge remote-tracking branch origin/main"
[main be1fcee] Merge remote-tracking branch origin/main

# 다시 업로드 → 성공
$ git push origin main
   fd7e0f2..be1fcee  main -> main
```

VS Code에서 GitHub 계정으로 로그인 후 저장소(`song0423ricky/Codyssey_Mission1`)와 연동을 완료했다.

---

## 11) 트러블슈팅

### 사례 1: docker 명령어를 찾을 수 없음
- 문제: `docker --version` 입력 시 `zsh: command not found: docker` 발생
- 원인 가설: OrbStack 앱이 실행되어 있지 않아 Docker 엔진 및 CLI 연결이 비활성화됨
- 확인: 메뉴바에 OrbStack 아이콘이 없는 것을 확인
- 해결: OrbStack 앱 실행 후 재시도 → 정상적으로 버전 출력됨

### 사례 2: 컨테이너 내부에서 docker 명령어 실행 시도
- 문제: 컨테이너 내부(`root@...:/#` 프롬프트)에서 `docker exec` 명령어 입력 시 `bash: docker: command not found` 발생
- 원인 가설: 컨테이너는 격리된 환경이라 내부에 docker 프로그램 자체가 설치되어 있지 않음
- 확인: 프롬프트 형태가 `root@(컨테이너ID):/#`였음을 확인 → 컨테이너 내부였음을 인지
- 해결: docker 명령어는 반드시 맥(호스트) 터미널에서만 실행해야 함을 확인

### 사례 3: nginx 웹페이지 한글 깨짐 우려
- 문제: 브라우저에서 index.html 접속 시 한글이 깨질 우려가 있었음
- 원인 가설: HTML에 문자 인코딩(charset) 명시가 없어 브라우저가 잘못 해석할 가능성
- 확인: `<head>` 안에 charset 메타태그 부재 확인
- 해결: `<meta charset="UTF-8">` 추가 후 재빌드/재실행하여 정상 표시 확인

### 사례 4: git push 시 rejected 에러 발생
- 문제: `git push origin main` 실행 시 `[rejected] main -> main (fetch first)` 에러 발생
- 원인 가설: GitHub 저장소에 로컬에는 없는 커밋이 이미 존재해 로컬과 원격 기록이 갈라짐(divergent)
- 확인: `git pull origin main` 시도 시 "divergent branches" 경고 확인
- 해결: `git config pull.rebase false`로 merge 방식 지정 후 pull, 편집기 오류로 자동 커밋이 안 되어 `git commit -m "..."`으로 수동 커밋 완료, 이후 `git push origin main` 성공
