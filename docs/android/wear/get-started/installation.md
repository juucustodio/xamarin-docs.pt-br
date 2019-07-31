---
title: 'Instalando e Configurando o sistema operacional de desgaste onXamarin. Android '
description: Este artigo explica as etapas de instalação e os detalhes de configuração necessários para preparar o computador e os dispositivos para o desenvolvimento de desgaste do Android. Ao final deste artigo, você terá uma instalação de desgaste do Xamarin. Android integrada ao Visual Studio para Mac e/ou Microsoft Visual Studio e estará pronto para começar a criar seu primeiro aplicativo de desgaste do Xamarin. Android.
ms.prod: xamarin
ms.assetid: 3BB395FA-0545-4024-A18F-98CF5E9CA55F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 0099414529027a9a6e52b79393cfa6f0f3bc24d6
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647797"
---
# <a name="setup-and-installation"></a>Instalação e Configuração

_Este artigo explica as etapas de instalação e os detalhes de configuração necessários para preparar o computador e os dispositivos para o desenvolvimento de desgaste do Android. Ao final deste artigo, você terá uma instalação de desgaste do Xamarin. Android integrada ao Visual Studio para Mac e/ou Microsoft Visual Studio e estará pronto para começar a criar seu primeiro aplicativo de desgaste do Xamarin. Android._

## <a name="requirements"></a>Requisitos

O seguinte é necessário para criar aplicativos de desgaste do Android baseados em Xamarin:

-   **Visual Studio ou Visual Studio para Mac** &ndash; A Comunidade do Visual Studio 2017 ou posterior é necessária.

-   **Xamarin. Android** &ndash; xamarin. Android 4,17 ou posterior deve ser instalado e configurado com o Visual Studio ou Visual Studio para Mac.

-   O **SDK do Android** -SDK do Android 5.0.1 (API 21) ou posterior deve ser instalado por meio do gerenciador de SDK do Android.

-   **Java Developer Kit** O desenvolvimento do Xamarin Android requer o [JDK 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) se você estiver desenvolvendo para o nível de API 24 ou superior (o JDK 1,8 também oferece suporte a níveis de API anteriores a 24). &ndash;

Você pode continuar a usar o [JDK 1,7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se estiver desenvolvendo especificamente para o nível de API 23 ou anterior.

> [!IMPORTANT]
> O Xamarin.Android não é compatível com o JDK 9.

## <a name="installation"></a>Instalação

Depois de instalar o Xamarin. Android, execute as seguintes etapas para que você esteja pronto para compilar e testar aplicativos de desgaste do Android: 

1.  Instale o SDK do Android e as ferramentas necessárias.
2.  Configurar um dispositivo de teste.
3.  Crie seu primeiro aplicativo de desgaste do Android.

Essas etapas são descritas nas seções a seguir.


### <a name="install-android-sdk-and-tools"></a>Instalar SDK do Android e ferramentas 

Inicie o **Gerenciador de SDK do Android**: 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Como iniciar o Gerenciador de SDK do Android no Visual Studio](installation-images/vs/sdk-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Como iniciar o Gerenciador de SDK do Android no Visual Studio para Mac](installation-images/xs/sdk-menu.png)

-----


Verifique se você tem as seguintes SDK do Android e ferramentas instaladas:

* Android SDK Tools v 24.0.0 ou superior e
* Android 4.4 W (API20) ou
* Android 5.0.1 (API21) ou superior.

Se você não tiver o SDK e as ferramentas mais recentes instalados, baixe as ferramentas de SDK necessárias *e* os bits de API (talvez seja necessário rolar um pouco para &ndash; encontrá-las a seleção de API é mostrada abaixo): 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Captura de tela de exemplo do Gerenciador SDK de habilitação de componentes do Android 5.0.1](installation-images/vs/sdk-select.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Captura de tela de exemplo do Gerenciador SDK de como habilitar os componentes do Android 4,4 e 5.0.1](installation-images/xs/sdk-select.png)

-----


## <a name="configuration"></a>Configuração

Antes de usar testar seu aplicativo, você deve configurar um emulador de desgaste do Android ou um dispositivo real de desgaste do Android. 


### <a name="android-wear-emulator"></a>Emulador de desgaste do Android

Antes de usar um emulador de desgaste do Android, você deve configurar um dispositivo virtual Android de desgaste do Android (AVD) usando o **Gerenciador do Google Emulator**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Como iniciar o Gerenciador de Android Emulator do Visual Studio](installation-images/vs/emulator-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Como iniciar o Gerenciador de Android Emulator de Visual Studio para Mac](installation-images/xs/emulator-menu.png)

-----

Para obter mais informações sobre como configurar um emulador de desgaste do Android, consulte [depurar o desgaste do Android em um emulador](~/android/wear/deploy-test/debug-on-emulator.md).


### <a name="android-wear-device"></a>Dispositivo Android de desgaste

Se você tiver um dispositivo de desgaste do Android, como um SmartWatch de desgaste do Android, poderá depurar o aplicativo nesse dispositivo em vez de usar um emulador. Para obter informações sobre como desenvolver com um dispositivo de desgaste, consulte [Depurar em um dispositivo de desgaste](~/android/wear/deploy-test/debug-on-device.md).


## <a name="create-your-first-android-wear-app"></a>Criar seu primeiro aplicativo de desgaste do Android

Siga as instruções [Hello, desgaste](~/android/wear/get-started/hello-wear.md) para criar seu primeiro aplicativo Watch.


## <a name="packaging-your-app"></a>Empacotando seu aplicativo

Os aplicativos de desgaste do Android sempre são distribuídos com um aplicativo de telefone Android complementar. 

Quando você adiciona seu aplicativo de desgaste do Android como uma referência ao aplicativo Android principal, ele é considerado automaticamente como um projeto de desgaste do Android e gerará todos os metadados e XML necessários para você. Além disso, ele verificará se o pacote e os números de versão correspondem para que você possa enviar facilmente seus aplicativos para Google Play. 

Para saber mais sobre aplicativos de desgaste de pacotes, consulte [trabalhando com empacotamento](~/android/wear/deploy-test/packaging.md).


## <a name="related-links"></a>Links relacionados

- [SkeletonWear (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-skeletonwear)
