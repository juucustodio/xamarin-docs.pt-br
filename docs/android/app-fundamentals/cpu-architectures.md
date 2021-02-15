---
title: Arquitetura de CPU
description: O Xamarin. Android dá suporte a várias arquiteturas de CPU, incluindo dispositivos de 32 bits e de 64 bits. Este artigo explica como direcionar um aplicativo para uma ou mais arquiteturas de CPU com suporte do Android.
ms.prod: xamarin
ms.assetid: D4BC889D-9164-49BB-9B7B-F6C4E4E109F1
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2019
ms.openlocfilehash: 40cbc8b18c9f0bd177d2c8829aa0eac72fc6e2b8
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456178"
---
# <a name="cpu-architectures"></a>Arquitetura de CPU

_O Xamarin. Android dá suporte a várias arquiteturas de CPU, incluindo dispositivos de 32 bits e de 64 bits. Este artigo explica como direcionar um aplicativo para uma ou mais arquiteturas de CPU com suporte do Android._

## <a name="cpu-architectures-overview"></a>Visão geral das arquiteturas de CPU

Ao preparar seu aplicativo para a versão, você deve especificar as arquiteturas de CPU de plataforma às quais seu aplicativo dá suporte. Um único APK pode conter código de computador para dar suporte a várias arquiteturas diferentes. Cada coleção de código específico à arquitetura é associada a uma Abi ( *interface binária de aplicativo* ). Cada ABI define como é esperado que esse código de computador interaja com o Android em tempo de execução.
Para obter mais informações sobre como isso funciona, consulte [dispositivos com vários núcleos &amp; Xamarin. Android](~/android/deploy-test/multicore-devices.md).

## <a name="how-to-specify-supported-architectures"></a>Como especificar as arquiteturas com suporte

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Normalmente, você seleciona explicitamente uma arquitetura (ou arquiteturas) quando seu aplicativo está configurado para **liberação**. Quando seu aplicativo está configurado para **depuração**, as opções **usar tempo de execução compartilhado** e **usar implantação rápida** estão habilitadas, o que desabilita a seleção de arquitetura explícita.

No Visual Studio, clique com o botão direito do mouse em seu projeto na **Gerenciador de soluções** e selecione **Propriedades**. Na página **Opções do Android** , verifique a seção **Propriedades de empacotamento** e verifique se **usar tempo de execução compartilhado** está desabilitado (desativar essa opção permite que você selecione explicitamente a qual abis o suporte). Clique no botão **avançado** e, em **arquiteturas com suporte**, verifique as arquiteturas para as quais você deseja oferecer suporte:

[![Selecionando ARMEABI e ARMEABI-v7a](cpu-architectures-images/vs/01-abi-selections-sml.png)](cpu-architectures-images/vs/01-abi-selections.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Normalmente, você seleciona explicitamente uma arquitetura (ou arquiteturas) quando seu aplicativo está configurado para **liberação**. Quando seu aplicativo está configurado para **depuração**, as opções **usar o tempo de execução mono compartilhado** e **implantação rápida de assembly** estão habilitadas, o que impede a seleção de arquitetura explícita.

Em Visual Studio para Mac, localize seu projeto no painel de **solução** , clique no ícone de engrenagem ao lado de seu projeto e selecione **Opções**. Na caixa de diálogo **Opções do projeto** , clique em **Build do Android**. Clique na guia **geral** e verifique se a opção **usar tempo de execução mono compartilhado** está desabilitada (desativar isso permite que você selecione explicitamente a qual abis o suporte). Clique na guia **avançado** e, em **abis com suporte**, verifique o abis para obter as arquiteturas para as quais você deseja oferecer suporte:

[![Selecionando ARMEABI e ARMEABI-v7a](cpu-architectures-images/xs/01-abi-selections-sml.png)](cpu-architectures-images/xs/01-abi-selections.png#lightbox)

-----

O Xamarin.Android é compatível com as seguintes arquiteturas:

- **ARMEABI** &ndash; CPUs baseadas em ARM que dão suporte a pelo menos o conjunto de instruções ARMv5TE. Observe que o `armeabi` não é thread-safe e não deve ser usado em dispositivos com várias CPUs.

> [!NOTE]
>  desde o [Xamarin.Android 9.2](/xamarin/android/release-notes/9/9.2#removal-of-support-for-armeabi-cpu-architecture), `armeabi` não é mais suportada.

- **ARMEABI-v7a** &ndash; CPUs baseadas em ARM com operações de ponto flutuante de hardware e vários dispositivos de CPU (SMP). Observe que o `armeabi-v7a` código do computador não será executado em dispositivos ARMv5.

- **arm64-V8A** &ndash; CPUs baseadas na arquitetura ARMv8 de 64 bits.

- **x86** &ndash; CPUs que dão suporte ao conjunto de instruções x86 (ou IA-32). Esse conjunto de instruções é equivalente ao do Pentium Pro, incluindo as instruções MMX, SSE, SSE2 e SSE3.

- **x86_64** CPUs que dão suporte ao conjunto de instruções x86 de 64 bits (também conhecido como   *x64* e *AMD64*).

O Xamarin. Android usa como padrão `armeabi-v7a` para compilações de **versão** . Essa configuração fornece um desempenho significativamente melhor do que o `armeabi` . Se você estiver visando uma plataforma ARM de 64 bits (como o Nexus 9), selecione `arm64-v8a` . Se você estiver implantando seu aplicativo em um dispositivo x86, selecione `x86` . Se o dispositivo x86 de destino usar uma arquitetura de CPU de 64 bits, selecione `x86_64` .

## <a name="targeting-multiple-platforms"></a>Direcionando várias plataformas

Para direcionar várias arquiteturas de CPU, você pode selecionar mais de uma ABI (às custas de maior tamanho de arquivo APK). Você pode usar a opção **gerar um pacote (. apk) por Abi selecionada** (descrita em [definir propriedades de empacotamento](~/android/deploy-test/release-prep/index.md#Set_Packaging_Properties)) para criar um apk separado para cada arquitetura com suporte.

Você não precisa selecionar **arm64-V8A** ou **x86_64** para direcionar dispositivos de 64 bits; o suporte a 64 bits não é necessário para executar seu aplicativo no hardware de 64 bits. Por exemplo, dispositivos ARM de 64 bits (como o [Nexus 9](https://www.google.com/nexus/9/)) podem executar aplicativos configurados para o `armeabi-v7a` . A principal vantagem de habilitar o suporte a 64 bits é possibilitar que seu aplicativo resolva mais memória.

> [!NOTE]
> A partir de agosto de 2018, os novos aplicativos precisarão direcionar o nível 26 da API, e a partir de agosto de 2019, os aplicativos [precisarão fornecer versões de 64 bits](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html) além da versão de 32 bits.

## <a name="additional-information"></a>Informações adicionais

Em algumas situações, talvez seja necessário criar um APK separado para cada arquitetura (para reduzir o tamanho de seu APK ou porque seu aplicativo tem bibliotecas compartilhadas específicas para uma arquitetura de CPU específica).
Para obter mais informações sobre essa abordagem, consulte [criar APKs específicas da Abi](~/android/deploy-test/building-apps/abi-specific-apks.md).