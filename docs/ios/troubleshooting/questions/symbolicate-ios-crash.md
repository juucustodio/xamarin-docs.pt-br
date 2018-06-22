---
title: Onde posso encontrar o arquivo .dSYM symbolicate logs de falha de iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/09/2018
ms.openlocfilehash: 60d897be8739ff5b78a322bc4ea3f43011785bb5
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
ms.locfileid: "33920637"
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>Onde posso encontrar o arquivo .dSYM symbolicate logs de falha de iOS?

Ao compilar um aplicativo do iOS com o Visual Studio para Mac ou o Visual Studio de 2017, o arquivo .dSYM que é necessário para symbolicate relatórios de falha será colocado na mesma hierarquia de diretório, como o arquivo de projeto do aplicativo (. csproj). O local exato depende de configurações de compilação do projeto:

- Se você tiver habilitado a versões específicas de dispositivo, o o .dSYM pode ser encontrado no seguinte diretório:

    **&lt;diretório do projeto&gt;/bin/&lt;plataforma&gt;/&lt;configuração&gt;/device-builds /&lt;dispositivo&gt; - &lt; versão do sistema operacional&gt;/**

    Por exemplo:
  
    **TestApp/bin/iPhone/Release/device-builds/iphone8.4-11.3.1/**

- Se você não tiver habilitado a versões específicas de dispositivo, o .dSYM podem ser encontradas no seguinte diretório:

    **&lt;diretório do projeto&gt;/bin/&lt;plataforma&gt;/&lt;configuração&gt;/**

    Por exemplo:

    **TestApp/bin/iPhone/versão /**

> [!NOTE]
> Como parte do processo de compilação, o Visual Studio de 2017 copia o arquivo de .dSYM entre o host de compilação de Mac e Windows. Se você não vir um arquivo .dSYM no Windows, certifique-se de que você definiu as configurações de compilação do seu aplicativo para [criar um arquivo. IPA](~/ios/deploy-test/app-distribution/ipa-support.md).

## <a name="see-also"></a>Consulte também

- [Symbolicating iOS arquivos falhar (xamarin)](http://jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Desmistificando Logs de falhas de aplicativo do iOS](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)

