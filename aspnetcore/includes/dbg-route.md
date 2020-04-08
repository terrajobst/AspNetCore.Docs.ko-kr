## <a name="debug-diagnostics"></a><span data-ttu-id="cf74f-101">디버그 진단</span><span class="sxs-lookup"><span data-stu-id="cf74f-101">Debug diagnostics</span></span>

<span data-ttu-id="cf74f-102">자세한 라우팅 진단 출력을 위해 `Logging:LogLevel:Microsoft`를 `Debug`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="cf74f-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="cf74f-103">예를 들어 개발 환경에서 *appsettings.Development.json*을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="cf74f-103">For example, in the development environment, set *appsettings.Development.json*:</span></span>

```JSON
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}