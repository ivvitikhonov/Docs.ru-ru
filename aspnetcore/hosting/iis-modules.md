---
title: "Использование с ASP.NET Core модули IIS"
author: guardrex
description: "Справочник по документ, описывающий активных и неактивных модули IIS для приложений ASP.NET Core."
keywords: "ASP.NET Core, iis, модуль, обратного прокси-сервера"
ms.author: riande
manager: wpickett
ms.date: 03/08/2017
ms.topic: article
ms.assetid: 492b3a7e-04c5-461b-b96a-38ecee5c64bc
ms.technology: aspnet
ms.prod: aspnet-core
uid: hosting/iis-modules
ms.openlocfilehash: 353cd4c18cb2708f2dece5ba2b5271f452379d52
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/12/2017
---
# <a name="using-iis-modules-with-aspnet-core"></a><span data-ttu-id="3edea-104">Использование с ASP.NET Core модули IIS</span><span class="sxs-lookup"><span data-stu-id="3edea-104">Using IIS Modules with ASP.NET Core</span></span>

<span data-ttu-id="3edea-105">По [Latham Люк](https://github.com/GuardRex)</span><span class="sxs-lookup"><span data-stu-id="3edea-105">By [Luke Latham](https://github.com/GuardRex)</span></span>

<span data-ttu-id="3edea-106">Приложения ASP.NET Core размещены в IIS в конфигурации обратного прокси-сервера.</span><span class="sxs-lookup"><span data-stu-id="3edea-106">ASP.NET Core applications are hosted by IIS in a reverse-proxy configuration.</span></span> <span data-ttu-id="3edea-107">Некоторые собственные модули IIS и всех модулей IIS управляемых недоступны для обработки запросов для приложений ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3edea-107">Some of the native IIS modules and all of the IIS managed modules are not available to process requests for ASP.NET Core apps.</span></span> <span data-ttu-id="3edea-108">Во многих случаях ASP.NET Core является альтернативой функции собственные и управляемые модули IIS.</span><span class="sxs-lookup"><span data-stu-id="3edea-108">In many cases, ASP.NET Core offers an alternative to the features of IIS native and managed modules.</span></span>

## <a name="native-modules"></a><span data-ttu-id="3edea-109">Модули в машинном коде</span><span class="sxs-lookup"><span data-stu-id="3edea-109">Native Modules</span></span>
<span data-ttu-id="3edea-110">Модуль</span><span class="sxs-lookup"><span data-stu-id="3edea-110">Module</span></span> | <span data-ttu-id="3edea-111">Активный .NET core</span><span class="sxs-lookup"><span data-stu-id="3edea-111">.NET Core Active</span></span> | <span data-ttu-id="3edea-112">Параметр ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3edea-112">ASP.NET Core Option</span></span>
--- | :---: | ---
<span data-ttu-id="3edea-113">**Анонимная проверка подлинности**</span><span class="sxs-lookup"><span data-stu-id="3edea-113">**Anonymous Authentication**</span></span><br>`AnonymousAuthenticationModule` | <span data-ttu-id="3edea-114">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-114">Yes</span></span> | 
<span data-ttu-id="3edea-115">**Обычная проверка подлинности**</span><span class="sxs-lookup"><span data-stu-id="3edea-115">**Basic Authentication**</span></span><br>`BasicAuthenticationModule` | <span data-ttu-id="3edea-116">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-116">Yes</span></span> | 
<span data-ttu-id="3edea-117">**Проверка подлинности с сопоставлением сертификации клиента**</span><span class="sxs-lookup"><span data-stu-id="3edea-117">**Client Certification Mapping Authentication**</span></span><br>`CertificateMappingAuthenticationModule` | <span data-ttu-id="3edea-118">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-118">Yes</span></span> | 
<span data-ttu-id="3edea-119">**CGI**</span><span class="sxs-lookup"><span data-stu-id="3edea-119">**CGI**</span></span><br>`CgiModule` | <span data-ttu-id="3edea-120">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-120">No</span></span> | 
<span data-ttu-id="3edea-121">**Проверка конфигурации**</span><span class="sxs-lookup"><span data-stu-id="3edea-121">**Configuration Validation**</span></span><br>`ConfigurationValidationModule` | <span data-ttu-id="3edea-122">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-122">Yes</span></span> | 
<span data-ttu-id="3edea-123">**Ошибки HTTP**</span><span class="sxs-lookup"><span data-stu-id="3edea-123">**HTTP Errors**</span></span><br>`CustomErrorModule` | <span data-ttu-id="3edea-124">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-124">No</span></span> | [<span data-ttu-id="3edea-125">По промежуточного слоя страницы кода состояния</span><span class="sxs-lookup"><span data-stu-id="3edea-125">Status Code Pages Middleware</span></span>](xref:fundamentals/error-handling#configuring-status-code-pages)
<span data-ttu-id="3edea-126">**Настраиваемое протоколирование**</span><span class="sxs-lookup"><span data-stu-id="3edea-126">**Custom Logging**</span></span><br>`CustomLoggingModule` | <span data-ttu-id="3edea-127">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-127">Yes</span></span> | 
<span data-ttu-id="3edea-128">**Документ по умолчанию**</span><span class="sxs-lookup"><span data-stu-id="3edea-128">**Default Document**</span></span><br>`DefaultDocumentModule` | <span data-ttu-id="3edea-129">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-129">No</span></span> | [<span data-ttu-id="3edea-130">По умолчанию файлы по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="3edea-130">Default Files Middleware</span></span>](xref:fundamentals/static-files#serving-a-default-document)
<span data-ttu-id="3edea-131">**Дайджест-проверка подлинности**</span><span class="sxs-lookup"><span data-stu-id="3edea-131">**Digest Authentication**</span></span><br>`DigestAuthenticationModule` | <span data-ttu-id="3edea-132">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-132">Yes</span></span> | 
<span data-ttu-id="3edea-133">**Просмотр каталогов**</span><span class="sxs-lookup"><span data-stu-id="3edea-133">**Directory Browsing**</span></span><br>`DirectoryListingModule` | <span data-ttu-id="3edea-134">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-134">No</span></span> | [<span data-ttu-id="3edea-135">По промежуточного слоя просмотра каталогов</span><span class="sxs-lookup"><span data-stu-id="3edea-135">Directory Browsing Middleware</span></span>](xref:fundamentals/static-files#enabling-directory-browsing)
<span data-ttu-id="3edea-136">**Динамическое сжатие**</span><span class="sxs-lookup"><span data-stu-id="3edea-136">**Dynamic Compression**</span></span><br>`DynamicCompressionModule` | <span data-ttu-id="3edea-137">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-137">Yes</span></span> | [<span data-ttu-id="3edea-138">По промежуточного слоя сжатия ответов</span><span class="sxs-lookup"><span data-stu-id="3edea-138">Response Compression Middleware</span></span>](xref:performance/response-compression)
<span data-ttu-id="3edea-139">**Трассировка**</span><span class="sxs-lookup"><span data-stu-id="3edea-139">**Tracing**</span></span><br>`FailedRequestsTracingModule` | <span data-ttu-id="3edea-140">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-140">Yes</span></span> | [<span data-ttu-id="3edea-141">Ведение журнала ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3edea-141">ASP.NET Core Logging</span></span>](xref:fundamentals/logging#the-tracesource-provider)
<span data-ttu-id="3edea-142">**Кэширование файлов**</span><span class="sxs-lookup"><span data-stu-id="3edea-142">**File Caching**</span></span><br>`FileCacheModule` | <span data-ttu-id="3edea-143">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-143">No</span></span> | [<span data-ttu-id="3edea-144">Кэширование ответов по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="3edea-144">Response Caching Middleware</span></span>](xref:performance/caching/middleware)
<span data-ttu-id="3edea-145">**Кэширование HTTP**</span><span class="sxs-lookup"><span data-stu-id="3edea-145">**HTTP Caching**</span></span><br>`HttpCacheModule` | <span data-ttu-id="3edea-146">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-146">No</span></span> | [<span data-ttu-id="3edea-147">Кэширование ответов по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="3edea-147">Response Caching Middleware</span></span>](xref:performance/caching/middleware)
<span data-ttu-id="3edea-148">**Ведение журнала HTTP**</span><span class="sxs-lookup"><span data-stu-id="3edea-148">**HTTP Logging**</span></span><br>`HttpLoggingModule` | <span data-ttu-id="3edea-149">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-149">Yes</span></span> | [<span data-ttu-id="3edea-150">Ведение журнала ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3edea-150">ASP.NET Core Logging</span></span>](xref:fundamentals/logging)<br><span data-ttu-id="3edea-151">Реализации: [elmah.io](https://github.com/elmahio/Elmah.Io.Extensions.Logging), [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging), [NLog](https://github.com/NLog/NLog.Extensions.Logging), [Serilog](https://github.com/serilog/serilog-extensions-logging)</span><span class="sxs-lookup"><span data-stu-id="3edea-151">Implementations: [elmah.io](https://github.com/elmahio/Elmah.Io.Extensions.Logging), [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging), [NLog](https://github.com/NLog/NLog.Extensions.Logging), [Serilog](https://github.com/serilog/serilog-extensions-logging)</span></span>
<span data-ttu-id="3edea-152">**Перенаправление HTTP**</span><span class="sxs-lookup"><span data-stu-id="3edea-152">**HTTP Redirection**</span></span><br>`HttpRedirectionModule` | <span data-ttu-id="3edea-153">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-153">Yes</span></span> | [<span data-ttu-id="3edea-154">По промежуточного слоя перезаписи URL-адрес</span><span class="sxs-lookup"><span data-stu-id="3edea-154">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting)
<span data-ttu-id="3edea-155">**Проверка подлинности с сопоставлением сертификата клиента IIS**</span><span class="sxs-lookup"><span data-stu-id="3edea-155">**IIS Client Certificate Mapping Authentication**</span></span><br>`IISCertificateMappingAuthenticationModule` | <span data-ttu-id="3edea-156">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-156">Yes</span></span> | 
<span data-ttu-id="3edea-157">**Ограничения IP-адресов и доменов**</span><span class="sxs-lookup"><span data-stu-id="3edea-157">**IP and Domain Restrictions**</span></span><br>`IpRestrictionModule` | <span data-ttu-id="3edea-158">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-158">Yes</span></span> | 
<span data-ttu-id="3edea-159">**Фильтры ISAPI**</span><span class="sxs-lookup"><span data-stu-id="3edea-159">**ISAPI Filters**</span></span><br>`IsapiFilterModule` | <span data-ttu-id="3edea-160">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-160">Yes</span></span> | [<span data-ttu-id="3edea-161">По промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="3edea-161">Middleware</span></span>](xref:fundamentals/middleware)
<span data-ttu-id="3edea-162">**ISAPI**</span><span class="sxs-lookup"><span data-stu-id="3edea-162">**ISAPI**</span></span><br>`IsapiModule` | <span data-ttu-id="3edea-163">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-163">Yes</span></span> | [<span data-ttu-id="3edea-164">По промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="3edea-164">Middleware</span></span>](xref:fundamentals/middleware)
<span data-ttu-id="3edea-165">**Поддержка протоколов**</span><span class="sxs-lookup"><span data-stu-id="3edea-165">**Protocol Support**</span></span><br>`ProtocolSupportModule` | <span data-ttu-id="3edea-166">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-166">Yes</span></span> | 
<span data-ttu-id="3edea-167">**Фильтрация запросов**</span><span class="sxs-lookup"><span data-stu-id="3edea-167">**Request Filtering**</span></span><br>`RequestFilteringModule` | <span data-ttu-id="3edea-168">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-168">Yes</span></span> | [<span data-ttu-id="3edea-169">По промежуточного слоя перезаписи URL-адрес`IRule`</span><span class="sxs-lookup"><span data-stu-id="3edea-169">URL Rewriting Middleware `IRule`</span></span>](xref:fundamentals/url-rewriting#irule-based-rule)
<span data-ttu-id="3edea-170">**Монитор запросов**</span><span class="sxs-lookup"><span data-stu-id="3edea-170">**Request Monitor**</span></span><br>`RequestMonitorModule` | <span data-ttu-id="3edea-171">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-171">Yes</span></span> | 
<span data-ttu-id="3edea-172">**Переписывание URL-адресов**</span><span class="sxs-lookup"><span data-stu-id="3edea-172">**URL Rewriting**</span></span><br>`RewriteModule` | <span data-ttu-id="3edea-173">Yes†</span><span class="sxs-lookup"><span data-stu-id="3edea-173">Yes†</span></span> | [<span data-ttu-id="3edea-174">По промежуточного слоя перезаписи URL-адрес</span><span class="sxs-lookup"><span data-stu-id="3edea-174">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting)
<span data-ttu-id="3edea-175">**Включения на стороне сервера**</span><span class="sxs-lookup"><span data-stu-id="3edea-175">**Server Side Includes**</span></span><br>`ServerSideIncludeModule` | <span data-ttu-id="3edea-176">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-176">No</span></span> | 
<span data-ttu-id="3edea-177">**Статическое сжатие**</span><span class="sxs-lookup"><span data-stu-id="3edea-177">**Static Compression**</span></span><br>`StaticCompressionModule` | <span data-ttu-id="3edea-178">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-178">No</span></span> | [<span data-ttu-id="3edea-179">По промежуточного слоя сжатия ответов</span><span class="sxs-lookup"><span data-stu-id="3edea-179">Response Compression Middleware</span></span>](xref:performance/response-compression)
<span data-ttu-id="3edea-180">**Статическое содержимое**</span><span class="sxs-lookup"><span data-stu-id="3edea-180">**Static Content**</span></span><br>`StaticFileModule` | <span data-ttu-id="3edea-181">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-181">No</span></span> | [<span data-ttu-id="3edea-182">По промежуточного слоя статических файлов</span><span class="sxs-lookup"><span data-stu-id="3edea-182">Static File Middleware</span></span>](xref:fundamentals/static-files)
<span data-ttu-id="3edea-183">**Кэширование маркеров**</span><span class="sxs-lookup"><span data-stu-id="3edea-183">**Token Caching**</span></span><br>`TokenCacheModule` | <span data-ttu-id="3edea-184">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-184">Yes</span></span> | 
<span data-ttu-id="3edea-185">**Кэширование URI**</span><span class="sxs-lookup"><span data-stu-id="3edea-185">**URI Caching**</span></span><br>`UriCacheModule` | <span data-ttu-id="3edea-186">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-186">Yes</span></span> | 
<span data-ttu-id="3edea-187">**Авторизация URL-адреса**</span><span class="sxs-lookup"><span data-stu-id="3edea-187">**URL Authorization**</span></span><br>`UrlAuthorizationModule` | <span data-ttu-id="3edea-188">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-188">Yes</span></span> | [<span data-ttu-id="3edea-189">Удостоверение ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3edea-189">ASP.NET Core Identity</span></span>](xref:security/authentication/identity)
<span data-ttu-id="3edea-190">**Проверка подлинности Windows**</span><span class="sxs-lookup"><span data-stu-id="3edea-190">**Windows Authentication**</span></span><br>`WindowsAuthenticationModule` | <span data-ttu-id="3edea-191">Да</span><span class="sxs-lookup"><span data-stu-id="3edea-191">Yes</span></span> | 

<span data-ttu-id="3edea-192">†The модуль переопределения URL-адрес `isFile` и `isDirectory` не работают с приложениями ASP.NET Core из-за изменений в [структуру каталогов](xref:hosting/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="3edea-192">†The URL Rewrite Module's `isFile` and `isDirectory` do not work with ASP.NET Core applications due to the changes in [directory structure](xref:hosting/directory-structure).</span></span>

## <a name="managed-modules"></a><span data-ttu-id="3edea-193">Управляемые модули</span><span class="sxs-lookup"><span data-stu-id="3edea-193">Managed Modules</span></span>
<span data-ttu-id="3edea-194">Модуль</span><span class="sxs-lookup"><span data-stu-id="3edea-194">Module</span></span> | <span data-ttu-id="3edea-195">Активный .NET core</span><span class="sxs-lookup"><span data-stu-id="3edea-195">.NET Core Active</span></span> | <span data-ttu-id="3edea-196">Параметр ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3edea-196">ASP.NET Core Option</span></span>
--- | :---: | ---
<span data-ttu-id="3edea-197">AnonymousIdentification</span><span class="sxs-lookup"><span data-stu-id="3edea-197">AnonymousIdentification</span></span> | <span data-ttu-id="3edea-198">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-198">No</span></span> | 
<span data-ttu-id="3edea-199">DefaultAuthentication</span><span class="sxs-lookup"><span data-stu-id="3edea-199">DefaultAuthentication</span></span> | <span data-ttu-id="3edea-200">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-200">No</span></span> | 
<span data-ttu-id="3edea-201">FileAuthorization</span><span class="sxs-lookup"><span data-stu-id="3edea-201">FileAuthorization</span></span> | <span data-ttu-id="3edea-202">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-202">No</span></span> | 
<span data-ttu-id="3edea-203">FormsAuthentication</span><span class="sxs-lookup"><span data-stu-id="3edea-203">FormsAuthentication</span></span> | <span data-ttu-id="3edea-204">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-204">No</span></span> | [<span data-ttu-id="3edea-205">Файл cookie проверки подлинности по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="3edea-205">Cookie Authentication Middleware</span></span>](xref:security/authentication/cookie)
<span data-ttu-id="3edea-206">OutputCache</span><span class="sxs-lookup"><span data-stu-id="3edea-206">OutputCache</span></span> | <span data-ttu-id="3edea-207">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-207">No</span></span> | [<span data-ttu-id="3edea-208">Кэширование ответов по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="3edea-208">Response Caching Middleware</span></span>](xref:performance/caching/middleware)
<span data-ttu-id="3edea-209">Профиль</span><span class="sxs-lookup"><span data-stu-id="3edea-209">Profile</span></span> | <span data-ttu-id="3edea-210">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-210">No</span></span> | 
<span data-ttu-id="3edea-211">RoleManager</span><span class="sxs-lookup"><span data-stu-id="3edea-211">RoleManager</span></span> | <span data-ttu-id="3edea-212">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-212">No</span></span> | 
<span data-ttu-id="3edea-213">ScriptModule 4.0</span><span class="sxs-lookup"><span data-stu-id="3edea-213">ScriptModule-4.0</span></span> | <span data-ttu-id="3edea-214">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-214">No</span></span> | 
<span data-ttu-id="3edea-215">Сеанс</span><span class="sxs-lookup"><span data-stu-id="3edea-215">Session</span></span> | <span data-ttu-id="3edea-216">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-216">No</span></span> | [<span data-ttu-id="3edea-217">Сеанс по промежуточного слоя</span><span class="sxs-lookup"><span data-stu-id="3edea-217">Session Middleware</span></span>](xref:fundamentals/app-state)
<span data-ttu-id="3edea-218">UrlAuthorization</span><span class="sxs-lookup"><span data-stu-id="3edea-218">UrlAuthorization</span></span> | <span data-ttu-id="3edea-219">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-219">No</span></span> | 
<span data-ttu-id="3edea-220">UrlMappingsModule</span><span class="sxs-lookup"><span data-stu-id="3edea-220">UrlMappingsModule</span></span> | <span data-ttu-id="3edea-221">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-221">No</span></span> | [<span data-ttu-id="3edea-222">По промежуточного слоя перезаписи URL-адрес</span><span class="sxs-lookup"><span data-stu-id="3edea-222">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting)
<span data-ttu-id="3edea-223">UrlRoutingModule 4.0</span><span class="sxs-lookup"><span data-stu-id="3edea-223">UrlRoutingModule-4.0</span></span> | <span data-ttu-id="3edea-224">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-224">No</span></span> | [<span data-ttu-id="3edea-225">Удостоверение ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3edea-225">ASP.NET Core  Identity</span></span>](xref:security/authentication/identity)
<span data-ttu-id="3edea-226">WindowsAuthentication</span><span class="sxs-lookup"><span data-stu-id="3edea-226">WindowsAuthentication</span></span> | <span data-ttu-id="3edea-227">Нет</span><span class="sxs-lookup"><span data-stu-id="3edea-227">No</span></span> | 

## <a name="iis-manager-application-changes"></a><span data-ttu-id="3edea-228">Изменения приложения диспетчера служб IIS</span><span class="sxs-lookup"><span data-stu-id="3edea-228">IIS Manager application changes</span></span>
<span data-ttu-id="3edea-229">При использовании диспетчера служб IIS для настройки параметров вы меняете непосредственно *web.config* файл приложения.</span><span class="sxs-lookup"><span data-stu-id="3edea-229">When you use IIS Manager to configure settings, you're directly changing the *web.config* file of the app.</span></span> <span data-ttu-id="3edea-230">Если развертывание приложения и включают *web.config*, любые изменения, внесенные с помощью диспетчера IIS будут перезаписаны с развернутой *файл web.config*.</span><span class="sxs-lookup"><span data-stu-id="3edea-230">If you deploy your app and include *web.config*, any changes you made with IIS Manger will be overwritten by the deployed *web.config file*.</span></span> <span data-ttu-id="3edea-231">Поэтому при внесении изменений на сервер *web.config* файл, скопировать обновленные *web.config* файл в локальном проекте немедленно.</span><span class="sxs-lookup"><span data-stu-id="3edea-231">Therefore if you make changes to the server's *web.config* file, copy the updated *web.config* file to your local project immediately.</span></span>

## <a name="disabling-iis-modules"></a><span data-ttu-id="3edea-232">Отключение модулей IIS</span><span class="sxs-lookup"><span data-stu-id="3edea-232">Disabling IIS modules</span></span>
<span data-ttu-id="3edea-233">Если у вас есть модуль IIS настроен на уровне сервера, который вы хотите отключить для приложения, это можно сделать с дополнением к вашей *web.config* файла.</span><span class="sxs-lookup"><span data-stu-id="3edea-233">If you have an IIS module configured at the server level that you would like to disable for an application, you can do so with an addition to your *web.config* file.</span></span> <span data-ttu-id="3edea-234">Модуль следует оставить на месте и отключите его с помощью параметра конфигурации (при наличии) либо удалить модуль из приложения.</span><span class="sxs-lookup"><span data-stu-id="3edea-234">Either leave the module in place and deactivate it using a configuration setting (if available) or remove the module from the app.</span></span>

### <a name="module-deactivation"></a><span data-ttu-id="3edea-235">Отключение модуля</span><span class="sxs-lookup"><span data-stu-id="3edea-235">Module deactivation</span></span>
<span data-ttu-id="3edea-236">Многие модули предоставляют параметр конфигурации, позволяющих сделать без их удаления из приложения.</span><span class="sxs-lookup"><span data-stu-id="3edea-236">Many modules offer a configuration setting that will allow you to disable them without removing them from the application.</span></span> <span data-ttu-id="3edea-237">Это простой и быстрый способ деактивировать модуля.</span><span class="sxs-lookup"><span data-stu-id="3edea-237">This is the simplest and quickest way to deactivate a module.</span></span> <span data-ttu-id="3edea-238">Например, если вы хотите отключить модуль переопределения URL-адреса IIS, используйте `<httpRedirect>` элемента, как показано ниже.</span><span class="sxs-lookup"><span data-stu-id="3edea-238">For example if you wish to disable the IIS URL Rewrite Module, use the `<httpRedirect>` element as shown below.</span></span> <span data-ttu-id="3edea-239">Дополнительные сведения об отключении модулей с параметрами конфигурации по ссылкам в *дочерние элементы* раздел [IIS `<system.webServer>` ](https://docs.microsoft.com/iis/configuration/system.webServer/).</span><span class="sxs-lookup"><span data-stu-id="3edea-239">For more information on disabling modules with configuration settings, follow the links in the *Child Elements* section of [IIS `<system.webServer>`](https://docs.microsoft.com/iis/configuration/system.webServer/).</span></span>

```xml
<configuration>
  <system.webServer>
     <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

### <a name="module-removal"></a><span data-ttu-id="3edea-240">Удаление модуля</span><span class="sxs-lookup"><span data-stu-id="3edea-240">Module removal</span></span>
<span data-ttu-id="3edea-241">Если вы решили удалить модуль с параметром *web.config*, необходимо разблокировать модуль и разблокировать `<modules>` раздел *web.config* первой.</span><span class="sxs-lookup"><span data-stu-id="3edea-241">If you opt to remove a module with a setting in *web.config*, you must unlock the module and unlock the `<modules>` section of *web.config* first.</span></span> <span data-ttu-id="3edea-242">Шаги, описанные ниже:</span><span class="sxs-lookup"><span data-stu-id="3edea-242">The steps are outlined below:</span></span>

1. <span data-ttu-id="3edea-243">Снятие блокировки модуля на уровне сервера.</span><span class="sxs-lookup"><span data-stu-id="3edea-243">Unlock the module at the server level.</span></span> <span data-ttu-id="3edea-244">Нажмите кнопку на сервере IIS с помощью диспетчера IIS **подключений** боковой панели.</span><span class="sxs-lookup"><span data-stu-id="3edea-244">Click on the IIS server in the IIS Manager **Connections** sidebar.</span></span> <span data-ttu-id="3edea-245">Откройте **модули** в **IIS** области.</span><span class="sxs-lookup"><span data-stu-id="3edea-245">Open the **Modules** in the **IIS** area.</span></span> <span data-ttu-id="3edea-246">Щелкните для модуля в списке.</span><span class="sxs-lookup"><span data-stu-id="3edea-246">Click on the module in the list.</span></span> <span data-ttu-id="3edea-247">В **действия** боковой панели справа щелкните **Unlock**.</span><span class="sxs-lookup"><span data-stu-id="3edea-247">In the **Actions** sidebar on the right, click **Unlock**.</span></span> <span data-ttu-id="3edea-248">Разблокировать столько модулей, которые планируется удалить с *web.config* позже.</span><span class="sxs-lookup"><span data-stu-id="3edea-248">Unlock as many modules as you plan to remove with *web.config* later.</span></span>

2. <span data-ttu-id="3edea-249">Развернуть приложение без `<modules>` статьи *web.config*. Если развернуть приложение с *web.config* содержащий `<modules>` раздела без разблокирован раздел сначала с помощью диспетчера IIS, Configuration Manager будет вызывать исключение при попытке разблокировать раздел.</span><span class="sxs-lookup"><span data-stu-id="3edea-249">Deploy your application without a `<modules>` section in *web.config*. If you deploy an app with a *web.config* containing the `<modules>` section without having unlocked the section first in the IIS Manager, the Configuration Manager will throw an exception when you try to unlock the section.</span></span> <span data-ttu-id="3edea-250">Таким образом, развертывания приложения без `<modules>` раздела.</span><span class="sxs-lookup"><span data-stu-id="3edea-250">Therefore, deploy your application without a `<modules>` section.</span></span>

3. <span data-ttu-id="3edea-251">Разблокировать `<modules>` раздел *web.config*. В **подключений** боковой панели щелкните веб-сайт в **узлы**.</span><span class="sxs-lookup"><span data-stu-id="3edea-251">Unlock the `<modules>` section of *web.config*. In the **Connections** sidebar, click the website in **Sites**.</span></span> <span data-ttu-id="3edea-252">В **управления** откройте **редактор конфигурации**.</span><span class="sxs-lookup"><span data-stu-id="3edea-252">In the **Management** area, open the **Configuration Editor**.</span></span> <span data-ttu-id="3edea-253">Позволяет выбрать элементы управления навигацией `system.webServer/modules` раздела.</span><span class="sxs-lookup"><span data-stu-id="3edea-253">Use the navigation controls to select the `system.webServer/modules` section.</span></span> <span data-ttu-id="3edea-254">В **действия** боковой панели справа щелкните, чтобы **Unlock** разделе.</span><span class="sxs-lookup"><span data-stu-id="3edea-254">In the **Actions** sidebar on the right, click to **Unlock** the section.</span></span>

4. <span data-ttu-id="3edea-255">На этом этапе можно добавить `<modules>` раздел вашей *web.config* файл с `<remove>` элемент, чтобы удалить модуль из приложения.</span><span class="sxs-lookup"><span data-stu-id="3edea-255">At this point, you will be able to add a `<modules>` section to your *web.config* file with a `<remove>` element to remove the module from the application.</span></span> <span data-ttu-id="3edea-256">Можно добавить несколько `<remove>` элементы, чтобы удалить несколько модулей.</span><span class="sxs-lookup"><span data-stu-id="3edea-256">You can add multiple `<remove>` elements to remove multiple modules.</span></span> <span data-ttu-id="3edea-257">Помните, что при внесении *web.config* изменения на сервере, чтобы сделать их непосредственно в проекте локально.</span><span class="sxs-lookup"><span data-stu-id="3edea-257">Don't forget that if you make *web.config* changes on the server to make them immediately in the project locally.</span></span> <span data-ttu-id="3edea-258">Удаление модуля таким образом не влияет на использование модуля с другими приложениями на сервере.</span><span class="sxs-lookup"><span data-stu-id="3edea-258">Removing a module this way won't affect your use of the module with other apps on the server.</span></span>

  ```xml
  <configuration> 
    <system.webServer> 
      <modules> 
        <remove name="MODULE_NAME" /> 
      </modules> 
    </system.webServer> 
  </configuration>
  ```

<span data-ttu-id="3edea-259">Для установки служб IIS с модулями по умолчанию установлен, можно использовать следующие `<module>` раздел, чтобы удалить модули по умолчанию.</span><span class="sxs-lookup"><span data-stu-id="3edea-259">For an IIS installation with the default modules installed, you can use the following `<module>` section to remove the default modules.</span></span>

```xml
<modules>
  <remove name="CustomErrorModule" />
  <remove name="DefaultDocumentModule" />
  <remove name="DirectoryListingModule" />
  <remove name="HttpCacheModule" />
  <remove name="HttpLoggingModule" />
  <remove name="ProtocolSupportModule" />
  <remove name="RequestFilteringModule" />
  <remove name="StaticCompressionModule" /> 
  <remove name="StaticFileModule" /> 
</modules>
```

<span data-ttu-id="3edea-260">Можно также удалить модуль IIS с *Appcmd.exe*.</span><span class="sxs-lookup"><span data-stu-id="3edea-260">You can also remove an IIS module with *Appcmd.exe*.</span></span> <span data-ttu-id="3edea-261">Укажите `MODULE_NAME` и `APPLICATION_NAME` в приведенной ниже команде:</span><span class="sxs-lookup"><span data-stu-id="3edea-261">Provide the `MODULE_NAME` and `APPLICATION_NAME` in the command shown below:</span></span>

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

<span data-ttu-id="3edea-262">Вот как можно удалить `DynamicCompressionModule` с веб-сайта по умолчанию:</span><span class="sxs-lookup"><span data-stu-id="3edea-262">Here's how to remove the `DynamicCompressionModule` from the Default Web Site:</span></span>

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimal-module-configuration"></a><span data-ttu-id="3edea-263">Модуль минимальной конфигурации</span><span class="sxs-lookup"><span data-stu-id="3edea-263">Minimal module configuration</span></span>
<span data-ttu-id="3edea-264">Только модули, необходимые для запуска приложения ASP.NET Core: модуль анонимной проверки подлинности и модуль ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3edea-264">The only modules required to run an ASP.NET Core application are the Anonymous Authentication Module and the ASP.NET Core Module.</span></span>

![Откройте диспетчер служб IIS с модулями с минимальной настройкой модулей показано](iis-modules/_static/modules.png)

## <a name="resources"></a><span data-ttu-id="3edea-266">Ресурсы</span><span class="sxs-lookup"><span data-stu-id="3edea-266">Resources</span></span>
* [<span data-ttu-id="3edea-267">Публикация в IIS</span><span class="sxs-lookup"><span data-stu-id="3edea-267">Publishing to IIS</span></span>](xref:publishing/iis)
* [<span data-ttu-id="3edea-268">Общие сведения о модули IIS</span><span class="sxs-lookup"><span data-stu-id="3edea-268">IIS Modules Overview</span></span>](https://docs.microsoft.com/iis/get-started/introduction-to-iis/iis-modules-overview)
* [<span data-ttu-id="3edea-269">Настройка IIS 7.0 ролей и модулей</span><span class="sxs-lookup"><span data-stu-id="3edea-269">Customizing IIS 7.0 Roles and Modules</span></span>](https://technet.microsoft.com/library/cc627313.aspx)
* [<span data-ttu-id="3edea-270">IIS`<system.webServer>`</span><span class="sxs-lookup"><span data-stu-id="3edea-270">IIS `<system.webServer>`</span></span>](https://docs.microsoft.com/iis/configuration/system.webServer/)