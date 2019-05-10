---
title: ASP.NET Core의 이미지 태그 도우미
author: pkellner
description: 이미지 태그 도우미의 사용 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: 916a68c187cbf516a59d3c5d7578cdb6ada01b86
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887668"
---
# <a name="image-tag-helper-in-aspnet-core"></a><span data-ttu-id="1665b-103">ASP.NET Core의 이미지 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="1665b-103">Image Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="1665b-104">작성자: [Peter Kellner](http://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="1665b-104">By [Peter Kellner](http://peterkellner.net)</span></span>

<span data-ttu-id="1665b-105">이미지 태그 도우미는 정적 이미지 파일에 캐시 버스팅 동작을 제공하도록 `<img>` 태그를 개선합니다.</span><span class="sxs-lookup"><span data-stu-id="1665b-105">The Image Tag Helper enhances the `<img>` tag to provide cache-busting behavior for static image files.</span></span>

<span data-ttu-id="1665b-106">캐시 버스팅 문자열은 자산의 URL에 추가된 정적 이미지 파일의 해시를 나타내는 고유한 값입니다.</span><span class="sxs-lookup"><span data-stu-id="1665b-106">A cache-busting string is a unique value representing the hash of the static image file appended to the asset's URL.</span></span> <span data-ttu-id="1665b-107">고유한 문자열은 클라이언트(및 일부 프록시)에게 클라이언트의 캐시가 아닌 호스트 웹 서버에서 이미지를 다시 로드할지 묻습니다.</span><span class="sxs-lookup"><span data-stu-id="1665b-107">The unique string prompts clients (and some proxies) to reload the image from the host web server and not from the client's cache.</span></span>

<span data-ttu-id="1665b-108">이미지 원본(`src`)은 호스트 웹 서버의 정적 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="1665b-108">If the image source (`src`) is a static file on the host web server:</span></span>

* <span data-ttu-id="1665b-109">고유한 캐시 버스팅 문자열이 이미지 원본에 쿼리 매개 변수로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="1665b-109">A unique cache-busting string is appended as a query parameter to the image source.</span></span>
* <span data-ttu-id="1665b-110">호스트 웹 서버에 위치한 파일이 변경될 경우 갱신된 요청 매개 변수를 포함하는 고유한 요청 URL이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="1665b-110">If the file on the host web server changes, a unique request URL is generated that includes the updated request parameter.</span></span>

<span data-ttu-id="1665b-111">태그 도우미에 대한 개요는 <xref:mvc/views/tag-helpers/intro>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1665b-111">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="image-tag-helper-attributes"></a><span data-ttu-id="1665b-112">이미지 태그 도우미 특성</span><span class="sxs-lookup"><span data-stu-id="1665b-112">Image Tag Helper Attributes</span></span>

### <a name="src"></a><span data-ttu-id="1665b-113">src</span><span class="sxs-lookup"><span data-stu-id="1665b-113">src</span></span>

<span data-ttu-id="1665b-114">이미지 태그 도우미를 활성화하려면 `<img>` 요소에 `src` 특성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="1665b-114">To activate the Image Tag Helper, the `src` attribute is required on the `<img>` element.</span></span>

<span data-ttu-id="1665b-115">이미지 원본(`src`)은 서버의 물리적 정적 파일을 가리켜야 합니다.</span><span class="sxs-lookup"><span data-stu-id="1665b-115">The image source (`src`) must point to a physical static file on the server.</span></span> <span data-ttu-id="1665b-116">`src`가 원격 URI일 경우 캐시 버스팅 쿼리 문자열 매개 변수가 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1665b-116">If the `src` is a remote URI, the cache-busting query string parameter isn't generated.</span></span>

### <a name="asp-append-version"></a><span data-ttu-id="1665b-117">asp-append-version</span><span class="sxs-lookup"><span data-stu-id="1665b-117">asp-append-version</span></span>

<span data-ttu-id="1665b-118">`asp-append-version`에 `src` 특성과 `true` 값이 지정되면 이미지 태그 도우미가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="1665b-118">When `asp-append-version` is specified with a `true` value along with a `src` attribute, the Image Tag Helper is invoked.</span></span>

<span data-ttu-id="1665b-119">다음 예에서는 이미지 태그 도우미를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="1665b-119">The following example uses an Image Tag Helper:</span></span>

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

<span data-ttu-id="1665b-120">정적 파일이 */wwwroot/images/* 디렉터리에 있을 경우, 생성되는 HTML은 다음과 비슷합니다(해시는 다름).</span><span class="sxs-lookup"><span data-stu-id="1665b-120">If the static file exists in the directory */wwwroot/images/*, the generated HTML is similar to the following (the hash will be different):</span></span>

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

<span data-ttu-id="1665b-121">매개 변수 `v`에 할당된 값은 디스크에 존재하는 *asplogo.png* 파일의 해시 값입니다.</span><span class="sxs-lookup"><span data-stu-id="1665b-121">The value assigned to the parameter `v` is the hash value of the *asplogo.png* file on disk.</span></span> <span data-ttu-id="1665b-122">웹 서버가 정적 파일에 대한 읽기 액세스 권한을 얻을 수 없으면 렌더링된 태그의 `src` 특성에 `v` 매개 변수가 추가되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1665b-122">If the web server is unable to obtain read access to the static file, no `v` parameter is added to the `src` attribute in the rendered markup.</span></span>

## <a name="hash-caching-behavior"></a><span data-ttu-id="1665b-123">해시 캐싱 동작</span><span class="sxs-lookup"><span data-stu-id="1665b-123">Hash caching behavior</span></span>

<span data-ttu-id="1665b-124">이미지 태그 도우미는 로컬 웹 서버에서 캐시 공급자를 사용해서 해당 파일의 계산된 `Sha512` 해시를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="1665b-124">The Image Tag Helper uses the cache provider on the local web server to store the calculated `Sha512` hash of a given file.</span></span> <span data-ttu-id="1665b-125">파일이 여러 번 요청되면 해시가 다시 계산되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1665b-125">If the file is requested multiple times, the hash isn't recalculated.</span></span> <span data-ttu-id="1665b-126">캐시는 파일의 `Sha512` 해시가 계산될 때 파일에 첨부된 파일 감시자에 의해 무효화됩니다.</span><span class="sxs-lookup"><span data-stu-id="1665b-126">The cache is invalidated by a file watcher that's attached to the file when the file's `Sha512` hash is calculated.</span></span> <span data-ttu-id="1665b-127">디스크에서 파일이 변경되면 새 해시가 계산되고 캐시됩니다.</span><span class="sxs-lookup"><span data-stu-id="1665b-127">When the file changes on disk, a new hash is calculated and cached.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1665b-128">추가 자료</span><span class="sxs-lookup"><span data-stu-id="1665b-128">Additional resources</span></span>

* <xref:performance/caching/memory>
