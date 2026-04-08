# C# CLI 프로그램 개발을 위한 라이브러리 가이드

> 이 문서는 C# CLI 프로그램 개발 시 옵션 처리에 유용한 라이브러리를 소개합니다.

---

## 1. 현재 프로젝트 흐름 요약

현재 코드 기준 핵심 흐름은 아래와 같습니다.

1. `Program.cs`에서 CLI 명령/옵션을 파싱한다.
2. 입력값(owner, repo, token, 기간 등)을 검증한다.
3. `Services/GitHubService.cs`를 생성한다.
4. Octokit으로 이슈/PR/커밋/RateLimit 데이터를 조회한다.
5. 점수 산정 규칙(`docs/score-guide.md`)에 따라 집계한다.
6. 결과를 콘솔에 출력한다.

---

## 2. reposcore-cs에서의 우선 선택

`reposcore-cs.csproj`에는 이미 아래 패키지가 포함되어 있습니다.

- `Cocona`
- `Octokit`

따라서 현재 단계에서는 새로운 CLI 프레임워크를 추가하기보다, **Cocona를 기본 CLI 프레임워크로 확정**하고 `Program.cs`를 Cocona 기반 명령형 구조로 확장하는 것을 권장합니다.

### 왜 Cocona를 우선 추천하나요?

- 이미 의존성에 포함되어 있어 즉시 사용 가능
- 속성(Attribute) 기반 명령 정의가 간단함
- 소규모~중규모 CLI에서 유지보수가 쉬움
- Octokit 호출 코드(`GitHubService`)와 결합하기 용이

---

## 3. Cocona + Octokit 연결 예시

아래 예시는 이 프로젝트 흐름에 맞춘 최소 형태의 예시입니다.

```csharp
using Cocona;
using Octokit;
using RepoScore.Services;

var builder = CoconaApp.CreateBuilder();
var app = builder.Build();

app.AddCommand("score", async (
    [Option("owner", Description = "GitHub owner 또는 organization")] string owner,
    [Option("repo", Description = "GitHub repository 이름")] string repo,
    [Option("token", Description = "GitHub Personal Access Token")] string? token,
    [Option("state", Description = "open | closed | all")] string state = "all") =>
{
    var service = new GitHubService(owner, repo, token);

    var itemState = state.ToLowerInvariant() switch
    {
        "open" => ItemStateFilter.Open,
        "closed" => ItemStateFilter.Closed,
        _ => ItemStateFilter.All
    };

    var issues = await service.GetIssuesAsync(itemState);
    var prs = await service.GetPullRequestsAsync(itemState);
    var commits = await service.GetCommitsAsync();

    Console.WriteLine($"Issues: {issues.Count}");
    Console.WriteLine($"PRs: {prs.Count}");
    Console.WriteLine($"Commits: {commits.Count}");
});

app.Run();
```

---

## 4. 다른 CLI 라이브러리 선택 기준

프로젝트가 커지거나 요구사항이 바뀌면 아래 대안을 검토할 수 있습니다.

### System.CommandLine

- 장점: .NET 공식 생태계, 서브커맨드/완성도 높은 파싱
- 적합한 경우: 복잡한 명령 트리, 장기적 확장성 우선
- 고려사항: 버전 정책 및 API 변경 추이를 확인해야 함

설치:

```bash
dotnet add package System.CommandLine --prerelease
```

---

### Spectre.Console / Spectre.Console.Cli

- 장점: 표, 색상, 진행률 등 출력 UX가 매우 좋음
- 적합한 경우: 채점 결과를 표/리포트 형태로 보여주고 싶을 때
- 권장 사용법: 명령 파싱(Cocona) + 출력 렌더링(Spectre.Console) 조합

설치:

```bash
dotnet add package Spectre.Console
dotnet add package Spectre.Console.Cli
```

---

### CommandLineParser

- 장점: 단순하고 빠르게 적용 가능
- 적합한 경우: 단일 명령 위주의 작은 도구
- 고려사항: 현재 프로젝트는 이미 Cocona를 사용 중이므로 전환 이점이 크지 않음

설치:

```bash
dotnet add package CommandLineParser
```

---

## 5. 프로젝트 적용 로드맵 (권장)

1. `Program.cs`를 Cocona 명령 구조로 변경
2. `score` 명령에서 `GitHubService` 호출 연결
3. 집계 결과를 `docs/score-guide.md` 공식에 맞춰 계산
4. 에러 처리(인증 실패, rate limit 초과, repo 미존재) 메시지 표준화
5. 필요 시 Spectre.Console로 결과 표 출력 추가

---

## 6. 라이브러리 비교 (reposcore-cs 관점)

| 라이브러리            | 현재 적합도       | 난이도 | 주요 장점                   | 비고                           |
| --------------------- | ----------------- | ------ | --------------------------- | ------------------------------ |
| Cocona                | 매우 높음         | 쉬움   | 이미 도입됨, 빠른 명령 구현 | **현재 기본 선택**             |
| System.CommandLine    | 중간              | 중간   | 공식 생태계, 확장성         | 추후 대형 CLI 시 고려          |
| Spectre.Console(.Cli) | 높음(출력 강화용) | 중간   | 콘솔 UX 우수                | 파서 대체보다 출력 보강에 유리 |
| CommandLineParser     | 낮음              | 쉬움   | 단순함, 러닝커브 낮음       | 현 시점 전환 필요성 낮음       |

---

## 참고 링크

- [Cocona GitHub](https://github.com/mayuki/Cocona)
- [System.CommandLine 공식 문서](https://learn.microsoft.com/ko-kr/dotnet/standard/commandline/)
- [Spectre.Console 공식 문서](https://spectreconsole.net/)
- [CommandLineParser GitHub](https://github.com/commandlineparser/commandline)
