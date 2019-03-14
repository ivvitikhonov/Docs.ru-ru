---
title: Политика схем в ASP.NET Core
author: rick-anderson
description: Схемы политики проверки подлинности облегчают имеют схему проверки подлинности единого логического
ms.author: riande
ms.date: 2/28/2019
uid: security/authentication/policyschemes
ms.openlocfilehash: c310b61e14df2b7846e32a602bb75914a5850aff
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346780"
---
# <a name="policy-schemes-in-aspnet-core"></a><span data-ttu-id="4ba9b-103">Политика схем в ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ba9b-103">Policy schemes in ASP.NET Core</span></span>

<span data-ttu-id="4ba9b-104">Схемы проверки подлинности политики упрощают имеют схему проверки подлинности единого логического потенциально могут использовать несколько подходов.</span><span class="sxs-lookup"><span data-stu-id="4ba9b-104">Authentication policy schemes make it easier to have a single logical authentication scheme potentially use multiple approaches.</span></span> <span data-ttu-id="4ba9b-105">Например схему политики может использовать Google для проблем и файл cookie проверки подлинности для всего остального.</span><span class="sxs-lookup"><span data-stu-id="4ba9b-105">For example, a policy scheme might use Google authentication for challenges, and cookie authentication for everything else.</span></span> <span data-ttu-id="4ba9b-106">Схемы политики проверки подлинности сделать.</span><span class="sxs-lookup"><span data-stu-id="4ba9b-106">Authentication policy schemes make it:</span></span>

* <span data-ttu-id="4ba9b-107">Легко пересылать любое действие проверки подлинности в другую схему.</span><span class="sxs-lookup"><span data-stu-id="4ba9b-107">Easy to forward any authentication action to another scheme.</span></span>
* <span data-ttu-id="4ba9b-108">Вперед, динамически на основе запроса.</span><span class="sxs-lookup"><span data-stu-id="4ba9b-108">Forward dynamically based on the request.</span></span>

<span data-ttu-id="4ba9b-109">Все схемы проверки подлинности, использование производных <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> и связанным [ `AuthenticationHandler<TOptions>` ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span><span class="sxs-lookup"><span data-stu-id="4ba9b-109">All authentication schemes that use derived <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> and the associated [`AuthenticationHandler<TOptions>`](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):</span></span>

* <span data-ttu-id="4ba9b-110">— Автоматически политики схемы в ASP.NET Core 2.1 и более поздних версий.</span><span class="sxs-lookup"><span data-stu-id="4ba9b-110">Are automatically policy schemes in ASP.NET Core 2.1 and later.</span></span>
* <span data-ttu-id="4ba9b-111">Можно включить, используя настройки параметров схемы.</span><span class="sxs-lookup"><span data-stu-id="4ba9b-111">Can be enabled via configuring the scheme's options.</span></span>

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a><span data-ttu-id="4ba9b-112">Примеры</span><span class="sxs-lookup"><span data-stu-id="4ba9b-112">Examples</span></span>

<span data-ttu-id="4ba9b-113">В следующем примере показано выше уровня схемы, сочетающий в себе нижнего уровня схемы.</span><span class="sxs-lookup"><span data-stu-id="4ba9b-113">The following example shows a higher level scheme that combines lower level schemes.</span></span> <span data-ttu-id="4ba9b-114">Для задач используется проверка подлинности Google, и файл cookie проверки подлинности используется для всего остального:</span><span class="sxs-lookup"><span data-stu-id="4ba9b-114">Google authentication is used for challenges, and cookie authentication is used for everything else:</span></span>

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

<span data-ttu-id="4ba9b-115">В следующем примере включается динамический выбор схем для каждого запроса.</span><span class="sxs-lookup"><span data-stu-id="4ba9b-115">The following example enables dynamic selection of schemes on a per request basis.</span></span> <span data-ttu-id="4ba9b-116">То есть как сочетать файлы cookie и API проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="4ba9b-116">That is, how to mix cookies and API authentication.</span></span>

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]