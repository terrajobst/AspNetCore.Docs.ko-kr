* `-F|--no-formatting`

  <span data-ttu-id="9f4da-101">HTTP 응답 형식을 표시하지 않는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="9f4da-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="9f4da-102">HTTP 요청 헤더를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="9f4da-102">Sets an HTTP request header.</span></span> <span data-ttu-id="9f4da-103">다음 두 가지 값 형식이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="9f4da-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response`

  <span data-ttu-id="9f4da-104">전체 HTTP 응답(헤더 및 본문 포함)을 작성해야 하는 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9f4da-104">Specifies a file to which the entire HTTP response (including headers and body) should be written.</span></span> <span data-ttu-id="9f4da-105">`--response "C:\response.txt"`)을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="9f4da-105">For example, `--response "C:\response.txt"`.</span></span> <span data-ttu-id="9f4da-106">파일이 없는 경우 새로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="9f4da-106">The file is created if it doesn't exist.</span></span>

* `--response:body`

  <span data-ttu-id="9f4da-107">HTTP 응답 본문을 작성할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9f4da-107">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="9f4da-108">`--response:body "C:\response.json"`)을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="9f4da-108">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="9f4da-109">파일이 없는 경우 새로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="9f4da-109">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="9f4da-110">HTTP 응답 헤더를 작성할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9f4da-110">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="9f4da-111">`--response:headers "C:\response.txt"`)을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="9f4da-111">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="9f4da-112">파일이 없는 경우 새로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="9f4da-112">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="9f4da-113">HTTP 응답의 스트리밍을 사용하도록 설정하는 플래그입니다.</span><span class="sxs-lookup"><span data-stu-id="9f4da-113">A flag whose presence enables streaming of the HTTP response.</span></span>
