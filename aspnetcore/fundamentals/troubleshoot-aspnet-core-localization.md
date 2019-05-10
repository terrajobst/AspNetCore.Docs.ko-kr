---
title: ASP.NET Core 지역화 문제 해결
author: hishamco
description: ASP.NET Core 앱에서 지역화 관련 문제를 진단하는 방법을 알아봅니다.
ms.author: riande
ms.date: 01/24/2019
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: c76732c1a0389818f8f9efae8fe384ca0f9ca308
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087373"
---
# <a name="troubleshoot-aspnet-core-localization"></a><span data-ttu-id="83d57-103">ASP.NET Core 지역화 문제 해결</span><span class="sxs-lookup"><span data-stu-id="83d57-103">Troubleshoot ASP.NET Core Localization</span></span>

<span data-ttu-id="83d57-104">작성자: [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="83d57-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="83d57-105">이 문서에서는 ASP.NET Core 앱 지역화 문제를 진단하는 방법에 대한 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-105">This article provides instructions on how to diagnose ASP.NET Core app localization issues.</span></span>

## <a name="localization-configuration-issues"></a><span data-ttu-id="83d57-106">지역화 구성 문제</span><span class="sxs-lookup"><span data-stu-id="83d57-106">Localization configuration issues</span></span>

<span data-ttu-id="83d57-107">**지역화 미들웨어 순서**</span><span class="sxs-lookup"><span data-stu-id="83d57-107">**Localization middleware order**</span></span>  
<span data-ttu-id="83d57-108">지역화 미들웨어가 예상대로 정렬되지 않으므로 앱이 지역화되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-108">The app may not localize because the localization middleware isn't ordered as expected.</span></span>

<span data-ttu-id="83d57-109">이 문제를 해결하려면 해당 지역화 미들웨어가 MVC 미들웨어 전에 등록되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-109">To resolve this issue, ensure that localization middleware is registered before MVC middleware.</span></span> <span data-ttu-id="83d57-110">그렇지 않으면 지역화 미들웨어는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-110">Otherwise, the localization middleware isn't applied.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

<span data-ttu-id="83d57-111">**지역화 리소스 경로가 없음**</span><span class="sxs-lookup"><span data-stu-id="83d57-111">**Localization resources path not found**</span></span>

<span data-ttu-id="83d57-112">**RequestCultureProvider에서 지원되는 문화권이 등록된 문화권과 한 번 일치하지 않음**</span><span class="sxs-lookup"><span data-stu-id="83d57-112">**Supported Cultures in RequestCultureProvider don't match with registered once**</span></span>  

## <a name="resource-file-naming-issues"></a><span data-ttu-id="83d57-113">리소스 파일 이름 지정 문제</span><span class="sxs-lookup"><span data-stu-id="83d57-113">Resource file naming issues</span></span>

<span data-ttu-id="83d57-114">ASP.NET Core에는 지역화 리소스 파일 이름 지정에 대해 미리 정의된 규칙 지침이 있고 [여기](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)에서 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-114">ASP.NET Core has predefined rules and guidelines for localization resources file naming, which are described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming).</span></span>

## <a name="missing-resources"></a><span data-ttu-id="83d57-115">누락된 리소스</span><span class="sxs-lookup"><span data-stu-id="83d57-115">Missing resources</span></span>

<span data-ttu-id="83d57-116">리소스를 찾을 수 없는 일반적인 원인은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-116">Common causes of resources not being found include:</span></span>

- <span data-ttu-id="83d57-117">`resx` 파일 또는 로컬라이저 요청에서 리소스 이름의 철자가 잘못되었습니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-117">Resource names are misspelled in either the `resx` file or the localizer request.</span></span>
- <span data-ttu-id="83d57-118">리소스가 일부 언어의 경우 `resx`에서 누락되었지만 다른 언어에서 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-118">The resource is missing from the `resx` for some languages, but exists in others.</span></span>
- <span data-ttu-id="83d57-119">여전히 문제가 있는 경우 누락된 리소스에 대한 자세한 내용은 `Debug` 로그 수준의 지역화 로그 메시지를 확인하세요.</span><span class="sxs-lookup"><span data-stu-id="83d57-119">If you're still having trouble, check the localization log messages (which are at `Debug` log level) for more details about the missing resources.</span></span>

<span data-ttu-id="83d57-120">_**힌트:** `CookieRequestCultureProvider`를 사용하는 경우 지역화 쿠키 값 안의 문화권에서 작은따옴표를 사용하지 않았는지 확인합니다. 예를 들어 `c='en-UK'|uic='en-US'`가 잘못된 쿠키 값인 반면, `c=en-UK|uic=en-US`는 유효합니다._</span><span class="sxs-lookup"><span data-stu-id="83d57-120">_**Hint:** When using `CookieRequestCultureProvider`, verify single quotes are not used with the cultures inside the localization cookie value. For example, `c='en-UK'|uic='en-US'` is an invalid cookie value, while `c=en-UK|uic=en-US` is a valid._</span></span>

## <a name="resources--class-libraries-issues"></a><span data-ttu-id="83d57-121">리소스 및 클래스 라이브러리 문제</span><span class="sxs-lookup"><span data-stu-id="83d57-121">Resources & Class Libraries issues</span></span>

<span data-ttu-id="83d57-122">기본적으로 ASP.NET Core는 클래스 라이브러리가 [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1)를 통해 해당 리소스 파일을 찾을 수 있는 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-122">ASP.NET Core by default provides a way to allow the class libraries to find their resource files via [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1).</span></span>

<span data-ttu-id="83d57-123">클래스 라이브러리와 관련된 일반적인 문제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-123">Common issues with class libraries include:</span></span>
- <span data-ttu-id="83d57-124">라이브러리 클래스에서 `ResourceLocationAttribute`가 누락되면 `ResourceManagerStringLocalizerFactory`가 리소스를 검색하지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-124">Missing the `ResourceLocationAttribute` in a class library will prevent `ResourceManagerStringLocalizerFactory` from discovering the resources.</span></span>
- <span data-ttu-id="83d57-125">리소스 파일 이름 지정</span><span class="sxs-lookup"><span data-stu-id="83d57-125">Resource file naming.</span></span> <span data-ttu-id="83d57-126">자세한 내용은 [리소스 파일 이름 지정 문제](#resource-file-naming-issues) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="83d57-126">For more information, see [Resource file naming issues](#resource-file-naming-issues) section.</span></span>
- <span data-ttu-id="83d57-127">클래스 라이브러리의 루트 네임스페이스 변경</span><span class="sxs-lookup"><span data-stu-id="83d57-127">Changing the root namespace of the class library.</span></span> <span data-ttu-id="83d57-128">자세한 내용은 [루트 네임스페이스 문제](#root-namespace-issues) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="83d57-128">For more information, see [Root Namespace issues](#root-namespace-issues) section.</span></span>

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a><span data-ttu-id="83d57-129">CustomRequestCultureProvider가 예상대로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-129">CustomRequestCultureProvider doesn't work as expected</span></span>

<span data-ttu-id="83d57-130">`RequestLocalizationOptions` 클래스에는 세 가지 기본 공급 기업이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-130">The `RequestLocalizationOptions` class has three default providers:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="83d57-131">[CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1)를 통해 앱에서 지역화 문화권을 제공하는 방법을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-131">The [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) allows you to customize how the localization culture is provided in your app.</span></span> <span data-ttu-id="83d57-132">기본 공급 기업이 사용자 요구 사항을 충족하지 못할 때 `CustomRequestCultureProvider`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-132">The `CustomRequestCultureProvider` is used when the default providers don't meet your requirements.</span></span>

- <span data-ttu-id="83d57-133">사용자 지정 공급 기업이 제대로 작동하지 않는 일반적인 원인은 `RequestCultureProviders` 목록의 첫 번째 공급 기업이 아니라는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-133">A common reason custom provider don't work properly is that it isn't the first provider in the `RequestCultureProviders` list.</span></span> <span data-ttu-id="83d57-134">이 문제를 해결하려면:</span><span class="sxs-lookup"><span data-stu-id="83d57-134">To resolve this issue:</span></span>

- <span data-ttu-id="83d57-135">다음과 같이 `RequestCultureProviders` 목록에서 0의 위치에 있는 사용자 지정 공급 기업을 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-135">Insert the custom provider at the position 0 in the `RequestCultureProviders` list as the following:</span></span>

```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```

- <span data-ttu-id="83d57-136">`AddInitialRequestCultureProvider` 확장 메서드를 사용하여 사용자 지정 공급 기업을 초기 공급 기업으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-136">Use `AddInitialRequestCultureProvider` extension method to set the custom provider as initial provider.</span></span>

## <a name="root-namespace-issues"></a><span data-ttu-id="83d57-137">루트 네임스페이스 문제</span><span class="sxs-lookup"><span data-stu-id="83d57-137">Root Namespace issues</span></span>

<span data-ttu-id="83d57-138">어셈블리의 루트 네임 스페이스가 어셈블리 이름과 다르면 지역화가 기본적으로 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-138">When the root namespace of an assembly is different than the assembly name, localization doesn't work by default.</span></span> <span data-ttu-id="83d57-139">이 문제를 방지하려면 [여기](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)에 자세히 설명된 대로 [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-139">To avoid this issue use [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), which is described in detail [here](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming)</span></span>

## <a name="resources--build-action"></a><span data-ttu-id="83d57-140">리소스 및 빌드 작업</span><span class="sxs-lookup"><span data-stu-id="83d57-140">Resources & Build Action</span></span>

<span data-ttu-id="83d57-141">지역화에 대해 리소스 파일을 사용하는 경우 적절한 빌드 작업이 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-141">If you use resource files for localization, it's important that they have an appropriate build action.</span></span> <span data-ttu-id="83d57-142">해당 리소스 파일이 **포함 리소스**여야 하며, 그렇지 않으면 `ResourceStringLocalizer`가 이러한 리소스를 찾을 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="83d57-142">They should be **Embedded Resource**, otherwise the `ResourceStringLocalizer` is not able to find these resources.</span></span>
