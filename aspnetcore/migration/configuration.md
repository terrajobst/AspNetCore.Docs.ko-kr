---
title: ASP.NET Core로 구성 마이그레이션
author: ardalis
description: ASP.NET Core MVC 프로젝트에 ASP.NET MVC 프로젝트에서 구성을 마이그레이션하는 방법에 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: migration/configuration
ms.openlocfilehash: c379f1f64dc5ab8aeb48055124e86e4e60d93785
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894930"
---
# <a name="migrate-configuration-to-aspnet-core"></a>ASP.NET Core로 구성 마이그레이션

작성자: [Steve Smith](https://ardalis.com/) 및 [Scott Addie](https://scottaddie.com)

시작 했던 이전 문서에서 [ASP.NET MVC 프로젝트를 ASP.NET Core MVC로 마이그레이션](xref:migration/mvc)합니다. 이 문서에서는 구성을 마이그레이션 했습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="setup-configuration"></a>설치 구성

ASP.NET Core에서 더 이상 사용 하는 *Global.asax* 하 고 *web.config* 이전 버전의 ASP.NET 사용 하는 파일입니다. 이전 버전의 ASP.NET 응용 프로그램 시작 논리에 배치 된를 `Application_StartUp` 메서드 내 *Global.asax*합니다. ASP.NET MVC에서 나중에 *Startup.cs* 프로젝트의 루트에 포함 된 파일 및 응용 프로그램을 시작할 때 호출 된 것입니다. ASP.NET Core는이 접근 방법을 채택 완전히에서 모든 시작 논리를 배치 하 여 합니다 *Startup.cs* 파일입니다.

합니다 *web.config* 파일 또한 ASP.NET Core의 대체 되었습니다. 자체 구성 이제 구성할 수 있습니다에 설명 된 응용 프로그램 시작 절차의 일부로 *Startup.cs*합니다. 구성 XML 파일을 사용할 수 있지만 일반적으로 ASP.NET Core 프로젝트 배치 구성 값을 JSON 형식 파일에서와 같은 *appsettings.json*합니다. ASP.NET Core 구성 시스템에는 환경 변수를 제공할 수 있는 쉽게 액세스할 수는 [더 안전 하 고 강력한 위치](xref:security/app-secrets) 환경 관련 값에 대 한 합니다. 연결 문자열 및 소스 제어에 체크 인할 수 하지 않아야 하는 API 키와 같은 비밀에 대 한 경우 특히 그렇습니다. 참조 [구성](xref:fundamentals/configuration/index) ASP.NET Core의 구성에 자세히 알아보려면 합니다.

이 문서에서는 시작 하는 부분적으로 마이그레이션된 ASP.NET Core 프로젝트에서 [이전 문서](xref:migration/mvc)합니다. 구성을 설정 하려면 다음 생성자 및 속성을 추가 합니다 *Startup.cs* 프로젝트의 루트에 있는 파일:

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

참고는이 시점에서 *Startup.cs* 도 다음을 추가 해야 하는 대로 파일 컴파일되지 않습니다 `using` 문:

```csharp
using Microsoft.Extensions.Configuration;
```

추가 된 *appsettings.json* 적절 한 항목 템플릿을 사용 하 여 프로젝트의 루트에 파일:

![AppSettings JSON 추가](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>Web.config에서 구성 설정을 마이그레이션합니다

ASP.NET MVC 프로젝트에서 필요한 데이터베이스 연결 문자열을 포함 *web.config*를 `<connectionStrings>` 요소입니다. ASP.NET Core 프로젝트에서 하겠습니다에서이 정보를 저장 합니다 *appsettings.json* 파일입니다. 오픈 *appsettings.json*, 및 다음 이미 포함 하는 참고 합니다.

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

위에 표시 된 강조 표시 된 줄에서 데이터베이스의 이름을 변경할 **_CHANGE_ME** 데이터베이스의 이름입니다.

## <a name="summary"></a>요약

ASP.NET Core는 필요한 서비스 및 종속성을 정의 하 고 구성 된 단일 파일에 응용 프로그램에 대 한 모든 시작 논리를 배치 합니다. 대체는 *web.config* 다양 한 환경 변수 뿐만 아니라 JSON과 같은 파일 형식의 활용할 수 있는 유연한 구성 기능을 사용 하 여 파일입니다.
