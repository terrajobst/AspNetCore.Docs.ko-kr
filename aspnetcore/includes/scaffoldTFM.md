스캐폴딩 오류가 발생하면 TFM(대상 프레임워크 모니커)이 프로젝트 파일의 NuGet 패키지 버전과 일치하는지 확인합니다. 예를 들어 다음 프로젝트 파일의 .NET Core 및 나열된 NuGet 패키지에는 버전 3.1이 포함되어 있습니다.

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore" Version="3.1.0" />
    <PackageReference Include="Microsoft.AspNetCore.Identity.EntityFrameworkCore" Version="3.1.0" />
    <PackageReference Include="Microsoft.AspNetCore.Identity.UI" Version="3.1.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.1.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="3.1.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="3.1.0" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="3.1.0" />
  </ItemGroup>

</Project>
```
