---
title: Arquitetura de CPU
description: "Xamarin dá suporte a várias arquiteturas de CPU, incluindo dispositivos de 32 bits e 64 bits. Este artigo explica como direcionar um aplicativo para um ou mais arquiteturas de CPU com suporte do Android."
ms.topic: article
ms.prod: xamarin
ms.assetid: D4BC889D-9164-49BB-9B7B-F6C4E4E109F1
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 6139b5e27e9689da6366a2107acc14a6adcfc928
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="cpu-architectures"></a>Arquitetura de CPU

_Xamarin dá suporte a várias arquiteturas de CPU, incluindo dispositivos de 32 bits e 64 bits. Este artigo explica como direcionar um aplicativo para um ou mais arquiteturas de CPU com suporte do Android._

## <a name="cpu-architectures-overview"></a>Visão geral de arquiteturas de CPU

Ao preparar seu aplicativo para a versão, você deve especificar quais arquiteturas de plataforma da CPU seu aplicativo suporta. Um único APK pode conter código de computador para dar suporte a várias arquiteturas diferentes. Cada coleção de código específico de arquitetura está associada com um *Interface binária de aplicativo* (ABI). Cada ABI define como esse código de máquina é esperado para interagir com Android em tempo de execução.
Para obter mais informações sobre como isso funciona, consulte [dispositivos de vários núcleos &amp; xamarin](~/android/deploy-test/multicore-devices.md).


## <a name="how-to-specify-supported-architectures"></a>Como especificar arquiteturas com suporte

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Normalmente, você selecionar uma arquitetura (ou arquiteturas) quando seu aplicativo está configurado para **versão**. Quando seu aplicativo está configurado para **depurar**, o **usar tempo de execução compartilhado** e **implantação rápida do uso** opções são habilitadas, o que desabilitar seleção explícita de arquitetura.

No Visual Studio, clique duas vezes em **propriedades** em seu projeto no **Solution Explorer** e selecione o **opções Android** página. Clique o **empacotamento** guia e verifique **usar tempo de execução compartilhado** está desabilitado (a desativação dessa opção permite que você selecione explicitamente quais ABIs para dar suporte a). Clique o **avançado** guia e, em **propriedades avançadas**, verifique as arquiteturas que você deseja dar suporte:

[ ![Selecionando armeabi e armeabi v7a](cpu-architectures-images/vs/01-abi-selections-sml.png)](cpu-architectures-images/vs/01-abi-selections.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Normalmente, você selecionar uma arquitetura (ou arquiteturas) quando seu aplicativo está configurado para **versão**. Quando seu aplicativo está configurado para **depurar**, o **usar compartilhada runtime Mono** e **a rápida implantação de assembly** opções são habilitadas, o que impedirá que a arquitetura explícita seleção.

No Visual Studio para Mac, localize seu projeto no **solução** acrescentar, clique no ícone de engrenagem ao lado de seu projeto e selecione **opções**. No **opções de projeto** caixa de diálogo, clique em **criar Android**. Clique o **geral** guia e verifique **usar compartilhada runtime Mono** está desabilitado (a desativação dessa opção permite que você selecione explicitamente quais ABIs para dar suporte a). Clique o **avançado** guia e, em **ABIs suporte**, verifique os ABIs para as arquiteturas que você deseja dar suporte:

[ ![Selecionando armeabi e armeabi v7a](cpu-architectures-images/xs/01-abi-selections-sml.png)](cpu-architectures-images/xs/01-abi-selections.png)

-----


Xamarin dá suporte a arquiteturas a seguir:

-   **armeabi** &ndash; CPUs baseado em ARM que dar suporte ao menos o conjunto de instruções de ARMv5TE. Observe que `armeabi` não é thread-safe e não deve ser usado em dispositivos com várias CPUs.

-   **armeabi v7a** &ndash; baseado em ARM CPUs com operações de ponto flutuante de hardware e de vários dispositivos de CPU (SMP). Observe que `armeabi-v7a` código de máquina não será executado em dispositivos ARMv5.

-   **arm64 v8a** &ndash; CPUs com base na arquitetura de ARMv8 de 64 bits.

-   **x86** &ndash; CPUs que dão suporte a x86 (ou IA-32) conjunto de instruções. Esse conjunto de instruções é equivalente do Pentium Pro, incluindo instruções MMX, SSE, SSE2 e SSE3.

-   **x86_64** CPUs que oferecem suporte a x86 de 64 bits (também conhecido como *x64* e *AMD64*) conjunto de instruções.

Xamarin padrão `armeabi-v7a` para **versão** cria. Essa configuração fornece um desempenho significativamente melhor do que `armeabi`. Se você direcionar uma plataforma ARM de 64 bits (como o Nexus 9), selecione `arm64-v8a`. Se você estiver implantando seu aplicativo para x86 dispositivo, selecione `x86`. Se o dispositivo de destino x86 usa uma arquitetura de CPU de 64 bits, selecione `x86_64`.

## <a name="targeting-multiple-platforms"></a>Direcionando várias plataformas

Para várias arquiteturas de CPU de destino, você pode selecionar mais de uma ABI (às custas de maior tamanho de arquivo APK). Você pode usar o **gerar um pacote (. Apk) por ABI selecionado** opção (descrito na [definir propriedades de empacotamento](~/android/deploy-test/release-prep/index.md#Set_Packaging_Properties)) criar um APK separado para cada arquitetura com suporte.

Você não precisa selecionar **arm64 v8a** ou **x86_64** como destino dispositivos de 64 bits; suporte de 64 bits não é necessário para executar seu aplicativo no hardware de 64 bits. Por exemplo, dispositivos ARM de 64 bits (como o [9 Nexus](http://www.google.com/nexus/9/)) pode executar aplicativos configurados para `armeabi-v7a`. A principal vantagem de habilitar o suporte de 64 bits é possibilitar que seu aplicativo tratar mais memória.

> [!NOTE]
> **Observação:**: suporte de tempo de execução de 64 bits é um recurso experimental atualmente. Lembre-se que os tempos de execução de 64 bits são *não* necessários para executar seu aplicativo em dispositivos de 64 bits. 

## <a name="additional-information"></a>Informações adicionais

Em algumas situações, talvez seja necessário criar um APK separado para cada arquitetura (para reduzir o tamanho do seu APK, ou porque o seu aplicativo tiver bibliotecas compartilhadas que são específicas para uma determinada arquitetura de CPU).
Para obter mais informações sobre essa abordagem, consulte [criar ABI específico APKs](~/android/deploy-test/building-apps/abi-specific-apks.md).
