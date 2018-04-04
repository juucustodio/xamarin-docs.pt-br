---
title: Assemblies
ms.prod: xamarin
ms.assetid: 0B1ACF06-65FF-49E2-B6BC-7AEC55638ED8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: 7d0ee27cfa2ae153ef481f943402f5fcfc5d04e4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="assemblies"></a>Assemblies

## <a name="supported-assemblies"></a>Assemblies com suporte

Esta é uma lista dos assemblies do suporte para o Xamarin para seus aplicativos Xamarin.tvOS. A lista detalhada desses esteja listada abaixo.  Alguns omissões importantes incluem `System.EnterpriseServices`, a pilha do ASP.NET e Windows. Forms.

|Assembly|Added|Compatibilidade de API|
|---|---|---|
|Mono.CompilerServices.SymbolWriter.dll|1.0|Para autores de compilador.|
|Mono.Data.Sqlite.dll|1.2|Provedor ADO.NET para SQLite; consulte [limitações](~/ios/data-cloud/system.data.md).|
|Mono.Data.Tds.dll|1.2|Suporte de protocolo TDS; usado para [SqlClient](https://developer.xamarin.com/api/namespace/System.Data.SqlClient/) suportem [System. Data](~/ios/data-cloud/system.data.md).|
|Mono.Security.dll|1.0|APIs de criptografia.|
|monotouch.dll|1.0|Este assembly contém o [c# associação para a API CocoaTouch](https://developer.xamarin.com/api/root/ios-unified/).|
|mscorlib.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|OpenTK.dll|1.0|O objeto OpenGL/OpenAL orientada a APIs, [estendidos para oferecer suporte a dispositivo iPhone](https://developer.xamarin.com/api/namespace/OpenGLES/).|
|System.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx), além de tipos dos namespaces a seguir: <ul><li>System.Collections.Specialized</li> <li>System.ComponentModel</li> <li>System.ComponentModel.Design</li> <li>System.Diagnostics</li> <li>System.IO.Compression</li> <li>System.Net</li> <li>System.Net.Cache</li> <li>System.Net.Mail</li> <li>System.Net.Mime</li> <li>System.Net.NetworkInformation</li> <li>System.Net.Security</li> <li>System.Net.Sockets</li> <li>System.Security.Authentication</li> <li>System.Security.Cryptography</li> <li>System.Timers</li></ul>|
|System.Core.dll|1.0|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Data.dll|1.2|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx), [com algumas funcionalidades removidas](~/ios/data-cloud/system.data.md).|
|System.Data.Service.Client.dll|3. x|Cliente oData completo.|
|System.Drawing|1.0|System. Drawing API - somente API clássica.<br />_Não há suporte para System. Drawing na API unificada para o Xamarin.Mac .NET 4.5 ou estruturas móveis._|
|System.Json.dll|1.1|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.Runtime.Serialization.dll|?|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.dll|1.1|[WCF](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services) de pilha como presentes no [Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx)|
|System.ServiceModel.Web.dll|?|[Silverlight](http://msdn.microsoft.com/library/cc838194(VS.95).aspx), além de tipos dos namespaces a seguir: <ul><li>Sistema</li><li>System.ServiceModel.Channels</li><li>System.ServiceModel.Description</li><li>System.ServiceModel.Web</li></ul>|
|System.Transactions.dll|1.2|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx); faz parte do [System. Data](https://docs.microsoft.com/xamarin/ios/data-cloud/system.data) suporte.|
|System.Web.Services|1.1|[Os serviços da Web básico](http://docs.xamarin.com/guides/cross-platform/application_fundamentals/introduction_to_web_services) do perfil do .NET 3.5, com os recursos de servidor removido.|
|System.Xml.dll|1.0|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|
|System.Xml.Linq.dll|1.0|[.NET 3.5](http://msdn.microsoft.com/library/ms229335.aspx)|

<a name="Summary" />

## <a name="portable-class-libraries"></a>Bibliotecas de Classes Portáteis

As associações de Mac, além de Xamarin.tvOS pode consumir [bibliotecas de classes portáteis do .NET](~/cross-platform/app-fundamentals/pcl.md).



## <a name="related-links"></a>Links relacionados

- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
