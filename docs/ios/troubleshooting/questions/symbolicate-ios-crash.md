---
title: Onde posso encontrar o arquivo .dSYM para simbolizar os logs de falha do iOS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CB8607B9-FFDA-4617-8210-8E43EC512588
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/09/2018
ms.openlocfilehash: 480ec0d4a815d62422e31a4110af52ca1628d1f7
ms.sourcegitcommit: d4d293174a8324ce82b8f961ae6eadce294cafd7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98194894"
---
# <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logs"></a>Onde posso encontrar o arquivo .dSYM para simbolizar os logs de falha do iOS?

Ao criar um aplicativo iOS com Visual Studio para Mac ou o Visual Studio 2017, o arquivo. dSYM necessário para os relatórios de falha simbólicos será colocado na mesma hierarquia de diretório que o arquivo de projeto do aplicativo (. csproj). O local exato depende das configurações de Build do seu projeto:

- Se você habilitou compilações específicas do dispositivo, o. dSYM pode ser encontrado no seguinte diretório:

    **&lt;diretório do &gt; projeto &lt; /bin/ &gt; / &lt; &gt; de configuração de plataforma/Device-Builds/so do &lt; dispositivo &gt; - &lt; -versão&gt;/**

    Por exemplo:
  
    **TestApp/bin/iPhone/Release/dispositivo-Builds/iPhone 8.4-11.3.1/**

- Se você não tiver habilitado compilações específicas do dispositivo, o. dSYM poderá ser encontrado no seguinte diretório:

    **&lt;configuração da &gt; plataforma/bin/do diretório do projeto &lt; &gt; / &lt;&gt;/**

    Por exemplo:

    **TestApp/bin/iPhone/versão/**

> [!NOTE]
> Como parte do processo de compilação, o Visual Studio 2017 copia o arquivo. dSYM do host de Build do Mac para o Windows. Se você não vir um arquivo. dSYM no Windows, certifique-se de ter configurado as configurações de Build do aplicativo para [criar um arquivo. ipa](~/ios/deploy-test/app-distribution/ipa-support.md).

## <a name="see-also"></a>Confira também

- [Arquivos de falha do Symbolicating iOS (Xamarin. iOS)](https://www.jmillerdev.com/symbolicating-ios-crash-files-xamarin-ios/)
- [Desmistificando os logs de falhas do aplicativo iOS](https://www.raywenderlich.com/23704/demystifying-ios-application-crash-logs)
