# Octokit.NET 활용 가이드

[Octockit 라이브러리 공식 홈페이지](https://github.com/octokit)

> 이 문서는 C# 환경에서 GitHub API를 사용하기 위한 라이브러리인 Octokit.NET의 기본 사용법을 설명합니다.
> 저장소 이슈 조회, Pull Request 조회 등 GitHub 정보 연동 기능 구현 전에 기본 구조를 이해하는 데 목적이 있습니다.

## 1. Octokit.NET 설치 방법

Octokit.NET은 GitHub 공식 .NET API 라이브러리입니다.

### 특징

* GitHub API를 C#에서 쉽게 호출 가능
* 이슈, Pull Request, 커밋, 저장소 정보 조회 가능
* 인증 토큰 연동 지원
* 비동기 API 기반

### 설치 방법

아래 명령어를 터미널에 입력합니다.

```bash
dotnet add package Octokit
```

---

## 2. GitHub API 접근을 위한 기본 설정

GitHub API를 사용하려면 먼저 `GitHubClient` 객체를 생성해야 합니다.

### 기본 설정

```csharp
using Octokit;

var client = new GitHubClient(new ProductHeaderValue("reposcore-app"));
```

### 설명

* `ProductHeaderValue`에는 프로그램 이름을 지정합니다.
* 공개 저장소 조회는 인증 없이 가능합니다.

### 인증 설정

GitHub Personal Access Token이 필요한 경우 다음과 같이 설정합니다.

```csharp
client.Credentials = new Credentials("YOUR_GITHUB_TOKEN");
```

---

## 3. 저장소 이슈 목록 조회 예시

특정 저장소의 이슈 목록을 가져오는 예시입니다.

### 사용 예시

```csharp
using Octokit;

var client = new GitHubClient(new ProductHeaderValue("reposcore-app"));

var issues = await client.Issue.GetAllForRepository("owner", "repo-name");

foreach (var issue in issues)
{
    Console.WriteLine($"이슈 제목: {issue.Title}");
}
```

### 설명

* `"owner"` : GitHub 사용자 또는 조직 이름
* `"repo-name"` : 저장소 이름

### 출력 예시

```bash
이슈 제목: 버그 수정 필요
이슈 제목: 로그인 기능 추가
```

---

## 4. Pull Request 목록 조회 예시

Pull Request 목록도 유사한 방식으로 조회할 수 있습니다.

### 사용 예시

```csharp
using Octokit;

var client = new GitHubClient(new ProductHeaderValue("reposcore-app"));

var pullRequests = await client.PullRequest.GetAllForRepository("owner", "repo-name");

foreach (var pr in pullRequests)
{
    Console.WriteLine($"PR 번호: {pr.Number} - {pr.Title}");
}
```

### 출력 예시

```bash
PR 번호: 12 - README 수정
PR 번호: 13 - API 연결 추가
```

---

## 추가 정보

### Rate Limit 확인

GitHub API는 요청 횟수 제한이 있으므로 현재 제한 상태를 확인할 수 있습니다.

```csharp
var rateLimits = await client.Miscellaneous.GetRateLimits();
Console.WriteLine(rateLimits.Resources.Core.Remaining);
```

### 참고 사항

* 공개 저장소는 인증 없이 일부 조회 가능
* 비공개 저장소는 토큰 인증 필요
* 많은 요청 시 API 제한 발생 가능

---

## 정리

| 기능    | 메서드                                 |
| ----- | ----------------------------------- |
| 이슈 조회 | `Issue.GetAllForRepository()`       |
| PR 조회 | `PullRequest.GetAllForRepository()` |
| 인증 설정 | `Credentials()`                     |

