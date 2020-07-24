---
title: Testando, otimizando e implantando aplicativos Xamarin.Android
description: Os guias desta seção explicam como testar um aplicativo, otimizar seu desempenho, prepará-lo para lançamento, assiná-lo com um certificado e publicá-lo em uma loja de aplicativos
ms.prod: xamarin
ms.assetid: 568C0B85-EFF3-AF6F-5605-95055193D367
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: bec2bef92433320925c4179535c8371b69b9bf22
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86996546"
---
# <a name="deployment-and-testing-of-xamarinandroid-apps"></a>Implantação e teste de aplicativos Xamarin. Android

Os guias desta seção explicam como testar um aplicativo, otimizar seu desempenho, prepará-lo para lançamento, assiná-lo com um certificado e publicá-lo em uma loja de aplicativos.

## <a name="application-package-sizes"></a>[Tamanho dos pacotes de aplicativos](app-package-size.md)

Este artigo examina as partes que constituem um pacote do aplicativo do Xamarin.Android e as estratégias associadas que podem ser usadas para a implantação eficiente do pacote durante a depuração e as etapas de lançamento de desenvolvimento.

## <a name="apply-changes"></a>[Aplicar alterações](apply-changes.md)

Este guia aborda o recurso aplicar alterações, que permite que você envie por push alterações de recursos para seu aplicativo em execução sem reiniciar o aplicativo.

## <a name="building-apps"></a>[Compilar aplicativos](building-apps/index.md)

Esta seção descreve como funciona o processo de compilação e explica como compilar APKs específicos para ABI.

## <a name="command-line-emulator"></a>[Emulador de linha de comando](command-line-emulator.md)

Este artigo aborda brevemente a inicialização do emulador por meio da linha de comando.

## <a name="debugging"></a>[Depuração](~/android/deploy-test/debugging/index.md)

As guias da seção ajudam a depurar o aplicativo usando os emuladores de Android, dispositivos Android reais e o log de depuração.

## <a name="setting-the-debuggable-attribute"></a>[Configuração do atributo depurável](~/android/deploy-test/debuggable-attribute.md)

Este artigo explica como definir o atributo depurável para que as ferramentas como `adb` possam se comunicar com o JVM.

## <a name="environment"></a>[Ambiente](environment.md)

Este artigo descreve o ambiente de execução do Xamarin.Android e as propriedades do sistema Android que influenciam na execução do programa.

## <a name="gdb"></a>[GDB](gdb.md)

Este artigo explica como usar o `gdb` para depurar um aplicativo Xamarin.Android.

## <a name="installing-a-system-app"></a>[Instalar um aplicativo do sistema](install-system-app.md)

Este guia explica como instalar um aplicativo Xamarin.Android como um Aplicativo de Sistema em um dispositivo Android ou como parte de um ROM personalizado.

## <a name="linking-on-android"></a>[Vinculação no Android](linker.md)

Este artigo aborda o processo de vinculação usado pelo Xamarin.Android para reduzir o tamanho final de um aplicativo. Ele descreve os vários níveis de vinculação que podem ser executados e fornece algumas diretrizes e orientações para atenuar os erros que possam resultar do uso do vinculador.

## <a name="xamarinandroid-performance"></a>[Desempenho do Xamarin.Android](~/android/deploy-test/performance.md)

Há várias técnicas para aumentar o desempenho dos aplicativos criados com o Xamarin.Android. Coletivamente, essas técnicas podem reduzir de forma considerável a quantidade de trabalho que está sendo executado por uma CPU e a quantidade de memória consumida por um aplicativo.

## <a name="profiling-android-apps"></a>[Criação de perfil de aplicativos Android](~/android/deploy-test/profiling.md)

Este guia explica como usar ferramentas de criação de perfil para examinar o desempenho e o uso de memória de um aplicativo Android.

## <a name="preparing-an-application-for-release"></a>[Preparando um aplicativo para a versão](~/android/deploy-test/release-prep/index.md)

Depois que um aplicativo tiver sido codificado e testado, será necessário preparar um pacote para distribuição. A primeira tarefa na preparação desse pacote é compilar o aplicativo para a versão, o que envolve principalmente a configuração de alguns atributos do aplicativo.

## <a name="signing-the-android-application-package"></a>[Assinando o pacote de aplicativos do Android](~/android/deploy-test/signing/index.md)

Saiba como criar uma identidade de assinatura do Android, criar um novo certificado de autenticação para aplicativos Android e assinar um aplicativo com o certificado de autenticação. Além disso, este tópico explica como exportar o aplicativo para o disco, para distribuição *ad-hoc*. O APK resultante pode ter o sideload realizado em dispositivos Android sem passar por uma loja de aplicativos.

## <a name="publishing-an-application"></a>[Publicando um aplicativo](~/android/deploy-test/publishing/index.md)

Esta série de artigos explica as etapas da distribuição pública de um aplicativo criado com o Xamarin.Android. A distribuição pode ocorrer por meio de canais como email, servidor Web privado, Google Play ou a Amazon App Store para Android.
