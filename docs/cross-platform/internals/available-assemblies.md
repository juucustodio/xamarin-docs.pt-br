---
title: Assemblies disponíveis
description: Este documento lista os assemblies disponíveis para uso no xamarin. IOS, xamarin. Android e xamarin. Mac. Ele também fornece links para documentação sobre bibliotecas do .NET Standard e bibliotecas de classes portáteis.
ms.prod: xamarin
ms.assetid: AEF4ED0E-391F-4FA4-9F18-842BC24C272D
author: asb3993
ms.author: amburns
ms.date: 03/13/2018
ms.openlocfilehash: 8882a7cd36eab5e7905585f5de4d6585dfb53439
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672255"
---
# <a name="available-assemblies"></a>Assemblies disponíveis

Xamarin. IOS, xamarin. Android e xamarin. Mac todos fornecido com diversos assemblies. Assim como o Silverlight é um subconjunto estendido dos assemblies do .NET da área de trabalho, plataformas Xamarin também é um subconjunto estendido de vários Silverlight e assemblies do .NET da área de trabalho.

Plataformas Xamarin não são ABI compatível com assemblies existentes compilados para um perfil diferente. Você deve recompilar seu código-fonte para gerar os assemblies destinados o perfil correto (assim como você precisa recompilar o código-fonte para o destino do Silverlight e do .NET 3.5 separadamente).

Aplicativos xamarin. Mac podem ser compilados em três modos: um que usa Xamarin da curadoria perfil móvel, o xamarin. Mac .NET Framework 4.5 que permite que você direcione assemblies existentes de área de trabalho completa e um sem suporte que usa a API .NET encontrado em um sistema Mono instalação. Para obter mais informações, consulte nosso [estruturas de destino](~/mac/platform/target-framework.md) documentação.

## <a name="net-standard-libraries"></a>.NET Standard Libraries

Além do iOS, Android e Mac, associações, Xamarin projetos podem consumir [bibliotecas .NET Standard](~/cross-platform/app-fundamentals/net-standard.md).

## <a name="portable-class-libraries"></a>Bibliotecas de Classes Portáteis

Projetos do Xamarin também podem consumir [bibliotecas de classes portátil .NET](~/cross-platform/app-fundamentals/pcl.md), embora essa tecnologia está sendo preterida em favor do .NET Standard.

## <a name="supported-assemblies"></a>Assemblies com suporte

Esses são os assemblies disponíveis na **Gerenciador de referências > Assemblies > Framework** (Visual Studio 2017) e **Editar referências > pacotes** (Visual Studio para Mac) e sua compatibilidade com plataformas Xamarin.

> [!div class="mx-tdCol2BreakAll"]
> |Assembly|Compatibilidade de API|Xamarin iOS|Xamarin Android|Mac do Xamarin|
> |--------|-----------------|-----------|---------------|-----------|
> |FSharp.Core.dll| |✓|✓|✓|
> |l18N.dll|Inclui CJK, Ásia, outros, raro, oeste|✓|✓|✓|
> |Microsoft.CSharp.dll| |✓|✓|✓|
> |Mono.CSharp.dll| |✓|✓|✓|
> |Mono.Data.Sqlite.dll|Provedor ADO.NET para SQLite; Consulte limitações.|✓|✓|✓|
> |Mono.Data.Tds.dll|Suporte de protocolo TDS; usado para [System.Data.SqlClient](xref:System.Data.SqlClient) suportem [System. Data](xref:System.Data).|✓|✓|✓|
> |Mono.Dynamic.&#8203;Interpreter.dll| |✓| | |
> |Mono.Security.dll|APIs de criptografia.|✓|✓|✓|
> |monotouch.dll|Esse assembly contém a associação para a API CocoaTouch c#. Isso só está disponível em projetos do iOS clássico.|✓| | |
> |MonoTouch.&#8203;Dialog-1.dll| |✓| | |
> |MonoTouch.&#8203;NUnitLite.dll| |✓| | |
> |mscorlib.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |OpenTK-1.0.dll|O objeto OpenGL/OpenAL orientado a APIs, estendidas para oferecer suporte a dispositivos iPhone.|✓|✓|✓|
> |System.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), além de tipos dos namespaces a seguir:<br />System.Collections.Specialized<br />System.&#8203;ComponentModel<br />System.ComponentModel.Design<br />System.Diagnostics<br />System.IO<br />System.IO.Compression<br />System.IO.Compression.FileSystem<br />System.Net<br />System.Net.Cache<br />System.Net.Mail<br />System.Net.Mime<br />System.Net.&#8203;NetworkInformation<br />System.Net.Security<br />System.Net.Sockets<br />System.Runtime.&#8203;InteropServices<br />System.Runtime.Versioning<br />System.Security.&#8203;AccessControl<br />System.Security.Authentication<br />System.Security.&#8203;Cryptography<br />System.Security.Permissions<br />System.Threading<br />System.Timers|✓|✓|✓|
> |System.&#8203;ComponentModel.&#8203;Composition.dll| |✓|✓|✓|
> |System.&#8203;ComponentModel.&#8203;DataAnnotations.dll| |✓|✓|✓|
> |System.Core.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Data.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx) , com [algumas funcionalidades removidas](~/ios/data-cloud/system.data.md).|✓|✓|✓|
> |System.Data.&#8203;Services.&#8203;Client.dll|Cliente oData completa.|✓|✓|✓|
> |System.IO.&#8203;Compression| |✓|✓|✓|
> |System.IO.&#8203;Compression.&#8203;FileSystem| |✓|✓|✓|
> |System.Json.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.Net.&#8203;Http.dll| |✓|✓|✓|
> |System.&#8203;Numerics.dll| |✓|✓|✓|
> |System.Runtime.&#8203;Serialization.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.&#8203;ServiceModel.dll|Pilha do WCF como presente no [Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Internals.dll| |✓|✓|✓|
> |System.&#8203;ServiceModel.&#8203;Web.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), além de tipos dos namespaces a seguir: <br />Sistema<br />System.ServiceModel.Channels<br />System.ServiceModel.Description<br />System.ServiceModel.Web|✓|✓|✓|
> |System.&#8203;Transactions.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx); faz parte do [System. Data](~/ios/data-cloud/system.data.md) dão suporte.|✓|✓|✓|
> |System.Web.&#8203;Services.dll|Serviços básicos da Web do perfil .NET 3.5, com os recursos de servidor removido.|✓|✓|✓|
> |System.&#8203;Windows.dll| |✓|✓|✓|
> |System.&#8203;Xml.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.&#8203;Linq.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|✓|✓|✓|
> |System.Xml.Serialization.dll| |✓|✓|✓|
> |Xamarin.iOS.dll|Esse assembly contém a associação para a API CocoaTouch c#. Isso é usado apenas em projetos do iOS unificado.|✓| | |
> |Java.Interop.dll| | |✓| |
> |Mono.Android.dll| | |✓| |
> |Mono.Android.&#8203;Export.dll| | |✓| |
> |Mono.Posix.dll| | |✓| |
> |System.&#8203;EnterpriseServices.dll| | |✓| |
> |Xamarin.Android.&#8203;NUnitLite.dll| | |✓| |
> |Mono.CompilerServices.&#8203;SymbolWriter.dll|Para autores de compilador.| | |✓|
> |Xamarin.Mac.dll| | | |✓|
> |System.&#8203;Drawing.dll|Não há suporte para System. Drawing na API unificada do xamarin. Mac, .NET 4.5 ou estruturas móveis. Suporte de System. Drawing pode ser adicionado para iOS e macOS usando o [sysdrawing coregraphics](https://github.com/mono/sysdrawing-coregraphics) biblioteca|✓| |✓|
