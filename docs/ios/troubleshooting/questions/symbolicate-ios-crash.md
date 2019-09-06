---
title: Onde posso encontrar o arquivo .dSYM para simbolizar os logs de falha do iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/09/2018
ms.openlocfilehash: edd5a2c1ed2efdffc9bd28bb06ac19348615eefc
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292107"
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>Onde posso encontrar o arquivo .dSYM para simbolizar os logs de falha do iOS?

Ao criar um aplicativo iOS com Visual Studio para Mac ou o Visual Studio 2017, o arquivo. dSYM necessário para os relatórios de falha simbólicos será colocado na mesma hierarquia de diretório que o arquivo de projeto do aplicativo (. csproj). O local exato depende das configurações de Build do seu projeto:

- Se você habilitou compilações específicas do dispositivo, o. dSYM pode ser encontrado no seguinte diretório:

    **&lt;diretório do&gt;projeto&lt;/bin/&gt;configuração/daplataforma- /Device-Builds/dispositivo&gt; &lt;&lt;&gt;&lt; versão do so&gt;/**

    Por exemplo:
  
    **TestApp/bin/iPhone/Release/device-builds/iphone8.4-11.3.1/**

- Se você não tiver habilitado compilações específicas do dispositivo, o. dSYM poderá ser encontrado no seguinte diretório:

    **&lt;configuração da&gt;plataforma&lt;/bin/do&gt;diretóriodoprojeto/&lt;&gt;/**

    Por exemplo:

    **TestApp/bin/iPhone/Release/**

> [!NOTE]
> Como parte do processo de compilação, o Visual Studio 2017 copia o arquivo. dSYM do host de Build do Mac para o Windows. Se você não vir um arquivo. dSYM no Windows, certifique-se de ter configurado as configurações de Build do aplicativo para [criar um arquivo. ipa](~/ios/deploy-test/app-distribution/ipa-support.md).

## <a name="see-also"></a>Consulte também

- [Arquivos de falha do Symbolicating iOS (Xamarin. iOS)](https://www.jmillerdev.net/symbolicating-ios-crash-files-xamarin-ios/)
- [Desmistificando os logs de falhas do aplicativo iOS](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)

