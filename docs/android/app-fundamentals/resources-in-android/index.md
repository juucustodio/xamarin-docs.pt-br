---
title: Recursos do Android
description: Este artigo apresenta o conceito de recursos do Android no xamarin. Android e documentar como usá-los. Ele aborda como usar recursos em seu aplicativo Android para dar suporte à localização do aplicativo e vários dispositivos, incluindo densidades e diferentes tamanhos de tela.
ms.prod: xamarin
ms.assetid: C0DCC856-FA36-04CD-443F-68D26075649E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/01/2018
ms.openlocfilehash: d9cd6bf3ae51c6e27be88481e412995bd4113c17
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117246"
---
# <a name="android-resources"></a>Recursos do Android

_Este artigo apresenta o conceito de recursos do Android no xamarin. Android e documentar como usá-los. Ele aborda como usar recursos em seu aplicativo Android para dar suporte à localização do aplicativo e vários dispositivos, incluindo densidades e diferentes tamanhos de tela._


## <a name="overview"></a>Visão geral

Um aplicativo do Android raramente é apenas o código-fonte. Geralmente, há muitos outros arquivos que compõem um aplicativo: vídeo, imagens, fontes e arquivos de áudio apenas para citar alguns. Coletivamente, esses arquivos de código sem origem são chamados de recursos e são compilados (juntamente com o código-fonte) durante o processo de compilação e empacotados como um APK para distribuição e a instalação em dispositivos:

![Diagrama de empacotamento](images/packaging-diagram.png)

Recursos oferecem várias vantagens para um aplicativo do Android:

-  **Separação de código** &ndash; separa o código-fonte de imagens, cadeias de caracteres, menus, animações, cores, etc. Como tal, recursos podem ajudar consideravelmente na localização.

-  **Vários dispositivos de destino** &ndash; fornece suporte a mais simples de configurações de dispositivo diferentes sem alterações de código.

-  **Verificação de tempo de compilação** &ndash; recursos são compilados no aplicativo e estático. Isso permite que o uso de recursos a ser verificado no tempo de compilação, quando será fácil pegar e corrigir os erros, em vez de tempo de execução quando ele é mais difícil localizar e dispendiosas corrigir.

Quando um novo projeto do xamarin. Android é iniciado, um diretório especial chamado de recursos é criado, junto com alguns subdiretórios:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Conteúdo e a pasta de recursos](images/resources-folder-vs.png)

Na imagem acima, os recursos do aplicativo são organizados de acordo com seu tipo para esses subdiretórios: imagens serão incluídas na **desenháveis** diretório; exibições de entrar a **layout** subdiretório, etc.
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Conteúdo e a pasta de recursos](images/resources-folder-xs.png)

Na imagem acima, os recursos do aplicativo são organizados de acordo com seu tipo para esses subdiretórios: imagens serão incluídas na **mipmap** diretório; exibições de entrar a **layout** subdiretório, etc.
 
-----

Há duas maneiras para acessar esses recursos em um aplicativo xamarin. Android: *programaticamente* no código e *declarativamente* no XML usando uma sintaxe XML especial.

Esses recursos são chamados *recursos padrão* e são usados por todos os dispositivos, a menos que uma correspondência mais específica foi especificada. Além disso, todos os tipos de recursos podem opcionalmente ter *recursos alternativos* que Android pode usar como destino dispositivos específicos. Por exemplo, os recursos podem ser fornecidos para direcionar a localidade do usuário, o tamanho da tela, ou se o dispositivo é girada em 90 graus de retrato para paisagem, etc. Em cada um desses casos, o Android carregará os recursos para uso pelo aplicativo sem qualquer esforço de codificação extra pelo desenvolvedor.

Recursos alternativos são especificados com a adição de uma cadeia de caracteres curta, chamada de um *qualificador*, até o final do diretório que contém um determinado tipo de recursos.

Por exemplo, **recursos/drawable-de** especificará as imagens para dispositivos que são definidos para uma localidade alemão, enquanto **recursos/drawable-fr** seria manter imagens para a localidade francês do conjunto de dispositivos. Um exemplo de fornecer recursos alternativos pode ser visto na imagem abaixo, onde o mesmo aplicativo está sendo executado com apenas a localidade de alterar o dispositivo:

![Telas de exemplo para diferentes localidades](images/localized-screenshots.png)

Este artigo será dar uma olhada abrangente usando os recursos e abrangem os tópicos a seguir:

-  **Noções básicas de recurso Android** &ndash; usando recursos padrão de forma programática e declarativa, adicionando tipos de recursos como imagens e as fontes a um aplicativo.

-  **Configurações específicas de dispositivo** &ndash; com suporte a diferentes resoluções de tela e densidades em um aplicativo.

-  **Localização** &ndash; usando recursos para dar suporte as diferentes regiões em que um aplicativo pode ser usado.


## <a name="related-links"></a>Links relacionados

- [Uso de recursos do Android](~/android/app-fundamentals/resources-in-android/android-assets.md)
- [Conceitos básicos de aplicativo](http://developer.android.com/guide/topics/fundamentals.html)
- [Recursos do aplicativo](http://developer.android.com/guide/topics/resources/index.html)
- [Suporte a várias telas](http://developer.android.com/guide/practices/screens_support.html)
