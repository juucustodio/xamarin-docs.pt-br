---
title: Assemblies disponíveis
description: Este documento lista os assemblies disponíveis para uso no Xamarin. iOS, Xamarin. Android e Xamarin. Mac. Ele também fornece links para a documentação sobre bibliotecas de .NET Standard e bibliotecas de classes portáteis.
ms.prod: xamarin
ms.assetid: AEF4ED0E-391F-4FA4-9F18-842BC24C272D
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: 31066d09b1e753dd054a6a908b626ca3edee008e
ms.sourcegitcommit: 04929b5ff4384ca807727bec7c0467111a7eb283
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867609"
---
# <a name="available-assemblies"></a>Assemblies disponíveis

O xamarin. iOS, o Xamarin. Android e o Xamarin. Mac são fornecidos com mais de uma dúzia de assemblies. Assim como o Silverlight é um subconjunto estendido dos assemblies do .NET da área de trabalho, as plataformas Xamarin também são um subconjunto estendido de vários assemblies do Silverlight e do .NET da área de trabalho

As plataformas Xamarin não são compatíveis com ABI com assemblies existentes compilados para um perfil diferente. Você deve recompilar o código-fonte para gerar assemblies destinados ao perfil correto (assim como você precisa recompilar o código-fonte para o Silverlight e o .NET 3,5 de destino separadamente).

Os aplicativos Xamarin. Mac podem ser compilados em três modos: um que usa o perfil móvel organizado do Xamarin, a estrutura Xamarin. Mac .NET 4,5, que permite que você direcione assemblies de área de trabalho completos existentes e um que usa a API .NET encontrada em um sistema mono instalação. Para obter mais informações, consulte nossa documentação de [estruturas de destino](~/mac/platform/target-framework.md) .

## <a name="net-standard-libraries"></a>Bibliotecas de .NET Standard

Além das associações do iOS, do Android e do Mac, os projetos do Xamarin podem consumir [.net Standard bibliotecas](~/cross-platform/app-fundamentals/net-standard.md).

## <a name="portable-class-libraries"></a>Bibliotecas de Classes Portáteis

Os projetos do Xamarin também podem consumir [bibliotecas de classes portáteis do .net](~/cross-platform/app-fundamentals/pcl.md), embora essa tecnologia esteja sendo substituída em favor da .net Standard.

## <a name="supported-assemblies"></a>Assemblies com suporte

Esses são os assemblies disponíveis no **Gerenciador de referências > assemblies > Framework** (Visual Studio 2017) e **Editar referências > pacotes** (Visual Studio para Mac) e sua compatibilidade com as plataformas Xamarin.

> [!div class="mx-tdCol2BreakAll"]
> |Assembly|Compatibilidade de API|Xamarin iOS|Xamarin Android|Xamarin Mac|
> |--------|-----------------|-----------|---------------|-----------|
> |FSharp.Core.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |l18N.dll|Inclui CJK, Oriente Médio, outro, raro, oeste|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |Microsoft.CSharp.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |Mono.CSharp.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |Mono.Data.Sqlite.dll|Provedor de ADO.NET para SQLite; consulte limitações.|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |Mono.Data.Tds.dll|Suporte a protocolo TDS; usado para suporte a [System. Data. SqlClient](xref:System.Data.SqlClient) em [System. Data](xref:System.Data).|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |Mono.Dynamic.&#8203;Interpreter.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")| | |
> |Mono.Security.dll|APIs de criptografia.|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |monotouch.dll|Esse assembly contém a C# associação à API Cocoatouch. Isso só está disponível em projetos clássicos do iOS.|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")| | |
> |MonoTouch.&#8203;Dialog-1.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")| | |
> |MonoTouch.&#8203;NUnitLite.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")| | |
> |mscorlib.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |OpenTK-1.0.dll|As APIs do OpenGL/com orientação a objeto aberto, estendidas para fornecer suporte a dispositivos iPhone.|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), além de tipos dos seguintes namespaces:<br />{1&gt;System.Collections.Specialized&lt;1}<br />System.&#8203;ComponentModel<br />System.ComponentModel.Design<br />{1&gt;System.Diagnostics&lt;1}<br />{1&gt;System.IO&lt;1}<br />{1&gt;{2&gt;System.IO.Compression&lt;2}&lt;1}<br />System.IO.Compression.FileSystem<br />{1&gt;{2&gt;System.Net&lt;2}&lt;1}<br />System.Net.Cache<br />System.Net.Mail<br />System.Net.Mime<br />System.Net.&#8203;NetworkInformation<br />System.Net.Security<br />System.Net.Sockets<br />System.Runtime.&#8203;InteropServices<br />{1&gt;System.Runtime.Versioning&lt;1}<br />System.Security.&#8203;AccessControl<br />System.Security.Authentication<br />System.Security.&#8203;Cryptography<br />System.Security.Permissions<br />{1&gt;System.Threading&lt;1}<br />System.Timers|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.&#8203;ComponentModel.&#8203;Composition.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.&#8203;ComponentModel.&#8203;DataAnnotations.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.Core.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.Data.dll|[.Net 3,5](https://msdn.microsoft.com/library/ms229335.aspx) , com [algumas funcionalidades removidas](~/ios/data-cloud/system.data.md).|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.Data.&#8203;Services.&#8203;Client.dll|Cliente oData completo.|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.IO.&#8203;Compression| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.IO.&#8203;Compression.&#8203;FileSystem| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.Json.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.Net.&#8203;Http.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.&#8203;Numerics.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.Runtime.&#8203;Serialization.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.&#8203;ServiceModel.dll|Pilha do WCF como presente no [Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx)|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.&#8203;ServiceModel.&#8203;Internals.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.&#8203;ServiceModel.&#8203;Web.dll|[Silverlight](https://msdn.microsoft.com/library/cc838194(VS.95).aspx), além de tipos dos seguintes namespaces: <br />System<br />{1&gt;System.ServiceModel.Channels&lt;1}<br />{1&gt;System.ServiceModel.Description&lt;1}<br />System.ServiceModel.Web|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.&#8203;Transactions.dll|[.Net 3,5](https://msdn.microsoft.com/library/ms229335.aspx); parte do suporte a [System. Data](~/ios/data-cloud/system.data.md) .|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.Web.&#8203;Services.dll|Serviços Web básicos do perfil .NET 3,5, com os recursos do servidor removidos.|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.&#8203;Windows.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.&#8203;Xml.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.Xml.&#8203;Linq.dll|[.NET 3.5](https://msdn.microsoft.com/library/ms229335.aspx)|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.Xml.Serialization.dll| |![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")|![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")|![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |Xamarin.iOS.dll|Esse assembly contém a C# associação à API Cocoatouch. Isso só é usado em projetos iOS unificados.|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")| | |
> |Java.Interop.dll| | |![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")| |
> |Mono.Android.dll| | |![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")| |
> |Mono.Android.&#8203;Export.dll| | |![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")| |
> |Mono.Posix.dll| | |![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")| |
> |System.&#8203;EnterpriseServices.dll| | |![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")| |
> |Xamarin.Android.&#8203;NUnitLite.dll| | |![Xamarin. Android com suporte](~/media/shared/yes.png "Xamarin. Android com suporte")| |
> |Mono.CompilerServices.&#8203;SymbolWriter.dll|Para escritores de compilador.| | |![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |Xamarin.Mac.dll| | | |![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|
> |System.&#8203;Drawing.dll|Não há suporte para System. Drawing no API Unificada para o Xamarin. Mac, o .NET 4,5 ou para as estruturas móveis. O suporte a System. Drawing pode ser adicionado ao iOS e ao macOS usando a biblioteca [sysdrawing-CoreGraphics](https://github.com/mono/sysdrawing-coregraphics)|![Xamarin. iOS com suporte](~/media/shared/yes.png "Xamarin. iOS com suporte")| |![Xamarin. Mac com suporte](~/media/shared/yes.png "Xamarin. Mac com suporte")|