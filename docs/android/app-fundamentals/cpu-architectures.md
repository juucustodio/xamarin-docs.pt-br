---
title: Arquitetura de CPU
description: Xamarin. Android dá suporte a várias arquiteturas de CPU, incluindo dispositivos de 32 bits e 64 bits. Este artigo explica como direcionar um aplicativo para um ou mais arquiteturas de CPU com suporte do Android.
ms.prod: xamarin
ms.assetid: D4BC889D-9164-49BB-9B7B-F6C4E4E109F1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: f2865858552d4445dff95c85767c41849c19cc29
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122264"
---
# <a name="cpu-architectures"></a>Arquitetura de CPU

_Xamarin. Android dá suporte a várias arquiteturas de CPU, incluindo dispositivos de 32 bits e 64 bits. Este artigo explica como direcionar um aplicativo para um ou mais arquiteturas de CPU com suporte do Android._

## <a name="cpu-architectures-overview"></a>Visão geral de arquiteturas de CPU

Ao preparar seu aplicativo para versão, você deve especificar quais arquiteturas de plataforma da CPU seu aplicativo dá suporte. Um único APK pode conter código de computador para dar suporte a várias arquiteturas diferentes. Cada coleção de código específico da arquitetura está associada com um *Interface binária de aplicativo* (ABI). Cada ABI define como esse código de máquina é esperado para interagir com o Android em tempo de execução.
Para obter mais informações sobre como isso funciona, consulte [dispositivos de vários núcleos &amp; xamarin. Android](~/android/deploy-test/multicore-devices.md).


## <a name="how-to-specify-supported-architectures"></a>Como especificar arquiteturas com suporte

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Normalmente, você selecionar uma arquitetura (ou arquiteturas) quando seu aplicativo está configurado para **versão**. Quando seu aplicativo está configurado para **Debug**, o **usar tempo de execução compartilhado** e **usar implantação rápida** opções estiverem habilitadas, o que desabilitar seleção explícita de arquitetura.

No Visual Studio, clique com botão direito no projeto sob o **Gerenciador de soluções** e selecione **propriedades**. Sob o **opções do Android** página seleção de **propriedades de empacotamento** seção e verifique **usar tempo de execução compartilhado** está desabilitado (desativar essa configuração permite que você explicitamente Selecione quais ABIs para dar suporte a). Clique o **Advanced** botão e, em **arquiteturas com suporte**, verifique as arquiteturas que você deseja dar suporte a:

[![Selecionando armeabi e armeabi-v7a](cpu-architectures-images/vs/01-abi-selections-sml.png)](cpu-architectures-images/vs/01-abi-selections.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Normalmente, você selecionar uma arquitetura (ou arquiteturas) quando seu aplicativo está configurado para **versão**. Quando seu aplicativo está configurado para **Debug**, o **usar compartilhada de tempo de execução Mono** e **implantação de assembly rápida** opções estiverem habilitadas, o que impedirá que a arquitetura explícita seleção.

No Visual Studio para Mac, localize seu projeto na **Solution** preencher, clique no ícone de engrenagem ao lado do seu projeto e selecione **opções**. No **opções de projeto** caixa de diálogo, clique em **Build do Android**. Clique o **gerais** guia e verifique **usar compartilhada de tempo de execução Mono** está desabilitado (desativar essa configuração permite que você selecione explicitamente quais ABIs para dar suporte a). Clique o **Advanced** guia e, em **ABIs com suporte**, verifique os ABIs para as arquiteturas que você deseja dar suporte a:

[![Selecionando armeabi e armeabi-v7a](cpu-architectures-images/xs/01-abi-selections-sml.png)](cpu-architectures-images/xs/01-abi-selections.png#lightbox)

-----


O Xamarin.Android é compatível com as seguintes arquiteturas:

-   **armeabi** &ndash; CPUs baseadas em ARM, compatível com pelo menos o conjunto de instruções ARMv5TE. Observe que `armeabi` não é thread-safe e não deve ser usado em dispositivos com várias CPUs.

-   **armeabi-v7a** &ndash; CPUs com base em ARM com operações de ponto flutuante de hardware e dispositivos de várias CPUs (SMP). Observe que `armeabi-v7a` código de computador não será executado em dispositivos ARMv5.

-   **arm64-v8a** &ndash; CPUs com base na arquitetura ARMv8 64 bits.

-   **x86** &ndash; CPUs compatíveis com x86 (ou IA-32) conjunto de instruções. Esse conjunto de instruções é equivalente ao de Pentium Pro, incluindo instruções MMX, SSE, SSE2 e SSE3.

-   **x86_64** CPUs compatíveis com x86 de 64 bits (também conhecido como *x64* e *AMD64*) conjunto de instruções.

Xamarin. Android padrão é `armeabi-v7a` para **versão** compilações. Essa configuração fornece um desempenho significativamente melhor do que `armeabi`. Se você estiver visando uma plataforma ARM de 64 bits (como o Nexus 9), selecione `arm64-v8a`. Se você estiver implantando seu aplicativo em um x86 dispositivo, selecione `x86`. Se o dispositivo de destino x86 usa uma arquitetura de CPU de 64 bits, selecione `x86_64`.

## <a name="targeting-multiple-platforms"></a>Direcionar várias plataformas

Para direcionar para várias arquiteturas de CPU, você pode selecionar mais de uma ABI (a custa de maior tamanho de arquivo APK). Você pode usar o **gerar um pacote (. Apk) por ABI selecionado** opção (descrito em [definir propriedades de empacotamento](~/android/deploy-test/release-prep/index.md#Set_Packaging_Properties)) criar um APK separado para cada arquitetura com suporte.

Você não precisa selecionar **arm64-v8a** ou **x86_64** para ter como destino dispositivos de 64 bits; suporte de 64 bits não é necessário para executar seu aplicativo no hardware de 64 bits. Por exemplo, dispositivos ARM de 64 bits (como o [Nexus 9](http://www.google.com/nexus/9/)) podem executar aplicativos configurados para `armeabi-v7a`. A principal vantagem de permitir o suporte de 64 bits é tornam possível para seu aplicativo tratar mais memória.

> [!NOTE]
> suporte de tempo de execução de 64 bits é um recurso experimental atualmente. Lembre-se de que os tempos de execução de 64 bits estejam *não* necessários para executar seu aplicativo em dispositivos de 64 bits. 

## <a name="additional-information"></a>Informações adicionais

Em algumas situações, talvez seja necessário criar um APK separado para cada arquitetura (para reduzir o tamanho de seu APK, ou porque seu aplicativo tem bibliotecas compartilhadas que são específicas para uma arquitetura de CPU específica).
Para obter mais informações sobre essa abordagem, consulte [Build específicos para ABI APKs](~/android/deploy-test/building-apps/abi-specific-apks.md).
