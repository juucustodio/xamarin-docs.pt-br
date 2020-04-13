---
title: Xamarin.Android Perguntas frequentes
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 28b13951a0d681ffdb8e643667e496e0b3606628
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292678"
---
# <a name="android-frequently-asked-questions"></a>Perguntas frequentes para Android

## <a name="installation--setup"></a>Instalação & Configuração

### <a name="which-android-sdk-packages-should-i-install"></a>[Quais pacotes de SDK do Android eu devo instalar?](install-android-sdk-packages.md)

A instalação do Android SDK não inclui automaticamente todos os pacotes mínimos necessários para o desenvolvimento. Embora as necessidades individuais do desenvolvedor variem, este guia discute os pacotes que geralmente serão necessários para o desenvolvimento com o Xamarin.Android.

### <a name="where-can-i-set-my-android-sdk-locations"></a>[Onde posso configurar os locais de SDK do Android?](android-sdk-location.md)

Este guia descreve as configurações padrão do SDK do Android, que deve funcionar para a maioria das configurações; e como alterar esses padrões no Visual Studio para Mac ou Visual Studio, se necessário.

### <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>[Como faço para atualizar a versão do JDK (Java Development Kit)?](update-jdk.md)

Este artigo ilustra como atualizar a versão Java Development Kit (JDK) no Windows e Mac.

### <a name="can-i-use-java-development-kit-jdk-version-9-or-later"></a>[Posso usar a versão 9 ou posterior do JDK (Java Development Kit)?](jdk9-errors.md)

Xamarin.Android requer JDK 8 ou o Microsoft Mobile OpenJDK. Este artigo lista algumas mensagens de erro comuns que você pode ver se JDK 9 ou posterior está instalado, juntamente com instruções para verificar a versão JDK.

### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>[Como posso instalar manualmente as bibliotecas de suporte do Android exigidas pelos pacotes Xamarin.Android.Support?](install-android-support-library.md)

Este guia fornece exemplos de `Xamarin.Android.Support.v4` etapas para instalar a biblioteca de suporte no Windows & Mac.

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>[Quais drivers USB são necessários para depurar o Android no Windows?](android-drivers-debug-windows.md)

Para depurar em um dispositivo Android ao desenvolver no Windows; você precisa instalar um driver USB compatível. O Android SDK Manager inclui o "Google USB Driver" por padrão, que adiciona suporte para dispositivos Nexus.
Outros dispositivos exigem drivers USB publicados pelo fabricante do dispositivo. Este guia fornece informações sobre como encontrar esses drivers, bem como métodos alternativos de teste.

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>[É possível se conectar aos emuladores de Android em execução em um Mac usando uma VM do Windows?](connect-android-emulator-mac-windows.md)

Este guia abrange métodos ao usar o emulador Android.

## <a name="general-questions"></a>Perguntas gerais

### <a name="how-do-i-automate-an-android-nunit-test-project"></a>[Como automatizar um projeto de Teste NUnit do Android?](automate-android-nunit-test.md)

Este guia abrange etapas para configurar um projeto de teste Android NUnit, _não_ um projeto Xamarin.UITest. Os guias xamarin.uITest podem ser encontrados [aqui](/appcenter/test-cloud/preparing-for-upload).

### <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>[Por que meu build de versão do Android não pode se conectar à Internet?](android-internet.md)

A causa mais comum deste problema é que a permissão da **INTERNET** é automaticamente incluída em uma compilação de depuração, mas deve ser definida manualmente para uma compilação de versão. Este guia descreve como ativar a permissão em compilações de liberação.

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>[Pacotes NuGet para suporte mais inteligente do Xamarin Android v4/v13](android-support-v4v13-libraries.md)

`Support-v4`e `Support-v13` não podem ser usados juntos no mesmo aplicativo, ou seja, são mutuamente exclusivos. Isso porque, `Support-v13` na verdade, contém `Support-v4`todos os tipos e implementação de . Se você tentar referenciar ambos no mesmo projeto, você encontrará erros de tipo duplicados.

### <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>[Como resolver um erro PathTooLongException Error?](path-too-long-exception.md)

Este artigo explica como resolver um erro **pathTooLongException** que pode ocorrer durante a construção de um projeto Xamarin.Android.

## <a name="deprecated"></a>Preterido

> [!NOTE]
> Os artigos abaixo aplicam-se a questões que foram resolvidas em versões recentes de Xamarin. No entanto, se o problema ocorrer na versão mais recente do software, por favor, arquive um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com suas informações de versão completa e saída completa do log de compilação.

### <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>[Qual versão do Xamarin.Android adicionou suporte ao Lollipop?](xa-lollipop.md)

Este guia foi originalmente escrito para a pré-visualização do Android L. Xamarin.Android 4.17 adicionou suporte ao Android L Preview & Xamarin.Android 4.20 adicionado Suporte Android Lollipop.

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>[Android.Support.v7.AppCompat – Não encontramos um recurso correspondente ao nome fornecido: attr 'android:actionModeShareDrawable'](missing-action-mode-share-drawable.md)

Esse erro pode ocorrer em versões mais antigas do Xamarin se alguns dos pacotes SDK do Android necessários estiverem faltando.

### <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>[Ajustar os parâmetros de memória do Java para o Android Designer](android-designer-java-memory.md)

Os parâmetros de memória padrão `java` que são usados ao iniciar o processo para o designer Android podem ser incompatíveis com algumas configurações do sistema. A partir de Xamarin Studio 5.7.2.7 e Xamarin para Visual Studio 3.9.344, essas configurações podem ser personalizadas por projeto.

### <a name="my-android-resourcedesignercs-file-will-not-update"></a>[Meu arquivo Resource.designer.cs do Android não será atualizado](resource-designer-wont-update.md)

Um bug no Xamarin.Studio 5.1 corrompeu anteriormente arquivos .csproj, excluindo parcial ou completamente o código xml no arquivo .csproj. Isso faria com que partes importantes do sistema de compilação do Android (como atualizar o Android Resource.designer.cs) falhassem. A partir da versão estável de 5.1.4 em 15 de julho, este bug foi corrigido; mas, em muitos casos, o arquivo do projeto tem que ser reparado manualmente, conforme descrito neste guia.
