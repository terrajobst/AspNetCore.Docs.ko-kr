---
title: ASP.NET Core에서 리소스 기반 권한 부여
author: scottaddie
description: Authorize 특성을 깨 때 ASP.NET Core 앱에서 리소스 기반 권한 부여를 구현 하는 방법에 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
uid: security/authorization/resourcebased
ms.openlocfilehash: afc152ea677cab42d57bd642b4821159f125117e
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891740"
---
# <a name="resource-based-authorization-in-aspnet-core"></a>ASP.NET Core에서 리소스 기반 권한 부여

권한 부여 전략 액세스 되는 리소스에 따라 다릅니다. 문서를 작성자 속성이 있는 것이 좋습니다. 작성자만 문서를 업데이트할 수 있습니다. 따라서 문서 권한 부여 평가 발생 하기 전에 데이터 저장소에서 검색 해야 합니다.

특성 평가 데이터 바인딩 전에 및 페이지 처리기 또는 문서를 로드 하는 작업을 실행 하기 전에 발생 합니다. 이러한 이유로 사용 하 여 선언적 권한 부여는 `[Authorize]` 특성 충분 하지 않습니다. 대신 사용자 지정 권한 부여 메서드를 호출할 수 있습니다&mdash;라고 하는 스타일 *명령적 권한 부여*합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

[권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기](xref:security/authorization/secure-data) 리소스 기반 권한 부여를 사용 하는 샘플 앱을 포함 합니다.

## <a name="use-imperative-authorization"></a>필수 권한 부여 사용

권한 부여로 구현 됩니다는 [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) 서비스 및 서비스 컬렉션 내에 등록 되는 `Startup` 클래스입니다. 서비스를 통해 제공 됩니다 [종속성 주입](xref:fundamentals/dependency-injection) 페이지 처리기 또는 작업을 합니다.

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

`IAuthorizationService` 에 두 개의 `AuthorizeAsync` 메서드 오버 로드: 리소스와 정책 이름 및 다른 리소스와 평가 하기 위한 요구 사항 목록을 받고 있는 하나의 수락 합니다.

::: moniker range=">= aspnetcore-2.0"

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

<a name="security-authorization-resource-based-imperative"></a>

다음 예제에서는 리소스 보안을 유지 하도록 사용자 지정에 로드 됩니다 `Document` 개체입니다. `AuthorizeAsync` 오버 로드는 현재 사용자가 제공된 문서를 편집할 수 있는지 여부를 확인 하려면 호출 됩니다. 사용자 지정 "EditPolicy" 권한 부여 정책 결정으로 구분 됩니다. 참조 [사용자 지정 정책 기반 권한 부여](xref:security/authorization/policies) 권한 부여 정책 만들기에 대 한 자세한 내용은 합니다.

> [!NOTE]
> 다음 코드 샘플에서는 인증 실행으로 가정 및 집합은 `User` 속성.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a>리소스 기반 처리기를 작성 합니다.

리소스 기반 권한 부여 보다 큰 차이가 없습니다.에 대 한 처리기를 작성 [작성 한 일반 요구 사항 처리기](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler)합니다. 사용자 지정 요구 사항 클래스를 만들고 요구 사항 처리기 클래스를 구현 합니다. 요구 사항 클래스를 만드는 방법에 대 한 자세한 내용은 참조 하세요. [요구 사항](xref:security/authorization/policies#requirements)합니다.

처리기 클래스 요구 사항 및 리소스 종류를 지정합니다. 예를 들어 한 처리기를 활용 하는 `SameAuthorRequirement` 및 `Document` 리소스 다음과 같습니다.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

앞의 예제는 imagine `SameAuthorRequirement` 는 보다 일반적인의 특수 `SpecificAuthorRequirement` 클래스입니다. `SpecificAuthorRequirement` (표시 되지 않음) 하는 클래스를 포함 한 `Name` 작성자의 이름을 나타내는 속성입니다. `Name` 현재 사용자에 게 속성을 설정할 수 있습니다.

요구 사항 및 처리기의 등록 `Startup.ConfigureServices`:

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]

### <a name="operational-requirements"></a>운영 요구 사항

CRUD (만들기, 읽기, 업데이트, 삭제) 작업의 결과에 따라 결정을 변경 하려는 경우 사용 합니다 [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) 도우미 클래스입니다. 이 클래스를 사용 하면 각 작업 유형에 대 한 개별 클래스 대신 단일 처리기를 작성할 수 있습니다. 를 사용 하려면 몇 가지 작업 이름을 제공 합니다.

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

처리기는 다음과 같이 사용 하 여 구현 된 `OperationAuthorizationRequirement` 요구 사항 및 `Document` 리소스:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

이전 처리기 리소스, 사용자의 id 및 요구 사항의 사용 하는 작업의 유효성을 검사 `Name` 속성입니다.

운영 리소스 처리기를 호출 하려면 작업을 호출할 때 지정 `AuthorizeAsync` 페이지 처리기에 작업 합니다. 다음 예제에서는 인증된 된 사용자 제공된 문서를 볼 수 있는지 여부를 결정 합니다.

> [!NOTE]
> 다음 코드 샘플에서는 인증 실행으로 가정 및 집합은 `User` 속성.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

권한 부여에 성공 하는 경우 문서를 보기 위한 페이지가 반환 됩니다. 경우 권한 부여 실패 하지만 사용자가 인증 되 면 반환 `ForbidResult` 권한 부여에 실패 하는 모든 인증 미들웨어에 알립니다. `ChallengeResult` 인증을 수행 해야 하는 경우 반환 됩니다. 대화형 브라우저 클라이언트에 대 한 적절 한 로그인 페이지로 사용자를 리디렉션할 수 있습니다.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

권한 부여에 성공 하는 경우 문서에 대 한 보기 반환 됩니다. 권한 부여에 실패 하면 반환 `ChallengeResult` 알리고 모든 인증 미들웨어는 권한 부여 실패, 미들웨어는 적절 한 응답을 걸릴 수 있습니다. 적절 한 응답을 401 또는 403 상태 코드를 반환할 수 있습니다. 대화형 브라우저 클라이언트에 대 한 사용자를 로그인 페이지로 리디렉션 의미할 수 있습니다.

::: moniker-end
