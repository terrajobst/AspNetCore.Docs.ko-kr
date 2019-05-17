---
title: ASP.NET Core 2.1 이상용 Microsoft.AspNetCore.App 메타패키지
author: Rick-Anderson
description: Microsoft.AspNetCore.App 메타패키지에는 지원되는 모든 ASP.NET Core 및 Entity Framework Core 패키지가 포함됩니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/21/2019
uid: fundamentals/metapackage-app
ms.openlocfilehash: 913e3d83fbf1af7ea995a88202f86c60b359a7e2
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085660"
---
# <a name="microsoftaspnetcoreapp-metapackage-for-aspnet-core-21-or-later"></a>ASP.NET Core 2.1 이상용 Microsoft.AspNetCore.App 메타패키지

이 기능을 사용하려면 .NET Core 2.1 이상을 대상으로 하는 ASP.NET Core 2.1 이상이 필요합니다.

ASP.NET Core용 [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [메타패키지](/dotnet/core/packages#metapackages):

* [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/) 및 [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/)를 제외한 타사 종속성을 포함하지 마세요. 이러한 타사 종속성은 주요 프레임워크 기능이 작동하도록 하는 데 필요한 것으로 간주됩니다.
* 이전에 언급한 것 이외의 타사 종속성을 포함하는 것을 제외하고 ASP.NET Core 팀에서 지원하는 모든 패키지를 포함합니다.
* 이전에 언급한 것 이외의 타사 종속성을 포함하는 것을 제외하고 Entity Framework 팀에서 지원하는 모든 패키지를 포함합니다.

ASP.NET Core 2.1 이상 Entity Framework Core 2.1 이상의 모든 기능은 `Microsoft.AspNetCore.App` 패키지에 포함됩니다. ASP.NET Core 2.1 이상을 대상으로 하는 기본 프로젝트 템플릿에는 이 패키지를 사용합니다. ASP.NET Core 2.1 이상과 Entity Framework Core 2.1 이상을 대상으로 하는 애플리케이션은 `Microsoft.AspNetCore.App` 패키지를 사용하는 것이 좋습니다.

`Microsoft.AspNetCore.App` 메타패키지의 버전 번호는 최소 ASP.NET Core 버전 및 Entity Framework Core 버전을 나타냅니다.

`Microsoft.AspNetCore.App` 메타패키지 사용 시에는 앱을 보호하기 위한 버전 제한 사항이 적용됩니다.

* `Microsoft.AspNetCore.App`에 패키지에 대한 전이적인(직접적이지 않은) 종속성을 가진 패키지가 포함되어 있고, 이러한 버전 번호가 서로 다를 경우 NuGet에서 오류를 생성합니다.
* 앱에 추가된 다른 패키지는 `Microsoft.AspNetCore.App`에 포함된 패키지 버전을 변경할 수 없습니다.
* 버전 일관성은 안정적인 환경을 보장합니다. `Microsoft.AspNetCore.App`은 동일한 앱에서 함께 사용되는 관련 비트의 테스트되지 않은 버전 조합을 방지하도록 설계되었습니다.

`Microsoft.AspNetCore.App` 메타패키지를 사용하는 애플리케이션은 ASP.NET Core 공유 프레임워크를 자동으로 활용합니다. `Microsoft.AspNetCore.App` 메타패키지를 사용할 경우, 참조되는 ASP.NET Core NuGet 패키지의 자산이 애플리케이션을 사용하여 배포되지 **않습니다**. 이러한 자산은 &mdash; ASP.NET Core 공유 프레임워크에 포함됩니다. 공유 프레임워크의 자산은 애플리케이션 시작 시간 단축을 위해 미리 컴파일됩니다. 자세한 내용은 [공유 프레임워크](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)를 참조하세요.

다음 프로젝트 파일은 ASP.NET Core용 `Microsoft.AspNetCore.App` 메타패키지를 참조하며 일반적인 ASP.NET Core 2.1 템플릿을 나타냅니다.

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

이전의 표시는 일반적인 ASP.NET Core 2.1 이상의 템플릿을 나타냅니다. `Microsoft.AspNetCore.App` 패키지 참조의 버전 번호는 지정하지 않습니다. 버전이 지정되지 않은 경우 SDK에서 [암시적](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) 버전, 즉 `Microsoft.NET.Sdk.Web`을 지정합니다. SDK에서 지정하는 암시적 버전을 사용하고, 패키지 참조에 버전 번호를 명시적으로 설정하지 않는 것이 좋습니다. 이 접근 방식에 대한 질문이 있는 경우 GitHub의 [Microsoft.AspNetCore.App 암시적 버전에 대한 토론](https://github.com/aspnet/AspNetCore.Docs/issues/6430)에 의견을 남겨 주세요.

휴대용 앱의 암시적 버전은 `major.minor.0`으로 설정됩니다. 공유 프레임워크 롤포워드 메커니즘은 설치된 공유 프레임워크 중 최신 호환 버전에서 앱을 실행합니다. 개발, 테스트 및 프로덕션에서 동일한 버전이 사용되도록 하려면 모든 환경에 동일한 버전의 공유 프레임워크를 설치하도록 하세요. 자체 포함 앱의 경우 암시적 버전 번호가 설치된 SDK에 포함된 공유 프레임워크의 `major.minor.patch`로 설정됩니다.

`Microsoft.AspNetCore.App` 참조에 버전 번호를 지정해도 해당 버전의 고유 프레임워크가 선택된다고 보장할 수 **없습니다**. 예를 들어 "2.1.1" 버전을 지정했는데 "2.1.3"이 설치되는 경우가 있습니다. 이 경우 앱은 "2.1.3"을 사용합니다. 권장되는 방법은 아니지만 롤포워드를 비활성화할 수 있습니다(패치 및/또는 부 버전). DotNet 호스트 롤포워드 및 이 동작을 구성하는 방법에 대한 자세한 내용은 [DotNet 호스트 롤포워드](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md)를 참조하세요.

::: moniker range="= aspnetcore-2.1"

암시적 버전 `Microsoft.AspNetCore.App`을 사용하려면 `<Project Sdk`를 `Microsoft.NET.Sdk.Web`으로 설정해야 합니다. `<Project Sdk="Microsoft.NET.Sdk">`가 후행 `.Web` 없이 사용되는 경우:

* 다음 경고가 생성됩니다.

  *경고 NU1604: 프로젝트 종속성 Microsoft.AspNetCore.App에 포함 하한이 포함되어 있지 않습니다. 일관된 복원 결과를 얻으려면 종속 버전의 하한을 포함하세요.*

* 이것은 .NET Core 2.1 SDK의 알려진 문제입니다.

::: moniker-end

<a name="update"></a>

## <a name="update-aspnet-core"></a>ASP.NET Core 업데이트

`Microsoft.AspNetCore.App` [메타패키지](/dotnet/core/packages#metapackages)는 NuGet에서 업데이트된 기존 패키지가 아닙니다. `Microsoft.NETCore.App`과 유사한 `Microsoft.AspNetCore.App`은 공유 런타임을 나타내며, NuGet 외부에서 처리되는 특별한 버전 관리 의미 체계가 있습니다. 자세한 내용은 [패키지, 메타패키지 및 프레임워크](/dotnet/core/packages)를 참조하세요.

ASP.NET Core를 업데이트하려면:

* 개발 머신 및 빌드 서버: [.NET Core SDK](https://www.microsoft.com/net/download)를 다운로드하여 설치합니다.
* 배포 서버: [.NET Core 런타임](https://www.microsoft.com/net/download)을 다운로드하여 설치합니다.

 애플리케이션을 다시 시작하면 애플리케이션이 최신 설치 버전으로 롤포워드됩니다. 프로젝트 파일에서 `Microsoft.AspNetCore.App` 버전 번호를 업데이트할 필요는 없습니다. 자세한 내용은 [Framework 종속 앱 롤포워드](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward)를 참조하세요.

애플리케이션에서 이전에 `Microsoft.AspNetCore.All`을 사용한 경우 [Microsoft.AspNetCore.All에서 Microsoft.AspNetCore.App으로 마이그레이션](xref:fundamentals/metapackage#migrate)을 참조하세요.
