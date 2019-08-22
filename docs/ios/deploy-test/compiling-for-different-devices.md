---
title: Compilar para diferentes dispositivos no Xamarin.iOS
description: Este documento descreve várias opções de configuração de build que podem ser usadas para personalizar um build do Xamarin.iOS para diferentes dispositivos.
ms.prod: xamarin
ms.assetid: 3B259248-887E-3E4F-E09C-7AD28C2A8CEE
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 1b3bc6874a8584b60b15d1d373ad97fde565dfc4
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527121"
---
# <a name="compiling-for-different-devices-in-xamarinios"></a>Compilar para diferentes dispositivos no Xamarin.iOS

As propriedades de build do executável podem ser configuradas da página de propriedades **Build do iOS** do projeto, que é encontrada clicando-se com o botão direito do mouse no nome do projeto e navegando-se até **Opções > Build do iOS** no Visual Studio para Mac, e em **Propriedades** no Visual Studio:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)


[![](compiling-for-different-devices-images/image1.png "A página de propriedades do Build do iOS do projeto")](compiling-for-different-devices-images/image1.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](compiling-for-different-devices-images/image1a.png "A página de propriedades do Build do iOS do projeto")](compiling-for-different-devices-images/image1a.png#lightbox)

-----

Além das opções de configuração disponíveis na interface do usuário, você também pode passar o seu próprio conjunto de opções de linha de comando para a [ferramenta de build do Xamarin.iOS (mtouch)](~/ios/deploy-test/mtouch.md).

[http://iossupportmatrix.com/](http://iossupportmatrix.com/) é um recurso útil que pode ser usado para ter certeza de que você está incluindo todos os dispositivos, as arquiteturas e as versões de iOS necessárias.

 <a name="SDK_Options" />


## <a name="sdk-options"></a>Opções do SDK

O Visual Studio para Mac permite que você configure duas propriedades importantes relacionadas ao SDK: a versão do SDK do iOS usada para compilar seu software e o destino de implantação (ou a versão do iOS mínima necessária).

A opção **Versão do SDK** do iOS permite que você use diferentes versões de um SDK publicado pela Apple, o que direciona o Xamarin.iOS aos compiladores, vinculadores e bibliotecas que ele deve referenciar durante o build. 

A configuração **Destino de Implantação** é usada para selecionar a versão mínima necessária do sistema operacional no qual o aplicativo será executado. Isso é definido no arquivo Info.plist do projeto. Você deve escolher a versão mínima que tem todas as APIs que você precisa executar em seu aplicativo.

Em geral, a API do Xamarin.iOS expõe todos os métodos disponíveis na versão mais recente do SDK e, quando necessário, fornecemos propriedades de conveniência que permitem que você detecte se a funcionalidade está disponível em tempo de execução (por exemplo, `UIDevice.UserInterfaceIdiom` e `UIDevice.IsMultitaskingSupported` sempre funcionam em Xamarin.iOS, fazemos todo o trabalho nos bastidores).

 <a name="Linking" />


## <a name="linking"></a>Vinculação

Consulte nossa página dedicada no [Vinculador](~/ios/deploy-test/linker.md) para saber mais sobre como o vinculador ajuda a reduzir o tamanho dos executáveis e como usá-lo efetivamente.

 <a name="Code_Generation_Engine" />


## <a name="code-generation-engine"></a>Mecanismo de geração de código

Começando com o Xamarin.iOS 4.0, há dois back-ends de geração de código para o Xamarin.iOS. O mecanismo de geração de código Mono normal e outro com base no Compilador de otimização do LLVM. Cada mecanismo tem seus prós e contras.

Normalmente, durante o processo de desenvolvimento, você provavelmente usará o mecanismo de geração de código Mono, pois ele permitirá iterar rapidamente. Para builds de versão e implantação na App Store, será melhor mudar para o mecanismo de geração de código do LLVM.

O mecanismo de back-end de otimização do LLVM produz código mais rígido que o do mecanismo Mono, tendo como contrapartida tempos de compilação longos.

Você pode habilitar essas opções de Build do iOS no Visual Studio para Mac ou no Visual Studio.

[![](compiling-for-different-devices-images/image2.png "Habilitando LLVM")](compiling-for-different-devices-images/image2.png#lightbox)

[![](compiling-for-different-devices-images/image2a.png "Habilitando LLVM")](compiling-for-different-devices-images/image2a.png#lightbox)

 <a name="ARMV7_and_ARMV7s_support" />


## <a name="architecture-support"></a>Suporte de arquitetura

<a name="armv6-discontinued" />

### <a name="armv6-xamarinios-discontinued-support-for-armv6-with-v810"></a>ARMv6 (o Xamarin.iOS descontinuou o suporte a ARMv6 com a v8.10)

- iPhone (original), 3G
- iPod de 1ª, 2ª geração

### <a name="armv7"></a>ARMv7

- iPhone 3GS, 4, 4S
- iPad 1, 2, 3, Mini
- iPod de 3a, 4a, 5a geração

### <a name="armv7s"></a>ARMv7s

- iPhone 5
- iPhone 5c
- iPad 4

Se você direcionar somente ao processador ARMv7s, o código gerado será um pouco mais rápido, mas ele não será mais executado nos sistemas ARMv7 ou ARMv6, a menos que você compile um binário multiarquitetura que contenha vários executáveis em seu pacote.

### <a name="arm64-xamarinios-started-supporting-arm64-in-v86"></a>ARM64 (o Xamarin.iOS começou a dar suporte ao ARM64 na v8.6)

- iPhone 5s
- iPhone SE
- iPhone 6, 6 Plus
- iPhone 6s, 6s Plus
- iPhone 7, 7 Plus
- iPhone 8, 8 Plus
- iPhone X
- iPad Air
- iPad Air 2
- iPad Mini 2, 3, 4
- iPad Pro (all)

Observe que quaisquer builds enviados à App Store devem conter o suporte de 64 bits, esse é um requisito estabelecido pela [Apple](https://developer.apple.com/news/?id=12172014b). Além disso, o iOS 11 só é compatível com aplicativos de 64 bits.

 <a name="ARM_Thumb_Support" />


### <a name="arm-thumb-2-support"></a>Suporte do ARM Thumb-2

Thumb é um conjunto de instruções mais compacto usado por processadores ARM. Habilitando o suporte a Thumb, você pode reduzir o tamanho de seu executável, tendo como contrapartida tempos de execução mais lentos. O Thumb é compatível com o ARMv7 e o ARMv7s.

 <a name="Conditional_framwork_useage" />


## <a name="conditional-framework-usage"></a>Uso de estrutura condicional

Se você quer que seu projeto aproveite alguns dos recursos das versões mais recentes do iOS, talvez você precise depender condicionalmente de certas estruturas novas. Um exemplo perfeito disso é quando você deseja usar iAd ao executar no iOS 4.0 ou posterior, mas sem deixar de dar suporte a dispositivos 3.x. Para fazer isso, você precisa informar ao Xamarin.iOS que você precisa vincular a estrutura iAd de modo "fraco". Associações fracas asseguram que a estrutura só é carregada sob demanda na primeira vez que uma classe da estrutura é necessária.

Para fazer isso, você deve realizar as seguintes etapas:

- Abra as **Opções do Projeto** e navegue até o painel **Build do iOS**.
- Adicione `'-gcc_flags "-weak_framework iAd"'` às **Opções Adicionais** para cada configuração que você deseja vincular de modo fraco em:


[![](compiling-for-different-devices-images/image3.png "Opções Adicionais")](compiling-for-different-devices-images/image3.png#lightbox)


Além disso, você precisará evitar que seu uso dos tipos execute em versões anteriores do iOS, em que eles podem não existir. Há vários métodos para fazer isso, um dos quais é analisar `UIDevice.CurrentDevice.SystemVersion`.



## <a name="related-links"></a>Links relacionados

- [Vinculador](~/ios/deploy-test/linker.md)
- [Externa – matriz de suporte do iOS](http://iossupportmatrix.com/)
