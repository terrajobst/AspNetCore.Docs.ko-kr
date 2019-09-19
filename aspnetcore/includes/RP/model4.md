<a name="codegenerator"></a> 다음 표에서는 ASP.NET Core 코드 생성기 매개 변수를 자세히 설명합니다.

| 매개 변수               | 설명|
| ----------------- | ------------ |
| -m  | 모델의 이름입니다. |
| -dc  | 사용할 `DbContext` 클래스입니다. |
| -udl | 기본 레이아웃을 사용합니다. |
| -outDir | 뷰를 만들기 위한 상태 출력 폴더 경로입니다. |
| --referenceScriptLibraries | 편집 및 만들기 페이지에 `_ValidationScriptsPartial`을 추가합니다. |

`h` 스위치를 사용하여 `aspnet-codegenerator razorpage` 명령에 대한 도움말을 확인합니다.

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

자세한 내용은 [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)를 참조하세요. 