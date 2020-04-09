## <a name="troubleshoot"></a><span data-ttu-id="2f722-101">문제 해결</span><span class="sxs-lookup"><span data-stu-id="2f722-101">Troubleshoot</span></span>

### <a name="cookies-and-site-data"></a><span data-ttu-id="2f722-102">쿠키 및 사이트 데이터</span><span class="sxs-lookup"><span data-stu-id="2f722-102">Cookies and site data</span></span>

<span data-ttu-id="2f722-103">쿠키 및 사이트 데이터는 앱 업데이트 전반에 걸쳐 유지되며 테스트 및 문제 해결을 방해할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2f722-103">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="2f722-104">앱 코드를 변경하거나, 공급자를 사용하거나, 공급자 앱 구성을 변경할 때 다음을 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="2f722-104">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="2f722-105">사용자 로그인 쿠키</span><span class="sxs-lookup"><span data-stu-id="2f722-105">User sign-in cookies</span></span>
* <span data-ttu-id="2f722-106">앱 쿠키</span><span class="sxs-lookup"><span data-stu-id="2f722-106">App cookies</span></span>
* <span data-ttu-id="2f722-107">캐시되고 저장된 사이트 데이터</span><span class="sxs-lookup"><span data-stu-id="2f722-107">Cached and stored site data</span></span>

<span data-ttu-id="2f722-108">느린 쿠키 및 사이트 데이터가 테스트 및 문제 해결을 방해하지 않도록 하는 한 가지 방법은 다음과 같은 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2f722-108">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="2f722-109">브라우저가 닫혀있을 때마다 모든 쿠키 및 사이트 데이터를 삭제하도록 구성할 수 있는 테스트를 위해 브라우저를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f722-109">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
* <span data-ttu-id="2f722-110">앱, 테스트 사용자 또는 공급자 구성에 대한 변경 간에 브라우저를 닫습니다.</span><span class="sxs-lookup"><span data-stu-id="2f722-110">Close the browser between any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="2f722-111">서버 앱 실행</span><span class="sxs-lookup"><span data-stu-id="2f722-111">Run the Server app</span></span>

<span data-ttu-id="2f722-112">호스팅된 Blazor 앱을 테스트하고 문제를 해결할 **때는 Server** 프로젝트에서 앱을 실행하고 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2f722-112">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **Server** project.</span></span> <span data-ttu-id="2f722-113">예를 들어 Visual Studio에서 다음 방법 중 한 가지로 앱을 시작하기 전에 서버 프로젝트가 **솔루션 탐색기에서** 강조 표시되어 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="2f722-113">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="2f722-114">**실행** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="2f722-114">Select the **Run** button.</span></span>
* <span data-ttu-id="2f722-115">메뉴에서 **디버그** > **시작 디버깅을** 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="2f722-115">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="2f722-116"><kbd>F5</kbd>키를 누릅니다.</span><span class="sxs-lookup"><span data-stu-id="2f722-116">Press <kbd>F5</kbd>.</span></span>
