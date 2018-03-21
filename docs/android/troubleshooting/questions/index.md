---
title: Perguntas frequentes
ms.topic: article
ms.prod: xamarin
ms.assetid: 0F0FDD2B-FFB1-476F-B674-81DB3A5E1CF3
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/19/2018
ms.openlocfilehash: 69a5c905367f9e6dbd38acb664ad21b9dbe63efc
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2018
---
# <a name="frequently-asked-questions"></a>Perguntas frequentes

## <a name="installation--setup"></a>Instalação e configuração

### <a name="which-android-sdk-packages-should-i-installinstall-android-sdk-packagesmd"></a>[Quais pacotes de SDK do Android eu devo instalar?](install-android-sdk-packages.md)

Instalar o SDK do Android não inclui automaticamente todos os pacotes necessários mínimos para o desenvolvimento. Enquanto os desenvolvedores individuais precisam variam, este guia aborda os pacotes que geralmente será necessários para o desenvolvimento com xamarin.

### <a name="where-can-i-set-my-android-sdk-locationsandroid-sdk-locationmd"></a>[Onde posso configurar os locais de SDK de meu Android?](android-sdk-location.md)

Este guia descreve as configurações padrão do SDK do Android, que deve funcionar para a maioria das configurações; e como alterar esses padrões no Visual Studio para Mac ou o Visual Studio, se necessário.

### <a name="how-do-i-update-the-java-development-kit-jdk-versionupdate-jdkmd"></a>[Como faço para atualizar a versão do JDK (Java Development Kit)?](update-jdk.md)

Este artigo ilustra como atualizar a versão do Java Development Kit (JDK) no Windows e Mac.

### <a name="can-i-use-java-development-kit-jdk-version-9jdk9-errorsmd"></a>[Posso usar a versão 9 do JDK (Java Development Kit)?](jdk9-errors.md)

Xamarin requer JDK 8 em vez de JDK 9. Este artigo lista algumas mensagens de erro comuns que você pode ver se JDK 9 é instalado, junto com instruções sobre como verificar a versão do JDK.


### <a name="how-can-i-manually-install-the-android-support-libraries-required-by-the-xamarinandroidsupport-packagesinstall-android-support-librarymd"></a>[Como posso instalar manualmente as Bibliotecas de suporte do Android exigidas pelos pacotes Xamarin.Android.Support?](install-android-support-library.md)

Este guia fornece etapas de exemplo para instalar o `Xamarin.Android.Support.v4` biblioteca de suporte do Windows & Mac.

### <a name="how-do-i-install-google-play-services-in-an-emulatorinstall-gpsmd"></a>[Como faço para instalar o Google Play Services em um emulador?](install-gps.md)

Links neste guia para obter informações sobre como instalar os serviços do Google executar no emulador do Android do Visual Studio.

### <a name="what-usb-drivers-do-i-need-to-debug-android-on-windowsandroid-drivers-debug-windowsmd"></a>[Quais drivers USB são necessários para depurar o Android no Windows?](android-drivers-debug-windows.md)

Para depurar em um dispositivo Android quando desenvolver no Windows. Você precisa instalar o driver compatível. O Gerenciador de SDK do Android inclui o Driver de USB"Google" por padrão, o que adiciona suporte para dispositivos de ligação.
Outros dispositivos requerem drivers USB especificamente publicados pelo fabricante do dispositivo. Este guia fornece informações sobre como encontrar esses drivers alternativos, bem como métodos de teste.

### <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vmconnect-android-emulator-mac-windowsmd"></a>[É possível se conectar aos emuladores de Android em execução em um Mac a partir de uma VM do Windows?](connect-android-emulator-mac-windows.md)

Este guia aborda os métodos ao usar o emulador do Google Android.

## <a name="general-questions"></a>Perguntas gerais

### <a name="how-do-i-automate-an-android-nunit-test-projectautomate-android-nunit-testmd"></a>[Como automatizar a um projeto de Teste NUnit do Android?](automate-android-nunit-test.md)

Este guia aborda as etapas para configurar um projeto de teste do NUnit Android _não_ um projeto Xamarin.UITest. Xamarin.UITest guias podem ser encontrados [aqui](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest).

### <a name="how-do-i-enable-intellisense-in-android-axml-filesenable-axml-intellisensemd"></a>[Como habilitar o Intellisense em arquivos .axml do Android?](enable-axml-intellisense.md)

Este guia descreve como ativar o Intellisense do Visual Studio para Android .axml arquivos.

### <a name="why-cant-my-android-release-build-connect-to-the-internetandroid-internetmd"></a>[Por que minha compilação de versão do Android não pode se conectar à Internet?](android-internet.md)

A causa mais comum desse problema é que o **INTERNET** permissão é incluída automaticamente em uma compilação de depuração, mas deve ser definida manualmente para um build de versão. Este guia descreve como habilitar a permissão em compilações de versão.

### <a name="smarter-xamarin-android-support-v4--v13-nuget-packagesandroid-support-v4v13-librariesmd"></a>[Pacotes NuGet para suporte mais inteligente do Xamarin Android v4/v13](android-support-v4v13-libraries.md)

`Support-v4` e `Support-v13` não podem ser usados juntos no mesmo aplicativo, ou seja, eles são mutuamente exclusivos. Isso ocorre porque `Support-v13` realmente contém todos os tipos e a implementação de `Support-v4`. Se você tentar e referência ambos no mesmo projeto, você encontrará erros de tipo duplicado.

### <a name="how-do-i-resolve-a-pathtoolongexception-errorpath-too-long-exceptionmd"></a>[Como resolver um erro PathTooLongException?](path-too-long-exception.md)

Este artigo explica como resolver um **PathTooLongException** erros que podem ocorrer ao compilar um projeto xamarin.



## <a name="deprecated"></a>Preterido

> [!NOTE]
> Os artigos a seguir se aplicam a problemas que foram resolvidos em versões recentes do Xamarin. No entanto, se o problema ocorre na versão mais recente do software, envie um [novo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) com o controle de versão completo completo e informações de log de saída de compilação.

### <a name="what-version-of-xamarinandroid-added-lollipop-supportxa-lollipopmd"></a>[Qual versão do Xamarin.Android adicionou suporte ao Lollipop?](xa-lollipop.md)

Este guia foi gravado originalmente para a visualização de L Android. Xamarin 4.17 adicionado suporte ao Android visualização de L & xamarin 4.20 adicionado suporte do Android pirulito.

### <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawablemissing-action-mode-share-drawablemd"></a>[Android.Support.v7.AppCompat – Não encontramos um recurso correspondente ao nome fornecido: attr 'android:actionModeShareDrawable'](missing-action-mode-share-drawable.md)

Esse erro pode ocorrer em versões mais antigas do Xamarin se alguns o pacakages do SDK do Android necessários estão ausentes.

### <a name="adjusting-java-memory-parameters-for-the-android-designerandroid-designer-java-memorymd"></a>[Ajustar os parâmetros de memória do Java para o Designer Android](android-designer-java-memory.md)

Os parâmetros de memória padrão que são usados ao iniciar o `java` processo para o Android designer pode ser incompatível com algumas configurações do sistema. Começando com o Xamarin Studio 5.7.2.7 e Xamarin para Visual Studio 3.9.344 essas configurações pode ser personalizado em uma base por projeto.

### <a name="my-android-resourcedesignercs-file-will-not-updateresource-designer-wont-updatemd"></a>[Meu arquivo Resource.designer.cs do Android não será atualizado.](resource-designer-wont-update.md)

Um bug no Xamarin.Studio 5.1. csproj arquivos corrompidos anteriormente excluindo parcialmente ou totalmente o código xml no arquivo. csproj. Isso causaria um importante de sistema (como atualizar o Android Resource.designer.cs) de compilação de partes do Android que a falha. A partir de 5.1.4 estável de versão no dia 15 de julho, esse bug foi corrigido; mas, em muitos casos o arquivo de projeto deve ser reparado manualmente, conforme descrito neste guia.



