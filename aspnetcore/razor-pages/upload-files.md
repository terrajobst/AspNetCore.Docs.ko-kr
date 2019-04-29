---
title: ASP.NET Core의 Razor 페이지에 파일 업로드
author: guardrex
description: FileUpload 클래스를 사용 하 여 ASP.NET Core에서 Razor 페이지에 파일을 업로드 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 11/10/2018
ms.custom: mvc, seodec18
uid: razor-pages/upload-files
ms.openlocfilehash: a12653e5dc99ad731de63e151af6dbdbe38ffefc
ms.sourcegitcommit: 8a84ce880b4c40d6694ba6423038f18fc2eb5746
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60165224"
---
# <a name="upload-files-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="5e714-103">ASP.NET Core의 Razor 페이지에 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="5e714-103">Upload files to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="5e714-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="5e714-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5e714-105">이 항목을 기반으로 합니다 [샘플 앱](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample) 에서 <xref:tutorials/razor-pages/razor-pages-start>합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-105">This topic builds upon the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample) in <xref:tutorials/razor-pages/razor-pages-start>.</span></span>

<span data-ttu-id="5e714-106">이 항목에서는 작은 파일 업로딩에 대해 제대로 작동 하는 단순 모델 바인딩을 사용 하 여 파일을 업로드 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-106">This topic shows how to use simple model binding to upload files, which works well for uploading small files.</span></span> <span data-ttu-id="5e714-107">큰 파일 스트리밍에 대한 자세한 내용은 [스트리밍으로 큰 파일 업로드](xref:mvc/models/file-uploads#uploading-large-files-with-streaming)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e714-107">For information on streaming large files, see [Uploading large files with streaming](xref:mvc/models/file-uploads#uploading-large-files-with-streaming).</span></span>

<span data-ttu-id="5e714-108">다음 단계에서는 동영상 일정 파일 업로드 기능이 샘플 앱에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-108">In the following steps, a movie schedule file upload feature is added to the sample app.</span></span> <span data-ttu-id="5e714-109">동영상 일정은 `Schedule` 클래스로 표현됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-109">A movie schedule is represented by a `Schedule` class.</span></span> <span data-ttu-id="5e714-110">클래스에는 일정의 두 버전이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-110">The class includes two versions of the schedule.</span></span> <span data-ttu-id="5e714-111">한 버전(`PublicSchedule`)은 고객에게 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-111">One version is provided to customers, `PublicSchedule`.</span></span> <span data-ttu-id="5e714-112">다른 버전(`PrivateSchedule`)은 회사 직원들이 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-112">The other version is used for company employees, `PrivateSchedule`.</span></span> <span data-ttu-id="5e714-113">각 버전은 별도 파일로 업로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-113">Each version is uploaded as a separate file.</span></span> <span data-ttu-id="5e714-114">이 자습서에서는 서버에 단일 게시물이 있는 페이지에서 두 개의 파일 업로드를 수행하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-114">The tutorial demonstrates how to perform two file uploads from a page with a single POST to the server.</span></span>

<span data-ttu-id="5e714-115">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/upload-files/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5e714-115">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/upload-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="5e714-116">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="5e714-116">Security considerations</span></span>

<span data-ttu-id="5e714-117">사용자에게 서버에 파일을 업로드하는 기능을 제공할 때는 주의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-117">Caution must be taken when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="5e714-118">공격자는 시스템에서 [서비스 거부](/windows-hardware/drivers/ifs/denial-of-service) 및 기타 공격을 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-118">Attackers may execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) and other attacks on a system.</span></span> <span data-ttu-id="5e714-119">공격이 성공할 가능성을 줄이는 몇 가지 보안 단계는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-119">Some security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="5e714-120">시스템의 전용 파일 업로드 영역에 파일을 업로드합니다. 그러면 업로드된 콘텐츠에 보안 조치를 더 쉽게 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-120">Upload files to a dedicated file upload area on the system, which makes it easier to impose security measures on uploaded content.</span></span> <span data-ttu-id="5e714-121">파일 업로드를 허용할 때 업로드 위치에 실행 권한이 사용하지 않도록 설정되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-121">When permitting file uploads, make sure that execute permissions are disabled on the upload location.</span></span>
* <span data-ttu-id="5e714-122">사용자 입력이나 업로드된 파일의 파일 이름이 아니라 앱에서 결정한 안전한 파일 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-122">Use a safe file name determined by the app, not from user input or the file name of the uploaded file.</span></span>
* <span data-ttu-id="5e714-123">특정 승인된 파일 확장명 집합만 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-123">Only allow a specific set of approved file extensions.</span></span>
* <span data-ttu-id="5e714-124">서버에서 클라이언트 쪽 검사가 수행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-124">Verify client-side checks are performed on the server.</span></span> <span data-ttu-id="5e714-125">클라이언트 쪽 검사는 피하기 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-125">Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="5e714-126">업로드 크기를 확인하고 예상보다 큰 업로드를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-126">Check the size of the upload and prevent larger uploads than expected.</span></span>
* <span data-ttu-id="5e714-127">업로드된 콘텐츠에 대해 바이러스/맬웨어 스캐너를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-127">Run a virus/malware scanner on uploaded content.</span></span>

> [!WARNING]
> <span data-ttu-id="5e714-128">시스템에 악성 코드를 업로드하는 행위는 흔히 다음을 수행할 수 있는 코드를 실행하기 위한 첫 단계가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
> * <span data-ttu-id="5e714-129">시스템을 완전히 장악합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-129">Completely takeover a system.</span></span>
> * <span data-ttu-id="5e714-130">시스템이 완전히 고장 나게 하는 결과로 시스템을 오버로드합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-130">Overload a system with the result that the system completely fails.</span></span>
> * <span data-ttu-id="5e714-131">사용자 또는 시스템 데이터를 손상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="5e714-132">공용 인터페이스에 그래피티를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-132">Apply graffiti to a public interface.</span></span>

## <a name="add-a-fileupload-class"></a><span data-ttu-id="5e714-133">FileUpload 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="5e714-133">Add a FileUpload class</span></span>

<span data-ttu-id="5e714-134">파일 업로드 쌍을 처리할 Razor 페이지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-134">Create a Razor Page to handle a pair of file uploads.</span></span> <span data-ttu-id="5e714-135">일정 데이터를 가져오기 위해 페이지에 바인딩되는 `FileUpload` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-135">Add a `FileUpload` class, which is bound to the page to obtain the schedule data.</span></span> <span data-ttu-id="5e714-136">*Models* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-136">Right click the *Models* folder.</span></span> <span data-ttu-id="5e714-137">**추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-137">Select **Add** > **Class**.</span></span> <span data-ttu-id="5e714-138">클래스 이름을 **FileUpload**로 지정하고 다음 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-138">Name the class **FileUpload** and add the following properties:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Models/FileUpload.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/FileUpload.cs)]

::: moniker-end

<span data-ttu-id="5e714-139">클래스에 일정 제목에 대한 속성 및 일정의 각 두 버전에 대한 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-139">The class has a property for the schedule's title and a property for each of the two versions of the schedule.</span></span> <span data-ttu-id="5e714-140">세 가지 속성 모두 필요하며 제목 길이는 3-60자여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-140">All three properties are required, and the title must be 3-60 characters long.</span></span>

## <a name="add-a-helper-method-to-upload-files"></a><span data-ttu-id="5e714-141">파일을 업로드하는 도우미 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="5e714-141">Add a helper method to upload files</span></span>

<span data-ttu-id="5e714-142">업로드된 일정 파일을 처리하기 위한 코드 중복을 방지하려면 먼저 정적 도우미 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-142">To avoid code duplication for processing uploaded schedule files, add a static helper method first.</span></span> <span data-ttu-id="5e714-143">앱에 *유틸리티* 폴더를 만들고 다음 콘텐츠가 포함된 *FileHelpers.cs* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-143">Create a *Utilities* folder in the app and add a *FileHelpers.cs* file with the following content.</span></span> <span data-ttu-id="5e714-144">도우미 메서드 `ProcessFormFile`은 [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) 및 [ModelStateDictionary](/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary)를 사용하여 파일의 크기와 콘텐츠를 포함하는 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-144">The helper method, `ProcessFormFile`, takes an [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) and [ModelStateDictionary](/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary) and returns a string containing the file's size and content.</span></span> <span data-ttu-id="5e714-145">콘텐츠 형식 및 길이를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-145">The content type and length are checked.</span></span> <span data-ttu-id="5e714-146">파일이 유효성 검사를 통과하지 못한 경우 오류가 `ModelState`에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-146">If the file doesn't pass a validation check, an error is added to the `ModelState`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Utilities/FileHelpers.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Utilities/FileHelpers.cs)]

::: moniker-end

### <a name="save-the-file-to-disk"></a><span data-ttu-id="5e714-147">디스크에 파일 저장</span><span class="sxs-lookup"><span data-stu-id="5e714-147">Save the file to disk</span></span>

<span data-ttu-id="5e714-148">샘플 앱은 업로드된 파일을 데이터베이스 필드에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-148">The sample app saves uploaded files into database fields.</span></span> <span data-ttu-id="5e714-149">디스크에 파일을 저장하려면 [FileStream](/dotnet/api/system.io.filestream)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-149">To save a file to disk, use a [FileStream](/dotnet/api/system.io.filestream).</span></span> <span data-ttu-id="5e714-150">다음 예제에서는 `FileUpload.UploadPublicSchedule`에 저장된 파일을 `OnPostAsync` 메서드의 `FileStream`으로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-150">The following example copies a file held by `FileUpload.UploadPublicSchedule` to a `FileStream` in an `OnPostAsync` method.</span></span> <span data-ttu-id="5e714-151">`FileStream`은 제공된 `<PATH-AND-FILE-NAME>`의 디스크에 파일을 씁니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-151">The `FileStream` writes the file to disk at the `<PATH-AND-FILE-NAME>` provided:</span></span>

```csharp
public async Task<IActionResult> OnPostAsync()
{
    // Perform an initial check to catch FileUpload class attribute violations.
    if (!ModelState.IsValid)
    {
        return Page();
    }

    var filePath = "<PATH-AND-FILE-NAME>";

    using (var fileStream = new FileStream(filePath, FileMode.Create))
    {
        await FileUpload.UploadPublicSchedule.CopyToAsync(fileStream);
    }

    return RedirectToPage("./Index");
}
```

<span data-ttu-id="5e714-152">작업자 프로세스는 `filePath`로 지정된 위치에 대한 쓰기 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-152">The worker process must have write permissions to the location specified by `filePath`.</span></span>

> [!NOTE]
> <span data-ttu-id="5e714-153">`filePath`에는 *파일 이름*이 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-153">The `filePath` *must* include the file name.</span></span> <span data-ttu-id="5e714-154">파일 이름이 제공되지 않는 경우 런타임 시 [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception)이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-154">If the file name isn't provided, an [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception) is thrown at runtime.</span></span>

> [!WARNING]
> <span data-ttu-id="5e714-155">업로드된 파일은 앱과 동일한 디렉터리 트리에 보관하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="5e714-155">Never persist uploaded files in the same directory tree as the app.</span></span>
>
> <span data-ttu-id="5e714-156">코드 샘플에서는 악성 파일 업로드에 대한 서버 쪽 보호 기능을 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-156">The code sample provides no server-side protection against malicious file uploads.</span></span> <span data-ttu-id="5e714-157">사용자의 파일을 수락할 때 공격 노출 영역을 줄이는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e714-157">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="5e714-158">무제한 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="5e714-158">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [<span data-ttu-id="5e714-159">Azure 보안: 사용자의 파일을 허용할 때 적절 한 제어가 수행 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-159">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

### <a name="save-the-file-to-azure-blob-storage"></a><span data-ttu-id="5e714-160">Azure Blob Storage에 파일 저장</span><span class="sxs-lookup"><span data-stu-id="5e714-160">Save the file to Azure Blob Storage</span></span>

<span data-ttu-id="5e714-161">Azure Blob Storage에 파일 콘텐츠를 업로드하려면 [.NET을 사용하여 Azure Blob Storage 시작](/azure/storage/blobs/storage-dotnet-how-to-use-blobs)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5e714-161">To upload file content to Azure Blob Storage, see [Get started with Azure Blob Storage using .NET](/azure/storage/blobs/storage-dotnet-how-to-use-blobs).</span></span> <span data-ttu-id="5e714-162">이 항목에서는 [UploadFromStream](/dotnet/api/microsoft.windowsazure.storage.file.cloudfile.uploadfromstreamasync)을 사용하여 Blob 스토리지에 [FileStream](/dotnet/api/system.io.filestream)을 저장하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-162">The topic demonstrates how to use [UploadFromStream](/dotnet/api/microsoft.windowsazure.storage.file.cloudfile.uploadfromstreamasync) to save a [FileStream](/dotnet/api/system.io.filestream) to blob storage.</span></span>

## <a name="add-the-schedule-class"></a><span data-ttu-id="5e714-163">일정 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="5e714-163">Add the Schedule class</span></span>

<span data-ttu-id="5e714-164">*Models* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-164">Right click the *Models* folder.</span></span> <span data-ttu-id="5e714-165">**추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-165">Select **Add** > **Class**.</span></span> <span data-ttu-id="5e714-166">클래스 이름을 **Schedule**로 지정하고 다음 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-166">Name the class **Schedule** and add the following properties:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Models/Schedule.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/Schedule.cs)]

::: moniker-end

<span data-ttu-id="5e714-167">클래스는 예약된 데이터가 렌더링될 때 친숙한 제목 및 서식 지정을 만드는 `Display` 및 `DisplayFormat` 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-167">The class uses `Display` and `DisplayFormat` attributes, which produce friendly titles and formatting when the schedule data is rendered.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="update-the-razorpagesmoviecontext"></a><span data-ttu-id="5e714-168">RazorPagesMovieContext 업데이트</span><span class="sxs-lookup"><span data-stu-id="5e714-168">Update the RazorPagesMovieContext</span></span>

<span data-ttu-id="5e714-169">일정의 경우 `RazorPagesMovieContext`(*Data/RazorPagesMovieContext.cs*)에서 `DbSet`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-169">Specify a `DbSet` in the `RazorPagesMovieContext` (*Data/RazorPagesMovieContext.cs*) for the schedules:</span></span>

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Data/RazorPagesMovieContext.cs?highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

## <a name="update-the-moviecontext"></a><span data-ttu-id="5e714-170">MovieContext 업데이트</span><span class="sxs-lookup"><span data-stu-id="5e714-170">Update the MovieContext</span></span>

<span data-ttu-id="5e714-171">일정의 경우 `MovieContext`(*Models/MovieContext.cs*)에서 `DbSet`을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-171">Specify a `DbSet` in the `MovieContext` (*Models/MovieContext.cs*) for the schedules:</span></span>

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/MovieContext.cs?highlight=13)]

::: moniker-end

## <a name="add-the-schedule-table-to-the-database"></a><span data-ttu-id="5e714-172">데이터베이스에 일정 테이블 추가</span><span class="sxs-lookup"><span data-stu-id="5e714-172">Add the Schedule table to the database</span></span>

<span data-ttu-id="5e714-173">패키지 관리자 콘솔 (PMC)을 엽니다. **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-173">Open the Package Manger Console (PMC): **Tools** > **NuGet Package Manager** > **Package Manager Console**.</span></span>

![PMC 메뉴](upload-files/_static/pmc.png)

<span data-ttu-id="5e714-175">PMC에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-175">In the PMC, execute the following commands.</span></span> <span data-ttu-id="5e714-176">이러한 명령은 `Schedule` 테이블을 데이터베이스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-176">These commands add a `Schedule` table to the database:</span></span>

```powershell
Add-Migration AddScheduleTable
Update-Database
```

## <a name="add-a-file-upload-razor-page"></a><span data-ttu-id="5e714-177">파일 업로드 Razor 페이지 추가</span><span class="sxs-lookup"><span data-stu-id="5e714-177">Add a file upload Razor Page</span></span>

<span data-ttu-id="5e714-178">*페이지* 폴더에서 *Schedules* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-178">In the *Pages* folder, create a *Schedules* folder.</span></span> <span data-ttu-id="5e714-179">*일정* 폴더에서 다음 콘텐츠를 포함한 일정을 업로드하기 위한 *Index.cshtml*이라는 페이지를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-179">In the *Schedules* folder, create a page named *Index.cshtml* for uploading a schedule with the following content:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-cshtml[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-cshtml[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

::: moniker-end

<span data-ttu-id="5e714-180">각 양식 그룹에는 각 클래스 속성의 이름을 표시하는 **\<레이블>** 이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-180">Each form group includes a **\<label>** that displays the name of each class property.</span></span> <span data-ttu-id="5e714-181">`FileUpload` 모델의 `Display` 특성은 레이블에 대한 표시 값을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-181">The `Display` attributes in the `FileUpload` model provide the display values for the labels.</span></span> <span data-ttu-id="5e714-182">예를 들어 `UploadPublicSchedule` 속성의 표시 이름이 `[Display(Name="Public Schedule")]`로 설정되어 있으면 양식이 렌더링할 때 레이블에서 “공개 일정”을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-182">For example, the `UploadPublicSchedule` property's display name is set with `[Display(Name="Public Schedule")]` and thus displays "Public Schedule" in the label when the form renders.</span></span>

<span data-ttu-id="5e714-183">각 양식 그룹은 유효성 검사 **\<span>** 을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-183">Each form group includes a validation **\<span>**.</span></span> <span data-ttu-id="5e714-184">사용자의 입력이 `FileUpload` 클래스에 설정된 속성 특성을 충족하지 못하거나 `ProcessFormFile` 메서드 파일 유효성 검사 확인에 실패하는 경우 모델 유효성 검사에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-184">If the user's input fails to meet the property attributes set in the `FileUpload` class or if any of the `ProcessFormFile` method file validation checks fail, the model fails to validate.</span></span> <span data-ttu-id="5e714-185">모델 유효성 검사에 실패하면 유용한 유효성 검사 메시지가 사용자에게 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-185">When model validation fails, a helpful validation message is rendered to the user.</span></span> <span data-ttu-id="5e714-186">예를 들어 `Title` 속성은 `[Required]` 및 `[StringLength(60, MinimumLength = 3)]`으로 주석이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-186">For example, the `Title` property is annotated with `[Required]` and `[StringLength(60, MinimumLength = 3)]`.</span></span> <span data-ttu-id="5e714-187">사용자가 제목을 입력하지 못하면 값이 필수 항목임을 나타내는 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-187">If the user fails to supply a title, they receive a message indicating that a value is required.</span></span> <span data-ttu-id="5e714-188">사용자가 3자 미만 또는 60자 이상의 값을 입력하는 경우 값의 길이가 잘못되었음을 나타내는 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-188">If the user enters a value less than three characters or more than sixty characters, they receive a message indicating that the value has an incorrect length.</span></span> <span data-ttu-id="5e714-189">콘텐츠가 없는 파일이 제공되는 경우 해당 파일이 비어 있음을 나타내는 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-189">If a file is provided that has no content, a message appears indicating that the file is empty.</span></span>

## <a name="add-the-page-model"></a><span data-ttu-id="5e714-190">페이지 모델 추가</span><span class="sxs-lookup"><span data-stu-id="5e714-190">Add the page model</span></span>

<span data-ttu-id="5e714-191">*Schedules* 폴더에 페이지 모델(*Index.cshtml.cs*)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-191">Add the page model (*Index.cshtml.cs*) to the *Schedules* folder:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

::: moniker-end

<span data-ttu-id="5e714-192">페이지 모델(*Index.cshtml.cs*의 `IndexModel`)은 `FileUpload` 클래스를 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-192">The page model (`IndexModel` in *Index.cshtml.cs*) binds the `FileUpload` class:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="5e714-193">또한 모델은 일정(`IList<Schedule>`)의 목록을 사용하여 데이터베이스에 저장된 일정을 페이지에 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-193">The model also uses a list of the schedules (`IList<Schedule>`) to display the schedules stored in the database on the page:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

::: moniker-end

<span data-ttu-id="5e714-194">페이지가 `OnGetAsync`로 로드되면 `Schedules`은 데이터베이스에서 채워지고 로드된 일정의 HTML 테이블을 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-194">When the page loads with `OnGetAsync`, `Schedules` is populated from the database and used to generate an HTML table of loaded schedules:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

::: moniker-end

<span data-ttu-id="5e714-195">양식이 서버에 게시될 때 `ModelState`가 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-195">When the form is posted to the server, the `ModelState` is checked.</span></span> <span data-ttu-id="5e714-196">유효하지 않은 경우 `Schedule`이 다시 작성되고 페이지는 페이지 유효성 검사에 실패한 이유를 나타내는 하나 이상의 유효성 검사 메시지로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-196">If invalid, `Schedule` is rebuilt, and the page renders with one or more validation messages stating why page validation failed.</span></span> <span data-ttu-id="5e714-197">유효한 경우 일정의 두 버전에 대한 파일 업로드를 완료하고 데이터를 저장하도록 새 `Schedule` 개체를 만드는 데 `FileUpload` 속성이 *OnPostAsync*에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-197">If valid, the `FileUpload` properties are used in *OnPostAsync* to complete the file upload for the two versions of the schedule and to create a new `Schedule` object to store the data.</span></span> <span data-ttu-id="5e714-198">일정이 다음 데이터베이스에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-198">The schedule is then saved to the database:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

::: moniker-end

## <a name="link-the-file-upload-razor-page"></a><span data-ttu-id="5e714-199">파일 업로드 Razor 페이지 연결</span><span class="sxs-lookup"><span data-stu-id="5e714-199">Link the file upload Razor Page</span></span>

<span data-ttu-id="5e714-200">*Pages/Shared/_Layout.cshtml*을 열고 일정 페이지에 도달하는 탐색 모음에 대한 링크를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-200">Open *Pages/Shared/_Layout.cshtml* and add a link to the navigation bar to reach the Schedules page:</span></span>

```cshtml
<div class="navbar-collapse collapse">
    <ul class="nav navbar-nav">
        <li><a asp-page="/Index">Home</a></li>
        <li><a asp-page="/Schedules/Index">Schedules</a></li>
        <li><a asp-page="/About">About</a></li>
        <li><a asp-page="/Contact">Contact</a></li>
    </ul>
</div>
```

## <a name="add-a-page-to-confirm-schedule-deletion"></a><span data-ttu-id="5e714-201">일정 삭제를 확인하는 페이지 추가</span><span class="sxs-lookup"><span data-stu-id="5e714-201">Add a page to confirm schedule deletion</span></span>

<span data-ttu-id="5e714-202">사용자가 일정을 삭제하기 위해 클릭할 때 작업을 취소할 기회가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-202">When the user clicks to delete a schedule, a chance to cancel the operation is provided.</span></span> <span data-ttu-id="5e714-203">삭제 확인 페이지(*Delete.cshtml*)를 *Schedules* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-203">Add a delete confirmation page (*Delete.cshtml*) to the *Schedules* folder:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-cshtml[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-cshtml[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

::: moniker-end

<span data-ttu-id="5e714-204">페이지 모델(*Delete.cshtml.cs*)은 요청의 경로 데이터에서 `id`로 식별되는 단일 일정을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-204">The page model (*Delete.cshtml.cs*) loads a single schedule identified by `id` in the request's route data.</span></span> <span data-ttu-id="5e714-205">*Delete.cshtml.cs* 파일을 *Schedules* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-205">Add the *Delete.cshtml.cs* file to the *Schedules* folder:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

::: moniker-end

<span data-ttu-id="5e714-206">`OnPostAsync` 메서드는 해당 `id`에 의해 일정 삭제를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-206">The `OnPostAsync` method handles deleting the schedule by its `id`:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

::: moniker-end

<span data-ttu-id="5e714-207">일정을 성공적으로 삭제한 후 `RedirectToPage`는 사용자에게 일정 *Index.cshtml* 페이지를 다시 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-207">After successfully deleting the schedule, the `RedirectToPage` sends the user back to the schedules *Index.cshtml* page.</span></span>

## <a name="the-working-schedules-razor-page"></a><span data-ttu-id="5e714-208">작업 일정 Razor 페이지</span><span class="sxs-lookup"><span data-stu-id="5e714-208">The working Schedules Razor Page</span></span>

<span data-ttu-id="5e714-209">페이지가 일정 제목에 대해 로드, 레이블 및 입력을 수행하면 공개 일정 및 개인 일정이 제출 단추로 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-209">When the page loads, labels and inputs for schedule title, public schedule, and private schedule are rendered with a submit button:</span></span>

![유효성 검사 오류가 없고 필드가 빈 초기 로드로 표시된 일정 Razor 페이지](upload-files/_static/browser1.png)

<span data-ttu-id="5e714-211">필드를 채우지 않고 **업로드** 단추를 선택하면 모델의 `[Required]` 특성을 위반하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-211">Selecting the **Upload** button without populating any of the fields violates the `[Required]` attributes on the model.</span></span> <span data-ttu-id="5e714-212">`ModelState`가 잘못되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-212">The `ModelState` is invalid.</span></span> <span data-ttu-id="5e714-213">유효성 검사 오류 메시지가 사용자에게 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-213">The validation error messages are displayed to the user:</span></span>

![유효성 검사 오류 메시지가 각 입력된 컨트롤 옆에 표시됩니다.](upload-files/_static/browser2.png)

<span data-ttu-id="5e714-215">두 개의 문자를 **제목** 필드에 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-215">Type two letters into the **Title** field.</span></span> <span data-ttu-id="5e714-216">유효성 검사 메시지가 제목이 3-60자 사이여야 함을 나타내는 것으로 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-216">The validation message changes to indicate that the title must be between 3-60 characters:</span></span>

![변경된 유효성 검사 메시지](upload-files/_static/browser3.png)

<span data-ttu-id="5e714-218">하나 이상의 일정이 업로드되면 **로드된 일정** 섹션이 로드된 일정을 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-218">When one or more schedules are uploaded, the **Loaded Schedules** section renders the loaded schedules:</span></span>

![각 일정의 제목, 로드된 날짜(UTC), 공용 버전 파일 크기 및 전용 버전을 표시하는 로드된 일정의 테이블](upload-files/_static/browser4.png)

<span data-ttu-id="5e714-220">사용자가 여기서 **삭제** 링크를 클릭하면 삭제 작업을 확인하거나 취소할 수 있는 기회가 있는 삭제 확인 보기로 이동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-220">The user can click the **Delete** link from there to reach the delete confirmation view, where they have an opportunity to confirm or cancel the delete operation.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="5e714-221">문제 해결</span><span class="sxs-lookup"><span data-stu-id="5e714-221">Troubleshooting</span></span>

<span data-ttu-id="5e714-222">문제 해결 정보는 `IFormFile` 참조에 업로드 [ASP.NET Core에서 파일 업로드: 문제 해결](xref:mvc/models/file-uploads#troubleshooting)합니다.</span><span class="sxs-lookup"><span data-stu-id="5e714-222">For troubleshooting information with `IFormFile` uploading, see [File uploads in ASP.NET Core: Troubleshooting](xref:mvc/models/file-uploads#troubleshooting).</span></span>
