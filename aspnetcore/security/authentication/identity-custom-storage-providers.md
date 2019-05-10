---
title: ASP.NET Core Id에 대 한 사용자 지정 저장소 공급자
author: ardalis
description: ASP.NET Core Id에 대 한 사용자 지정 저장소 공급자를 구성 하는 방법에 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: 5a0797fcfe93d49b941b61688ae8f58a1b5d7614
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65086343"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a><span data-ttu-id="aeb42-103">ASP.NET Core Id에 대 한 사용자 지정 저장소 공급자</span><span class="sxs-lookup"><span data-stu-id="aeb42-103">Custom storage providers for ASP.NET Core Identity</span></span>

<span data-ttu-id="aeb42-104">작성자: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="aeb42-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="aeb42-105">ASP.NET Core Id는 확장 가능한 시스템 사용자 지정 저장소 공급자를 만들고 앱에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-105">ASP.NET Core Identity is an extensible system which enables you to create a custom storage provider and connect it to your app.</span></span> <span data-ttu-id="aeb42-106">이 항목에서는 ASP.NET Core Id에 대 한 사용자 지정된 저장소 공급자를 만드는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-106">This topic describes how to create a customized storage provider for ASP.NET Core Identity.</span></span> <span data-ttu-id="aeb42-107">사용자 고유의 저장소 공급자를 만들기 위한 중요 한 개념을 다루지만 단계별 연습은 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-107">It covers the important concepts for creating your own storage provider, but isn't a step-by-step walkthrough.</span></span>

<span data-ttu-id="aeb42-108">[GitHub에서 샘플 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample)</span><span class="sxs-lookup"><span data-stu-id="aeb42-108">[View or download sample from GitHub](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample).</span></span>

## <a name="introduction"></a><span data-ttu-id="aeb42-109">소개</span><span class="sxs-lookup"><span data-stu-id="aeb42-109">Introduction</span></span>

<span data-ttu-id="aeb42-110">ASP.NET Core Id 시스템을 기본적으로 Entity Framework Core를 사용 하 여 SQL Server 데이터베이스에 사용자 정보를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-110">By default, the ASP.NET Core Identity system stores user information in a SQL Server database using Entity Framework Core.</span></span> <span data-ttu-id="aeb42-111">대부분의 앱에 대 한이 방법은 잘 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-111">For many apps, this approach works well.</span></span> <span data-ttu-id="aeb42-112">그러나 다음 다른 지 속성 메커니즘 또는 데이터 스키마를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-112">However, you may prefer to use a different persistence mechanism or data schema.</span></span> <span data-ttu-id="aeb42-113">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="aeb42-113">For example:</span></span>

* <span data-ttu-id="aeb42-114">사용할 [Azure Table Storage](/azure/storage/) 또는 다른 데이터 저장소입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-114">You use [Azure Table Storage](/azure/storage/) or another data store.</span></span>
* <span data-ttu-id="aeb42-115">데이터베이스 테이블 구조가 서로 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-115">Your database tables have a different structure.</span></span> 
* <span data-ttu-id="aeb42-116">와 같은 다른 데이터 액세스 접근 방식을 사용 하려고 할 수 있습니다 [Dapper](https://github.com/StackExchange/Dapper)합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-116">You may wish to use a different data access approach, such as [Dapper](https://github.com/StackExchange/Dapper).</span></span> 

<span data-ttu-id="aeb42-117">이러한 경우 각 저장소 메커니즘에 대 한 사용자 지정된 공급자를 작성할 수 있으며 앱에 해당 공급자를 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-117">In each of these cases, you can write a customized provider for your storage mechanism and plug that provider into your app.</span></span>

<span data-ttu-id="aeb42-118">ASP.NET Core Id는 "개별 사용자 계정" 옵션을 사용 하 여 Visual Studio에서 프로젝트 템플릿에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-118">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="aeb42-119">.NET Core CLI를 사용 하는 경우 추가 `-au Individual`:</span><span class="sxs-lookup"><span data-stu-id="aeb42-119">When using the .NET Core CLI, add `-au Individual`:</span></span>

```console
dotnet new mvc -au Individual
dotnet new webapi -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a><span data-ttu-id="aeb42-120">ASP.NET Core Id 아키텍처</span><span class="sxs-lookup"><span data-stu-id="aeb42-120">The ASP.NET Core Identity architecture</span></span>

<span data-ttu-id="aeb42-121">ASP.NET Core Id 관리자 및 저장소를 호출 하는 클래스로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-121">ASP.NET Core Identity consists of classes called managers and stores.</span></span> <span data-ttu-id="aeb42-122">*관리자* 는 앱 개발자는 Id 사용자를 만드는 등의 작업을 수행 하는 상위 수준 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-122">*Managers* are high-level classes which an app developer uses to perform operations, such as creating an Identity user.</span></span> <span data-ttu-id="aeb42-123">*저장소* 사용자 역할과 같은 엔터티를 유지 되는 방법을 지정 하는 하위 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-123">*Stores* are lower-level classes that specify how entities, such as users and roles, are persisted.</span></span> <span data-ttu-id="aeb42-124">상점과 리포지토리 패턴에 따라 지 속성 메커니즘을 사용 하 여 밀접 하 게 결합 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-124">Stores follow the repository pattern and are closely coupled with the persistence mechanism.</span></span> <span data-ttu-id="aeb42-125">관리자 (구성) 제외 하 고 응용 프로그램 코드 변경 없이 지 속성 메커니즘을 바꿀 수 있습니다 즉 저장소에서 분리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-125">Managers are decoupled from stores, which means you can replace the persistence mechanism without changing your application code (except for configuration).</span></span>

<span data-ttu-id="aeb42-126">다음 다이어그램에서는 저장소 데이터 액세스 계층 상호 작용 하는 동안 웹 앱 관리자 상호 작용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-126">The following diagram shows how a web app interacts with the managers, while stores interact with the data access layer.</span></span>

![ASP.NET Core 앱 관리자 (예: 'UserManager', 'RoleManager')를 사용 하 여 작동합니다.](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

<span data-ttu-id="aeb42-129">사용자 지정 저장소 공급자를 만들려면이 데이터 액세스 계층 (위의 다이어그램에서 녹색 및 회색 상자)를 사용 하 여 데이터 원본, 데이터 액세스 계층 및 상호 작용 하는 저장소 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-129">To create a custom storage provider, create the data source, the data access layer, and the store classes that interact with this data access layer (the green and grey boxes in the diagram above).</span></span> <span data-ttu-id="aeb42-130">관리자나 합니다 (위의 파란색 상자는) 상호 작용 하는 앱 코드를 사용자 지정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-130">You don't need to customize the managers or your app code that interacts with them (the blue boxes above).</span></span>

<span data-ttu-id="aeb42-131">새 인스턴스를 만들 때 `UserManager` 또는 `RoleManager` 사용자 클래스의 형식을 제공 하 고 저장소 클래스의 인스턴스를 인수로 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-131">When creating a new instance of `UserManager` or `RoleManager` you provide the type of the user class and pass an instance of the store class as an argument.</span></span> <span data-ttu-id="aeb42-132">이 방법을 사용 하면 ASP.NET Core에 사용자 지정된 클래스를 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-132">This approach enables you to plug your customized classes into ASP.NET Core.</span></span> 

<span data-ttu-id="aeb42-133">[새 저장소 공급자를 사용 하도록 앱을 다시 구성](#reconfigure-app-to-use-a-new-storage-provider) 인스턴스화하는 방법을 보여 줍니다 `UserManager` 고 `RoleManager` 사용자 지정된 저장소를 사용 하 여 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-133">[Reconfigure app to use new storage provider](#reconfigure-app-to-use-a-new-storage-provider) shows how to instantiate `UserManager` and `RoleManager` with a customized store.</span></span>

## <a name="aspnet-core-identity-stores-data-types"></a><span data-ttu-id="aeb42-134">ASP.NET Core Id 데이터 형식 저장</span><span class="sxs-lookup"><span data-stu-id="aeb42-134">ASP.NET Core Identity stores data types</span></span>

<span data-ttu-id="aeb42-135">[ASP.NET Core Id](https://github.com/aspnet/identity) 데이터 형식은 다음 섹션에서 자세히 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-135">[ASP.NET Core Identity](https://github.com/aspnet/identity) data types are detailed in the following sections:</span></span>

### <a name="users"></a><span data-ttu-id="aeb42-136">Users</span><span class="sxs-lookup"><span data-stu-id="aeb42-136">Users</span></span>

<span data-ttu-id="aeb42-137">웹 사이트의 사용자를 등록된 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-137">Registered users of your web site.</span></span> <span data-ttu-id="aeb42-138">합니다 [IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) 형식은 확장 하거나 사용자 고유의 사용자 지정 형식에 대 한 예제로 사용 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-138">The [IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) type may be extended or used as an example for your own custom type.</span></span> <span data-ttu-id="aeb42-139">사용자 고유의 사용자 지정 id 저장소 솔루션을 구현 하는 특정 형식에서 상속할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-139">You don't need to inherit from a particular type to implement your own custom identity storage solution.</span></span>

### <a name="user-claims"></a><span data-ttu-id="aeb42-140">사용자 클레임</span><span class="sxs-lookup"><span data-stu-id="aeb42-140">User Claims</span></span>

<span data-ttu-id="aeb42-141">문 집합 (또는 [클레임](/dotnet/api/system.security.claims.claim)) 사용자의 id를 나타내는 사용자에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-141">A set of statements (or [Claims](/dotnet/api/system.security.claims.claim)) about the user that represent the user's identity.</span></span> <span data-ttu-id="aeb42-142">사용자의 id 역할을 통해 수행할 수 있습니다 보다 큰 식을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-142">Can enable greater expression of the user's identity than can be achieved through roles.</span></span>

### <a name="user-logins"></a><span data-ttu-id="aeb42-143">사용자 로그인</span><span class="sxs-lookup"><span data-stu-id="aeb42-143">User Logins</span></span>

<span data-ttu-id="aeb42-144">외부 인증 공급자 (예: Facebook 또는 Microsoft 계정)에 대 한 정보는 사용자를 로그인 할 때 사용 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-144">Information about the external authentication provider (like Facebook or a Microsoft account) to use when logging in a user.</span></span> [<span data-ttu-id="aeb42-145">예제</span><span class="sxs-lookup"><span data-stu-id="aeb42-145">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a><span data-ttu-id="aeb42-146">역할</span><span class="sxs-lookup"><span data-stu-id="aeb42-146">Roles</span></span>

<span data-ttu-id="aeb42-147">사이트에 대 한 권한 부여 그룹입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-147">Authorization groups for your site.</span></span> <span data-ttu-id="aeb42-148">역할 Id 및 역할 이름 (예: "Admin" 또는 "Employee")를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-148">Includes the role Id and role name (like "Admin" or "Employee").</span></span> [<span data-ttu-id="aeb42-149">예제</span><span class="sxs-lookup"><span data-stu-id="aeb42-149">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a><span data-ttu-id="aeb42-150">데이터 액세스 계층</span><span class="sxs-lookup"><span data-stu-id="aeb42-150">The data access layer</span></span>

<span data-ttu-id="aeb42-151">이 항목에서는 사용 하고자 하는 지 속성 메커니즘 및 해당 메커니즘에 대 한 엔터티를 만드는 방법에 익숙하다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-151">This topic assumes you are familiar with the persistence mechanism that you are going to use and how to create entities for that mechanism.</span></span> <span data-ttu-id="aeb42-152">이 항목에서는 저장소 또는 데이터 액세스 클래스를 만드는 방법에 대 한 정보를 제공 하지 않습니다. ASP.NET Core Id를 사용 하 여 작업할 때 디자인 결정에 대 한 몇 가지 제안을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-152">This topic doesn't provide details about how to create the repositories or data access classes; it provides some suggestions about design decisions when working with ASP.NET Core Identity.</span></span>

<span data-ttu-id="aeb42-153">사용자 지정된 저장소 공급자에 대 한 데이터 액세스 계층을 디자인할 때 자유롭게를 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-153">You have a lot of freedom when designing the data access layer for a customized store provider.</span></span> <span data-ttu-id="aeb42-154">앱에서 사용 하려는 기능에 대 한 지 속성 메커니즘을 만드는 데만 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-154">You only need to create persistence mechanisms for features that you intend to use in your app.</span></span> <span data-ttu-id="aeb42-155">예를 들어 앱에서 역할을 사용 하지 않는 경우에 역할 또는 사용자 역할 연결에 대 한 저장소를 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-155">For example, if you are not using roles in your app, you don't need to create storage for roles or user role associations.</span></span> <span data-ttu-id="aeb42-156">기술 및 기존 인프라에는 ASP.NET Core Id의 기본 구현에서 매우 다른 구조가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-156">Your technology and existing infrastructure may require a structure that's very different from the default implementation of ASP.NET Core Identity.</span></span> <span data-ttu-id="aeb42-157">데이터 액세스 계층, 저장소 구현의 구조를 사용 하는 논리를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-157">In your data access layer, you provide the logic to work with the structure of your storage implementation.</span></span>

<span data-ttu-id="aeb42-158">데이터 액세스 계층에는 ASP.NET Core Id에서 데이터 원본에 데이터를 저장 하는 논리를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-158">The data access layer provides the logic to save the data from ASP.NET Core Identity to a data source.</span></span> <span data-ttu-id="aeb42-159">사용자 지정된 저장소 공급자에 대 한 데이터 액세스 계층 사용자 및 역할 정보를 저장 하는 다음 클래스를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-159">The data access layer for your customized storage provider might include the following classes to store user and role information.</span></span>

### <a name="context-class"></a><span data-ttu-id="aeb42-160">Context 클래스</span><span class="sxs-lookup"><span data-stu-id="aeb42-160">Context class</span></span>

<span data-ttu-id="aeb42-161">지 속성 메커니즘에 연결 하 여 쿼리를 실행 하는 정보를 캡슐화 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-161">Encapsulates the information to connect to your persistence mechanism and execute queries.</span></span> <span data-ttu-id="aeb42-162">일반적으로 종속성 주입을 통해 제공 되는이 클래스의 인스턴스를 필요로 하는 여러 데이터 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-162">Several data classes require an instance of this class, typically provided through dependency injection.</span></span> <span data-ttu-id="aeb42-163">[예제](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1)합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-163">[Example](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).</span></span>

### <a name="user-storage"></a><span data-ttu-id="aeb42-164">사용자 저장소</span><span class="sxs-lookup"><span data-stu-id="aeb42-164">User Storage</span></span>

<span data-ttu-id="aeb42-165">저장 하 고 사용자 정보 (예: 사용자 이름 및 암호 해시)을 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-165">Stores and retrieves user information (such as user name and password hash).</span></span> [<span data-ttu-id="aeb42-166">예제</span><span class="sxs-lookup"><span data-stu-id="aeb42-166">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a><span data-ttu-id="aeb42-167">역할 저장소</span><span class="sxs-lookup"><span data-stu-id="aeb42-167">Role Storage</span></span>

<span data-ttu-id="aeb42-168">저장 하 고 (예: 역할 이름) 역할 정보를 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-168">Stores and retrieves role information (such as the role name).</span></span> [<span data-ttu-id="aeb42-169">예제</span><span class="sxs-lookup"><span data-stu-id="aeb42-169">Example</span></span>](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a><span data-ttu-id="aeb42-170">UserClaims 저장소</span><span class="sxs-lookup"><span data-stu-id="aeb42-170">UserClaims Storage</span></span>

<span data-ttu-id="aeb42-171">저장 하 고 사용자 클레임 정보 (예: 클레임 유형과 값)을 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-171">Stores and retrieves user claim information (such as the claim type and value).</span></span> [<span data-ttu-id="aeb42-172">예제</span><span class="sxs-lookup"><span data-stu-id="aeb42-172">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a><span data-ttu-id="aeb42-173">UserLogins 저장소</span><span class="sxs-lookup"><span data-stu-id="aeb42-173">UserLogins Storage</span></span>

<span data-ttu-id="aeb42-174">저장 하 고 사용자 로그인 정보 (예: 외부 인증 공급자)를 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-174">Stores and retrieves user login information (such as an external authentication provider).</span></span> [<span data-ttu-id="aeb42-175">예제</span><span class="sxs-lookup"><span data-stu-id="aeb42-175">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a><span data-ttu-id="aeb42-176">UserRole 저장소</span><span class="sxs-lookup"><span data-stu-id="aeb42-176">UserRole Storage</span></span>

<span data-ttu-id="aeb42-177">저장 하 고는 사용자에 게 할당 된 역할을 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-177">Stores and retrieves which roles are assigned to which users.</span></span> [<span data-ttu-id="aeb42-178">예제</span><span class="sxs-lookup"><span data-stu-id="aeb42-178">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

<span data-ttu-id="aeb42-179">**TIP:** 만 앱에서 사용 하려는 클래스를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-179">**TIP:** Only implement the classes you intend to use in your app.</span></span>

<span data-ttu-id="aeb42-180">데이터 액세스 클래스에서 지 속성 메커니즘에 대 한 데이터 작업을 수행 하는 코드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-180">In the data access classes, provide code to perform data operations for your persistence mechanism.</span></span> <span data-ttu-id="aeb42-181">예를 들어, 사용자 지정 공급자를 내 해야에서 새 사용자를 만들려면 다음 코드를 *저장할* 클래스:</span><span class="sxs-lookup"><span data-stu-id="aeb42-181">For example, within a custom provider, you might have the following code to create a new user in the *store* class:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

<span data-ttu-id="aeb42-182">사용자를 만들기 위한 구현 논리는 `_usersTable.CreateAsync` 메서드를 아래에 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-182">The implementation logic for creating the user is in the `_usersTable.CreateAsync` method, shown below.</span></span>

## <a name="customize-the-user-class"></a><span data-ttu-id="aeb42-183">사용자 클래스를 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="aeb42-183">Customize the user class</span></span>

<span data-ttu-id="aeb42-184">저장소 공급자를 구현할 때 해당 하는 사용자 클래스를 만듭니다는 [IdentityUser 클래스](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-184">When implementing a storage provider, create a user class which is equivalent to the [IdentityUser class](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser).</span></span>

<span data-ttu-id="aeb42-185">최소한 user 클래스에 포함 해야 합니다는 `Id` 및 `UserName` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-185">At a minimum, your user class must include an `Id` and a `UserName` property.</span></span>

<span data-ttu-id="aeb42-186">`IdentityUser` 클래스 속성을 정의 합니다.는 `UserManager` 호출을 수행할 때 작업을 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-186">The `IdentityUser` class defines the properties that the `UserManager` calls when performing requested operations.</span></span> <span data-ttu-id="aeb42-187">기본 형식을 합니다 `Id` 속성은 문자열 이지만에서 상속할 수 있습니다 `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` 유형을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-187">The default type of the `Id` property is a string, but you can inherit from `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` and specify a different type.</span></span> <span data-ttu-id="aeb42-188">프레임 워크는 데이터 형식 변환을 처리 하기 위해 저장소 구현이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-188">The framework expects the storage implementation to handle data type conversions.</span></span>

## <a name="customize-the-user-store"></a><span data-ttu-id="aeb42-189">사용자 저장소를 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="aeb42-189">Customize the user store</span></span>

<span data-ttu-id="aeb42-190">만들기는 `UserStore` 사용자에 모든 데이터 작업에 대 한 메서드를 제공 하는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-190">Create a `UserStore` class that provides the methods for all data operations on the user.</span></span> <span data-ttu-id="aeb42-191">이 클래스는 해당 하는 [UserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-191">This class is equivalent to the [UserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) class.</span></span> <span data-ttu-id="aeb42-192">사용자 `UserStore` 클래스에서 구현 `IUserStore<TUser>` 및 필요한 선택적 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-192">In your `UserStore` class, implement `IUserStore<TUser>` and the optional interfaces required.</span></span> <span data-ttu-id="aeb42-193">앱에서 제공 하는 기능에 따라 구현 해야 하는 선택적 인터페이스를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-193">You select which optional interfaces to implement based on the functionality provided in your app.</span></span>

### <a name="optional-interfaces"></a><span data-ttu-id="aeb42-194">선택적 인터페이스</span><span class="sxs-lookup"><span data-stu-id="aeb42-194">Optional interfaces</span></span>

* [<span data-ttu-id="aeb42-195">IUserRoleStore</span><span class="sxs-lookup"><span data-stu-id="aeb42-195">IUserRoleStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
* [<span data-ttu-id="aeb42-196">IUserClaimStore</span><span class="sxs-lookup"><span data-stu-id="aeb42-196">IUserClaimStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
* [<span data-ttu-id="aeb42-197">IUserPasswordStore</span><span class="sxs-lookup"><span data-stu-id="aeb42-197">IUserPasswordStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
* [<span data-ttu-id="aeb42-198">IUserSecurityStampStore</span><span class="sxs-lookup"><span data-stu-id="aeb42-198">IUserSecurityStampStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
* [<span data-ttu-id="aeb42-199">IUserEmailStore</span><span class="sxs-lookup"><span data-stu-id="aeb42-199">IUserEmailStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
* [<span data-ttu-id="aeb42-200">IUserPhoneNumberStore</span><span class="sxs-lookup"><span data-stu-id="aeb42-200">IUserPhoneNumberStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
* [<span data-ttu-id="aeb42-201">IQueryableUserStore</span><span class="sxs-lookup"><span data-stu-id="aeb42-201">IQueryableUserStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
* [<span data-ttu-id="aeb42-202">IUserLoginStore</span><span class="sxs-lookup"><span data-stu-id="aeb42-202">IUserLoginStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
* [<span data-ttu-id="aeb42-203">IUserTwoFactorStore</span><span class="sxs-lookup"><span data-stu-id="aeb42-203">IUserTwoFactorStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
* [<span data-ttu-id="aeb42-204">IUserLockoutStore</span><span class="sxs-lookup"><span data-stu-id="aeb42-204">IUserLockoutStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)

<span data-ttu-id="aeb42-205">선택적 인터페이스에서 상속 `IUserStore<TUser>`합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-205">The optional interfaces inherit from `IUserStore<TUser>`.</span></span> <span data-ttu-id="aeb42-206">에 저장 하는 부분적으로 구현 된 샘플 사용자를 볼 수 있습니다 합니다 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs)합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-206">You can see a partially implemented sample user store in the [sample app](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs).</span></span>

<span data-ttu-id="aeb42-207">내는 `UserStore` 클래스 작업을 수행 하기 위해 만든 데이터 액세스 클래스를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-207">Within the `UserStore` class, you use the data access classes that you created to perform operations.</span></span> <span data-ttu-id="aeb42-208">이러한 종속성 주입을 사용 하 여 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-208">These are passed in using dependency injection.</span></span> <span data-ttu-id="aeb42-209">Dapper 구현에서는 SQL Server의 예를 들어, 합니다 `UserStore` 클래스에는 `CreateAsync` 의 인스턴스를 사용 하는 메서드 `DapperUsersTable` 새 레코드를 삽입:</span><span class="sxs-lookup"><span data-stu-id="aeb42-209">For example, in the SQL Server with Dapper implementation, the `UserStore` class has the `CreateAsync` method which uses an instance of `DapperUsersTable` to insert a new record:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a><span data-ttu-id="aeb42-210">사용자 저장소를 사용자 지정할 때 구현 하는 인터페이스</span><span class="sxs-lookup"><span data-stu-id="aeb42-210">Interfaces to implement when customizing user store</span></span>

* <span data-ttu-id="aeb42-211">**IUserStore**</span><span class="sxs-lookup"><span data-stu-id="aeb42-211">**IUserStore**</span></span>  
 <span data-ttu-id="aeb42-212">합니다 [IUserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) 인터페이스는 유일한 인터페이스는 사용자 저장소에 구현 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-212">The [IUserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) interface is the only interface you must implement in the user store.</span></span> <span data-ttu-id="aeb42-213">만들기, 업데이트, 삭제 및 사용자를 검색 하기 위한 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-213">It defines methods for creating, updating, deleting, and retrieving users.</span></span>
* <span data-ttu-id="aeb42-214">**IUserClaimStore**</span><span class="sxs-lookup"><span data-stu-id="aeb42-214">**IUserClaimStore**</span></span>  
 <span data-ttu-id="aeb42-215">합니다 [IUserClaimStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) 인터페이스는 사용자 클레임을 사용 하도록 구현 하는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-215">The [IUserClaimStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) interface defines the methods you implement to enable user claims.</span></span> <span data-ttu-id="aeb42-216">추가, 제거 및 사용자 클레임을 검색 하는 메서드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-216">It contains methods for adding, removing and retrieving user claims.</span></span>
* <span data-ttu-id="aeb42-217">**IUserLoginStore**</span><span class="sxs-lookup"><span data-stu-id="aeb42-217">**IUserLoginStore**</span></span>  
 <span data-ttu-id="aeb42-218">합니다 [IUserLoginStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) 외부 인증 공급자를 사용 하도록 구현 하는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-218">The [IUserLoginStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) defines the methods you implement to enable external authentication providers.</span></span> <span data-ttu-id="aeb42-219">추가, 제거 및 사용자 로그인 및 로그인 정보를 기반으로 사용자를 검색 하는 메서드를 검색 하는 메서드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-219">It contains methods for adding, removing and retrieving user logins, and a method for retrieving a user based on the login information.</span></span>
* <span data-ttu-id="aeb42-220">**IUserRoleStore**</span><span class="sxs-lookup"><span data-stu-id="aeb42-220">**IUserRoleStore**</span></span>  
 <span data-ttu-id="aeb42-221">합니다 [IUserRoleStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) 인터페이스 역할에 사용자를 매핑하기 위해 구현할 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-221">The [IUserRoleStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) interface defines the methods you implement to map a user to a role.</span></span> <span data-ttu-id="aeb42-222">추가, 제거 및 사용자의 역할 및 역할에 사용자가 할당 되었는지 확인 하는 메서드를 검색 하는 메서드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-222">It contains methods to add, remove, and retrieve a user's roles, and a method to check if a user is assigned to a role.</span></span>
* <span data-ttu-id="aeb42-223">**IUserPasswordStore**</span><span class="sxs-lookup"><span data-stu-id="aeb42-223">**IUserPasswordStore**</span></span>  
 <span data-ttu-id="aeb42-224">합니다 [IUserPasswordStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) 인터페이스 해시 된 암호를 유지 하기 위해 구현할 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-224">The [IUserPasswordStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) interface defines the methods you implement to persist hashed passwords.</span></span> <span data-ttu-id="aeb42-225">가져와 사용자가 암호를 설정 하는지 여부를 나타내는 메서드와 해시 된 암호를 설정 하는 메서드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-225">It contains methods for getting and setting the hashed password, and a method that indicates whether the user has set a password.</span></span>
* <span data-ttu-id="aeb42-226">**IUserSecurityStampStore**</span><span class="sxs-lookup"><span data-stu-id="aeb42-226">**IUserSecurityStampStore**</span></span>  
 <span data-ttu-id="aeb42-227">합니다 [IUserSecurityStampStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) 인터페이스는 사용자의 계정 정보 변경 되었는지 여부를 나타내는 보안 스탬프를 사용 하기 위해 구현할 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-227">The [IUserSecurityStampStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) interface defines the methods you implement to use a security stamp for indicating whether the user's account information has changed.</span></span> <span data-ttu-id="aeb42-228">사용자 암호를 변경 또는 추가 하거나 로그인을 제거 하는 경우이 스탬프 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-228">This stamp is updated when a user changes the password, or adds or removes logins.</span></span> <span data-ttu-id="aeb42-229">가져오기 및 설정의 보안 스탬프에 대 한 메서드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-229">It contains methods for getting and setting the security stamp.</span></span>
* <span data-ttu-id="aeb42-230">**IUserTwoFactorStore**</span><span class="sxs-lookup"><span data-stu-id="aeb42-230">**IUserTwoFactorStore**</span></span>  
 <span data-ttu-id="aeb42-231">합니다 [IUserTwoFactorStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) 인터페이스 2 단계 인증을 지원 하기 위해 구현할 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-231">The [IUserTwoFactorStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) interface defines the methods you implement to support two factor authentication.</span></span> <span data-ttu-id="aeb42-232">가져와 사용자에 대 한 2 단계 인증이 사용 되는지 여부를 설정 하는 메서드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-232">It contains methods for getting and setting whether two factor authentication is enabled for a user.</span></span>
* <span data-ttu-id="aeb42-233">**IUserPhoneNumberStore**</span><span class="sxs-lookup"><span data-stu-id="aeb42-233">**IUserPhoneNumberStore**</span></span>  
 <span data-ttu-id="aeb42-234">합니다 [IUserPhoneNumberStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) 인터페이스 사용자 전화 번호를 저장 하는를 구현 하는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-234">The [IUserPhoneNumberStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) interface defines the methods you implement to store user phone numbers.</span></span> <span data-ttu-id="aeb42-235">가져오기 및 전화 번호 및 전화 번호가 확인 되었는지 여부를 설정에 대 한 메서드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-235">It contains methods for getting and setting the phone number and whether the phone number is confirmed.</span></span>
* <span data-ttu-id="aeb42-236">**IUserEmailStore**</span><span class="sxs-lookup"><span data-stu-id="aeb42-236">**IUserEmailStore**</span></span>  
 <span data-ttu-id="aeb42-237">합니다 [IUserEmailStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) 인터페이스는 사용자 전자 메일 주소가 저장을 구현 하는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-237">The [IUserEmailStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) interface defines the methods you implement to store user email addresses.</span></span> <span data-ttu-id="aeb42-238">가져오기 및 전자 메일 주소 및 전자 메일 확인 되었는지 여부를 설정에 대 한 메서드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-238">It contains methods for getting and setting the email address and whether the email is confirmed.</span></span>
* <span data-ttu-id="aeb42-239">**IUserLockoutStore**</span><span class="sxs-lookup"><span data-stu-id="aeb42-239">**IUserLockoutStore**</span></span>  
 <span data-ttu-id="aeb42-240">합니다 [IUserLockoutStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) 인터페이스 계정 잠금에 대 한 정보를 저장할 구현 하는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-240">The [IUserLockoutStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) interface defines the methods you implement to store information about locking an account.</span></span> <span data-ttu-id="aeb42-241">실패 한 액세스 시도 및 잠금을 추적 하기 위한 메서드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-241">It contains methods for tracking failed access attempts and lockouts.</span></span>
* <span data-ttu-id="aeb42-242">**IQueryableUserStore**</span><span class="sxs-lookup"><span data-stu-id="aeb42-242">**IQueryableUserStore**</span></span>  
 <span data-ttu-id="aeb42-243">합니다 [IQueryableUserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) 인터페이스를 쿼리 가능한 사용자 저장소를 제공 하기 위해 구현할 멤버를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-243">The [IQueryableUserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) interface defines the members you implement to provide a queryable user store.</span></span>

<span data-ttu-id="aeb42-244">앱에서 필요한 인터페이스만 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-244">You implement only the interfaces that are needed in your app.</span></span> <span data-ttu-id="aeb42-245">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="aeb42-245">For example:</span></span>

```csharp
public class UserStore : IUserStore<IdentityUser>,
                         IUserClaimStore<IdentityUser>,
                         IUserLoginStore<IdentityUser>,
                         IUserRoleStore<IdentityUser>,
                         IUserPasswordStore<IdentityUser>,
                         IUserSecurityStampStore<IdentityUser>
{
    // interface implementations not shown
}
```

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a><span data-ttu-id="aeb42-246">IdentityUserClaim, IdentityUserLogin, 및 IdentityUserRole</span><span class="sxs-lookup"><span data-stu-id="aeb42-246">IdentityUserClaim, IdentityUserLogin, and IdentityUserRole</span></span>

<span data-ttu-id="aeb42-247">`Microsoft.AspNet.Identity.EntityFramework` 네임 스페이스의 구현을 포함 합니다 [IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin), 및 [IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-247">The `Microsoft.AspNet.Identity.EntityFramework` namespace contains implementations of the [IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin), and [IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) classes.</span></span> <span data-ttu-id="aeb42-248">이러한 기능을 사용 하는 경우에 이러한 클래스의 고유한 버전을 만들고 앱에 대 한 속성을 정의 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-248">If you are using these features, you may want to create your own versions of these classes and define the properties for your app.</span></span> <span data-ttu-id="aeb42-249">그러나 경우가 없습니다 (예: 추가 또는 제거 하는 사용자의 클레임) 기본 작업을 수행할 때 이러한 엔터티를 메모리로 로드 하는 것이 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-249">However, sometimes it's more efficient to not load these entities into memory when performing basic operations (such as adding or removing a user's claim).</span></span> <span data-ttu-id="aeb42-250">대신 백 엔드 저장소 클래스는 데이터 원본에서 직접 이러한 작업을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-250">Instead, the backend store classes can execute these operations directly on the data source.</span></span> <span data-ttu-id="aeb42-251">예를 들어 합니다 `UserStore.GetClaimsAsync` 메서드를 호출할 수는 `userClaimTable.FindByUserId(user.Id)` 에서 쿼리를 실행 하는 메서드를 직접 테이블 클레임의 목록을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-251">For example, the `UserStore.GetClaimsAsync` method can call the `userClaimTable.FindByUserId(user.Id)` method to execute a query on that table directly and return a list of claims.</span></span>

## <a name="customize-the-role-class"></a><span data-ttu-id="aeb42-252">역할 클래스를 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="aeb42-252">Customize the role class</span></span>

<span data-ttu-id="aeb42-253">역할 저장소 공급자를 구현할 때 사용자 지정 역할 유형을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-253">When implementing a role storage provider, you can create a custom role type.</span></span> <span data-ttu-id="aeb42-254">특정 인터페이스를 구현 하지는 필요 하지만 권한이 있어야 합니다는 `Id` 갖습니다 일반적으로 `Name` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-254">It need not implement a particular interface, but it must have an `Id` and typically it will have a `Name` property.</span></span>

<span data-ttu-id="aeb42-255">다음은 예제 역할 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-255">The following is an example role class:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a><span data-ttu-id="aeb42-256">역할 저장소를 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="aeb42-256">Customize the role store</span></span>

<span data-ttu-id="aeb42-257">만들 수는 `RoleStore` 역할에서 모든 데이터 작업에 대 한 메서드를 제공 하는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-257">You can create a `RoleStore` class that provides the methods for all data operations on roles.</span></span> <span data-ttu-id="aeb42-258">이 클래스는 해당 하는 [RoleStore&lt;TRole&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-258">This class is equivalent to the [RoleStore&lt;TRole&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) class.</span></span> <span data-ttu-id="aeb42-259">에 `RoleStore` 구현 클래스를 `IRoleStore<TRole>` 및 필요에 따라는 `IQueryableRoleStore<TRole>` 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-259">In the `RoleStore` class, you implement the `IRoleStore<TRole>` and optionally the `IQueryableRoleStore<TRole>` interface.</span></span>

* <span data-ttu-id="aeb42-260">**IRoleStore&lt;TRole&gt;**</span><span class="sxs-lookup"><span data-stu-id="aeb42-260">**IRoleStore&lt;TRole&gt;**</span></span>  
 <span data-ttu-id="aeb42-261">합니다 [IRoleStore&lt;TRole&gt; ](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) 인터페이스 역할 저장소 클래스에서 구현 하는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-261">The [IRoleStore&lt;TRole&gt;](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) interface defines the methods to implement in the role store class.</span></span> <span data-ttu-id="aeb42-262">만들기, 업데이트, 삭제 및 역할을 검색 하는 메서드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-262">It contains methods for creating, updating, deleting, and retrieving roles.</span></span>
* <span data-ttu-id="aeb42-263">**RoleStore&lt;TRole&gt;**</span><span class="sxs-lookup"><span data-stu-id="aeb42-263">**RoleStore&lt;TRole&gt;**</span></span>  
 <span data-ttu-id="aeb42-264">사용자 지정 하려면 `RoleStore`를 구현 하는 클래스를 만듭니다는 `IRoleStore<TRole>` 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-264">To customize `RoleStore`, create a class that implements the `IRoleStore<TRole>` interface.</span></span> 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a><span data-ttu-id="aeb42-265">새 저장소 공급자를 사용 하도록 앱을 다시 구성</span><span class="sxs-lookup"><span data-stu-id="aeb42-265">Reconfigure app to use a new storage provider</span></span>

<span data-ttu-id="aeb42-266">저장소 공급자를 구현한 경우에 앱 사용을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-266">Once you have implemented a storage provider, you configure your app to use it.</span></span> <span data-ttu-id="aeb42-267">앱의 기본 공급자를 사용 하는 경우 사용자 지정 공급자를 사용 하 여 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-267">If your app used the default provider, replace it with your custom provider.</span></span>

1. <span data-ttu-id="aeb42-268">제거 된 `Microsoft.AspNetCore.EntityFramework.Identity` NuGet 패키지.</span><span class="sxs-lookup"><span data-stu-id="aeb42-268">Remove the `Microsoft.AspNetCore.EntityFramework.Identity` NuGet package.</span></span>
1. <span data-ttu-id="aeb42-269">저장소 공급자는 별도 프로젝트 또는 패키지에 있는 경우 해당 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-269">If the storage provider resides in a separate project or package, add a reference to it.</span></span>
1. <span data-ttu-id="aeb42-270">에 대 한 모든 참조를 대체 `Microsoft.AspNetCore.EntityFramework.Identity` 을 사용 하 여 저장소 공급자의 네임 스페이스에 대 한 문입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-270">Replace all references to `Microsoft.AspNetCore.EntityFramework.Identity` with a using statement for the namespace of your storage provider.</span></span>
1. <span data-ttu-id="aeb42-271">에 `ConfigureServices` 메서드를 변경 합니다 `AddIdentity` 사용자 지정 형식을 사용 하는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-271">In the `ConfigureServices` method, change the `AddIdentity` method to use your custom types.</span></span> <span data-ttu-id="aeb42-272">이 목적을 위해 사용자 고유의 확장 메서드를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-272">You can create your own extension methods for this purpose.</span></span> <span data-ttu-id="aeb42-273">참조 [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) 예입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-273">See [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) for an example.</span></span>
1. <span data-ttu-id="aeb42-274">역할을 사용 하는 경우 업데이트를 `RoleManager` 사용 하 여 `RoleStore` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-274">If you are using Roles, update the `RoleManager` to use your `RoleStore` class.</span></span>
1. <span data-ttu-id="aeb42-275">앱의 구성에는 연결 문자열 및 자격 증명을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-275">Update the connection string and credentials to your app's configuration.</span></span>

<span data-ttu-id="aeb42-276">예제:</span><span class="sxs-lookup"><span data-stu-id="aeb42-276">Example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add identity types
    services.AddIdentity<ApplicationUser, ApplicationRole>()
        .AddDefaultTokenProviders();

    // Identity Services
    services.AddTransient<IUserStore<ApplicationUser>, CustomUserStore>();
    services.AddTransient<IRoleStore<ApplicationRole>, CustomRoleStore>();
    string connectionString = Configuration.GetConnectionString("DefaultConnection");
    services.AddTransient<SqlConnection>(e => new SqlConnection(connectionString));
    services.AddTransient<DapperUsersTable>();

    // additional configuration
}
```

## <a name="references"></a><span data-ttu-id="aeb42-277">참조</span><span class="sxs-lookup"><span data-stu-id="aeb42-277">References</span></span>

* [<span data-ttu-id="aeb42-278">ASP.NET 4.x Id에 대 한 사용자 지정 저장소 공급자</span><span class="sxs-lookup"><span data-stu-id="aeb42-278">Custom Storage Providers for ASP.NET 4.x Identity</span></span>](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* <span data-ttu-id="aeb42-279">[ASP.NET Core Id](https://github.com/aspnet/identity) &ndash; 이 리포지토리는 커뮤니티에서 저장소 공급자를 유지 관리에 대 한 링크를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="aeb42-279">[ASP.NET Core Identity](https://github.com/aspnet/identity) &ndash; This repository includes links to community maintained store providers.</span></span>
