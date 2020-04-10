---
title: ASP.NET SignalR 코어 자바 스크립트 클라이언트
author: bradygaster
description: ASP.NET 핵심 SignalR 자바 스크립트 클라이언트의 개요.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- SignalR
uid: signalr/javascript-client
ms.openlocfilehash: a99c1dd2aba6ef6ff925783762a98e2c81ed7225
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994577"
---
# <a name="aspnet-core-opno-locsignalr-javascript-client"></a><span data-ttu-id="b2a14-103">ASP.NET SignalR 코어 자바 스크립트 클라이언트</span><span class="sxs-lookup"><span data-stu-id="b2a14-103">ASP.NET Core SignalR JavaScript client</span></span>

<span data-ttu-id="b2a14-104">작성자: [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="b2a14-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="b2a14-105">ASP.NET 코어 SignalR JavaScript 클라이언트 라이브러리를 사용하면 개발자가 서버 쪽 허브 코드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="b2a14-106">[샘플 코드 보기 또는 다운로드(다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) [방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="b2a14-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/javascript-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-opno-locsignalr-client-package"></a><span data-ttu-id="b2a14-107">SignalR 클라이언트 패키지 설치</span><span class="sxs-lookup"><span data-stu-id="b2a14-107">Install the SignalR client package</span></span>

<span data-ttu-id="b2a14-108">SignalR 자바 스크립트 클라이언트 라이브러리는 [npm](https://www.npmjs.com/) 패키지로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="b2a14-109">다음 섹션에서는 클라이언트 라이브러리를 설치하는 여러 가지 방법을 간략하게 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="b2a14-110">npm으로 설치</span><span class="sxs-lookup"><span data-stu-id="b2a14-110">Install with npm</span></span>

<span data-ttu-id="b2a14-111">Visual Studio를 사용하는 경우 루트 폴더에 있는 동안 **패키지 관리자 콘솔에서** 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-111">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="b2a14-112">Visual Studio 코드의 경우 **통합 터미널에서**다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

::: moniker range=">= aspnetcore-3.0"

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="b2a14-113">npm은 \*node_modules\\ \* 폴더에 패키지 내용을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="b2a14-114">*\\wwwroot lib* 폴더 아래에 *시그널이라는* 새 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="b2a14-115">*signalr.js* 파일을 *wwwroot\lib\signalr* 폴더에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="b2a14-116">npm은 \*node_modules\\ \* 폴더에 패키지 내용을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-116">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="b2a14-117">*\\wwwroot lib* 폴더 아래에 *시그널이라는* 새 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-117">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="b2a14-118">*signalr.js* 파일을 *wwwroot\lib\signalr* 폴더에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-118">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

::: moniker-end

<span data-ttu-id="b2a14-119">요소에서 SignalR 자바스크립트 클라이언트를 참조합니다. `<script>`</span><span class="sxs-lookup"><span data-stu-id="b2a14-119">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="b2a14-120">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="b2a14-120">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="b2a14-121">CDN(콘텐츠 전송 네트워크) 사용</span><span class="sxs-lookup"><span data-stu-id="b2a14-121">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="b2a14-122">npm 필수 구성 조건 없이 클라이언트 라이브러리를 사용하려면 클라이언트 라이브러리의 CDN 호스팅 복사본을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="b2a14-122">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="b2a14-123">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="b2a14-123">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="b2a14-124">클라이언트 라이브러리는 다음 CDN에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-124">The client library is available on the following CDNs:</span></span>

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="b2a14-125">cdnjs</span><span class="sxs-lookup"><span data-stu-id="b2a14-125">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="b2a14-126">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="b2a14-126">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="b2a14-127">언프kg</span><span class="sxs-lookup"><span data-stu-id="b2a14-127">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="b2a14-128">cdnjs</span><span class="sxs-lookup"><span data-stu-id="b2a14-128">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="b2a14-129">jsDelivr</span><span class="sxs-lookup"><span data-stu-id="b2a14-129">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="b2a14-130">언프kg</span><span class="sxs-lookup"><span data-stu-id="b2a14-130">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

::: moniker-end

### <a name="install-with-libman"></a><span data-ttu-id="b2a14-131">LibMan으로 설치</span><span class="sxs-lookup"><span data-stu-id="b2a14-131">Install with LibMan</span></span>

<span data-ttu-id="b2a14-132">[LibMan은](xref:client-side/libman/index) CDN 호스팅 클라이언트 라이브러리에서 특정 클라이언트 라이브러리 파일을 설치하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-132">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="b2a14-133">예를 들어, 프로젝트에 만 다진 자바 스크립트 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-133">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="b2a14-134">해당 접근 방식에 대한 자세한 내용은 [클라이언트 라이브러리 추가를 SignalR ](xref:tutorials/signalr#add-the-signalr-client-library)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="b2a14-134">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="b2a14-135">허브에 연결</span><span class="sxs-lookup"><span data-stu-id="b2a14-135">Connect to a hub</span></span>

<span data-ttu-id="b2a14-136">다음 코드는 연결을 만들고 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-136">The following code creates and starts a connection.</span></span> <span data-ttu-id="b2a14-137">허브의 이름은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-137">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=9-13,43-45)]

### <a name="cross-origin-connections"></a><span data-ttu-id="b2a14-138">오리진 간 연결</span><span class="sxs-lookup"><span data-stu-id="b2a14-138">Cross-origin connections</span></span>

<span data-ttu-id="b2a14-139">일반적으로 브라우저는 요청된 페이지와 동일한 도메인에서 연결을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-139">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="b2a14-140">그러나 다른 도메인에 연결해야 하는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-140">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="b2a14-141">악의적인 사이트가 다른 사이트에서 중요한 데이터를 읽지 못하도록 하려면 기본적으로 [원본 간 연결이](xref:security/cors) 비활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-141">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="b2a14-142">원본 간 요청을 허용하려면 클래스에서 `Startup` 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-142">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/sample/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="b2a14-143">클라이언트에서 호출 허브 메서드</span><span class="sxs-lookup"><span data-stu-id="b2a14-143">Call hub methods from client</span></span>

<span data-ttu-id="b2a14-144">JavaScript 클라이언트는 [HubConnection의](/javascript/api/%40aspnet/signalr/hubconnection) [호출](/javascript/api/%40aspnet/signalr/hubconnection#invoke) 메서드를 통해 허브에서 공용 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-144">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="b2a14-145">메서드는 `invoke` 두 개의 인수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-145">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="b2a14-146">허브 메서드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-146">The name of the hub method.</span></span> <span data-ttu-id="b2a14-147">다음 예제에서는 허브의 메서드 이름은 `SendMessage`입니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-147">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="b2a14-148">허브 메서드에 정의된 모든 인수입니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-148">Any arguments defined in the hub method.</span></span> <span data-ttu-id="b2a14-149">다음 예제에서 인수 이름은 `message`입니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-149">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="b2a14-150">예제 코드는 Internet Explorer를 제외한 모든 주요 브라우저의 현재 버전에서 지원되는 화살표 함수 구문을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-150">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/sample/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="b2a14-151">*서버리스 모드에서*Azure SignalR 서비스를 사용하는 경우 클라이언트에서 허브 메서드를 호출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-151">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="b2a14-152">자세한 내용은 서비스 [ SignalR 설명서를](/azure/azure-signalr/signalr-concept-serverless-development-config)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="b2a14-152">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

<span data-ttu-id="b2a14-153">이 `invoke` 메서드는 자바 스크립트 [약속을](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-153">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="b2a14-154">서버의 `Promise` 메서드가 반환될 때 반환 값(있는 경우)으로 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-154">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="b2a14-155">서버의 메서드에서 오류가 발생하면 오류 `Promise` 메시지와 함께 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-155">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="b2a14-156">`then` 자체의 `catch` 메서드 및 `Promise` 메서드를 사용하여 이러한 `await` 사례(또는 구문)를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-156">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="b2a14-157">메서드는 `send` 자바 스크립트를 `Promise`반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-157">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="b2a14-158">이 `Promise` 메시지는 서버로 전송될 때 해결됩니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-158">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="b2a14-159">메시지를 보내는 오류가 있으면 오류 `Promise` 메시지와 함께 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-159">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="b2a14-160">`then` 자체의 `catch` 메서드 및 `Promise` 메서드를 사용하여 이러한 `await` 사례(또는 구문)를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-160">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="b2a14-161">서버가 메시지를 받을 때까지 사용 `send` 해도 지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-161">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="b2a14-162">따라서 서버에서 데이터 또는 오류를 반환할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-162">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="b2a14-163">허브에서 클라이언트 메서드 호출</span><span class="sxs-lookup"><span data-stu-id="b2a14-163">Call client methods from hub</span></span>

<span data-ttu-id="b2a14-164">허브에서 메시지를 받으려면 `HubConnection`의 [on](/javascript/api/%40aspnet/signalr/hubconnection#on) 메서드를 사용하여 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-164">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="b2a14-165">자바 스크립트 클라이언트 메서드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-165">The name of the JavaScript client method.</span></span> <span data-ttu-id="b2a14-166">다음 예제에서 메서드 이름은 `ReceiveMessage`입니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-166">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="b2a14-167">인수 허브 메서드에 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-167">Arguments the hub passes to the method.</span></span> <span data-ttu-id="b2a14-168">다음 예제에서 인수 값은 `message`입니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-168">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/sample/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="b2a14-169">위의 코드는 `connection.on` 서버 측 코드가 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) 메서드를 사용하여 호출할 때 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-169">The preceding code in `connection.on` runs when server-side code calls it using the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method.</span></span>

[!code-csharp[Call client-side](javascript-client/sample/hubs/chathub.cs?range=8-11)]

SignalR<span data-ttu-id="b2a14-170">에서 정의된 `SendAsync` 메서드 이름과 인수를 일치시켜 호출할 클라이언트 `connection.on`메서드를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-170"> determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="b2a14-171">가장 좋은 방법은 after의 `on` [시작](/javascript/api/%40aspnet/signalr/hubconnection#start) `HubConnection` 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-171">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="b2a14-172">이렇게 하면 메시지를 받기 전에 처리기가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-172">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="b2a14-173">오류 처리 및 로깅</span><span class="sxs-lookup"><span data-stu-id="b2a14-173">Error handling and logging</span></span>

<span data-ttu-id="b2a14-174">메서드를 `catch` 메서드 끝에 `start` 연결하여 클라이언트 측 오류를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-174">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="b2a14-175">브라우저 `console.error` 콘솔에 오류를 출력하는 데 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-175">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/sample/wwwroot/js/chat.js?range=49-51)]

<span data-ttu-id="b2a14-176">연결이 이루어질 때 로거 및 이벤트 유형을 전달하여 클라이언트 측 로그 추적을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-176">Setup client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="b2a14-177">메시지는 지정된 로그 수준 이상으로 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-177">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="b2a14-178">사용 가능한 로그 수준은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-178">Available log levels are as follows:</span></span>

* <span data-ttu-id="b2a14-179">`signalR.LogLevel.Error`&ndash; 오류 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-179">`signalR.LogLevel.Error` &ndash; Error messages.</span></span> <span data-ttu-id="b2a14-180">메시지만 `Error` 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-180">Logs `Error` messages only.</span></span>
* <span data-ttu-id="b2a14-181">`signalR.LogLevel.Warning`&ndash; 잠재적인 오류에 대한 경고 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-181">`signalR.LogLevel.Warning` &ndash; Warning messages about potential errors.</span></span> <span data-ttu-id="b2a14-182">`Warning`로그 및 `Error` 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-182">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="b2a14-183">`signalR.LogLevel.Information`&ndash; 오류 없이 상태 메시지.</span><span class="sxs-lookup"><span data-stu-id="b2a14-183">`signalR.LogLevel.Information` &ndash; Status messages without errors.</span></span> <span data-ttu-id="b2a14-184">`Information`로그 `Warning`및 `Error` 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-184">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="b2a14-185">`signalR.LogLevel.Trace`&ndash; 메시지를 추적합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-185">`signalR.LogLevel.Trace` &ndash; Trace messages.</span></span> <span data-ttu-id="b2a14-186">허브와 클라이언트 간에 전송되는 데이터를 포함하여 모든 것을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-186">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="b2a14-187">[HubConnectionBuilder에서](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) 메서드를 사용하여 로그 수준을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-187">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="b2a14-188">메시지가 브라우저 콘솔에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-188">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/sample/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="b2a14-189">클라이언트 다시 연결</span><span class="sxs-lookup"><span data-stu-id="b2a14-189">Reconnect clients</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="b2a14-190">자동으로 다시 연결</span><span class="sxs-lookup"><span data-stu-id="b2a14-190">Automatically reconnect</span></span>

<span data-ttu-id="b2a14-191">에 대 한 SignalR 자바 스크립트 `withAutomaticReconnect` [클라이언트는 HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b2a14-191">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="b2a14-192">기본적으로 자동으로 다시 연결되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-192">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="b2a14-193">매개 변수가 `withAutomaticReconnect()` 없으면 클라이언트가 각각 0, 2, 10 및 30초를 기다린 후 각 재연결 시도를 시도하여 4번의 실패한 시도 후에 중지하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-193">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="b2a14-194">다시 연결 시도를 `HubConnection` 시작하기 전에 `HubConnectionState.Reconnecting` 상태가 상태로 전환되고 `onreconnecting` 자동으로 다시 `Disconnected` `onclose` `HubConnection` 연결되지 않은 것처럼 콜백을 트리거하는 대신 콜백을 발생시면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-194">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="b2a14-195">이렇게 하면 연결이 손실되었음을 사용자에게 경고하고 UI 요소를 사용하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-195">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="b2a14-196">클라이언트가 처음 네 번의 시도 내에서 `HubConnection` 성공적으로 다시 연결되면 `Connected` 클라이언트가 `onreconnected` 상태로 다시 전환되고 콜백이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-196">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="b2a14-197">이렇게 하면 연결이 다시 설정되었음을 사용자에게 알릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-197">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="b2a14-198">연결이 서버에 완전히 새 것처럼 보이므로 `connectionId` `onreconnected` 콜백에 새 연결이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-198">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="b2a14-199">협상을 [건너뛰도록](xref:signalr/configuration#configure-client-options) `connectionId` 구성된 경우 콜백의 `HubConnection` 매개 변수가 정의되지 않습니다. `onreconnected`</span><span class="sxs-lookup"><span data-stu-id="b2a14-199">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected((connectionId) => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="b2a14-200">`withAutomaticReconnect()`초기 시작 실패를 `HubConnection` 다시 시도하도록 구성하지 않으므로 시작 실패를 수동으로 처리해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-200">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("connected");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="b2a14-201">클라이언트가 처음 네 번의 시도 내에서 성공적으로 `HubConnection` 다시 연결되지 `Disconnected` 않으면 클라이언트가 상태로 전환되어 [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) 콜백을 발생시면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-201">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="b2a14-202">이렇게 하면 연결이 영구적으로 손실되었음을 사용자에게 알리고 페이지를 새로 고칠 것을 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-202">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose((error) => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="b2a14-203">연결을 끊거나 다시 연결 타이밍을 변경하기 전에 사용자 지정 `withAutomaticReconnect` 재연결 시도 수를 구성하려면 각 다시 연결 시도를 시작하기 전에 기다릴 수 있는 밀리초 의 지연을 나타내는 숫자 배열을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-203">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="b2a14-204">앞의 예제는 연결이 `HubConnection` 끊어진 직후다시 연결을 시도할 수 있도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-204">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="b2a14-205">기본 구성에도 마찬가지입니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-205">This is also true for the default configuration.</span></span>

<span data-ttu-id="b2a14-206">첫 번째 다시 연결 시도가 실패하면 기본 구성에서와 마찬가지로 2초를 기다리는 대신 두 번째 다시 연결 시도도 즉시 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-206">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="b2a14-207">두 번째 다시 연결 시도가 실패하면 세 번째 다시 연결 시도가 기본 구성과 같은 10초 후에 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-207">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="b2a14-208">그런 다음 사용자 지정 동작은 기본 구성에서와 마찬가지로 다른 30초 동안 다시 연결시도를 한 번 더 시도하는 대신 세 번째 다시 연결 시도 실패 후에 중지하여 기본 동작에서 다시 분기됩니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-208">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="b2a14-209">자동 다시 연결 시도의 `withAutomaticReconnect` `IRetryPolicy` 타이밍과 수를 보다 세한 제어를 원하는 `nextRetryDelayInMilliseconds`경우.</span><span class="sxs-lookup"><span data-stu-id="b2a14-209">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="b2a14-210">`nextRetryDelayInMilliseconds`형식이 `RetryContext`있는 단일 인수를 취합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-210">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="b2a14-211">는 `RetryContext` 세 가지 `previousRetryCount` `elapsedMilliseconds` 속성을 `retryReason` 가지고 `number`있습니다: `number` 및 `Error` 는 각각 .</span><span class="sxs-lookup"><span data-stu-id="b2a14-211">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="b2a14-212">첫 번째 다시 연결 `previousRetryCount` 시도 `elapsedMilliseconds` 전에 둘 다 `retryReason` 0이 되고 연결이 손실된 오류가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-212">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="b2a14-213">실패한 각 재시도 `previousRetryCount` 후 하나씩 증분되고 지금까지 `elapsedMilliseconds` 다시 연결하는 데 소요된 시간을 밀리초 단위로 반영하도록 `retryReason` 업데이트되며 마지막 다시 연결 시도가 실패하게 한 오류입니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-213">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="b2a14-214">`nextRetryDelayInMilliseconds`다음 다시 연결 시도 전에 기다릴 밀리초 수를 나타내는 숫자를 `null` 반환하거나 다시 연결을 중지해야 하는 경우 를 `HubConnection` 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-214">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

<span data-ttu-id="b2a14-215">또는 수동으로 다시 연결에서 설명한 대로 클라이언트를 수동으로 다시 연결하는 코드를 [작성할](#manually-reconnect)수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-215">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="b2a14-216">수동으로 다시 연결</span><span class="sxs-lookup"><span data-stu-id="b2a14-216">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="b2a14-217">3.0 이전에는 JavaScript 클라이언트가 자동으로 다시 연결되지 SignalR 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-217">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="b2a14-218">클라이언트를 수동으로 다시 연결하는 코드를 작성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-218">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="b2a14-219">다음 코드는 일반적인 수동 재연결 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-219">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="b2a14-220">연결을 시작하는 함수(이 `start` 경우 함수)가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-220">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="b2a14-221">연결의 `start` 이벤트 처리기에서 `onclose` 함수를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-221">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/sample/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="b2a14-222">실제 구현은 포기하기 전에 지수 백오프를 사용하거나 지정된 횟수로 다시 시도합니다.</span><span class="sxs-lookup"><span data-stu-id="b2a14-222">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b2a14-223">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="b2a14-223">Additional resources</span></span>

* [<span data-ttu-id="b2a14-224">JavaScript API 참조</span><span class="sxs-lookup"><span data-stu-id="b2a14-224">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="b2a14-225">자바 스크립트 튜토리얼</span><span class="sxs-lookup"><span data-stu-id="b2a14-225">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="b2a14-226">웹팩 및 타이프스크립트 튜토리얼</span><span class="sxs-lookup"><span data-stu-id="b2a14-226">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="b2a14-227">허브</span><span class="sxs-lookup"><span data-stu-id="b2a14-227">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="b2a14-228">.NET 클라이언트</span><span class="sxs-lookup"><span data-stu-id="b2a14-228">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="b2a14-229">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="b2a14-229">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="b2a14-230">교차 원산지 요청(CORS)</span><span class="sxs-lookup"><span data-stu-id="b2a14-230">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* <span data-ttu-id="b2a14-231">[Azure SignalR 서비스 서버없는 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="b2a14-231">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
