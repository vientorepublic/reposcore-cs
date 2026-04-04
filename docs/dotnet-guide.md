# C# 개발을 위한 .NET 설치 및 프로젝트 구성 가이드

## 1. .NET SDK 설치
- **다운로드:** [.NET 공식 다운로드 페이지](https://dotnet.microsoft.com/download)
- **권장 버전:** .NET 10.0 SDK (LTS) — 2028년 11월까지 지원

### Codespaces (Linux) 환경

우리 프로젝트는 주로 Codespaces(리눅스) 환경에서 작업하므로, 리눅스 기준으로 .NET SDK 설치 방법을 정리합니다.

먼저 패키지 목록을 최신 상태로 갱신합니다.
```bash
sudo apt update
```
그 다음 최신버전 .NET 10.0 SDK를 설치합니다.
```bash
sudo apt install -y dotnet-sdk-10.0
```
### 설치 확인
설치가 완료되면 아래 명령어로 정상 설치 여부를 확인할 수 있습니다.
```bash
dotnet --version
```
정상적으로 설치되었다면 `10.x.x` 형태의 버전 번호가 출력됩니다.

---

## 2. 신규 프로젝트 생성 (CLI 방식)

터미널 명령어를 사용하여 프로젝트의 기초 뼈대를 생성할 수 있습니다.

콘솔 애플리케이션을 생성합니다.
```bash
dotnet new console -n [프로젝트명]
```
(예: `dotnet new console -n MyConsoleApp`)

생성된 프로젝트 폴더로 이동합니다.
```bash
cd [프로젝트명]
```

---
## 3. `.csproj` 파일로 프로젝트 구성하기

`.csproj` 파일은 C# 프로젝트의 설정 정보를 담는 파일입니다.
대상 프레임워크, 출력 형식 등 기본 설정을 포함합니다.

예시:
```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net10.0</TargetFramework>
  </PropertyGroup>
</Project>
```

## 4. 기본 디렉토리 구조 예시
일반적인 C# 프로젝트는 아래와 같은 구조로 구성될 수 있습니다.
``` text
MyApp/
├─ MyApp.csproj
├─ Program.cs
├─ bin/
└─ obj/
```
- `Program.cs` : 프로그램 시작 파일
- `.csproj` : 프로젝트 설정 파일
- `bin/` : 빌드 결과물 폴더
- `obj/` : 빌드 중간 파일 폴더

## 5. 빌드 및 실행 방법
프로젝트 폴더에서 아래 명령어를 사용합니다.

코드를 빌드(오류 체크)합니다.
```bash
dotnet build
```
프로그램을 실행합니다.
```bash
dotnet run
```
