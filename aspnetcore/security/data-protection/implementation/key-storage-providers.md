---
title: ASP.NET Core에서 키 저장소 공급자
author: rick-anderson
description: ASP.NET Core 및 키 저장소 위치를 구성 하는 방법에 대 한 키 저장소 공급자에 알아봅니다.
ms.author: riande
ms.date: 12/05/2019
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 219ebc471de32d15e4a43c938eef156c52e5f11e
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172587"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="25dcd-103">ASP.NET Core에서 키 저장소 공급자</span><span class="sxs-lookup"><span data-stu-id="25dcd-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="25dcd-104">데이터 보호 시스템은 [기본적으로 검색 메커니즘을 채택](xref:security/data-protection/configuration/default-settings) 하 여 암호화 키가 유지 되어야 하는 위치를 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="25dcd-105">개발자는 기본 검색 메커니즘을 무시 하 고 수동으로 위치를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="25dcd-106">명시적 키 지 속성 위치를 지정 하는 경우 데이터 보호 시스템 키는 더 이상 휴지 상태로 암호화 되므로 rest 메커니즘을 기본 키 암호화를 등록 취소 합니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="25dcd-107">프로덕션 배포를 위한 [명시적 키 암호화 메커니즘도 추가로 지정](xref:security/data-protection/implementation/key-encryption-at-rest) 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="25dcd-108">파일 시스템</span><span class="sxs-lookup"><span data-stu-id="25dcd-108">File system</span></span>

<span data-ttu-id="25dcd-109">파일 시스템 기반 키 리포지토리를 구성 하려면 아래와 같이 [Persistkeystofilesystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) 구성 루틴을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="25dcd-110">키를 저장 해야 하는 리포지토리를 가리키는 [system.io.directoryinfo](/dotnet/api/system.io.directoryinfo) 를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="25dcd-111">`DirectoryInfo`는 로컬 컴퓨터의 디렉터리를 가리키거나 네트워크 공유의 폴더를 가리킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="25dcd-112">로컬 컴퓨터의 디렉터리를 가리키는 경우 (그리고 로컬 컴퓨터의 앱만이 리포지토리를 사용 하기 위해 액세스 해야 하는 경우) [WINDOWS DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (windows)를 사용 하 여 미사용 키를 암호화 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="25dcd-113">그렇지 않으면 [x.509 인증서](xref:security/data-protection/implementation/key-encryption-at-rest) 를 사용 하 여 미사용 키를 암호화 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-storage"></a><span data-ttu-id="25dcd-114">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="25dcd-114">Azure Storage</span></span>

<span data-ttu-id="25dcd-115">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) 패키지를 사용 하 여 Azure Blob Storage에 데이터 보호 키를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-115">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) package allows storing data protection keys in Azure Blob Storage.</span></span> <span data-ttu-id="25dcd-116">웹 앱의 여러 인스턴스 키를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="25dcd-117">앱에는 여러 서버에서 인증 쿠키 또는 CSRF 보호 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-117">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

<span data-ttu-id="25dcd-118">Azure Blob Storage 공급자를 구성 하려면 [Persistkeystoazureblobstorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) 오버 로드 중 하나를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-118">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

<span data-ttu-id="25dcd-119">웹 앱이 Azure 서비스로 실행 되 고 있는 경우에는 인증 토큰을 [Microsoft. azure. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)을 사용 하 여 자동으로 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-119">If the web app is running as an Azure service, authentication tokens can be automatically created using [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span></span>

```csharp
var tokenProvider = new AzureServiceTokenProvider();
var token = await tokenProvider.GetAccessTokenAsync("https://storage.azure.com/");
var credentials = new StorageCredentials(new TokenCredential(token));
var storageAccount = new CloudStorageAccount(credentials, "mystorageaccount", "core.windows.net", useHttps: true);
var client = storageAccount.CreateCloudBlobClient();
var container = client.GetContainerReference("my-key-container");

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

<span data-ttu-id="25dcd-120">[서비스 간 인증 구성에 대 한 자세한 내용을](/azure/key-vault/service-to-service-authentication) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="25dcd-120">See [more details about configuring service-to-service authentication.](/azure/key-vault/service-to-service-authentication)</span></span>

## <a name="redis"></a><span data-ttu-id="25dcd-121">Redis</span><span class="sxs-lookup"><span data-stu-id="25dcd-121">Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="25dcd-122">[AspNetCore StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) 패키지를 사용 하면 Redis cache에 데이터 보호 키를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-122">The [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="25dcd-123">웹 앱의 여러 인스턴스 키를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-123">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="25dcd-124">앱에는 여러 서버에서 인증 쿠키 또는 CSRF 보호 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-124">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="25dcd-125">[AspNetCore Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) 패키지를 사용 하면 Redis cache에 데이터 보호 키를 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-125">The [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="25dcd-126">웹 앱의 여러 인스턴스 키를 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-126">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="25dcd-127">앱에는 여러 서버에서 인증 쿠키 또는 CSRF 보호 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-127">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="25dcd-128">Redis에서를 구성 하려면 [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) 오버 로드 중 하나를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-128">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="25dcd-129">Redis에서를 구성 하려면 [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) 오버 로드 중 하나를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-129">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="25dcd-130">자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="25dcd-130">For more information, see the following topics:</span></span>

* [<span data-ttu-id="25dcd-131">StackExchange. Redis ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="25dcd-131">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="25dcd-132">Azure Redis 캐시(영문)</span><span class="sxs-lookup"><span data-stu-id="25dcd-132">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="25dcd-133">ASP.NET Core DataProtection 샘플</span><span class="sxs-lookup"><span data-stu-id="25dcd-133">ASP.NET Core DataProtection samples</span></span>](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="25dcd-134">레지스트리</span><span class="sxs-lookup"><span data-stu-id="25dcd-134">Registry</span></span>

<span data-ttu-id="25dcd-135">**Windows 배포에만 적용 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="25dcd-135">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="25dcd-136">경우에 따라 앱 파일 시스템에 쓰기 액세스 하지 못할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-136">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="25dcd-137">앱이 가상 서비스 계정 (예: w3wp.exe의 응용 프로그램 풀 id *)으로 실행*되는 시나리오를 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="25dcd-137">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe*'s app pool identity).</span></span> <span data-ttu-id="25dcd-138">이러한 경우 관리자 서비스 계정 id에서 액세스할 수 있는 레지스트리 키를 프로 비전 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-138">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="25dcd-139">아래와 같이 [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) 확장 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-139">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="25dcd-140">암호화 키를 저장 해야 하는 위치를 가리키는 [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) 를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-140">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> <span data-ttu-id="25dcd-141">[WINDOWS DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) 를 사용 하 여 미사용 키를 암호화 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-141">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="25dcd-142">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="25dcd-142">Entity Framework Core</span></span>

<span data-ttu-id="25dcd-143">[AspNetCore microsoft.entityframeworkcore.tools.dotnet](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) 패키지는 Entity Framework Core을 사용 하 여 데이터베이스에 데이터 보호 키를 저장 하기 위한 메커니즘을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-143">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="25dcd-144">`Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet 패키지를 프로젝트 파일에 추가 해야 합니다. [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)의 일부가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-144">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="25dcd-145">이 패키지를 사용 하 여 키를 웹 앱의 여러 인스턴스에서 공유할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-145">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="25dcd-146">EF Core 공급자를 구성 하려면 [PersistKeysToDbContext\<TContext >](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-146">To configure the EF Core provider, call the [PersistKeysToDbContext\<TContext>](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="25dcd-147">제네릭 매개 변수 `TContext`는 [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) 에서 상속 하 고 [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext)를 구현 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-147">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and implement [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

<span data-ttu-id="25dcd-148">`DataProtectionKeys` 테이블을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-148">Create the `DataProtectionKeys` table.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="25dcd-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="25dcd-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="25dcd-150">**패키지 관리자 콘솔** (PMC) 창에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-150">Execute the following commands in the **Package Manager Console** (PMC) window:</span></span>

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="25dcd-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="25dcd-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="25dcd-152">명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-152">Execute the following commands in a command shell:</span></span>

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

<span data-ttu-id="25dcd-153">`MyKeysContext`는 위의 코드 샘플에 정의 된 `DbContext`입니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-153">`MyKeysContext` is the `DbContext` defined in the preceding code sample.</span></span> <span data-ttu-id="25dcd-154">다른 이름을 사용 하 여 `DbContext`를 사용 하는 경우 `MyKeysContext`의 `DbContext` 이름으로 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-154">If you're using a `DbContext` with a different name, substitute your `DbContext` name for `MyKeysContext`.</span></span>

<span data-ttu-id="25dcd-155">`DataProtectionKeys` 클래스/엔터티는 다음 표에 나와 있는 구조를 가집니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-155">The `DataProtectionKeys` class/entity adopts the structure shown in the following table.</span></span>

| <span data-ttu-id="25dcd-156">속성/필드</span><span class="sxs-lookup"><span data-stu-id="25dcd-156">Property/Field</span></span> | <span data-ttu-id="25dcd-157">CLR 형식</span><span class="sxs-lookup"><span data-stu-id="25dcd-157">CLR Type</span></span> | <span data-ttu-id="25dcd-158">SQL 형식</span><span class="sxs-lookup"><span data-stu-id="25dcd-158">SQL Type</span></span>              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | <span data-ttu-id="25dcd-159">`int`, PK, not null</span><span class="sxs-lookup"><span data-stu-id="25dcd-159">`int`, PK, not null</span></span>   |
| `FriendlyName` | `string` | <span data-ttu-id="25dcd-160">`nvarchar(MAX)`, null</span><span class="sxs-lookup"><span data-stu-id="25dcd-160">`nvarchar(MAX)`, null</span></span> |
| `Xml`          | `string` | <span data-ttu-id="25dcd-161">`nvarchar(MAX)`, null</span><span class="sxs-lookup"><span data-stu-id="25dcd-161">`nvarchar(MAX)`, null</span></span> |

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="25dcd-162">사용자 지정 키 저장소</span><span class="sxs-lookup"><span data-stu-id="25dcd-162">Custom key repository</span></span>

<span data-ttu-id="25dcd-163">기본 제공 메커니즘이 적절 하지 않은 경우 개발자는 사용자 지정 [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)을 제공 하 여 고유한 키 지 속성 메커니즘을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="25dcd-163">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
