# <a name="upload-files-sample-app"></a><span data-ttu-id="b94c5-101">파일 업로드 샘플 앱</span><span class="sxs-lookup"><span data-stu-id="b94c5-101">Upload Files Sample App</span></span>

<span data-ttu-id="b94c5-102">이 샘플 앱은 [ASP.NET Core에서 파일 업로드](https://docs.microsoft.com/aspnet/core/mvc/models/file-uploads) 항목에 설명된 개념을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-102">This sample app demonstrates concepts described in the [Upload files in ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/models/file-uploads) topic.</span></span>

## <a name="security-considerations"></a><span data-ttu-id="b94c5-103">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="b94c5-103">Security considerations</span></span>

<span data-ttu-id="b94c5-104">사용자에게 서버에 파일을 업로드하는 기능을 제공할 때는 주의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-104">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="b94c5-105">공격자가 [서비스 거부](/windows-hardware/drivers/ifs/denial-of-service) 공격을 실행하거나, 바이러스 또는 맬웨어 업로드를 시도하거나, 다른 방법으로 네트워크 및 서버를 손상시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-105">Attackers may execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks, attempt to upload viruses or malware, or attempt to compromise networks and servers in other ways.</span></span>

<span data-ttu-id="b94c5-106">공격이 성공할 가능성을 줄이는 보안 단계는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-106">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="b94c5-107">시스템의 전용 파일 업로드 영역(바람직하게는 시스템 드라이브가 아닌 위치)에 파일을 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-107">Upload files to a dedicated file upload area on the system, preferably to a non-system drive.</span></span> <span data-ttu-id="b94c5-108">전용 위치를 사용하면 업로드된 파일에 대한 보안 조치를 더 쉽게 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-108">Use of a dedicated location makes it easier to impose security measures on uploaded files.</span></span> <span data-ttu-id="b94c5-109">파일 업로드 위치에 대한 실행 권한을 사용하지 않도록 설정합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="b94c5-109">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="b94c5-110">업로드된 파일을 앱과 동일한 디렉터리 트리에 보관하지 마세요.&dagger;</span><span class="sxs-lookup"><span data-stu-id="b94c5-110">Never persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="b94c5-111">앱에 의해 결정된 안전한 파일 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-111">Use a safe file name determined by the app.</span></span> <span data-ttu-id="b94c5-112">사용자 입력으로 제공된 파일 이름이나 업로드된 파일의 신뢰할 수 없는 파일 이름을 사용하지 마세요.&dagger;</span><span class="sxs-lookup"><span data-stu-id="b94c5-112">Don't use a file name provided by user input or the untrusted file name of the uploaded file.&dagger;</span></span>
* <span data-ttu-id="b94c5-113">특정 승인된 파일 확장명 집합만 허용합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="b94c5-113">Only allow a specific set of approved file extensions.&dagger;</span></span>
* <span data-ttu-id="b94c5-114">파일 형식 서명을 확인하여 사용자가 위장한 파일을 업로드하지 못하도록 합니다(예를 들어 *.txt* 확장명을 사용하여 *.exe* 파일을 업로드).&dagger;</span><span class="sxs-lookup"><span data-stu-id="b94c5-114">Check the file format signature to prevent a user from uploading a masqueraded file (for example, uploading an *.exe* file with a *.txt* extension).&dagger;</span></span>
* <span data-ttu-id="b94c5-115">서버에서도 클라이언트 쪽 검사가 수행되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-115">Verify that client-side checks are also performed on the server.</span></span> <span data-ttu-id="b94c5-116">클라이언트 쪽 검사는 피하기 쉽습니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="b94c5-116">Client-side checks are easy to circumvent.&dagger;</span></span>
* <span data-ttu-id="b94c5-117">업로드 크기를 확인하고 예상보다 큰 업로드를 방지합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="b94c5-117">Check the size of the upload and prevent uploads that are larger than expected.&dagger;</span></span>
* <span data-ttu-id="b94c5-118">업로드된 파일이 같은 이름의 파일을 덮어쓰면 안 되는 경우 파일을 업로드하기 전에 데이터베이스 또는 실제 스토리지에서 파일 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-118">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="b94c5-119">**파일이 저장되기 전에 업로드된 콘텐츠에 대해 바이러스/맬웨어 스캐너를 실행합니다.**</span><span class="sxs-lookup"><span data-stu-id="b94c5-119">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="b94c5-120">&dagger;샘플 앱은 조건을 충족하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-120">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="b94c5-121">시스템에 악성 코드를 업로드하는 행위는 흔히 다음을 수행할 수 있는 코드를 실행하기 위한 첫 단계가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-121">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="b94c5-122">시스템을 완전히 장악합니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-122">Completely takeover a system.</span></span>
> * <span data-ttu-id="b94c5-123">시스템 작동이 중단되는 결과로 시스템을 오버로드합니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-123">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="b94c5-124">사용자 또는 시스템 데이터를 손상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-124">Compromise user or system data.</span></span>
> * <span data-ttu-id="b94c5-125">공용 UI에 그래피티를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-125">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="b94c5-126">사용자의 파일을 수락할 때 공격 노출 영역을 줄이는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b94c5-126">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="b94c5-127">무제한 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="b94c5-127">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [<span data-ttu-id="b94c5-128">Azure Security: 사용자의 파일을 허용할 때 적절한 제어가 수행되는지 확인</span><span class="sxs-lookup"><span data-stu-id="b94c5-128">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="b94c5-129">자세한 내용은 [ASP.NET Core에서 파일 업로드](https://docs.microsoft.com/aspnet/core/mvc/models/file-uploads)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b94c5-129">For additional information, see [Upload files in ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/models/file-uploads).</span></span>

## <a name="how-to-use-the-sample"></a><span data-ttu-id="b94c5-130">샘플을 사용하는 방법</span><span class="sxs-lookup"><span data-stu-id="b94c5-130">How to use the sample</span></span>

<span data-ttu-id="b94c5-131">*appsettings.json* 파일에서</span><span class="sxs-lookup"><span data-stu-id="b94c5-131">In the *appsettings.json* file:</span></span>

1. <span data-ttu-id="b94c5-132">저장된 파일의 경로를 설정합니다(`StoredFilesPath`).</span><span class="sxs-lookup"><span data-stu-id="b94c5-132">Set the path for stored files (`StoredFilesPath`).</span></span>

   * <span data-ttu-id="b94c5-133">샘플 앱은 이 값을 `c:\\files`로 설정합니다. 이는 *files*라는 폴더가 시스템의 C: 드라이브 루트에 존재하는 것으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-133">The sample app sets the value to `c:\\files`, which assumes that a folder named *files* exists at the system's C: drive root.</span></span>
   * <span data-ttu-id="b94c5-134">이 경로는 반드시 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-134">The path must exist.</span></span> <span data-ttu-id="b94c5-135">시스템의 C: 드라이브에 *files* 폴더를 만들거나 적절한 위치에 대한 경로를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-135">Create a *files* folder on the system's C: drive or set the path to a suitable location.</span></span>
   * <span data-ttu-id="b94c5-136">앱의 프로세스에는 경로에 대한 읽기/쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-136">The app's process requires read/write permissions to the path.</span></span>
   * <span data-ttu-id="b94c5-137">**중요!**</span><span class="sxs-lookup"><span data-stu-id="b94c5-137">**IMPORTANT!**</span></span> <span data-ttu-id="b94c5-138">경로에서 모든 사용자에 대해 실행 권한을 사용하지 않도록 설정하세요.</span><span class="sxs-lookup"><span data-stu-id="b94c5-138">Disable execute permissions for all users at the path.</span></span>

1. <span data-ttu-id="b94c5-139">파일 크기 제한(`FileSizeLimit`)을 설정하세요(단위: 바이트).</span><span class="sxs-lookup"><span data-stu-id="b94c5-139">Set the file size limit (`FileSizeLimit`) in bytes.</span></span> <span data-ttu-id="b94c5-140">샘플 앱의 기본값 `2097152`(2,097,152바이트)는 파일 업로드를 최대 2MB까지 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="b94c5-140">The sample app's default value of `2097152` (2,097,152 bytes) permits file uploads up to 2 MB.</span></span>
