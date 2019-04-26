---
title: Onde posso encontrar o arquivo .dSYM para simbolizar os logs de falha do iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/09/2018
ms.openlocfilehash: 0b8f3aa736cba6e70fbf346766499c23a9bbe270
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61418198"
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>Onde posso encontrar o arquivo .dSYM para simbolizar os logs de falha do iOS?

Ao criar um aplicativo iOS com o Visual Studio para Mac ou Visual Studio 2017, o arquivo. dsym que é necessário para simbolizar relatórios de falha será colocado na mesma hierarquia de diretório do arquivo de projeto do seu aplicativo (. csproj). O local exato depende de configurações de compilação do seu projeto:

- Se você tiver habilitado builds específicos de dispositivo, o. dsym podem ser encontradas no seguinte diretório:

    **&lt;diretório do projeto&gt;/bin/&lt;plataforma&gt;/&lt;configuração&gt;/device-builds /&lt;dispositivo&gt; - &lt; versão do sistema operacional&gt;/**

    Por exemplo:
  
    **TestApp/bin/iPhone/Release/device-builds/iphone8.4-11.3.1/**

- Se você não tiver habilitado os builds específicos de dispositivo, o. dsym podem ser encontradas no seguinte diretório:

    **&lt;diretório do projeto&gt;/bin/&lt;plataforma&gt;/&lt;configuração&gt;/**

    Por exemplo:

    **TestApp/bin/iPhone/Release/**

> [!NOTE]
> Como parte do processo de compilação, o Visual Studio 2017 copia o arquivo. dsym do host de build do Mac para Windows. Se você não vir um arquivo. dsym no Windows, certifique-se de que você configurou as configurações de compilação do seu aplicativo para [criar um arquivo. IPA](~/ios/deploy-test/app-distribution/ipa-support.md).

## <a name="see-also"></a>Consulte também

- [Symbolicating iOS (xamarin. IOS) de arquivos de falhas](http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Desmistificando os Logs de falha de aplicativo do iOS](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)

