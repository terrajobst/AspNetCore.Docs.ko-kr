---
title: ASP.NET Core의 컨트롤러에 종속성 주입
author: ardalis
description: ASP.NET Core MVC 컨트롤러가 ASP.NET Core의 종속성 주입을 사용하여 해당 생성자를 통해 명시적으로 해당 종속성을 요청하는 방법을 알아봅니다.
ms.author: riande
ms.date: 02/24/2019
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 6b08c321f4cae1f4efd8ea40300eaf4dfc2f63a1
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64890938"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="fe839-103">ASP.NET Core의 컨트롤러에 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="fe839-103">Dependency injection into controllers in ASP.NET Core</span></span>

<a name="dependency-injection-controllers"></a>

<span data-ttu-id="fe839-104">작성자: [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="fe839-104">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="fe839-105">ASP.NET Core MVC 컨트롤러는 생성자를 통해 명시적으로 종속성을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="fe839-105">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="fe839-106">ASP.NET Core는 기본적으로 [DI(종속성 주입 )](xref:fundamentals/dependency-injection)를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="fe839-106">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="fe839-107">DI를 사용하면 앱의 테스트와 유지 관리가 쉬워집니다.</span><span class="sxs-lookup"><span data-stu-id="fe839-107">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="fe839-108">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fe839-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="fe839-109">생성자 주입</span><span class="sxs-lookup"><span data-stu-id="fe839-109">Constructor Injection</span></span>

<span data-ttu-id="fe839-110">서비스는 생성자 매개 변수로 추가되며, 런타임은 서비스 컨테이너에서 서비스를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="fe839-110">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="fe839-111">서비스는 일반적으로 인터페이스를 사용하여 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe839-111">Services are typically defined using interfaces.</span></span> <span data-ttu-id="fe839-112">예를 들어, 현재 필요한 앱을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="fe839-112">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="fe839-113">다음 인터페이스는 `IDateTime` 서비스를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="fe839-113">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="fe839-114">다음 코드는 `IDateTime` 인터페이스를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="fe839-114">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="fe839-115">서비스를 서비스 컨테이너에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="fe839-115">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="fe839-116"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>에 대한 자세한 내용은 [DI 서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fe839-116">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="fe839-117">다음 코드는 하루 중 시간을 기준으로 사용자에게 인사말을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="fe839-117">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="fe839-118">앱을 실행하면 시간을 기준으로 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="fe839-118">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="fe839-119">FromServices로 작업 삽입</span><span class="sxs-lookup"><span data-stu-id="fe839-119">Action injection with FromServices</span></span>

<span data-ttu-id="fe839-120"><xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute>를 사용하면 생성자 주입을 사용하지 않고 작업 메서드에 직접 서비스를 주입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fe839-120">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="fe839-121">컨트롤러에서 설정 액세스</span><span class="sxs-lookup"><span data-stu-id="fe839-121">Access settings from a controller</span></span>

<span data-ttu-id="fe839-122">컨트롤러 내에서 앱 또는 구성 설정에 액세스하는 것은 일반적인 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="fe839-122">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="fe839-123"><xref:fundamentals/configuration/options>에 설명된 *옵션 패턴*은 설정을 관리하기 위해 선호되는 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="fe839-123">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="fe839-124">일반적으로 컨트롤러에 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 직접 주입하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="fe839-124">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="fe839-125">옵션을 나타내는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fe839-125">Create a class that represents the options.</span></span> <span data-ttu-id="fe839-126">예:</span><span class="sxs-lookup"><span data-stu-id="fe839-126">For example:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="fe839-127">서비스 컬렉션에 구성 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="fe839-127">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="fe839-128">JSON 형식 파일에서 설정을 읽도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="fe839-128">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="fe839-129">다음 코드는 서비스 컨테이너에서 `IOptions<SampleWebSettings>` 설정을 요청하여 `Index` 메서드에 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fe839-129">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="fe839-130">추가 자료</span><span class="sxs-lookup"><span data-stu-id="fe839-130">Additional resources</span></span>

* <span data-ttu-id="fe839-131">컨트롤러에서 종속성을 명시적으로 요청하여 코드를 더 쉽게 테스트할 수 있는 방법을 알아보려면 <xref:mvc/controllers/testing>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fe839-131">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="fe839-132">[기본 종속성 주입 컨테이너를 타사 구현으로 바꿉니다](xref:fundamentals/dependency-injection#default-service-container-replacement).</span><span class="sxs-lookup"><span data-stu-id="fe839-132">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>
