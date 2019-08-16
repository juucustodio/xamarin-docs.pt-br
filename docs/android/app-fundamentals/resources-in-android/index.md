---
title: Recursos do Android
description: Este artigo apresenta o conceito de recursos do Android no Xamarin. Android e documentará como usá-los. Ele aborda como usar recursos em seu aplicativo Android para dar suporte à localização de aplicativos e vários dispositivos, incluindo diferentes tamanhos de tela e densidades.
ms.prod: xamarin
ms.assetid: C0DCC856-FA36-04CD-443F-68D26075649E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/01/2018
ms.openlocfilehash: d23b29b7e49c210dc44163e41735fa96b55d12f2
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526318"
---
# <a name="android-resources"></a>Recursos do Android

_Este artigo apresenta o conceito de recursos do Android no Xamarin. Android e documentará como usá-los. Ele aborda como usar recursos em seu aplicativo Android para dar suporte à localização de aplicativos e vários dispositivos, incluindo diferentes tamanhos de tela e densidades._


## <a name="overview"></a>Visão geral

Um aplicativo do Android raramente é apenas o código-fonte. Geralmente, há muitos outros arquivos que compõem um aplicativo: vídeo, imagens, fontes e arquivos de áudio apenas para citar alguns. Coletivamente, esses arquivos de código não-fonte são chamados de recursos e são compilados (juntamente com o código-fonte) durante o processo de compilação e empacotados como um APK para distribuição e instalação em dispositivos:

![Diagrama de empacotamento](images/packaging-diagram.png)

Os recursos oferecem várias vantagens para um aplicativo Android:

- **Separação de código** &ndash; Separa o código-fonte de imagens, cadeias de caracteres, menus, animações, cores etc. Como esses recursos podem ajudar consideravelmente na localização.

- **Direcionar vários dispositivos** &ndash; Fornece suporte mais simples a diferentes configurações de dispositivo sem alterações de código.

- **Verificação de tempo de compilação** &ndash; Os recursos são estáticos e compilados no aplicativo. Isso permite que o uso dos recursos seja verificado no momento da compilação, quando será fácil detectar e corrigir os erros, em oposição ao tempo de execução, quando for mais difícil de localizar e dispendioso para corrigir.

Quando um novo projeto Xamarin. Android é iniciado, um diretório especial chamado Resources é criado, juntamente com alguns subdiretórios:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Conteúdo e pasta de recursos](images/resources-folder-vs.png)

Na imagem acima, os recursos do aplicativo são organizados de acordo com seu tipo nesses subdiretórios: as imagens vão para o diretório desenhável; as exibições vão para o subdiretório de **layout** , etc.
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Conteúdo e pasta de recursos](images/resources-folder-xs.png)

Na imagem acima, os recursos do aplicativo são organizados de acordo com seu tipo nesses subdiretórios: as imagens vão para o diretório **mipmap** ; as exibições vão para o subdiretório de **layout** , etc.
 
-----

Há duas maneiras de acessar esses recursos em um aplicativo Xamarin. Android: programaticamente no código e *declarativamente* em XML usando uma sintaxe XML especial.

Esses recursos são chamados de *recursos padrão* e são usados por todos os dispositivos, a menos que uma correspondência mais específica seja especificada. Além disso, cada tipo de recurso pode opcionalmente ter *recursos alternativos* que o Android pode usar para direcionar dispositivos específicos. Por exemplo, os recursos podem ser fornecidos para direcionar a localidade do usuário, o tamanho da tela ou se o dispositivo for girado 90 graus de retrato para paisagem, etc. Em cada um desses casos, o Android carregará os recursos para uso pelo aplicativo sem qualquer esforço de codificação extra pelo desenvolvedor.

Recursos alternativos são especificados pela adição de uma cadeia de caracteres curta, chamada de qualificador, ao final do diretório que contém um determinado tipo de recursos.

Por exemplo, os **recursos/desenhar-de** especificarão as imagens para dispositivos que são definidos como uma localidade alemã, enquanto os **recursos/desenháveis-fr** armazenarão imagens para dispositivos definidos como uma localidade francesa. Um exemplo de como fornecer recursos alternativos pode ser visto na imagem abaixo, onde o mesmo aplicativo está sendo executado apenas com a localidade do dispositivo que está sendo alterada:

![Telas de exemplo para localidades diferentes](images/localized-screenshots.png)

Este artigo usará uma visão abrangente de como usar recursos e abordará os seguintes tópicos:

- **Noções básicas de recursos do Android** &ndash; Usando recursos padrão de forma programática e declarativa, adicionando tipos de recursos como imagens e fontes a um aplicativo.

- **Configurações específicas do dispositivo** &ndash; Suporte a diferentes resoluções de tela e densidades em um aplicativo.

- **Localização** &ndash; Usando recursos para dar suporte a regiões diferentes, um aplicativo pode ser usado.


## <a name="related-links"></a>Links relacionados

- [Uso de recursos do Android](~/android/app-fundamentals/resources-in-android/android-assets.md)
- [Conceitos básicos de aplicativo](https://developer.android.com/guide/topics/fundamentals.html)
- [Recursos do aplicativo](https://developer.android.com/guide/topics/resources/index.html)
- [Suporte a várias telas](https://developer.android.com/guide/practices/screens_support.html)
