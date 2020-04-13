---
title: Compilar para diferentes dispositivos no Xamarin.iOS
description: Este documento descreve várias opções de configuração de build que podem ser usadas para personalizar um build do Xamarin.iOS para diferentes dispositivos.
ms.prod: xamarin
ms.assetid: 3B259248-887E-3E4F-E09C-7AD28C2A8CEE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 1f71179ccafc2daf65e792c4538bf47ea2df1e7d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75663719"
---
# <a name="compiling-for-different-devices-in-xamarinios"></a>Compilação para diferentes dispositivos em Xamarin.iOS

As propriedades de build do executável podem ser configuradas da página de propriedades **Build do iOS** do projeto, que é encontrada clicando-se com o botão direito do mouse no nome do projeto e navegando-se até **Opções > Build do iOS** no Visual Studio para Mac, e em **Propriedades** no Visual Studio:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![](compiling-for-different-devices-images/image1.png "The Projects iOS Build properties page")](compiling-for-different-devices-images/image1.png#lightbox) 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![](compiling-for-different-devices-images/image1a.png "The Projects iOS Build properties page")](compiling-for-different-devices-images/image1a.png#lightbox)

-----

Além das opções de configuração disponíveis na interface do usuário, você também pode passar o seu próprio conjunto de opções de linha de comando para a [ferramenta de build do Xamarin.iOS (mtouch)](~/ios/deploy-test/mtouch.md).

## <a name="sdk-options"></a>Opções de SDK

O Visual Studio para Mac permite que você configure duas propriedades importantes relacionadas ao SDK: a versão do SDK do iOS usada para compilar seu software e o destino de implantação (ou a versão do iOS mínima necessária).

A opção **Versão do SDK** do iOS permite que você use diferentes versões de um SDK publicado pela Apple, o que direciona o Xamarin.iOS aos compiladores, vinculadores e bibliotecas que ele deve referenciar durante o build. Clique com o botão direito do mouse no projeto e escolha **Opções,** em seguida, **iOS Build** na janela de opções:

[![Escolha a versão SDK na janela de opções](compiling-for-different-devices-images/sdk-version-sml.png)](compiling-for-different-devices-images/sdk-version.png#lightbox)

A configuração **Destino de Implantação** é usada para selecionar a versão mínima necessária do sistema operacional no qual o aplicativo será executado. Isso está definido no arquivo **Info.plist** do seu projeto. Você deve escolher a versão mínima que tem todas as APIs que você precisa executar em seu aplicativo.

[![Defina o alvo de implantação no arquivo Info.plist](compiling-for-different-devices-images/deployment-target-sml.png)](compiling-for-different-devices-images/deployment-target.png#lightbox)

Em geral, a API do Xamarin.iOS expõe todos os métodos disponíveis na versão mais recente do SDK e, quando necessário, fornecemos propriedades de conveniência que permitem que você detecte se a funcionalidade está disponível em runtime (por exemplo, `UIDevice.UserInterfaceIdiom` e `UIDevice.IsMultitaskingSupported` sempre funcionam em Xamarin.iOS, fazemos todo o trabalho nos bastidores).

## <a name="linking"></a>Vinculação

Consulte nossa página dedicada no [Vinculador](~/ios/deploy-test/linker.md) para saber mais sobre como o vinculador ajuda a reduzir o tamanho dos executáveis e como usá-lo efetivamente.

## <a name="code-generation-engine"></a>Motor de geração de código

Começando com o Xamarin.iOS 4.0, há dois back-ends de geração de código para o Xamarin.iOS. O mecanismo de geração de código Mono normal e outro com base no Compilador de otimização do LLVM. Cada mecanismo tem seus prós e contras.

Normalmente, durante o processo de desenvolvimento, você provavelmente usará o mecanismo de geração de código Mono, pois ele permitirá iterar rapidamente. Para builds de versão e implantação na App Store, será melhor mudar para o mecanismo de geração de código do LLVM.

O mecanismo de back-end de otimização do LLVM produz código mais rígido que o do mecanismo Mono, tendo como contrapartida tempos de compilação longos.

Você pode habilitar essas opções de Build do iOS no Visual Studio para Mac ou no Visual Studio.

[![](compiling-for-different-devices-images/image2.png "Enabling LLVM")](compiling-for-different-devices-images/image2.png#lightbox)

[![](compiling-for-different-devices-images/image2a.png "Enabling LLVM")](compiling-for-different-devices-images/image2a.png#lightbox)

## <a name="architecture-support"></a>Suporte à arquitetura

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

### <a name="arm-thumb-2-support"></a>Suporte arm Thumb-2

Thumb é um conjunto de instruções mais compacto usado por processadores ARM. Habilitando o suporte a Thumb, você pode reduzir o tamanho de seu executável, tendo como contrapartida tempos de execução mais lentos. O Thumb é compatível com o ARMv7 e o ARMv7s.

## <a name="conditional-framework-usage"></a>Uso de estrutura condicional

Se você quer que seu projeto aproveite alguns dos recursos das versões mais recentes do iOS, talvez você precise depender condicionalmente de certas estruturas novas. Um exemplo perfeito disso é quando você deseja usar iAd ao executar no iOS 4.0 ou posterior, mas sem deixar de dar suporte a dispositivos 3.x. Para fazer isso, você precisa informar ao Xamarin.iOS que você precisa vincular a estrutura iAd de modo "fraco". Associações fracas asseguram que a estrutura só é carregada sob demanda na primeira vez que uma classe da estrutura é necessária.

Para fazer isso, você deve realizar as seguintes etapas:

- Abra as **Opções do Projeto** e navegue até o painel **Build do iOS**.
- Adicione `'-gcc_flags "-weak_framework iAd"'` às **Opções Adicionais** para cada configuração que você deseja vincular de modo fraco em:

[![](compiling-for-different-devices-images/image3.png "Additional Options")](compiling-for-different-devices-images/image3.png#lightbox)

Além disso, você precisará evitar que seu uso dos tipos execute em versões anteriores do iOS, em que eles podem não existir. Há vários métodos para fazer isso, um dos quais é analisar `UIDevice.CurrentDevice.SystemVersion`.

## <a name="related-links"></a>Links relacionados

- [Vinculador](~/ios/deploy-test/linker.md)
