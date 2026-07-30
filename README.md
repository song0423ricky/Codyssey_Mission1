# 내 컴퓨터에 개발자용 '작업실' 꾸미기

## 1) 프로젝트 개요
(미션 목표 한두 문장 요약)

## 2) 실행 환경
- OS: macOS (버전)
- Shell: zsh
- Docker: (docker --version 결과)
- Git: (git --version 결과)

## 3) 수행 체크리스트
- [x] 터미널 기본 조작 및 폴더 구성
- [x] 권한 변경 실습
- [x] Docker 설치/점검
- [x] hello-world 실행
- [x] ubuntu 컨테이너 진입 실습
- [x] Dockerfile 빌드/실행
- [x] 포트 매핑 접속(2회)
- [x] 바인드 마운트 반영
- [x] 볼륨 영속성
- [x] Git 설정 + VSCode GitHub 연동

## 4) 수행 로그
### 4-1 터미널 기초

#### 절대 경로,상대경로
```bash
$ pwd
/Users/song0423ricky4374/Codessey_Mission1/Codyssey_Mission1/practice   (절대 경로)

$ cd ..        (상대 경로로 상위 이동)
$ cd practice  (상대 경로로 다시 진입)
```

#### 파일/폴더 기본 조작
```bash
$ cp memo.txt memo_copy.txt
$ ls -la
(memo.txt, memo_copy.txt 둘 다 존재)

$ mv memo_copy.txt memo_renamed.txt
$ ls -la
(memo_renamed.txt로 이름 변경 확인)

$ rm memo_renamed.txt
$ ls -la
(memo.txt만 남음)
```

#### 파일권한 실습
```bash
$ ls -l memo.txt
-rw-r--r--  1 song0423ricky4374  song0423ricky4374  6  7 30 17:36 memo.txt   (변경 전, 644)

$ chmod 755 memo.txt
(실행 권한 추가)

$ ls -l memo.txt
-rwxr-xr-x  1 song0423ricky4374  song0423ricky4374  6  7 30 17:36 memo.txt   (변경 후, 755)
```

#### 디렉토리권한 실습
```bash
$ mkdir test_dir
$ ls -ld test_dir 
drwxr-xr-x  2 song0423ricky4374  song0423ricky4374  64  7 30 17:49 test_dir   (변경 전, 755)

$ chmod 700 test_dir
$ ls -ld test_dir
drwx------  2 song0423ricky4374  song0423ricky4374  64  7 30 17:49 test_dir   (변경 후, 700)
```

### 4-2 Docker(OrbStack) 설치 및 점검

#### Docker(OrbStack)설치, 데몬(백그라운드 프로그램) 동작 확인
```bash
$ docker --version
Docker version 28.5.2, build ecc6942

$ docker info
Client:
 Version:    28.5.2
 Context:    orbstack
 Debug Mode: false
 Plugins:
  buildx: Docker Buildx (Docker Inc.)
    Version:  v0.29.1
    Path:     /Users/song0423ricky4374/.docker/cli-plugins/docker-buildx
  compose: Docker Compose (Docker Inc.)
    Version:  v2.40.3
    Path:     /Users/song0423ricky4374/.docker/cli-plugins/docker-compose
    
중략... 

WARNING: DOCKER_INSECURE_NO_IPTABLES_RAW is set
(orbstack이 dorker와 iptables raw 규칙이 달라서 생기는 경고)
```


### 4-3 Docker 기본 운영 명령 익히기

#### 기본운영명령, hello-world 실행
```bash
$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
ubuntu        latest    de7345b16e94   2 weeks ago    100MB
hello-world   latest    e2ac70e7319a   4 months ago   10.1kB

$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
(실행 중인 컨테이너 없음)

$ docker ps -a
CONTAINER ID   IMAGE         COMMAND    CREATED         STATUS                     PORTS     NAMES
104f2e839d87   ubuntu        "bash"     8 minutes ago   Exited (0) 5 minutes ago             my-ubuntu
4367b013568c   hello-world   "/hello"   9 minutes ago   Exited (0) 9 minutes ago             naughty_williamson
```

#### ubuntu 컨테이너 진입 실습
```bash
$ docker rm -f my-ubuntu
my-ubuntu

$ docker run -it --name my-ubuntu-v2 ubuntu bash
root@f19eafa3998d:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@f19eafa3998d:/# pwd
/
root@f19eafa3998d:/# echo "hello from container"
hello from container
root@f19eafa3998d:/# exit
```

#### 로그 / 리소스 확인
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
```










​```bash
(여기에 pwd, ls -la, chmod 등 명령+결과)
​```
### Docker 점검
​```bash
(docker --version, docker info 결과)
​```

### 컨테이너 실행 실습
​```bash
(hello-world, ubuntu 진입 로그)
​```

## 5) 커스텀 Dockerfile
- 베이스: nginx:alpine
- 커스텀 포인트: (요약)
​```dockerfile
(Dockerfile 전체 내용)
​```

## 6) 포트 매핑 접속 증거
(브라우저 스크린샷 또는 curl 결과, 2회분)

## 7) 바인드 마운트 / 볼륨 영속성
(변경 전/후, 삭제 전/후 비교 로그)

## 8) Git/GitHub 연동
​```bash
(git config --list 결과)
​```
(VS Code GitHub 로그인 스크린샷)

## 9) 트러블슈팅

### 사례1: docker 명령어를 찾을 수 없음
- 문제: `docker --version` 입력 시 `zsh: command not found: docker` 발생
- 원인 가설: OrbStack 앱이 실행되어 있지 않아 Docker 엔진 및 CLI 연결이 활성화되지 않음
- 확인: 메뉴바에 OrbStack 아이콘이 없는 것을 확인
- 해결: OrbStack 앱 실행 후 재시도 → 정상적으로 버전 출력됨

### 사례 2
- 문제:
- 원인 가설:
- 확인:
- 해결:
