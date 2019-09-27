# <a name="aspnet-core-middleware-extensibility-sample"></a><span data-ttu-id="4739c-101">ASP.NET Core 미들웨어 확장성 샘플</span><span class="sxs-lookup"><span data-stu-id="4739c-101">ASP.NET Core Middleware Extensibility Sample</span></span>

<span data-ttu-id="4739c-102">이 샘플에서는 [ASP.NET Core의 팩터리 기반 미들웨어 활성화](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility)에 설명된 시나리오를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="4739c-102">This sample demonstrates the scenarios described in [Factory-based middleware activation in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility).</span></span>

<span data-ttu-id="4739c-103">샘플 앱은 다음에 의해 활성화되는 미들웨어를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4739c-103">The sample app demonstrates middleware activated by:</span></span>

* <span data-ttu-id="4739c-104">규칙.</span><span class="sxs-lookup"><span data-stu-id="4739c-104">Convention.</span></span> <span data-ttu-id="4739c-105">규칙에 따른 미들웨어 활성화에 대한 자세한 내용은 [미들웨어](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/) 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="4739c-105">For more information on conventional middleware activation, see the [Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/) topic.</span></span>
* <span data-ttu-id="4739c-106">[IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="4739c-106">An [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) implementation.</span></span> <span data-ttu-id="4739c-107">기본 [IMiddlewareFactory 클래스](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory)가 미들웨어를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="4739c-107">The default [IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) class activates the middleware.</span></span>

<span data-ttu-id="4739c-108">미들웨어 구현은 동일하게 작동하며, 쿼리 문자열 매개 변수(`key`)에서 제공한 값을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="4739c-108">The middleware implementations function identically and record the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="4739c-109">미들웨어는 주입된 데이터베이스 컨텍스트(범위가 지정된 서비스)를 사용하여 메모리 내 데이터베이스에 쿼리 문자열 값을 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="4739c-109">The middlewares use an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>