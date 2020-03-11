---
title: Perguntas frequentes sobre o Xamarin. Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 28b13951a0d681ffdb8e643667e496e0b3606628
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78292678"
---
# <a name="android-frequently-asked-questions"></a>Perguntas frequentes sobre o Android

## <a name="installation--setup"></a>Instalação & configuração

### <a name="which-android-sdk-packages-should-i-install"></a>[Quais pacotes de SDK do Android eu devo instalar?](install-android-sdk-packages.md)

A instalação do SDK do Android não inclui automaticamente todos os pacotes mínimos necessários para o desenvolvimento. Embora as necessidades individuais do desenvolvedor variem, este guia discute os pacotes que geralmente serão necessários para o desenvolvimento com o Xamarin. Android.

### <a name="where-can-i-set-my-android-sdk-locations"></a>[Onde posso configurar os locais de SDK de meu Android?](android-sdk-location.md)

Este guia descreve as configurações padrão do SDK do Android, que devem funcionar para a maioria das instalações; e como alterar esses padrões no Visual Studio para Mac ou no Visual Studio, se necessário.

### <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>[Como faço para atualizar a versão do JDK (Java Development Kit)?](update-jdk.md)

Este artigo ilustra como atualizar a versão do Java Development Kit (JDK) no Windows e no Mac.

### <a name="can-i-use-java-development-kit-jdk-version-9-or-later"></a>[Posso usar o Java Development Kit (JDK) versão 9 ou posterior?](jdk9-errors.md)

O Xamarin. Android requer o JDK 8 ou o Microsoft Mobile OpenJDK. Este artigo lista algumas mensagens de erro comuns que você pode ver se o JDK 9 ou posterior estiver instalado, juntamente com instruções para verificar a versão do JDK.

### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packages"></a>[Como posso instalar manualmente as Bibliotecas de suporte do Android exigidas pelos pacotes Xamarin.Android.Support?](install-android-support-library.md)

Este guia fornece as etapas de exemplo para instalar a biblioteca de suporte do `Xamarin.Android.Support.v4` no Windows & Mac.

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>[Quais drivers USB são necessários para depurar o Android no Windows?](android-drivers-debug-windows.md)

Para depurar em um dispositivo Android ao desenvolver no Windows; Você precisa instalar um driver USB compatível. O Gerenciador de SDK do Android inclui o "driver de USB do Google" por padrão, que adiciona suporte para dispositivos Nexus.
Outros dispositivos exigem drivers USB publicados pelo fabricante do dispositivo. Este guia fornece informações sobre como encontrar esses drivers, bem como métodos de teste alternativos.

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>[É possível se conectar aos emuladores de Android em execução em um Mac a partir de uma VM do Windows?](connect-android-emulator-mac-windows.md)

Este guia aborda métodos ao usar o emulador do Android.

## <a name="general-questions"></a>Perguntas gerais

### <a name="how-do-i-automate-an-android-nunit-test-project"></a>[Como automatizar a um projeto de Teste NUnit do Android?](automate-android-nunit-test.md)

Este guia aborda as etapas para configurar um projeto de teste do Android NUnit, _não_ um projeto Xamarin. UITest. Os guias do Xamarin. UITest podem ser encontrados [aqui](/appcenter/test-cloud/preparing-for-upload).

### <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>[Por que minha compilação de versão do Android não pode se conectar à Internet?](android-internet.md)

A causa mais comum desse problema é que a permissão da **Internet** é incluída automaticamente em uma compilação de depuração, mas deve ser definida manualmente para uma compilação de versão. Este guia descreve como habilitar a permissão em builds de versão.

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>[Pacotes NuGet para suporte mais inteligente do Xamarin Android v4/v13](android-support-v4v13-libraries.md)

`Support-v4` e `Support-v13` não podem ser usados juntos no mesmo aplicativo, ou seja, eles são mutuamente exclusivos. Isso ocorre porque `Support-v13` realmente contém todos os tipos e a implementação de `Support-v4`. Se você tentar referenciar ambos no mesmo projeto, encontrará erros de tipo duplicados.

### <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>[Como fazer resolver um erro PathTooLongException?](path-too-long-exception.md)

Este artigo explica como resolver um erro **PathTooLongException** que pode ocorrer durante a criação de um projeto Xamarin. Android.

## <a name="deprecated"></a>Preterido

> [!NOTE]
> Os artigos a seguir se aplicam a problemas que foram resolvidos em versões recentes do Xamarin. No entanto, se o problema ocorrer na versão mais recente do software, registre um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com suas informações completas de controle de versão e saída de log de compilação completa.

### <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>[Qual versão do Xamarin.Android adicionou suporte ao Lollipop?](xa-lollipop.md)

Este guia foi escrito originalmente para a visualização do Android L. Xamarin. Android 4,17 adicionou suporte ao Android L preview & Xamarin. Android 4,20 adicionou suporte a pirulito do Android.

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>[Android.Support.v7.AppCompat – Não encontramos um recurso correspondente ao nome fornecido: attr 'android:actionModeShareDrawable'](missing-action-mode-share-drawable.md)

Esse erro pode ocorrer em versões mais antigas do Xamarin se alguns dos pacotes de SDK do Android necessários estiverem ausentes.

### <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>[Ajustar os parâmetros de memória do Java para o Android Designer](android-designer-java-memory.md)

Os parâmetros de memória padrão que são usados ao iniciar o processo de `java` para o Android designer podem ser incompatíveis com algumas configurações do sistema. A partir do Xamarin Studio 5.7.2.7 e do Xamarin para Visual Studio 3.9.344, essas configurações podem ser personalizadas em uma base por projeto.

### <a name="my-android-resourcedesignercs-file-will-not-update"></a>[Meu arquivo Resource.designer.cs do Android não será atualizado.](resource-designer-wont-update.md)

Um bug no Xamarin. Studio 5,1 arquivo. csproj corrompido anteriormente, excluindo parcialmente ou completamente o código XML no arquivo. csproj. Isso faria com que partes importantes do sistema de compilação do Android (como atualizar o Android Resource.designer.cs) falhem. A partir da versão estável do 5.1.4 em 15 de julho, esse bug foi corrigido; Mas, em muitos casos, o arquivo de projeto precisa ser reparado manualmente, conforme descrito neste guia.
