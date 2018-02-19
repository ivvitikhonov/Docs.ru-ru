---
title: "Активация ПО промежуточного слоя на основе фабрики в ASP.NET Core"
author: guardrex
description: "В этой статье приводятся сведения о том, как использовать строго типизированное ПО промежуточного слоя с реализацией активации на основе фабрики в ASP.NET Core."
ms.author: riande
manager: wpickett
ms.custom: mvc
ms.date: 01/29/2018
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 57ff9db2edbf307f2442443dc14e69b0498f7475
ms.sourcegitcommit: f2a11a89037471a77ad68a67533754b7bb8303e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="fd879-103">Активация ПО промежуточного слоя на основе фабрики в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fd879-103">Factory-based middleware activation in ASP.NET Core</span></span>

<span data-ttu-id="fd879-104">Автор [Люк Латэм](https://github.com/guardrex) (Luke Latham)</span><span class="sxs-lookup"><span data-stu-id="fd879-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="fd879-105">[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory)/[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) — это точка расширяемости для активации [ПО промежуточного слоя](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="fd879-105">[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory)/[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="fd879-106">Методы расширения `UseMiddleware` проверяют, реализует ли зарегистрированный тип ПО промежуточного слоя `IMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="fd879-106">`UseMiddleware` extension methods check if a middleware's registered type implements `IMiddleware`.</span></span> <span data-ttu-id="fd879-107">Если да, то экземпляр `IMiddlewareFactory`, зарегистрированный в контейнере, используется для разрешения реализации `IMiddleware` вместо стандартной логики активации ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="fd879-107">If it does, the `IMiddlewareFactory` instance registered in the container is used to resolve the `IMiddleware` implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="fd879-108">ПО промежуточного слоя регистрируется как ограниченная или временная служба в контейнере служб приложения.</span><span class="sxs-lookup"><span data-stu-id="fd879-108">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="fd879-109">Преимущества:</span><span class="sxs-lookup"><span data-stu-id="fd879-109">Benefits:</span></span>

* <span data-ttu-id="fd879-110">Активация по запросу (внедрение ограниченных служб)</span><span class="sxs-lookup"><span data-stu-id="fd879-110">Activation per request (injection of scoped services)</span></span>
* <span data-ttu-id="fd879-111">Строгая типизация ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="fd879-111">Strong typing of middleware</span></span>

<span data-ttu-id="fd879-112">`IMiddleware` активируется по запросу, благодаря чему ограниченные службы могут внедряться в конструктор ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="fd879-112">`IMiddleware` is activated per request, so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="fd879-113">[Просмотреть или скачать образец кода](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/sample) ([как скачивать](xref:tutorials/index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fd879-113">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/sample) ([how to download](xref:tutorials/index#how-to-download-a-sample))</span></span>

<span data-ttu-id="fd879-114">В примере приложения показана активация ПО промежуточного слоя следующими способами:</span><span class="sxs-lookup"><span data-stu-id="fd879-114">The sample app demonstrates middleware activated by:</span></span>

* <span data-ttu-id="fd879-115">Стандартный (`ConventionalMiddleware`).</span><span class="sxs-lookup"><span data-stu-id="fd879-115">Convention (`ConventionalMiddleware`).</span></span> <span data-ttu-id="fd879-116">Дополнительные сведения о стандартной активации ПО промежуточного слоя см. [здесь](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="fd879-116">For more information on conventional middleware activation, see the [Middleware](xref:fundamentals/middleware/index) topic.</span></span>
* <span data-ttu-id="fd879-117">Реализация [IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) (`IMiddlewareMiddleware`).</span><span class="sxs-lookup"><span data-stu-id="fd879-117">An [IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) implementation (`IMiddlewareMiddleware`).</span></span> <span data-ttu-id="fd879-118">По умолчанию ПО промежуточного слоя активируется [классом MiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.middlewarefactory).</span><span class="sxs-lookup"><span data-stu-id="fd879-118">The default [MiddlewareFactory class](/dotnet/api/microsoft.aspnetcore.http.middlewarefactory) activates the middleware.</span></span>

<span data-ttu-id="fd879-119">Реализации ПО промежуточного слоя функционируют идентичным образом и записывают значение, предоставленное в параметре строки запроса (`key`).</span><span class="sxs-lookup"><span data-stu-id="fd879-119">The middleware implementations function identically and record the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="fd879-120">ПО промежуточного слоя использует внедренный контекст базы данных (ограниченная служба) для записи значения строки запроса в базу данных, выполняющуюся в памяти.</span><span class="sxs-lookup"><span data-stu-id="fd879-120">The middlewares use an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

## <a name="imiddleware"></a><span data-ttu-id="fd879-121">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="fd879-121">IMiddleware</span></span>

<span data-ttu-id="fd879-122">[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) определяет ПО промежуточного слоя для конвейера запросов приложения.</span><span class="sxs-lookup"><span data-stu-id="fd879-122">[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="fd879-123">Метод [InvokeAsync(HttpContext, RequestDelegate)](/dotnet/api/microsoft.aspnetcore.http.imiddleware.invokeasync#Microsoft_AspNetCore_Http_IMiddleware_InvokeAsync_Microsoft_AspNetCore_Http_HttpContext_Microsoft_AspNetCore_Http_RequestDelegate_) обрабатывает запрос и возвращает объект `Task`, который представляет выполнение ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="fd879-123">The [InvokeAsync(HttpContext, RequestDelegate)](/dotnet/api/microsoft.aspnetcore.http.imiddleware.invokeasync#Microsoft_AspNetCore_Http_IMiddleware_InvokeAsync_Microsoft_AspNetCore_Http_HttpContext_Microsoft_AspNetCore_Http_RequestDelegate_) method handles requests and returns a `Task` that represents the execution of the middleware.</span></span>

<span data-ttu-id="fd879-124">Стандартная активация ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="fd879-124">Middleware activated by convention:</span></span>

[!code-csharp[Main](extensibility/sample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="fd879-125">Активация ПО промежуточного слоя с помощью `MiddlewareFactory`:</span><span class="sxs-lookup"><span data-stu-id="fd879-125">Middleware activated by `MiddlewareFactory`:</span></span>

[!code-csharp[Main](extensibility/sample/Middleware/IMiddlewareMiddleware.cs?name=snippet1)]

<span data-ttu-id="fd879-126">Расширения, создаваемые для ПО промежуточного слоя:</span><span class="sxs-lookup"><span data-stu-id="fd879-126">Extensions are created for the middlewares:</span></span>

[!code-csharp[Main](extensibility/sample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="fd879-127">Невозможна передача объектов в ПО промежуточного слоя, активируемое на основе фабрики с помощью `UseMiddleware`:</span><span class="sxs-lookup"><span data-stu-id="fd879-127">It isn't possible to pass objects to the factory-activated middleware with `UseMiddleware`:</span></span>

```csharp
public static IApplicationBuilder UseIMiddlewareMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<IMiddlewareMiddleware>(option);
}
```

<span data-ttu-id="fd879-128">Активируемое на основе фабрики ПО промежуточного слоя добавляется во встроенный контейнер в файле *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="fd879-128">The factory-activated middleware is added to the built-in container in *Startup.cs*:</span></span>

[!code-csharp[Main](extensibility/sample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="fd879-129">Оба ПО промежуточного слоя регистрируются в конвейере обработки запросов в `Configure`:</span><span class="sxs-lookup"><span data-stu-id="fd879-129">Both middlewares are registered in the request processing pipeline in `Configure`:</span></span>

[!code-csharp[Main](extensibility/sample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="fd879-130">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="fd879-130">IMiddlewareFactory</span></span>

<span data-ttu-id="fd879-131">[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) предоставляет методы для создания ПО промежуточного слоя.</span><span class="sxs-lookup"><span data-stu-id="fd879-131">[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) provides methods to create middleware.</span></span> <span data-ttu-id="fd879-132">Реализация ПО промежуточного слоя на основе фабрики регистрируется в контейнере в виде ограниченной службы.</span><span class="sxs-lookup"><span data-stu-id="fd879-132">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="fd879-133">Реализация `IMiddlewareFactory` по умолчанию, [MiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.middlewarefactory), находится в пакете [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) ([ссылки на источник](https://github.com/aspnet/HttpAbstractions/blob/release/2.0/src/Microsoft.AspNetCore.Http/MiddlewareFactory.cs)).</span><span class="sxs-lookup"><span data-stu-id="fd879-133">The default `IMiddlewareFactory` implementation, [MiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.middlewarefactory), is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package ([reference source](https://github.com/aspnet/HttpAbstractions/blob/release/2.0/src/Microsoft.AspNetCore.Http/MiddlewareFactory.cs)).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fd879-134">Дополнительные ресурсы</span><span class="sxs-lookup"><span data-stu-id="fd879-134">Additional resources</span></span>

* [<span data-ttu-id="fd879-135">ПО промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="fd879-135">Middleware</span></span>](xref:fundamentals/middleware/index)