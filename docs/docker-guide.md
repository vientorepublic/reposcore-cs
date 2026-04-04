# Docker 설치 및 구동 가이드(WSL2 Ubuntu 기준)  
## WSL2 Ubuntu 환경 설치  
#### 모든 설치는 윈도우 11 환경에서 설치하였습니다.
1. powershell 관리자 권한으로 실행.

2. WSL2 설치
   ```
   wsl --install
   ```
3. 재부팅
4. powershell에서 WSL2 설치 확인
   ``` 
   wsl -l -v
   ```
## Docker 설치
#### Docker Desktop 다운로드  
   [도커 다운로드](https://docs.docker.com/desktop/setup/install/windows-install/)'
   Docker Desktop  
   => 윈도우 환경에 도커 설치  
#### Docker Desktop Setting
Docker Desktop 실행  
=> setting  
=> General 탭  
=> Use the WSL 2 based engine 체크  
=>Resources 탭  
=>WSL Integration 탭에서 Ubuntu 활성화  

#### Docker(리눅스환경) 다운로드
   ```
   sudo apt update
   sudo apt install docker.io -y
   ```
## 도커 실행
   ```
   sudo service docker start
   ```
## Docker --version 확인 방법
```
   docker version
```
## sudo 없이 Docker 명령어 사용하는 설정
```
sudo usermod -aG docker $USER
```
## 간단한 테스트 컨테이너 실행 방법
```
docker run python python -c "print('Hello from Python')"
```
