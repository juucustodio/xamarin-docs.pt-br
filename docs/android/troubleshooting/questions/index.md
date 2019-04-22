---
title: Perguntas frequentes sobre o xamarin. Android
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 9c0e6d014f27651710bf3b8e713b2bf80322d628
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58796515"
---
# <a name="android-frequently-asked-questions"></a>Perguntas frequentes do Android

## <a name="installation--setup"></a>Instalação e configuração

### <a name="which-android-sdk-packages-should-i-installinstall-android-sdk-packagesmd"></a>[Quais pacotes de SDK do Android eu devo instalar?](install-android-sdk-packages.md)

Instalar o SDK do Android não inclui automaticamente todos os pacotes necessários mínimos para o desenvolvimento. Enquanto o desenvolvedor individual precisa variam, este guia aborda os pacotes que geralmente serão necessários para desenvolver com o xamarin. Android.

### <a name="where-can-i-set-my-android-sdk-locationsandroid-sdk-locationmd"></a>[Onde posso configurar os locais de SDK de meu Android?](android-sdk-location.md)

Este guia descreve as configurações padrão do SDK do Android, que deve funcionar para a maioria das configurações; e como alterar esses padrões no Visual Studio para Mac ou Visual Studio, se necessário.

### <a name="how-do-i-update-the-java-development-kit-jdk-versionupdate-jdkmd"></a>[Como faço para atualizar a versão do JDK (Java Development Kit)?](update-jdk.md)

Este artigo ilustra como atualizar a versão do Java Development Kit (JDK) no Windows e Mac.

### <a name="can-i-use-java-development-kit-jdk-version-9-or-laterjdk9-errorsmd"></a>[Posso usar o Java Development Kit (JDK) versão 9 ou posterior?](jdk9-errors.md)

Xamarin. Android exige o JDK 8 ou o Microsoft Mobile OpenJDK. Este artigo lista algumas mensagens de erro comuns que você pode ver se o JDK 9 ou posterior está instalado, juntamente com instruções sobre como verificar a versão do JDK.


### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packagesinstall-android-support-librarymd"></a>[Como posso instalar manualmente as Bibliotecas de suporte do Android exigidas pelos pacotes Xamarin.Android.Support?](install-android-support-library.md)

Este guia fornece etapas de exemplo para instalar o `Xamarin.Android.Support.v4` biblioteca de suporte no Windows e Mac.

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windowsandroid-drivers-debug-windowsmd"></a>[Quais drivers USB são necessários para depurar o Android no Windows?](android-drivers-debug-windows.md)

Para depurar em um dispositivo Android ao desenvolver no Windows; Você precisa instalar um driver USB compatível. O Gerenciador de SDK do Android inclui o "Driver de USB do Google" por padrão, que adiciona suporte para dispositivos Nexus.
Outros dispositivos requerem drivers USB publicados pelo fabricante do dispositivo. Este guia fornece informações sobre como encontrar esses drivers métodos de teste, bem como alternativos.

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vmconnect-android-emulator-mac-windowsmd"></a>[É possível se conectar aos emuladores de Android em execução em um Mac a partir de uma VM do Windows?](connect-android-emulator-mac-windows.md)

Este guia aborda os métodos ao usar o emulador do Android.

## <a name="general-questions"></a>Perguntas gerais

### <a name="how-do-i-automate-an-android-nunit-test-projectautomate-android-nunit-testmd"></a>[Como automatizar a um projeto de Teste NUnit do Android?](automate-android-nunit-test.md)

Este guia aborda as etapas para configurar um projeto de teste do NUnit do Android _não_ um projeto xamarin. uitest. Xamarin. uitest guias podem ser encontrados [aqui](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

### <a name="why-cant-my-android-release-build-connect-to-the-internetandroid-internetmd"></a>[Por que minha compilação de versão do Android não pode se conectar à Internet?](android-internet.md)

A causa mais comum desse problema é que o **INTERNET** permissão é incluída automaticamente em uma compilação de depuração, mas deve ser definido manualmente para um build de versão. Este guia descreve como habilitar a permissão em builds de versão.

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packagesandroid-support-v4v13-librariesmd"></a>[Pacotes NuGet para suporte mais inteligente do Xamarin Android v4/v13](android-support-v4v13-libraries.md)

`Support-v4` e `Support-v13` não podem ser usados juntos no mesmo aplicativo, ou seja, eles são mutuamente exclusivos. Isso ocorre porque `Support-v13` , na verdade, contém todos os tipos e a implementação de `Support-v4`. Se você tentar fazer referência a ambos no mesmo projeto, você encontrará erros de tipo duplicado.

### <a name="how-do-i-resolve-a-pathtoolongexception-errorpath-too-long-exceptionmd"></a>[Como resolvo um erro PathTooLongException?](path-too-long-exception.md)

Este artigo explica como resolver uma **PathTooLongException** erro que pode ocorrer durante a criação de um projeto xamarin. Android.



## <a name="deprecated"></a>Preterido

> [!NOTE]
> Os artigos a seguir se aplicam a problemas que foram resolvidos em versões recentes do Xamarin. No entanto, se o problema ocorre na versão mais recente do software, envie uma [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com o controle de versão completo completo e informações saída de log de compilação.

### <a name="what-version-of-xamarinandroid-added-lollipop-supportxa-lollipopmd"></a>[Qual versão do Xamarin.Android adicionou suporte ao Lollipop?](xa-lollipop.md)

Este guia foi escrito originalmente para a visualização do Android L. Xamarin. Android 4.17 adicionado o suporte de visualização do Android L & 4.20 xamarin. Android adicionou suporte do Android Lollipop.

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawablemissing-action-mode-share-drawablemd"></a>[Android.Support.v7.AppCompat – Não encontramos um recurso correspondente ao nome fornecido: attr 'android:actionModeShareDrawable'](missing-action-mode-share-drawable.md)

Esse erro pode ocorrer em versões mais antigas do Xamarin, se alguns dos pacotes do SDK do Android necessários estão ausentes.

### <a name="adjusting-java-memory-parameters-for-the-android-designerandroid-designer-java-memorymd"></a>[Ajustar os parâmetros de memória do Java para o Designer Android](android-designer-java-memory.md)

Os parâmetros de memória padrão que são usados ao iniciar o `java` processar para o designer do Android pode ser incompatível com algumas configurações do sistema. Começando com o Xamarin Studio 5.7.2.7 e o Xamarin para Visual Studio 3.9.344 essas configurações pode ser personalizado em uma base por projeto.

### <a name="my-android-resourcedesignercs-file-will-not-updateresource-designer-wont-updatemd"></a>[Meu arquivo Resource.designer.cs do Android não será atualizado.](resource-designer-wont-update.md)

Um bug no xamarin. Studio 5.1 anteriormente corrompido arquivos. csproj, parcial ou completamente, excluindo o código xml no arquivo. csproj. Isso faria com que importantes de sistema (como atualizar o Resource.designer.cs do Android) de compilação de partes do Android falhe. A partir de 5.1.4 estável de versão no dia 15 de julho, esse bug foi corrigido; mas, em muitos casos o arquivo de projeto deve ser reparado manualmente, conforme descrito neste guia.



