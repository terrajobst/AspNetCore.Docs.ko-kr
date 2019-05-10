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
# <a name="resource-based-authorization-in-aspnet-core"></a><span data-ttu-id="dd3b4-103">ASP.NET Core에서 리소스 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="dd3b4-103">Resource-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="dd3b4-104">권한 부여 전략 액세스 되는 리소스에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-104">Authorization strategy depends upon the resource being accessed.</span></span> <span data-ttu-id="dd3b4-105">문서를 작성자 속성이 있는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-105">Consider a document that has an author property.</span></span> <span data-ttu-id="dd3b4-106">작성자만 문서를 업데이트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-106">Only the author is allowed to update the document.</span></span> <span data-ttu-id="dd3b4-107">따라서 문서 권한 부여 평가 발생 하기 전에 데이터 저장소에서 검색 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-107">Consequently, the document must be retrieved from the data store before authorization evaluation can occur.</span></span>

<span data-ttu-id="dd3b4-108">특성 평가 데이터 바인딩 전에 및 페이지 처리기 또는 문서를 로드 하는 작업을 실행 하기 전에 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-108">Attribute evaluation occurs before data binding and before execution of the page handler or action that loads the document.</span></span> <span data-ttu-id="dd3b4-109">이러한 이유로 사용 하 여 선언적 권한 부여는 `[Authorize]` 특성 충분 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-109">For these reasons, declarative authorization with an `[Authorize]` attribute doesn't suffice.</span></span> <span data-ttu-id="dd3b4-110">대신 사용자 지정 권한 부여 메서드를 호출할 수 있습니다&mdash;라고 하는 스타일 *명령적 권한 부여*합니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-110">Instead, you can invoke a custom authorization method&mdash;a style known as *imperative authorization*.</span></span>

<span data-ttu-id="dd3b4-111">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dd3b4-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="dd3b4-112">[권한 부여로 보호 되는 사용자 데이터를 사용 하 여 ASP.NET Core 앱 만들기](xref:security/authorization/secure-data) 리소스 기반 권한 부여를 사용 하는 샘플 앱을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-112">[Create an ASP.NET Core app with user data protected by authorization](xref:security/authorization/secure-data) contains a sample app that uses resource-based authorization.</span></span>

## <a name="use-imperative-authorization"></a><span data-ttu-id="dd3b4-113">필수 권한 부여 사용</span><span class="sxs-lookup"><span data-stu-id="dd3b4-113">Use imperative authorization</span></span>

<span data-ttu-id="dd3b4-114">권한 부여로 구현 됩니다는 [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) 서비스 및 서비스 컬렉션 내에 등록 되는 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-114">Authorization is implemented as an [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) service and is registered in the service collection within the `Startup` class.</span></span> <span data-ttu-id="dd3b4-115">서비스를 통해 제공 됩니다 [종속성 주입](xref:fundamentals/dependency-injection) 페이지 처리기 또는 작업을 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-115">The service is made available via [dependency injection](xref:fundamentals/dependency-injection) to page handlers or actions.</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

<span data-ttu-id="dd3b4-116">`IAuthorizationService` 에 두 개의 `AuthorizeAsync` 메서드 오버 로드: 리소스와 정책 이름 및 다른 리소스와 평가 하기 위한 요구 사항 목록을 받고 있는 하나의 수락 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-116">`IAuthorizationService` has two `AuthorizeAsync` method overloads: one accepting the resource and the policy name and the other accepting the resource and a list of requirements to evaluate.</span></span>

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

<span data-ttu-id="dd3b4-117">다음 예제에서는 리소스 보안을 유지 하도록 사용자 지정에 로드 됩니다 `Document` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-117">In the following example, the resource to be secured is loaded into a custom `Document` object.</span></span> <span data-ttu-id="dd3b4-118">`AuthorizeAsync` 오버 로드는 현재 사용자가 제공된 문서를 편집할 수 있는지 여부를 확인 하려면 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-118">An `AuthorizeAsync` overload is invoked to determine whether the current user is allowed to edit the provided document.</span></span> <span data-ttu-id="dd3b4-119">사용자 지정 "EditPolicy" 권한 부여 정책 결정으로 구분 됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-119">A custom "EditPolicy" authorization policy is factored into the decision.</span></span> <span data-ttu-id="dd3b4-120">참조 [사용자 지정 정책 기반 권한 부여](xref:security/authorization/policies) 권한 부여 정책 만들기에 대 한 자세한 내용은 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-120">See [Custom policy-based authorization](xref:security/authorization/policies) for more on creating authorization policies.</span></span>

> [!NOTE]
> <span data-ttu-id="dd3b4-121">다음 코드 샘플에서는 인증 실행으로 가정 및 집합은 `User` 속성.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-121">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a><span data-ttu-id="dd3b4-122">리소스 기반 처리기를 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-122">Write a resource-based handler</span></span>

<span data-ttu-id="dd3b4-123">리소스 기반 권한 부여 보다 큰 차이가 없습니다.에 대 한 처리기를 작성 [작성 한 일반 요구 사항 처리기](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler)합니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-123">Writing a handler for resource-based authorization isn't much different than [writing a plain requirements handler](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span></span> <span data-ttu-id="dd3b4-124">사용자 지정 요구 사항 클래스를 만들고 요구 사항 처리기 클래스를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-124">Create a custom requirement class, and implement a requirement handler class.</span></span> <span data-ttu-id="dd3b4-125">요구 사항 클래스를 만드는 방법에 대 한 자세한 내용은 참조 하세요. [요구 사항](xref:security/authorization/policies#requirements)합니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-125">For more information on creating a requirement class, see [Requirements](xref:security/authorization/policies#requirements).</span></span>

<span data-ttu-id="dd3b4-126">처리기 클래스 요구 사항 및 리소스 종류를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-126">The handler class specifies both the requirement and resource type.</span></span> <span data-ttu-id="dd3b4-127">예를 들어 한 처리기를 활용 하는 `SameAuthorRequirement` 및 `Document` 리소스 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-127">For example, a handler utilizing a `SameAuthorRequirement` and a `Document` resource follows:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

<span data-ttu-id="dd3b4-128">앞의 예제는 imagine `SameAuthorRequirement` 는 보다 일반적인의 특수 `SpecificAuthorRequirement` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-128">In the preceding example, imagine that `SameAuthorRequirement` is a special case of a more generic `SpecificAuthorRequirement` class.</span></span> <span data-ttu-id="dd3b4-129">`SpecificAuthorRequirement` (표시 되지 않음) 하는 클래스를 포함 한 `Name` 작성자의 이름을 나타내는 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-129">The `SpecificAuthorRequirement` class (not shown) contains a `Name` property representing the name of the author.</span></span> <span data-ttu-id="dd3b4-130">`Name` 현재 사용자에 게 속성을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-130">The `Name` property could be set to the current user.</span></span>

<span data-ttu-id="dd3b4-131">요구 사항 및 처리기의 등록 `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="dd3b4-131">Register the requirement and handler in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]

### <a name="operational-requirements"></a><span data-ttu-id="dd3b4-132">운영 요구 사항</span><span class="sxs-lookup"><span data-stu-id="dd3b4-132">Operational requirements</span></span>

<span data-ttu-id="dd3b4-133">CRUD (만들기, 읽기, 업데이트, 삭제) 작업의 결과에 따라 결정을 변경 하려는 경우 사용 합니다 [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) 도우미 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-133">If you're making decisions based on the outcomes of CRUD (Create, Read, Update, Delete) operations, use the [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) helper class.</span></span> <span data-ttu-id="dd3b4-134">이 클래스를 사용 하면 각 작업 유형에 대 한 개별 클래스 대신 단일 처리기를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-134">This class enables you to write a single handler instead of an individual class for each operation type.</span></span> <span data-ttu-id="dd3b4-135">를 사용 하려면 몇 가지 작업 이름을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-135">To use it, provide some operation names:</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

<span data-ttu-id="dd3b4-136">처리기는 다음과 같이 사용 하 여 구현 된 `OperationAuthorizationRequirement` 요구 사항 및 `Document` 리소스:</span><span class="sxs-lookup"><span data-stu-id="dd3b4-136">The handler is implemented as follows, using an `OperationAuthorizationRequirement` requirement and a `Document` resource:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

<span data-ttu-id="dd3b4-137">이전 처리기 리소스, 사용자의 id 및 요구 사항의 사용 하는 작업의 유효성을 검사 `Name` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-137">The preceding handler validates the operation using the resource, the user's identity, and the requirement's `Name` property.</span></span>

<span data-ttu-id="dd3b4-138">운영 리소스 처리기를 호출 하려면 작업을 호출할 때 지정 `AuthorizeAsync` 페이지 처리기에 작업 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-138">To call an operational resource handler, specify the operation when invoking `AuthorizeAsync` in your page handler or action.</span></span> <span data-ttu-id="dd3b4-139">다음 예제에서는 인증된 된 사용자 제공된 문서를 볼 수 있는지 여부를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-139">The following example determines whether the authenticated user is permitted to view the provided document.</span></span>

> [!NOTE]
> <span data-ttu-id="dd3b4-140">다음 코드 샘플에서는 인증 실행으로 가정 및 집합은 `User` 속성.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-140">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

<span data-ttu-id="dd3b4-141">권한 부여에 성공 하는 경우 문서를 보기 위한 페이지가 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-141">If authorization succeeds, the page for viewing the document is returned.</span></span> <span data-ttu-id="dd3b4-142">경우 권한 부여 실패 하지만 사용자가 인증 되 면 반환 `ForbidResult` 권한 부여에 실패 하는 모든 인증 미들웨어에 알립니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-142">If authorization fails but the user is authenticated, returning `ForbidResult` informs any authentication middleware that authorization failed.</span></span> <span data-ttu-id="dd3b4-143">`ChallengeResult` 인증을 수행 해야 하는 경우 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-143">A `ChallengeResult` is returned when authentication must be performed.</span></span> <span data-ttu-id="dd3b4-144">대화형 브라우저 클라이언트에 대 한 적절 한 로그인 페이지로 사용자를 리디렉션할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-144">For interactive browser clients, it may be appropriate to redirect the user to a login page.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

<span data-ttu-id="dd3b4-145">권한 부여에 성공 하는 경우 문서에 대 한 보기 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-145">If authorization succeeds, the view for the document is returned.</span></span> <span data-ttu-id="dd3b4-146">권한 부여에 실패 하면 반환 `ChallengeResult` 알리고 모든 인증 미들웨어는 권한 부여 실패, 미들웨어는 적절 한 응답을 걸릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-146">If authorization fails, returning `ChallengeResult` informs any authentication middleware that authorization failed, and the middleware can take the appropriate response.</span></span> <span data-ttu-id="dd3b4-147">적절 한 응답을 401 또는 403 상태 코드를 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-147">An appropriate response could be returning a 401 or 403 status code.</span></span> <span data-ttu-id="dd3b4-148">대화형 브라우저 클라이언트에 대 한 사용자를 로그인 페이지로 리디렉션 의미할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dd3b4-148">For interactive browser clients, it could mean redirecting the user to a login page.</span></span>

::: moniker-end
