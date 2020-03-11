---
title: ASP.NET Core의 정책 스키마
author: rick-anderson
description: 인증 정책 스키마를 사용 하면 단일 논리 인증 체계를 보다 쉽게 수행할 수 있습니다.
ms.author: riande
ms.date: 12/05/2019
uid: security/authentication/policyschemes
ms.openlocfilehash: f02d8e5cac20a9b60c5eddbd28253efacf682ea1
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652353"
---
# <a name="policy-schemes-in-aspnet-core"></a>ASP.NET Core의 정책 스키마

인증 정책 스키마를 사용 하면 단일 논리 인증 체계에서 잠재적으로 여러 방법을 사용할 수 있습니다. 예를 들어 정책 체계가 챌린지에 Google 인증을 사용 하 고 다른 모든 항목에 대 한 쿠키 인증을 사용할 수 있습니다. 인증 정책 스키마는 다음과 같습니다.

* 다른 체계에 인증 작업을 쉽게 전달할 수 있습니다.
* 요청에 따라 동적으로 전달 합니다.

파생 <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions>를 사용 하는 모든 인증 스키마와 관련 [Authenticationhandler\<TOptions >](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):

* 는 ASP.NET Core 2.1 이상에서 자동으로 정책 스키마를 구성 합니다.
* 구성표의 옵션을 구성 하 여 사용할 수 있습니다.

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>예

다음 예에서는 하위 수준 스키마를 조합 하는 더 높은 수준의 스키마를 보여 줍니다. Google 인증은 챌린지에 사용 되며 쿠키 인증은 다른 모든 항목에 사용 됩니다.

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

다음 예에서는 요청당 스키마를 동적으로 선택할 수 있습니다. 즉, 쿠키와 API 인증을 혼합 하는 방법:

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
