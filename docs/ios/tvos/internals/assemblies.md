---
title: Assemblies com suporte do Xamarin para tvOS
description: Para ajudar a esclarecer os recursos disponíveis para aplicativos tvOS, este documento fornece uma lista de assemblies com suporte do Xamarin para o desenvolvimento do tvOS.
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/07/2016
ms.openlocfilehash: 193f4a445e21416abf2fd6279cdc18228e16c985
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283607"
---
# <a name="assemblies-supported-by-xamarin-for-tvos"></a>Assemblies com suporte do Xamarin para tvOS

## <a name="supported-assemblies"></a>Assemblies com suporte

Esta é uma lista dos assemblies com suporte do Xamarin para seus aplicativos Xamarin. tvOS. A lista detalhada desses é listada abaixo.  Algumas omissões notáveis incluem `System.EnterpriseServices`, a pilha ASP.net e o Windows. Forms.

|Assembly|Added|Compatibilidade de API|
|---|---|---|
|Mono.CompilerServices.SymbolWriter.dll|1.0|Para escritores de compilador.|
|Mono.Data.Sqlite.dll|1.2|Provedor de ADO.NET para SQLite; consulte [limitações](~/ios/data-cloud/system.data.md).|
|Mono.Data.Tds.dll|1.2|Suporte a protocolo TDS; usado para suporte a [System. Data. SqlClient](xref:System.Data.SqlClient) em [System. Data](~/ios/data-cloud/system.data.md).|
|Mono.Security.dll|1.0|APIs de criptografia.|
|monotouch.dll|1.0|Esse assembly contém a [ C# associação à API Cocoatouch](https://docs.microsoft.com/dotnet/api/?view=xamarinios-10.8).|
|mscorlib.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|OpenTK.dll|1.0|As APIs do OpenGL/com orientação a objeto aberto, [estendidas para fornecer suporte a dispositivos iPhone](xref:OpenGLES).|
|System.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), além de tipos dos seguintes namespaces: <ul><li>System.Collections.Specialized</li> <li>System.ComponentModel</li> <li>System.ComponentModel.Design</li> <li>System.Diagnostics</li> <li>System.IO.Compression</li> <li>System.Net</li> <li>System.Net.Cache</li> <li>System.Net.Mail</li> <li>System.Net.Mime</li> <li>System.Net.NetworkInformation</li> <li>System.Net.Security</li> <li>System.Net.Sockets</li> <li>System.Security.Authentication</li> <li>System.Security.Cryptography</li> <li>System.Timers</li></ul>|
|System.Core.dll|1.0|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Data.dll|1.2|[.Net 3,5](https://msdn.microsoft.com/library/ms229335.aspx), [com algumas funcionalidades removidas](~/ios/data-cloud/system.data.md).|
|System.Data.Service.Client.dll|Win|Cliente oData completo.|
|System.Drawing|1.0|API de sistema. Drawing-somente API Clássica.<br />_Não há suporte para System. Drawing no API Unificada para o Xamarin. Mac .NET 4,5 ou para as estruturas móveis._|
|System.Json.dll|1.1|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Runtime.Serialization.dll|?|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.dll|1.1|Pilha do [WCF](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services) como presente no [Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.Web.dll|?|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), além de tipos dos seguintes namespaces: <ul><li>Sistema</li><li>System.ServiceModel.Channels</li><li>System.ServiceModel.Description</li><li>System.ServiceModel.Web</li></ul>|
|System.Transactions.dll|1.2|[.Net 3,5](https://msdn.microsoft.com/library/ms229335.aspx); parte do suporte a [System. Data](https://docs.microsoft.com/xamarin/ios/data-cloud/system.data) .|
|System.Web.Services|1.1|[Serviços Web básicos](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services) do perfil .net 3,5, com os recursos do servidor removidos.|
|System.Xml.dll|1.0|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|
|System.Xml.Linq.dll|1.0|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|

<a name="Summary" />

## <a name="portable-class-libraries"></a>Bibliotecas de Classes Portáteis

Além das ligações Mac, o Xamarin. tvOS pode consumir bibliotecas de [classes portáteis .net](~/cross-platform/app-fundamentals/pcl.md).

## <a name="related-links"></a>Links relacionados

- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
