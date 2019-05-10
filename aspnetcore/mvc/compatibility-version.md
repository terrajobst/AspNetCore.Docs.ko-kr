---
title: ASP.NET Core MVC에 대한 호환성 버전
author: rick-anderson
description: ASP.NET Core의 시작 클래스에서 서비스 및 앱의 요청 파이프라인을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/15/2019
uid: mvc/compatibility-version
ms.openlocfilehash: b360da105799a1dccb1902e167e50e78864b76a9
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085896"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a>ASP.NET Core MVC에 대한 호환성 버전

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> 메서드를 사용하면 ASP.NET Core MVC 2.1 이상에서 도입된 주요 동작 변경 내용을 앱이 옵트인(opt-in) 또는 옵트아웃(opt-out)할 수 있습니다. 이 주요 동작 변경 내용은 일반적으로 MVC 하위 시스템의 작동 방식과 런타임에서 **코드**를 호출하는 방법과 관련이 있습니다. 옵트인을 하면 ASP.NET Core의 최신 동작과 장기 동작을 가져올 수 있습니다.

다음 코드는 호환성 모드를 ASP.NET Core 2.2로 설정합니다.

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

최신 버전(`CompatibilityVersion.Version_2_2`)을 사용하여 앱을 테스트하는 것이 좋습니다. 대부분의 앱은 최신 버전을 사용하여 주요 동작을 변경하지 않을 것으로 예상합니다.

`SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`을 호출하는 앱은 ASP.NET Core 2.1 MVC 및 이후의 2.x 버전에서 도입된 주요 동작 변경으로부터 보호됩니다. 이 보호:

* 2.1 이상의 모든 변경 내용에 적용되지는 않으며, MVC 하위 시스템의 주요 ASP.NET Core 런타임 동작 변경을 대상으로 합니다.
* 다음의 주 버전으로 확장되지 않습니다.

`SetCompatibilityVersion`을 호출하지 **않는** ASP.NET Core 2.1 및 이후 2.x 앱의 기본 호환성은 2.0 호환성입니다. 즉, `SetCompatibilityVersion`을 호출하지 않는 것은 `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`을 호출하는 것과 같습니다.

다음 코드는 다음 동작을 제외하고, 호환성 모드를 ASP.NET Core 2.2로 설정합니다.

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

적절한 호환성 스위치를 사용하여 주요 동작 변경 내용이 발생하는 앱의 경우:

* 최신 릴리스를 사용하고 특정 주요 동작 변경 내용을 옵트아웃(opt out)할 수 있습니다.
* 최신 변경 내용과 함께 작동하도록 앱을 업데이트할 시간을 제공합니다.

<xref:Microsoft.AspNetCore.Mvc.MvcOptions> 설명서에는 변경된 내용과 변경 내용이 대부분의 사용자에게 향상된 기능인 이유가 잘 설명되어 있습니다.

이후에 [ASP.NET Core 3.0 버전](https://github.com/aspnet/Home/wiki/Roadmap)이 나올 예정입니다. 호환성 스위치가 지원하는 이전 동작은 3.0 버전에서 제거됩니다. 거의 모든 사용자에게 혜택을 주는 긍정적인 변화라고 생각합니다. 이제 이러한 변경 내용을 도입함으로써 대부분의 앱이 혜택을 받을 수 있으며, 다른 사람은 앱을 업데이트할 시간을 얻게 됩니다.
