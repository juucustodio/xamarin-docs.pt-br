---
title: Recursos do Android
description: "Este artigo apresenta o conceito de recursos Android em xamarin e irá documentar como usá-los. Ele aborda como usar recursos em seu aplicativo do Android para oferecer suporte a localização do aplicativo e vários dispositivos, incluindo densidades e diferentes tamanhos de tela."
ms.topic: article
ms.prod: xamarin
ms.assetid: C0DCC856-FA36-04CD-443F-68D26075649E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/01/2018
ms.openlocfilehash: 6546870d85f7b77e60dff0cb9e6075f982c9cb8e
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="android-resources"></a>Recursos do Android

_Este artigo apresenta o conceito de recursos Android em xamarin e irá documentar como usá-los. Ele aborda como usar recursos em seu aplicativo do Android para oferecer suporte a localização do aplicativo e vários dispositivos, incluindo densidades e diferentes tamanhos de tela._


## <a name="overview"></a>Visão geral

Um aplicativo do Android raramente é apenas o código fonte. Geralmente, há muitos outros arquivos que compõem um aplicativo: vídeo, imagens, fontes e arquivos de áudio para citar alguns. Coletivamente, esses arquivos de código-fonte não são chamados de recursos e são compilados (junto com o código-fonte) durante o processo de compilação e empacotados como um APK para a distribuição e a instalação em dispositivos:

![Diagrama de empacotamento](images/packaging-diagram.png)

Recursos oferecem várias vantagens para um aplicativo do Android:

-  **Separação de código** &ndash; separa o código-fonte de imagens, cadeias de caracteres, menus, animações, cores, etc. Como tal, recursos podem ajudar consideravelmente na localização.

-  **Vários dispositivos de destino** &ndash; fornece suporte a mais simples de configurações de dispositivo diferentes sem alterações de código.

-  **Verificação de tempo de compilação** &ndash; recursos são compilados no aplicativo e estático. Isso permite o uso de recursos a ser verificada em tempo de compilação, quando é fácil de identificar e corrigir os erros, em vez de tempo de execução quando ele é mais difícil localizar e caros corrigir.

Quando um novo projeto de xamarin é iniciado, um diretório especial chamado de recursos é criado, juntamente com alguns subdiretórios:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Conteúdo e a pasta de recursos](images/resources-folder-vs.png)

Na imagem acima, os recursos de aplicativo são organizados de acordo com seu tipo nesses subdiretórios: imagens entrará **drawable** diretório; exibições entrar o **layout** subdiretório, etc.
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Conteúdo e a pasta de recursos](images/resources-folder-xs.png)

Na imagem acima, os recursos de aplicativo são organizados de acordo com seu tipo nesses subdiretórios: imagens entrará **mipmap** diretório; exibições entrar o **layout** subdiretório, etc.
 
-----

Há duas maneiras para acessar esses recursos em um aplicativo xamarin: *programaticamente* no código e *declarativamente* no XML usando uma sintaxe XML especial.

Esses recursos são chamados *recursos padrão* e são usados por todos os dispositivos, a menos que uma correspondência mais específica foi especificada. Além disso, cada tipo de recurso pode opcionalmente ter *recursos alternativos* que Android pode usar como destino dispositivos específicos. Por exemplo, os recursos podem ser fornecidos para a localidade do usuário, o tamanho de tela de destino ou se o dispositivo for girados 90 graus de retrato para paisagem, etc. Em cada um desses casos, o Android carregará os recursos para uso pelo aplicativo sem qualquer esforço extra codificação pelo desenvolvedor.

Recursos alternativos são especificados com a adição de uma cadeia de caracteres curta, chamada um *qualificador*, até o final do diretório contendo um determinado tipo de recursos.

Por exemplo, **recursos/drawable-de** especificará as imagens para dispositivos que são definidas para uma localidade alemão, enquanto **recursos/drawable-fr** deveria conter imagens para dispositivos definido para uma localidade francesa. Um exemplo de fornecer recursos alternativos pode ser visto na imagem abaixo onde o mesmo aplicativo está sendo executado com apenas a localidade de alterar o dispositivo:

![Telas de exemplo para localidades diferentes](images/localized-screenshots.png)

Este artigo dar uma olhada abrangente usando os recursos e abrange os seguintes tópicos:

-  **Noções básicas de recurso Android** &ndash; usando recursos padrão forma programática e declarativa, adicionando tipos de recursos, como imagens e fontes a um aplicativo.

-  **Configurações específicas de dispositivo** &ndash; suporte a diferentes resoluções de tela e densidades em um aplicativo.

-  **Localização** &ndash; usando recursos para dar suporte as diferentes regiões em que um aplicativo pode ser usado.


## <a name="related-links"></a>Links relacionados

- [Uso de recursos do Android](~/android/app-fundamentals/resources-in-android/android-assets.md)
- [Conceitos básicos de aplicativo](http://developer.android.com/guide/topics/fundamentals.html)
- [Recursos do aplicativo](http://developer.android.com/guide/topics/resources/index.html)
- [Suporte a várias telas](http://developer.android.com/guide/practices/screens_support.html)
