---
title: ASP.NET Core Id에 대 한 사용자 지정 저장소 공급자
author: ardalis
description: ASP.NET Core Id에 대 한 사용자 지정 저장소 공급자를 구성 하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: 574e66e4dedaf0bfd01d600c3ded4bfb5d1865cd
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653871"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a><span data-ttu-id="254a1-103">ASP.NET Core Id에 대 한 사용자 지정 저장소 공급자</span><span class="sxs-lookup"><span data-stu-id="254a1-103">Custom storage providers for ASP.NET Core Identity</span></span>

<span data-ttu-id="254a1-104">작성자 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="254a1-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="254a1-105">Id ASP.NET Core은 사용자 지정 저장소 공급자를 만들어 앱에 연결할 수 있는 확장 가능한 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-105">ASP.NET Core Identity is an extensible system which enables you to create a custom storage provider and connect it to your app.</span></span> <span data-ttu-id="254a1-106">이 항목에서는 ASP.NET Core Id에 대 한 사용자 지정 저장소 공급자를 만드는 방법에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-106">This topic describes how to create a customized storage provider for ASP.NET Core Identity.</span></span> <span data-ttu-id="254a1-107">사용자 고유의 저장소 공급자를 만드는 데 중요 한 개념을 설명 하지만 단계별 연습은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-107">It covers the important concepts for creating your own storage provider, but isn't a step-by-step walkthrough.</span></span>

<span data-ttu-id="254a1-108">[GitHub에서 샘플을 보거나 다운로드합니다](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample).</span><span class="sxs-lookup"><span data-stu-id="254a1-108">[View or download sample from GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample).</span></span>

## <a name="introduction"></a><span data-ttu-id="254a1-109">소개</span><span class="sxs-lookup"><span data-stu-id="254a1-109">Introduction</span></span>

<span data-ttu-id="254a1-110">기본적으로 ASP.NET Core Id 시스템은 Entity Framework Core을 사용 하 여 SQL Server 데이터베이스에 사용자 정보를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-110">By default, the ASP.NET Core Identity system stores user information in a SQL Server database using Entity Framework Core.</span></span> <span data-ttu-id="254a1-111">이 접근 방식은 많은 앱에서 잘 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-111">For many apps, this approach works well.</span></span> <span data-ttu-id="254a1-112">그러나 다른 지 속성 메커니즘 또는 데이터 스키마를 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-112">However, you may prefer to use a different persistence mechanism or data schema.</span></span> <span data-ttu-id="254a1-113">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-113">For example:</span></span>

* <span data-ttu-id="254a1-114">[Azure Table Storage](/azure/storage/) 또는 다른 데이터 저장소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-114">You use [Azure Table Storage](/azure/storage/) or another data store.</span></span>
* <span data-ttu-id="254a1-115">데이터베이스 테이블의 구조가 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-115">Your database tables have a different structure.</span></span> 
* <span data-ttu-id="254a1-116">[Dapper](https://github.com/StackExchange/Dapper)와 같은 다른 데이터 액세스 방법을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-116">You may wish to use a different data access approach, such as [Dapper](https://github.com/StackExchange/Dapper).</span></span> 

<span data-ttu-id="254a1-117">이러한 각 경우에서 저장소 메커니즘에 대 한 사용자 지정 공급자를 작성 하 고 해당 공급자를 앱에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-117">In each of these cases, you can write a customized provider for your storage mechanism and plug that provider into your app.</span></span>

<span data-ttu-id="254a1-118">ASP.NET Core Id는 "개별 사용자 계정" 옵션을 사용 하 여 Visual Studio의 프로젝트 템플릿에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-118">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="254a1-119">.NET Core CLI 사용 하는 경우 `-au Individual`를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-119">When using the .NET Core CLI, add `-au Individual`:</span></span>

```dotnetcli
dotnet new mvc -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a><span data-ttu-id="254a1-120">ASP.NET Core Id 아키텍처</span><span class="sxs-lookup"><span data-stu-id="254a1-120">The ASP.NET Core Identity architecture</span></span>

<span data-ttu-id="254a1-121">ASP.NET Core Id는 관리자 및 저장소 라는 클래스로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-121">ASP.NET Core Identity consists of classes called managers and stores.</span></span> <span data-ttu-id="254a1-122">*관리자* 는 응용 프로그램 개발자가 id 사용자 만들기와 같은 작업을 수행 하는 데 사용 하는 상위 수준 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-122">*Managers* are high-level classes which an app developer uses to perform operations, such as creating an Identity user.</span></span> <span data-ttu-id="254a1-123">*저장소* 는 사용자 및 역할과 같은 엔터티를 유지 하는 방법을 지정 하는 하위 수준 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-123">*Stores* are lower-level classes that specify how entities, such as users and roles, are persisted.</span></span> <span data-ttu-id="254a1-124">저장소는 리포지토리 패턴을 따르고 지 속성 메커니즘과 긴밀 하 게 결합 됩니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-124">Stores follow the repository pattern and are closely coupled with the persistence mechanism.</span></span> <span data-ttu-id="254a1-125">관리자는 저장소에서 분리 됩니다. 즉, 구성을 제외 하 고 응용 프로그램 코드를 변경 하지 않고 지 속성 메커니즘을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-125">Managers are decoupled from stores, which means you can replace the persistence mechanism without changing your application code (except for configuration).</span></span>

<span data-ttu-id="254a1-126">다음 다이어그램은 웹 앱이 관리자와 상호 작용 하는 방법을 보여 주며, 저장소는 데이터 액세스 계층과 상호 작용 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-126">The following diagram shows how a web app interacts with the managers, while stores interact with the data access layer.</span></span>

![ASP.NET Core 앱은 관리자와 함께 작동 합니다 (예: ' UserManager ', ' RoleManager ').](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

<span data-ttu-id="254a1-129">사용자 지정 저장소 공급자를 만들려면 데이터 원본, 데이터 액세스 계층 및이 데이터 액세스 계층과 상호 작용 하는 저장소 클래스 (위 다이어그램의 녹색 및 회색 상자)를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-129">To create a custom storage provider, create the data source, the data access layer, and the store classes that interact with this data access layer (the green and grey boxes in the diagram above).</span></span> <span data-ttu-id="254a1-130">관리자 또는 관리자와 상호 작용 하는 앱 코드 (위의 파란색 상자)를 사용자 지정할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-130">You don't need to customize the managers or your app code that interacts with them (the blue boxes above).</span></span>

<span data-ttu-id="254a1-131">`UserManager` 또는 `RoleManager`의 새 인스턴스를 만들 때 사용자 클래스의 형식을 제공 하 고 저장소 클래스의 인스턴스를 인수로 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-131">When creating a new instance of `UserManager` or `RoleManager` you provide the type of the user class and pass an instance of the store class as an argument.</span></span> <span data-ttu-id="254a1-132">이 방법을 사용 하면 사용자 지정 클래스를 ASP.NET Core에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-132">This approach enables you to plug your customized classes into ASP.NET Core.</span></span> 

<span data-ttu-id="254a1-133">[새 저장소 공급자를 사용 하도록 앱 다시 구성](#reconfigure-app-to-use-a-new-storage-provider) 사용자 지정 저장소를 사용 하 여 `UserManager` 및 `RoleManager`를 인스턴스화하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-133">[Reconfigure app to use new storage provider](#reconfigure-app-to-use-a-new-storage-provider) shows how to instantiate `UserManager` and `RoleManager` with a customized store.</span></span>

## <a name="aspnet-core-identity-stores-data-types"></a><span data-ttu-id="254a1-134">ASP.NET Core Identity에서 데이터 형식 저장</span><span class="sxs-lookup"><span data-stu-id="254a1-134">ASP.NET Core Identity stores data types</span></span>

<span data-ttu-id="254a1-135">[ASP.NET Core Identity](https://github.com/aspnet/identity) 데이터 형식은 다음 섹션에 자세히 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-135">[ASP.NET Core Identity](https://github.com/aspnet/identity) data types are detailed in the following sections:</span></span>

### <a name="users"></a><span data-ttu-id="254a1-136">사용자</span><span class="sxs-lookup"><span data-stu-id="254a1-136">Users</span></span>

<span data-ttu-id="254a1-137">웹 사이트의 등록 된 사용자입니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-137">Registered users of your web site.</span></span> <span data-ttu-id="254a1-138">[IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) 형식은 사용자 지정 형식에 대 한 예제로 확장 되거나 사용 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-138">The [IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) type may be extended or used as an example for your own custom type.</span></span> <span data-ttu-id="254a1-139">사용자 고유의 사용자 지정 id 저장소 솔루션을 구현 하기 위해 특정 형식에서 상속할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-139">You don't need to inherit from a particular type to implement your own custom identity storage solution.</span></span>

### <a name="user-claims"></a><span data-ttu-id="254a1-140">사용자 클레임</span><span class="sxs-lookup"><span data-stu-id="254a1-140">User Claims</span></span>

<span data-ttu-id="254a1-141">사용자의 id를 나타내는 사용자에 대 한 문 (또는 [클레임](/dotnet/api/system.security.claims.claim)) 집합입니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-141">A set of statements (or [Claims](/dotnet/api/system.security.claims.claim)) about the user that represent the user's identity.</span></span> <span data-ttu-id="254a1-142">에서는 역할을 통해 달성할 수 있는 것 보다 더 큰 사용자 id 식을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-142">Can enable greater expression of the user's identity than can be achieved through roles.</span></span>

### <a name="user-logins"></a><span data-ttu-id="254a1-143">사용자 로그인</span><span class="sxs-lookup"><span data-stu-id="254a1-143">User Logins</span></span>

<span data-ttu-id="254a1-144">사용자에 게 로그인 할 때 사용할 외부 인증 공급자 (예: Facebook 또는 Microsoft 계정)에 대 한 정보입니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-144">Information about the external authentication provider (like Facebook or a Microsoft account) to use when logging in a user.</span></span> [<span data-ttu-id="254a1-145">예제</span><span class="sxs-lookup"><span data-stu-id="254a1-145">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a><span data-ttu-id="254a1-146">역할</span><span class="sxs-lookup"><span data-stu-id="254a1-146">Roles</span></span>

<span data-ttu-id="254a1-147">사이트에 대 한 권한 부여 그룹.</span><span class="sxs-lookup"><span data-stu-id="254a1-147">Authorization groups for your site.</span></span> <span data-ttu-id="254a1-148">역할 Id와 역할 이름 (예: "Admin" 또는 "Employee")을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-148">Includes the role Id and role name (like "Admin" or "Employee").</span></span> [<span data-ttu-id="254a1-149">예제</span><span class="sxs-lookup"><span data-stu-id="254a1-149">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a><span data-ttu-id="254a1-150">데이터 액세스 계층</span><span class="sxs-lookup"><span data-stu-id="254a1-150">The data access layer</span></span>

<span data-ttu-id="254a1-151">이 항목에서는 사용자가 사용 하려는 지 속성 메커니즘 및 해당 메커니즘에 대 한 엔터티를 만드는 방법을 잘 알고 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-151">This topic assumes you are familiar with the persistence mechanism that you are going to use and how to create entities for that mechanism.</span></span> <span data-ttu-id="254a1-152">이 항목에서는 리포지토리 또는 데이터 액세스 클래스를 만드는 방법에 대 한 세부 정보를 제공 하지 않습니다. ASP.NET Core Id로 작업할 때 디자인 결정에 대 한 몇 가지 제안을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-152">This topic doesn't provide details about how to create the repositories or data access classes; it provides some suggestions about design decisions when working with ASP.NET Core Identity.</span></span>

<span data-ttu-id="254a1-153">사용자 지정 저장소 공급자에 대 한 데이터 액세스 계층을 디자인할 때 자유롭게 자유롭게 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-153">You have a lot of freedom when designing the data access layer for a customized store provider.</span></span> <span data-ttu-id="254a1-154">앱에서 사용 하려는 기능에 대 한 지 속성 메커니즘도 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-154">You only need to create persistence mechanisms for features that you intend to use in your app.</span></span> <span data-ttu-id="254a1-155">예를 들어 앱에서 역할을 사용 하지 않는 경우 역할 또는 사용자 역할 연결에 대 한 저장소를 만들 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-155">For example, if you are not using roles in your app, you don't need to create storage for roles or user role associations.</span></span> <span data-ttu-id="254a1-156">기술 및 기존 인프라에는 ASP.NET Core Id의 기본 구현과 매우 다른 구조가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-156">Your technology and existing infrastructure may require a structure that's very different from the default implementation of ASP.NET Core Identity.</span></span> <span data-ttu-id="254a1-157">데이터 액세스 계층에서 저장소 구현의 구조를 사용 하는 논리를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-157">In your data access layer, you provide the logic to work with the structure of your storage implementation.</span></span>

<span data-ttu-id="254a1-158">데이터 액세스 계층은 ASP.NET Core Id에서 데이터 원본으로 데이터를 저장 하는 논리를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-158">The data access layer provides the logic to save the data from ASP.NET Core Identity to a data source.</span></span> <span data-ttu-id="254a1-159">사용자 지정 저장소 공급자에 대 한 데이터 액세스 계층에는 사용자 및 역할 정보를 저장 하는 다음과 같은 클래스가 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-159">The data access layer for your customized storage provider might include the following classes to store user and role information.</span></span>

### <a name="context-class"></a><span data-ttu-id="254a1-160">Context 클래스</span><span class="sxs-lookup"><span data-stu-id="254a1-160">Context class</span></span>

<span data-ttu-id="254a1-161">정보를 캡슐화 하 여 지 속성 메커니즘에 연결 하 고 쿼리를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-161">Encapsulates the information to connect to your persistence mechanism and execute queries.</span></span> <span data-ttu-id="254a1-162">여러 데이터 클래스에는이 클래스의 인스턴스가 필요 합니다. 일반적으로 종속성 주입을 통해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-162">Several data classes require an instance of this class, typically provided through dependency injection.</span></span> <span data-ttu-id="254a1-163">[예제](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).</span><span class="sxs-lookup"><span data-stu-id="254a1-163">[Example](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).</span></span>

### <a name="user-storage"></a><span data-ttu-id="254a1-164">사용자 저장소</span><span class="sxs-lookup"><span data-stu-id="254a1-164">User Storage</span></span>

<span data-ttu-id="254a1-165">사용자 정보 (예: 사용자 이름 및 암호 해시)를 저장 하 고 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-165">Stores and retrieves user information (such as user name and password hash).</span></span> [<span data-ttu-id="254a1-166">예제</span><span class="sxs-lookup"><span data-stu-id="254a1-166">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a><span data-ttu-id="254a1-167">역할 저장소</span><span class="sxs-lookup"><span data-stu-id="254a1-167">Role Storage</span></span>

<span data-ttu-id="254a1-168">역할 이름 등의 역할 정보를 저장 하 고 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-168">Stores and retrieves role information (such as the role name).</span></span> [<span data-ttu-id="254a1-169">예제</span><span class="sxs-lookup"><span data-stu-id="254a1-169">Example</span></span>](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a><span data-ttu-id="254a1-170">UserClaims 저장소</span><span class="sxs-lookup"><span data-stu-id="254a1-170">UserClaims Storage</span></span>

<span data-ttu-id="254a1-171">사용자 클레임 정보 (예: 클레임 유형 및 값)를 저장 하 고 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-171">Stores and retrieves user claim information (such as the claim type and value).</span></span> [<span data-ttu-id="254a1-172">예제</span><span class="sxs-lookup"><span data-stu-id="254a1-172">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a><span data-ttu-id="254a1-173">UserLogins 저장소</span><span class="sxs-lookup"><span data-stu-id="254a1-173">UserLogins Storage</span></span>

<span data-ttu-id="254a1-174">사용자 로그인 정보 (예: 외부 인증 공급자)를 저장 하 고 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-174">Stores and retrieves user login information (such as an external authentication provider).</span></span> [<span data-ttu-id="254a1-175">예제</span><span class="sxs-lookup"><span data-stu-id="254a1-175">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a><span data-ttu-id="254a1-176">UserRole 저장소</span><span class="sxs-lookup"><span data-stu-id="254a1-176">UserRole Storage</span></span>

<span data-ttu-id="254a1-177">사용자에 게 할당 된 역할을 저장 하 고 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-177">Stores and retrieves which roles are assigned to which users.</span></span> [<span data-ttu-id="254a1-178">예제</span><span class="sxs-lookup"><span data-stu-id="254a1-178">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

<span data-ttu-id="254a1-179">**팁:** 앱에서 사용 하려는 클래스만 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-179">**TIP:** Only implement the classes you intend to use in your app.</span></span>

<span data-ttu-id="254a1-180">데이터 액세스 클래스에서 지 속성 메커니즘에 대 한 데이터 작업을 수행 하는 코드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-180">In the data access classes, provide code to perform data operations for your persistence mechanism.</span></span> <span data-ttu-id="254a1-181">예를 들어 사용자 지정 공급자 내에서 *store* 클래스에 새 사용자를 만드는 다음 코드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-181">For example, within a custom provider, you might have the following code to create a new user in the *store* class:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

<span data-ttu-id="254a1-182">사용자를 만들기 위한 구현 논리는 아래와 같이 `_usersTable.CreateAsync` 메서드에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-182">The implementation logic for creating the user is in the `_usersTable.CreateAsync` method, shown below.</span></span>

## <a name="customize-the-user-class"></a><span data-ttu-id="254a1-183">사용자 클래스 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="254a1-183">Customize the user class</span></span>

<span data-ttu-id="254a1-184">저장소 공급자를 구현 하는 경우 [IdentityUser 클래스](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)에 해당 하는 사용자 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-184">When implementing a storage provider, create a user class which is equivalent to the [IdentityUser class](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser).</span></span>

<span data-ttu-id="254a1-185">사용자 클래스에는 최소한 `Id`와 `UserName` 속성이 포함 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-185">At a minimum, your user class must include an `Id` and a `UserName` property.</span></span>

<span data-ttu-id="254a1-186">`IdentityUser` 클래스는 요청 된 작업을 수행할 때 `UserManager`에서 호출 하는 속성을 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-186">The `IdentityUser` class defines the properties that the `UserManager` calls when performing requested operations.</span></span> <span data-ttu-id="254a1-187">`Id` 속성의 기본 형식은 문자열 이지만 `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>`에서 상속 하 고 다른 형식을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-187">The default type of the `Id` property is a string, but you can inherit from `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` and specify a different type.</span></span> <span data-ttu-id="254a1-188">프레임 워크는 저장소 구현이 데이터 형식 변환을 처리 하는 것으로 예상 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-188">The framework expects the storage implementation to handle data type conversions.</span></span>

## <a name="customize-the-user-store"></a><span data-ttu-id="254a1-189">사용자 저장소 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="254a1-189">Customize the user store</span></span>

<span data-ttu-id="254a1-190">사용자에 대 한 모든 데이터 작업에 대해 메서드를 제공 하는 `UserStore` 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-190">Create a `UserStore` class that provides the methods for all data operations on the user.</span></span> <span data-ttu-id="254a1-191">이 클래스는 [Userstore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) 클래스와 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-191">This class is equivalent to the [UserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) class.</span></span> <span data-ttu-id="254a1-192">`UserStore` 클래스에서 필요한 `IUserStore<TUser>` 및 선택적 인터페이스를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-192">In your `UserStore` class, implement `IUserStore<TUser>` and the optional interfaces required.</span></span> <span data-ttu-id="254a1-193">앱에서 제공 하는 기능에 따라 구현할 선택적 인터페이스를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-193">You select which optional interfaces to implement based on the functionality provided in your app.</span></span>

### <a name="optional-interfaces"></a><span data-ttu-id="254a1-194">선택적 인터페이스</span><span class="sxs-lookup"><span data-stu-id="254a1-194">Optional interfaces</span></span>

* [<span data-ttu-id="254a1-195">IUserRoleStore</span><span class="sxs-lookup"><span data-stu-id="254a1-195">IUserRoleStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
* [<span data-ttu-id="254a1-196">IUserClaimStore</span><span class="sxs-lookup"><span data-stu-id="254a1-196">IUserClaimStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
* [<span data-ttu-id="254a1-197">IUserPasswordStore</span><span class="sxs-lookup"><span data-stu-id="254a1-197">IUserPasswordStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
* [<span data-ttu-id="254a1-198">IUserSecurityStampStore</span><span class="sxs-lookup"><span data-stu-id="254a1-198">IUserSecurityStampStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
* [<span data-ttu-id="254a1-199">IUserEmailStore</span><span class="sxs-lookup"><span data-stu-id="254a1-199">IUserEmailStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
* [<span data-ttu-id="254a1-200">IUserPhoneNumberStore</span><span class="sxs-lookup"><span data-stu-id="254a1-200">IUserPhoneNumberStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
* [<span data-ttu-id="254a1-201">IQueryableUserStore</span><span class="sxs-lookup"><span data-stu-id="254a1-201">IQueryableUserStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
* [<span data-ttu-id="254a1-202">IUserLoginStore</span><span class="sxs-lookup"><span data-stu-id="254a1-202">IUserLoginStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
* [<span data-ttu-id="254a1-203">IUserTwoFactorStore</span><span class="sxs-lookup"><span data-stu-id="254a1-203">IUserTwoFactorStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
* [<span data-ttu-id="254a1-204">IUserLockoutStore</span><span class="sxs-lookup"><span data-stu-id="254a1-204">IUserLockoutStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)

<span data-ttu-id="254a1-205">선택적 인터페이스는 `IUserStore<TUser>`에서 상속 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-205">The optional interfaces inherit from `IUserStore<TUser>`.</span></span> <span data-ttu-id="254a1-206">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs)에서 부분적으로 구현 된 샘플 사용자 저장소를 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-206">You can see a partially implemented sample user store in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs).</span></span>

<span data-ttu-id="254a1-207">`UserStore` 클래스 내에서 사용자가 만든 데이터 액세스 클래스를 사용 하 여 작업을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-207">Within the `UserStore` class, you use the data access classes that you created to perform operations.</span></span> <span data-ttu-id="254a1-208">이러한는 종속성 주입을 사용 하 여 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-208">These are passed in using dependency injection.</span></span> <span data-ttu-id="254a1-209">예를 들어 Dapper 구현이 포함 된 SQL Server에서 `UserStore` 클래스에는 `DapperUsersTable`의 인스턴스를 사용 하 여 새 레코드를 삽입 하는 `CreateAsync` 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-209">For example, in the SQL Server with Dapper implementation, the `UserStore` class has the `CreateAsync` method which uses an instance of `DapperUsersTable` to insert a new record:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a><span data-ttu-id="254a1-210">사용자 저장소를 사용자 지정할 때 구현할 인터페이스</span><span class="sxs-lookup"><span data-stu-id="254a1-210">Interfaces to implement when customizing user store</span></span>

* <span data-ttu-id="254a1-211">**IUserStore**</span><span class="sxs-lookup"><span data-stu-id="254a1-211">**IUserStore**</span></span>  
 <span data-ttu-id="254a1-212">[Iuserstore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) 인터페이스만 사용자 저장소에서 구현 해야 하는 유일한 인터페이스입니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-212">The [IUserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) interface is the only interface you must implement in the user store.</span></span> <span data-ttu-id="254a1-213">사용자를 만들고, 업데이트 하 고, 삭제 하 고, 검색 하는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-213">It defines methods for creating, updating, deleting, and retrieving users.</span></span>
* <span data-ttu-id="254a1-214">**IUserClaimStore**</span><span class="sxs-lookup"><span data-stu-id="254a1-214">**IUserClaimStore**</span></span>  
 <span data-ttu-id="254a1-215">[Iuserclaimstore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) 인터페이스는 사용자 클레임을 사용 하기 위해 구현 하는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-215">The [IUserClaimStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) interface defines the methods you implement to enable user claims.</span></span> <span data-ttu-id="254a1-216">여기에는 사용자 클레임을 추가, 제거 및 검색 하는 메서드가 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-216">It contains methods for adding, removing and retrieving user claims.</span></span>
* <span data-ttu-id="254a1-217">**IUserLoginStore**</span><span class="sxs-lookup"><span data-stu-id="254a1-217">**IUserLoginStore**</span></span>  
 <span data-ttu-id="254a1-218">[IUserLoginStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) 는 외부 인증 공급자를 사용 하도록 구현 하는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-218">The [IUserLoginStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) defines the methods you implement to enable external authentication providers.</span></span> <span data-ttu-id="254a1-219">사용자 로그인을 추가, 제거 및 검색 하는 메서드와 로그인 정보를 기반으로 사용자를 검색 하는 방법이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-219">It contains methods for adding, removing and retrieving user logins, and a method for retrieving a user based on the login information.</span></span>
* <span data-ttu-id="254a1-220">**IUserRoleStore**</span><span class="sxs-lookup"><span data-stu-id="254a1-220">**IUserRoleStore**</span></span>  
 <span data-ttu-id="254a1-221">[Iuserrolestore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) 인터페이스는 사용자를 역할에 매핑하기 위해 구현 하는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-221">The [IUserRoleStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) interface defines the methods you implement to map a user to a role.</span></span> <span data-ttu-id="254a1-222">사용자의 역할을 추가, 제거 및 검색 하는 메서드와 사용자가 역할에 할당 되었는지 여부를 확인 하는 메서드가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-222">It contains methods to add, remove, and retrieve a user's roles, and a method to check if a user is assigned to a role.</span></span>
* <span data-ttu-id="254a1-223">**IUserPasswordStore**</span><span class="sxs-lookup"><span data-stu-id="254a1-223">**IUserPasswordStore**</span></span>  
 <span data-ttu-id="254a1-224">[Iuserpasswordstore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) 인터페이스는 해시 된 암호를 유지 하기 위해 구현 하는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-224">The [IUserPasswordStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) interface defines the methods you implement to persist hashed passwords.</span></span> <span data-ttu-id="254a1-225">해시 된 암호를 가져오고 설정 하는 메서드와 사용자가 암호를 설정 했는지 여부를 나타내는 메서드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-225">It contains methods for getting and setting the hashed password, and a method that indicates whether the user has set a password.</span></span>
* <span data-ttu-id="254a1-226">**IUserSecurityStampStore**</span><span class="sxs-lookup"><span data-stu-id="254a1-226">**IUserSecurityStampStore**</span></span>  
 <span data-ttu-id="254a1-227">[IUserSecurityStampStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) 인터페이스는 사용자의 계정 정보가 변경 되었는지 여부를 나타내는 보안 스탬프를 사용 하기 위해 구현 하는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-227">The [IUserSecurityStampStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) interface defines the methods you implement to use a security stamp for indicating whether the user's account information has changed.</span></span> <span data-ttu-id="254a1-228">이 스탬프는 사용자가 암호를 변경 하거나 로그인을 추가 하거나 제거 하는 경우 업데이트 됩니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-228">This stamp is updated when a user changes the password, or adds or removes logins.</span></span> <span data-ttu-id="254a1-229">보안 스탬프를 가져오고 설정 하기 위한 메서드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-229">It contains methods for getting and setting the security stamp.</span></span>
* <span data-ttu-id="254a1-230">**IUserTwoFactorStore**</span><span class="sxs-lookup"><span data-stu-id="254a1-230">**IUserTwoFactorStore**</span></span>  
 <span data-ttu-id="254a1-231">[IUserTwoFactorStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) 인터페이스는 2 단계 인증을 지원 하기 위해 구현 하는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-231">The [IUserTwoFactorStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) interface defines the methods you implement to support two factor authentication.</span></span> <span data-ttu-id="254a1-232">사용자에 대해 2 단계 인증을 사용할 수 있는지 여부를 가져오고 설정 하는 메서드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-232">It contains methods for getting and setting whether two factor authentication is enabled for a user.</span></span>
* <span data-ttu-id="254a1-233">**IUserPhoneNumberStore**</span><span class="sxs-lookup"><span data-stu-id="254a1-233">**IUserPhoneNumberStore**</span></span>  
 <span data-ttu-id="254a1-234">[IUserPhoneNumberStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) 인터페이스는 사용자 전화 번호를 저장 하기 위해 구현 하는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-234">The [IUserPhoneNumberStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) interface defines the methods you implement to store user phone numbers.</span></span> <span data-ttu-id="254a1-235">전화 번호를 가져오고 설정 하는 방법 및 전화 번호가 확인 되었는지 여부를 나타내는 메서드가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-235">It contains methods for getting and setting the phone number and whether the phone number is confirmed.</span></span>
* <span data-ttu-id="254a1-236">**IUserEmailStore**</span><span class="sxs-lookup"><span data-stu-id="254a1-236">**IUserEmailStore**</span></span>  
 <span data-ttu-id="254a1-237">[Iuseremailstore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) 인터페이스는 사용자 전자 메일 주소를 저장 하기 위해 구현 하는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-237">The [IUserEmailStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) interface defines the methods you implement to store user email addresses.</span></span> <span data-ttu-id="254a1-238">전자 메일 주소를 가져오고 설정 하는 방법과 전자 메일이 확인 되었는지 여부를 나타내는 메서드가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-238">It contains methods for getting and setting the email address and whether the email is confirmed.</span></span>
* <span data-ttu-id="254a1-239">**IUserLockoutStore**</span><span class="sxs-lookup"><span data-stu-id="254a1-239">**IUserLockoutStore**</span></span>  
 <span data-ttu-id="254a1-240">[IUserLockoutStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) 인터페이스는 계정 잠금에 대 한 정보를 저장 하기 위해 구현 하는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-240">The [IUserLockoutStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) interface defines the methods you implement to store information about locking an account.</span></span> <span data-ttu-id="254a1-241">실패 한 액세스 시도 및 잠금을 추적 하는 메서드가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-241">It contains methods for tracking failed access attempts and lockouts.</span></span>
* <span data-ttu-id="254a1-242">**IQueryableUserStore**</span><span class="sxs-lookup"><span data-stu-id="254a1-242">**IQueryableUserStore**</span></span>  
 <span data-ttu-id="254a1-243">[Iqueryableuserstore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) 인터페이스는 쿼리 가능한 사용자 저장소를 제공 하기 위해 구현 하는 멤버를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-243">The [IQueryableUserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) interface defines the members you implement to provide a queryable user store.</span></span>

<span data-ttu-id="254a1-244">앱에 필요한 인터페이스만 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-244">You implement only the interfaces that are needed in your app.</span></span> <span data-ttu-id="254a1-245">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-245">For example:</span></span>

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

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a><span data-ttu-id="254a1-246">IdentityUserClaim, IdentityUserLogin 및 IdentityUserRole</span><span class="sxs-lookup"><span data-stu-id="254a1-246">IdentityUserClaim, IdentityUserLogin, and IdentityUserRole</span></span>

<span data-ttu-id="254a1-247">`Microsoft.AspNet.Identity.EntityFramework` 네임 스페이스에는 [IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)및 [IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) 클래스의 구현이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-247">The `Microsoft.AspNet.Identity.EntityFramework` namespace contains implementations of the [IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin), and [IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) classes.</span></span> <span data-ttu-id="254a1-248">이러한 기능을 사용 하는 경우 이러한 클래스의 고유한 버전을 만들고 앱에 대 한 속성을 정의 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-248">If you are using these features, you may want to create your own versions of these classes and define the properties for your app.</span></span> <span data-ttu-id="254a1-249">그러나 기본 작업 (예: 사용자의 클레임 추가 또는 제거)을 수행할 때 이러한 엔터티를 메모리로 로드 하지 않는 것이 더 효율적일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-249">However, sometimes it's more efficient to not load these entities into memory when performing basic operations (such as adding or removing a user's claim).</span></span> <span data-ttu-id="254a1-250">대신 백엔드 저장소 클래스는 데이터 소스에서 이러한 작업을 직접 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-250">Instead, the backend store classes can execute these operations directly on the data source.</span></span> <span data-ttu-id="254a1-251">예를 들어 `UserStore.GetClaimsAsync` 메서드는 `userClaimTable.FindByUserId(user.Id)` 메서드를 호출 하 여 해당 테이블에 대 한 쿼리를 직접 실행 하 고 클레임 목록을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-251">For example, the `UserStore.GetClaimsAsync` method can call the `userClaimTable.FindByUserId(user.Id)` method to execute a query on that table directly and return a list of claims.</span></span>

## <a name="customize-the-role-class"></a><span data-ttu-id="254a1-252">Role 클래스 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="254a1-252">Customize the role class</span></span>

<span data-ttu-id="254a1-253">역할 저장소 공급자를 구현 하는 경우 사용자 지정 역할 유형을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-253">When implementing a role storage provider, you can create a custom role type.</span></span> <span data-ttu-id="254a1-254">특정 인터페이스를 구현 하지 않아도 되지만 `Id` 있어야 하 고 일반적으로 `Name` 속성을 갖게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-254">It need not implement a particular interface, but it must have an `Id` and typically it will have a `Name` property.</span></span>

<span data-ttu-id="254a1-255">예제 역할 클래스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-255">The following is an example role class:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a><span data-ttu-id="254a1-256">역할 저장소 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="254a1-256">Customize the role store</span></span>

<span data-ttu-id="254a1-257">역할에 대 한 모든 데이터 작업에 대해 메서드를 제공 하는 `RoleStore` 클래스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-257">You can create a `RoleStore` class that provides the methods for all data operations on roles.</span></span> <span data-ttu-id="254a1-258">이 클래스는 [Rolestore&lt;TRole&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) 클래스와 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-258">This class is equivalent to the [RoleStore&lt;TRole&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) class.</span></span> <span data-ttu-id="254a1-259">`RoleStore` 클래스에서 `IRoleStore<TRole>`를 구현 하 고 선택적으로 `IQueryableRoleStore<TRole>` 인터페이스를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-259">In the `RoleStore` class, you implement the `IRoleStore<TRole>` and optionally the `IQueryableRoleStore<TRole>` interface.</span></span>

* <span data-ttu-id="254a1-260">**IRoleStore&lt;TRole&gt;**</span><span class="sxs-lookup"><span data-stu-id="254a1-260">**IRoleStore&lt;TRole&gt;**</span></span>  
 <span data-ttu-id="254a1-261">[Irolestore&lt;TRole&gt;](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) 인터페이스는 역할 저장소 클래스에서 구현 하는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-261">The [IRoleStore&lt;TRole&gt;](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) interface defines the methods to implement in the role store class.</span></span> <span data-ttu-id="254a1-262">역할을 만들고, 업데이트 하 고, 삭제 하 고, 검색 하는 메서드가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-262">It contains methods for creating, updating, deleting, and retrieving roles.</span></span>
* <span data-ttu-id="254a1-263">**RoleStore&lt;TRole&gt;**</span><span class="sxs-lookup"><span data-stu-id="254a1-263">**RoleStore&lt;TRole&gt;**</span></span>  
 <span data-ttu-id="254a1-264">`RoleStore`를 사용자 지정 하려면 `IRoleStore<TRole>` 인터페이스를 구현 하는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-264">To customize `RoleStore`, create a class that implements the `IRoleStore<TRole>` interface.</span></span> 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a><span data-ttu-id="254a1-265">새 저장소 공급자를 사용 하도록 앱 다시 구성</span><span class="sxs-lookup"><span data-stu-id="254a1-265">Reconfigure app to use a new storage provider</span></span>

<span data-ttu-id="254a1-266">저장소 공급자를 구현한 후에는 해당 공급자를 사용 하도록 앱을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-266">Once you have implemented a storage provider, you configure your app to use it.</span></span> <span data-ttu-id="254a1-267">앱이 기본 공급자를 사용 하는 경우 사용자 지정 공급자로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-267">If your app used the default provider, replace it with your custom provider.</span></span>

1. <span data-ttu-id="254a1-268">`Microsoft.AspNetCore.EntityFramework.Identity` NuGet 패키지를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-268">Remove the `Microsoft.AspNetCore.EntityFramework.Identity` NuGet package.</span></span>
1. <span data-ttu-id="254a1-269">저장소 공급자가 별도의 프로젝트 또는 패키지에 있는 경우이에 대 한 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-269">If the storage provider resides in a separate project or package, add a reference to it.</span></span>
1. <span data-ttu-id="254a1-270">`Microsoft.AspNetCore.EntityFramework.Identity`에 대 한 모든 참조를 저장소 공급자의 네임 스페이스에 대 한 using 문으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-270">Replace all references to `Microsoft.AspNetCore.EntityFramework.Identity` with a using statement for the namespace of your storage provider.</span></span>
1. <span data-ttu-id="254a1-271">`ConfigureServices` 메서드에서 사용자 지정 형식을 사용 하도록 `AddIdentity` 메서드를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-271">In the `ConfigureServices` method, change the `AddIdentity` method to use your custom types.</span></span> <span data-ttu-id="254a1-272">이러한 목적을 위해 사용자 고유의 확장 메서드를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-272">You can create your own extension methods for this purpose.</span></span> <span data-ttu-id="254a1-273">예는 [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="254a1-273">See [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) for an example.</span></span>
1. <span data-ttu-id="254a1-274">역할을 사용 하는 경우 `RoleStore` 클래스를 사용 하도록 `RoleManager`를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-274">If you are using Roles, update the `RoleManager` to use your `RoleStore` class.</span></span>
1. <span data-ttu-id="254a1-275">응용 프로그램의 구성에 대 한 연결 문자열 및 자격 증명을 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-275">Update the connection string and credentials to your app's configuration.</span></span>

<span data-ttu-id="254a1-276">예제:</span><span class="sxs-lookup"><span data-stu-id="254a1-276">Example:</span></span>

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

## <a name="references"></a><span data-ttu-id="254a1-277">참조</span><span class="sxs-lookup"><span data-stu-id="254a1-277">References</span></span>

* [<span data-ttu-id="254a1-278">ASP.NET 4.x Id의 사용자 지정 저장소 공급자</span><span class="sxs-lookup"><span data-stu-id="254a1-278">Custom Storage Providers for ASP.NET 4.x Identity</span></span>](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* <span data-ttu-id="254a1-279">[ASP.NET Core id](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) &ndash;이 리포지토리에는 커뮤니티에서 유지 관리 하는 저장소 공급자에 대 한 링크가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="254a1-279">[ASP.NET Core Identity](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) &ndash; This repository includes links to community maintained store providers.</span></span>
