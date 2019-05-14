---
title: Visual Studio에서 ASP.NET Core와 LibMan 사용하기
author: scottaddie
description: Visual Studio로 ASP.NET Core 프로젝트에서 LibMan을 사용하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: d0494b96447ad2b256acbde7748209ff430ae7c1
ms.sourcegitcommit: 6afe57fb8d9055f88fedb92b16470398c4b9b24a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65610176"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="42b64-103">Visual Studio에서 ASP.NET Core와 LibMan 사용하기</span><span class="sxs-lookup"><span data-stu-id="42b64-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="42b64-104">작성자: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="42b64-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="42b64-105">Visual Studio는 ASP.NET Core 프로젝트에서 [LibMan](xref:client-side/libman/index)에 대한 다음과 같은 기본 제공 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="42b64-106">빌드 시 LibMan 복원 작업을 구성 및 실행하기 위한 지원.</span><span class="sxs-lookup"><span data-stu-id="42b64-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="42b64-107">LibMan 복원 및 정리 작업을 실행하기 위한 메뉴 항목.</span><span class="sxs-lookup"><span data-stu-id="42b64-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="42b64-108">라이브러리를 검색하고 프로젝트에 파일을 추가하기 위한 검색 대화 상자.</span><span class="sxs-lookup"><span data-stu-id="42b64-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="42b64-109">LibMan 매니페스트 파일인 *libman.json*&mdash;에 대한 편집 지원.</span><span class="sxs-lookup"><span data-stu-id="42b64-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="42b64-110">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(다운로드 방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="42b64-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="42b64-111">전제 조건</span><span class="sxs-lookup"><span data-stu-id="42b64-111">Prerequisites</span></span>

* <span data-ttu-id="42b64-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 사용 하 여 합니다 **ASP.NET 및 웹 개발** 워크 로드</span><span class="sxs-lookup"><span data-stu-id="42b64-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="42b64-113">라이브러리 파일 추가하기</span><span class="sxs-lookup"><span data-stu-id="42b64-113">Add library files</span></span>

<span data-ttu-id="42b64-114">두 가지 방법으로 ASP.NET Core 프로젝트에 라이브러리 파일을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="42b64-115">클라이언트 쪽 라이브러리 추가 대화 상자 사용하기</span><span class="sxs-lookup"><span data-stu-id="42b64-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="42b64-116">직접 LibMan 매니페스트 파일 항목 구성하기</span><span class="sxs-lookup"><span data-stu-id="42b64-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="42b64-117">클라이언트 쪽 라이브러리 추가 대화 상자 사용하기</span><span class="sxs-lookup"><span data-stu-id="42b64-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="42b64-118">클라이언트 쪽 라이브러리를 설치하려면 다음의 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="42b64-119">**솔루션 탐색기**에서 파일을 추가할 프로젝트 폴더를 마우스 오른쪽 버튼으로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="42b64-120">**추가** > **클라이언트 라이브러리 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="42b64-121">**클라이언트 쪽 라이브러리 추가** 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![클라이언트 쪽 라이브러리 추가 대화 상자](_static/add-library-dialog.png)

* <span data-ttu-id="42b64-123">**공급자** 드롭다운에서 라이브러리 공급자를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="42b64-124">기본 공급자는 CDNJS입니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="42b64-125">**라이브러리** 텍스트 상자에 가져올 라이브러리 이름을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="42b64-126">IntelliSense가 입력한 텍스트로 시작하는 라이브러리의 목록을 제공해줍니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="42b64-127">IntelliSense 목록에서 라이브러리를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="42b64-128">라이브러리 이름에 접미사로 `@` 기호와 선택한 공급자에게 알려진 최신의 안정적인 버전이 추가되어 있는 것에 주의하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="42b64-129">포함할 파일을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-129">Decide which files to include:</span></span>
  * <span data-ttu-id="42b64-130">라이브러리의 모든 파일을 포함시키려면 **모든 라이브러리 파일 포함** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="42b64-131">라이브러리의 일부 파일만 포함시키려면 **특정 파일 선택** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="42b64-132">이 라디오 단추를 선택하면 파일 선택기 트리가 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="42b64-133">다운로드 할 파일명의 왼쪽에 위치한 상자를 체크합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="42b64-134">**대상 위치** 텍스트 상자에 파일을 저장할 프로젝트 폴더를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="42b64-135">각 라이브러리를 별도의 폴더에 저장하는 것을 권장합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="42b64-136">제안되는 **대상 위치** 폴더는 대화 상자가 시작된 위치에 따라서 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="42b64-137">프로젝트 루트에서 시작된 경우:</span><span class="sxs-lookup"><span data-stu-id="42b64-137">If launched from the project root:</span></span>
    * <span data-ttu-id="42b64-138">*wwwroot*가 존재하면 *wwwroot/lib*가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="42b64-139">*wwwroot*가 존재하지 않으면 *lib*가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="42b64-140">프로젝트 폴더에서 시작된 경우 해당 폴더 이름이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="42b64-141">폴더 제안 뒤에는 라이브러리의 이름이 붙습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="42b64-142">다음 표는 Razor Pages 프로젝트에 jQuery를 설치하는 경우의 폴더 제안을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="42b64-143">시작 위치</span><span class="sxs-lookup"><span data-stu-id="42b64-143">Launch location</span></span>                           |<span data-ttu-id="42b64-144">제안되는 폴더</span><span class="sxs-lookup"><span data-stu-id="42b64-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="42b64-145">프로젝트 루트 (*wwwroot*가 존재할 경우)</span><span class="sxs-lookup"><span data-stu-id="42b64-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="42b64-146">*jquery/wwwroot/lib/*</span><span class="sxs-lookup"><span data-stu-id="42b64-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="42b64-147">프로젝트 루트 (*wwwroot*가 존재하지 않을 경우)</span><span class="sxs-lookup"><span data-stu-id="42b64-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="42b64-148">*lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="42b64-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="42b64-149">프로젝트의 *Pages* 폴더</span><span class="sxs-lookup"><span data-stu-id="42b64-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="42b64-150">*Pages/jquery/*</span><span class="sxs-lookup"><span data-stu-id="42b64-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="42b64-151">*libman.json*의 구성에 따라 파일을 다운로드하려면 **설치** 버튼을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="42b64-152">**출력** 창의 **라이브러리 관리자** 피드에서 설치 세부 정보를 검토할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="42b64-153">예를 들어 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-153">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="42b64-154">직접 LibMan 매니페스트 파일 항목 구성하기</span><span class="sxs-lookup"><span data-stu-id="42b64-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="42b64-155">Visual Studio의 모든 LibMan 작업은 프로젝트 루트의 LibMan 매니페스트(*libman.json*)의 내용을 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="42b64-156">직접 *libman.json*을 편집하여 프로젝트의 라이브러리 파일을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="42b64-157">*libman.json*이 저장되면 Visual Studio는 모든 라이브러리 파일을 복원합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="42b64-158">편집을 위해 *libman.json*을 열려면 다음과 같은 방법을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="42b64-159">**솔루션 탐색기**에서 *libman.json* 파일을 더블 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="42b64-160">**솔루션 탐색기**에서 마우스 오른쪽 버튼으로 프로젝트를 클릭하고 **클라이언트 쪽 라이브러리 관리**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="42b64-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="42b64-161">**&#8224;**</span></span>
* <span data-ttu-id="42b64-162">Visual studio의 **프로젝트** 메뉴에서 **클라이언트 쪽 라이브러리 관리**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="42b64-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="42b64-163">**&#8224;**</span></span>

<span data-ttu-id="42b64-164">**&#8224;** 프로젝트 루트에 아직 *libman.json* 파일이 존재하지 않으면 기본 항목 템플릿 내용을 담은 파일이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="42b64-165">Visual Studio는 색 지정, 서식 지정, IntelliSense 및 스키마 유효성 검사 같은 다양한 JSON 편집 지원을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="42b64-166">LibMan 매니페스트의 JSON 스키마는 [http://json.schemastore.org/libman](http://json.schemastore.org/libman)에서 살펴볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-166">The LibMan manifest's JSON schema is found at [http://json.schemastore.org/libman](http://json.schemastore.org/libman).</span></span>

<span data-ttu-id="42b64-167">다음 매니페스트 파일을 사용하는 경우 LibMan은 `libraries` 속성에 정의된 각 구성에 따라 파일을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="42b64-168">`libraries` 내에 정의된 개체 리터럴에 대한 설명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="42b64-169">CDNJS 공급자에서 [jQuery](https://jquery.com/) 버전 3.3.1의 하위 집합이 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="42b64-170">하위 집합은 `files` 속성에 정의됩니다 (&mdash;*jquery.min.js*, *jquery.js* 및 *jquery.min.map*).</span><span class="sxs-lookup"><span data-stu-id="42b64-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="42b64-171">이러한 파일은 프로젝트의 *wwwroot/lib/jquery* 폴더에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="42b64-172">[부트스트랩](https://getbootstrap.com/) 버전 4.1.3 전체가 검색되어 *wwwroot/lib/bootstrap* 폴더에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="42b64-173">개체 리터럴의 `provider` 속성은 `defaultProvider` 속성 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="42b64-174">LibMan은 unpkg 공급자에서 부트스트랩 파일을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="42b64-175">[Lodash](https://lodash.com/)의 하위 집합은 조직 내 관리 기관의 승인을 받았습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="42b64-176">*lodash.js* 및 *lodash.min.js* 파일은 로컬 파일 시스템의 *C:\\temp\\lodash\\* 에서 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="42b64-177">이러한 파일은 프로젝트의 *wwwroot/lib/lodash* 폴더에 복사됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="42b64-178">LibMan은 각 공급자의 각 라이브러리에 대해 하나의 버전만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="42b64-179">지정된 공급자에 대해 동일한 라이브러리 이름을 가진 두 개의 라이브러리가 존재할 경우 *libman.json* 파일은 스키마 유효성 검증에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="42b64-180">라이브러리 파일 복원하기</span><span class="sxs-lookup"><span data-stu-id="42b64-180">Restore library files</span></span>

<span data-ttu-id="42b64-181">Visual Studio에서 라이브러리 파일을 복원하려면 프로젝트 루트에 유효한 *libman.json* 파일이 존재해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="42b64-182">복원된 파일은 각 라이브러리에 대해 지정된 프로젝트의 위치에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="42b64-183">ASP.NET Core 프로젝트에서 두 가지 방법으로 라이브러리 파일을 복원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="42b64-184">빌드 시 파일 복원하기</span><span class="sxs-lookup"><span data-stu-id="42b64-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="42b64-185">직접 파일 복원하기</span><span class="sxs-lookup"><span data-stu-id="42b64-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="42b64-186">빌드 시 파일 복원하기</span><span class="sxs-lookup"><span data-stu-id="42b64-186">Restore files during build</span></span>

<span data-ttu-id="42b64-187">LibMan은 빌드 프로세스의 일부로 정의된 라이브러리 파일을 복원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="42b64-188">기본적으로 *빌드 시 복원* 동작은 비활성화되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="42b64-189">빌드 시 복원 동작을 활성화시키고 테스트해 보려면:</span><span class="sxs-lookup"><span data-stu-id="42b64-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="42b64-190">**솔루션 탐색기**에서 *libman.json*을 마우스 오른쪽 버튼으로 클릭하고 컨텍스트 메뉴에서 **빌드에 클라이언트 쪽 라이브러리 복원 사용**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="42b64-191">NuGet 패키지를 설치하라는 메시지가 나타나면 **예** 버튼을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="42b64-192">그러면 [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet 패키지가 프로젝트에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="42b64-193">프로젝트를 빌드해서 LibMan 파일 복원이 발생하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="42b64-194">`Microsoft.Web.LibraryManager.Build`  패키지는 프로젝트의 빌드 작업 중 LibMan을 실행하는 MSBuild 대상을 주입합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="42b64-195">LibMan 활동 로그에 대한 **출력** 창의 **빌드** 피드를 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

<span data-ttu-id="42b64-196">빌드 시 복원 동작을 활성화시키면 *libman.json*의 컨텍스트 메뉴에는 **빌드에 클라이언트 쪽 라이브러리 사용하지 않음** 옵션이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="42b64-197">이 옵션을 선택하면 프로젝트 파일에서 `Microsoft.Web.LibraryManager.Build` 패키지 참조가 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="42b64-198">따라서 더 이상 클라이언트 쪽 라이브러리가 빌드 시마다 복원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="42b64-199">빌드 시 복원 설정과 관계없이 *libman.json*의 컨텍스트 메뉴에서 언제든지 수동으로 복원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="42b64-200">자세한 내용은 [직접 파일 복원하기](#restore-files-manually)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="42b64-201">직접 파일 복원하기</span><span class="sxs-lookup"><span data-stu-id="42b64-201">Restore files manually</span></span>

<span data-ttu-id="42b64-202">라이브러리 파일을 직접 복원하려면:</span><span class="sxs-lookup"><span data-stu-id="42b64-202">To manually restore library files:</span></span>

* <span data-ttu-id="42b64-203">솔루션의 모든 프로젝트를 복원하는 경우:</span><span class="sxs-lookup"><span data-stu-id="42b64-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="42b64-204">**솔루션 탐색기**에서 솔루션 이름을 마우스 오른쪽 버튼으로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="42b64-205">**클라이언트 라이브러리를 복원** 옵션을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="42b64-206">특정 프로젝트를 복원하는 경우:</span><span class="sxs-lookup"><span data-stu-id="42b64-206">For a specific project:</span></span>
  * <span data-ttu-id="42b64-207">**솔루션 탐색기**에서 *libman.json* 파일을 마우스 오른쪽 버튼으로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="42b64-208">**클라이언트 라이브러리를 복원** 옵션을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="42b64-209">복원 작업이 실행되는 동안:</span><span class="sxs-lookup"><span data-stu-id="42b64-209">While the restore operation is running:</span></span>

* <span data-ttu-id="42b64-210">Visual Studio 상태 표시줄의 작업 상태 센터(TSC) 아이콘이 애니메이션되고 *복원 작업이 시작됨*으로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="42b64-211">이 아이콘을 클릭하면 알려진 백그라운드 작업을 나열하는 도구 설명이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="42b64-212">메시지는 상태 표시줄 및 **출력** 창의 **라이브러리 관리자** 피드로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="42b64-213">예를 들어 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-213">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a><span data-ttu-id="42b64-214">라이브러리 파일 삭제하기</span><span class="sxs-lookup"><span data-stu-id="42b64-214">Delete library files</span></span>

<span data-ttu-id="42b64-215">Visual Studio에서 이전에 복원한 라이브러리 파일을 삭제하는 *정리* 작업을 수행하려면:</span><span class="sxs-lookup"><span data-stu-id="42b64-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="42b64-216">*솔루션 탐색기*에서 **libman.json** 파일을 마우스 오른쪽 버튼으로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="42b64-217">**청소** 옵션을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="42b64-218">정리 작업은 의도하지 않은 비-라이브러리 파일의 제거를 방지하기 위해서 전체 디렉터리를 삭제하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="42b64-219">이전 복원에 포함된 파일들만 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="42b64-220">정리 작업이 실행되는 동안:</span><span class="sxs-lookup"><span data-stu-id="42b64-220">While the clean operation is running:</span></span>

* <span data-ttu-id="42b64-221">Visual Studio 상태 표시줄의 TSC 아이콘이 애니메이션되고 *라이브러리 정리 작업이 시작됨*으로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="42b64-222">이 아이콘을 클릭하면 알려진 백그라운드 작업을 나열하는 도구 설명이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="42b64-223">상태 표시줄 메시지와 **라이브러리 관리자** 의 피드를 **출력** 창입니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="42b64-224">예를 들어 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="42b64-225">정리 작업은 프로젝트에서 파일만 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="42b64-226">라이브러리 파일은 이후 복원 작업 시 빠른 검색을 위해 캐시에 남아 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="42b64-227">로컬 컴퓨터의 캐시에 저장된 라이브러리 파일을 관리하려면 [LibMan CLI](xref:client-side/libman/libman-cli)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="42b64-228">라이브러리 파일 제거하기</span><span class="sxs-lookup"><span data-stu-id="42b64-228">Uninstall library files</span></span>

<span data-ttu-id="42b64-229">라이브러리 파일을 제거하려면:</span><span class="sxs-lookup"><span data-stu-id="42b64-229">To uninstall library files:</span></span>

* <span data-ttu-id="42b64-230">*libman.json*을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-230">Open *libman.json*.</span></span>
* <span data-ttu-id="42b64-231">캐럿을 해당 `libraries` 개체 리터럴에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="42b64-232">왼쪽 여백에 표시되는 전구 아이콘을 클릭하고 **\<library_name>@\<library_version> 제거**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![라이브러리 제거 컨텍스트 메뉴 옵션](_static/uninstall-menu-option.png)

<span data-ttu-id="42b64-234">또는 직접 LibMan 매니페스트(*libman.json*)를 편집하고 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="42b64-235">파일을 저장하면 [복원 작업](#restore-library-files)이 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="42b64-236">*libman.json*에 더 이상 정의되어 있지 않은 라이브러리 파일은 프로젝트에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="42b64-237">라이브러리 버전 업데이트</span><span class="sxs-lookup"><span data-stu-id="42b64-237">Update library version</span></span>

<span data-ttu-id="42b64-238">업데이트된 라이브러리 버전을 확인하려면:</span><span class="sxs-lookup"><span data-stu-id="42b64-238">To check for an updated library version:</span></span>

* <span data-ttu-id="42b64-239">*libman.json*을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-239">Open *libman.json*.</span></span>
* <span data-ttu-id="42b64-240">캐럿을 해당 `libraries` 개체 리터럴에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="42b64-241">왼쪽 여백에 표시되는 전구 아이콘을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="42b64-242">**업데이트 확인** 위에 마우스를 올려 놓습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="42b64-243">LibMan이 설치되어 있는 버전보다 새로운 라이브러리 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="42b64-244">다음과 같은 결과가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="42b64-245">이미 가장 최신 버전이 설치되어 있는 경우 **No updates found** 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="42b64-246">안정적인 최신 버전이 아직 설치되지 않았으면 해당 버전이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-246">The latest stable version is displayed if not already installed.</span></span>

  ![업데이트 확인 컨텍스트 메뉴 옵션](_static/update-menu-option.png)

* <span data-ttu-id="42b64-248">설치되어 있는 버전보다 새로운 시험판 버전을 사용할 수 있는 경우 시험판 버전이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="42b64-249">이전 라이브러리 버전으로 다운그레이드하려면 *libman.json* 파일을 직접 편집합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="42b64-250">파일이 저장되면 LibMan [복원 작업](#restore-library-files)에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="42b64-251">이전 버전에서 중복 파일을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="42b64-252">새 버전에서 새로운 파일과 업데이트된 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="42b64-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="42b64-253">추가 자료</span><span class="sxs-lookup"><span data-stu-id="42b64-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="42b64-254">LibMan GitHub 리포지토리</span><span class="sxs-lookup"><span data-stu-id="42b64-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
