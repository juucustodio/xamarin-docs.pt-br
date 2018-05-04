---
title: 'Instalando e configurando o desempenham onXamarin.Android do sistema operacional '
description: Este artigo explica as etapas de instalação e os detalhes de configuração necessárias para preparar seu computador e dispositivos para o desenvolvimento de desgaste Android. No final deste artigo, você terá um trabalho de instalação desgaste xamarin integrada ao Visual Studio para Mac e/ou Microsoft Visual Studio, e você estará pronto para começar a criar seu primeiro aplicativo xamarin desgaste.
ms.prod: xamarin
ms.assetid: 3BB395FA-0545-4024-A18F-98CF5E9CA55F
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/25/2018
ms.openlocfilehash: 14162663c518fdd1324f2b0340592fbae491d112
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="setup-and-installation"></a>Instalação e Configuração

_Este artigo explica as etapas de instalação e os detalhes de configuração necessárias para preparar seu computador e dispositivos para o desenvolvimento de desgaste Android. No final deste artigo, você terá um trabalho de instalação desgaste xamarin integrada ao Visual Studio para Mac e/ou Microsoft Visual Studio, e você estará pronto para começar a criar seu primeiro aplicativo xamarin desgaste._

## <a name="requirements"></a>Requisitos

O exemplo a seguir é necessário para criar aplicativos baseados em Xamarin Android desgaste:

-   **O Visual Studio ou o Visual Studio para Mac** &ndash; você se você estiver usando o Visual Studio, o Visual Studio 2015 Professional ou posterior é necessário.

-   **Xamarin** &ndash; xamarin 4.17 ou posterior deve ser instalado e configurado com o Visual Studio ou Visual Studio para Mac.

-   **SDK do Android** -Android SDK 5.0.1 (API 21) ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.

-   **Java Developer Kit** &ndash; requer o desenvolvimento do Xamarin Android [1.8 JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) se você estiver desenvolvendo para o nível de API 24 ou superior (JDK 1.8 também dá suporte a API níveis anteriores a 24).

Você pode continuar a usar [JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se você estiver desenvolvendo especificamente para API nível 23 ou anterior.

> [!IMPORTANT]
> O Xamarin.Android não é compatível com o JDK 9.

## <a name="installation"></a>Instalação

Depois que você tiver instalado o xamarin, execute as seguintes etapas para que você está pronto para compilar e testar aplicativos do Android desgaste: 

1.  Instale o SDK do Android e as ferramentas necessárias.
2.  Configure um dispositivo de teste.
3.  Crie seu primeiro aplicativo do Android desgaste.

Essas etapas são descritas nas seções a seguir.


### <a name="install-android-sdk-and-tools"></a>Instalar ferramentas e SDK do Android 

Inicie o **Manager SDK do Android**: 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Como iniciar o Gerenciador de SDK do Android no Visual Studio](installation-images/vs/sdk-menu.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Como iniciar o Gerenciador de SDK do Android no Visual Studio para Mac](installation-images/xs/sdk-menu.png)

-----


Certifique-se de que você tenha o seguinte SDK do Android e as ferramentas instaladas:

* Android ferramentas SDK v 24.0.0 ou superior, e
* Android 4.4W (API20), ou
* Android 5.0.1 (API21) ou superior.

Se você não tiver o SDK e ferramentas instaladas o mais recente, baixe as ferramentas SDK *e* os bits de API (talvez seja necessário rolar um pouco para encontrá-los &ndash; a seleção de API é mostrada abaixo): 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Captura de tela de Gerenciador de SDK do exemplo de habilitação 5.0.1 Android componentes](installation-images/vs/sdk-select.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Captura de tela de Gerenciador de SDK do exemplo de como habilitar o Android 4.4 e 5.0.1 componentes](installation-images/xs/sdk-select.png)

-----


## <a name="configuration"></a>Configuração

Antes de usar o testar seu aplicativo, você deve configurar um emulador desgaste Android ou um dispositivo real desgaste Android. 


### <a name="android-wear-emulator"></a>Emulador do Android desgaste

Antes de usar um emulador desgaste Android, você deve configurar um Android desgaste dispositivo Virtual Android (AVD) usando o **Gerenciador de emulador do Google**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Como iniciar o Gerenciador de emulador do Android do Visual Studio](installation-images/vs/emulator-menu.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Como iniciar o Gerenciador de emulador do Android do Visual Studio para Mac](installation-images/xs/emulator-menu.png)

-----

Para obter mais informações sobre como configurar um emulador desgaste Android, consulte [depurar Android desempenham em um emulador](~/android/wear/deploy-test/debug-on-emulator.md).


### <a name="android-wear-device"></a>Dispositivo de desgaste Android

Se você tiver um dispositivo Android desgaste como um Smartwatch de desgaste Android, você pode depurar o aplicativo no dispositivo em vez de usar um emulador. Para obter informações sobre como desenvolver com um dispositivo de desgaste, consulte [depurar em um dispositivo de desgaste](~/android/wear/deploy-test/debug-on-device.md).


## <a name="create-your-first-android-wear-app"></a>Criar seu primeiro aplicativo do Android desgaste

Siga o [desgaste Hello,](~/android/wear/get-started/hello-wear.md) as instruções para criar seu primeiro aplicativo de inspeção.


## <a name="packaging-your-app"></a>Empacotando seu aplicativo

Aplicativos do Android desgaste sempre são distribuídos com um aplicativo de telefone Android complementar. 

Quando você adiciona seu aplicativo Android usam como uma referência ao seu aplicativo Android principal-lo automaticamente é considerado como um projeto Android desgaste e irá gerar todos os XML e metadados necessários para você. Além disso, ele verificará que números de pacote e versão correspondem para que você pode facilmente enviar seus aplicativos para o Google Play. 

Para saber mais sobre como empacotar aplicativos desgaste, consulte [trabalhar com empacotamento](~/android/wear/deploy-test/packaging.md).


## <a name="related-links"></a>Links relacionados

- [SkeletonWear (exemplo)](https://developer.xamarin.com/samples/SkeletonWear/)
