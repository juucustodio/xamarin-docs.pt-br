---
title: 'Instalando e configurando o desgaste onXamarin.Android do sistema operacional '
description: Este artigo explica as etapas de instalação e os detalhes de configuração necessárias para preparar seu computador e dispositivos para o desenvolvimento de Android Wear. No final deste artigo, você terá um trabalho de instalação do xamarin. Android Wear integrada no Visual Studio para Mac e/ou Microsoft Visual Studio, e você estará pronto para começar a criar seu primeiro aplicativo xamarin. Android Wear.
ms.prod: xamarin
ms.assetid: 3BB395FA-0545-4024-A18F-98CF5E9CA55F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 96fd6d32f37dd90422f05caf33cfda9a65683fd2
ms.sourcegitcommit: 650458de1d362cd7de174cacef7838f0e74426f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2019
ms.locfileid: "58071028"
---
# <a name="setup-and-installation"></a>Instalação e Configuração

_Este artigo explica as etapas de instalação e os detalhes de configuração necessárias para preparar seu computador e dispositivos para o desenvolvimento de Android Wear. No final deste artigo, você terá um trabalho de instalação do xamarin. Android Wear integrada no Visual Studio para Mac e/ou Microsoft Visual Studio, e você estará pronto para começar a criar seu primeiro aplicativo xamarin. Android Wear._

## <a name="requirements"></a>Requisitos

O exemplo a seguir é necessário para criar aplicativos baseados em Xamarin Android Wear:

-   **Visual Studio ou Visual Studio para Mac** &ndash; Visual Studio 2017 Community ou posterior é necessário.

-   **Xamarin. Android** &ndash; xamarin. Android 4.17 ou posterior deve ser instalado e configurado com o Visual Studio ou Visual Studio para Mac.

-   **SDK do Android** -SDK do Android 5.0.1 (API 21) ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.

-   **Java Developer Kit** &ndash; requer o desenvolvimento Xamarin Android [JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) se você estiver desenvolvendo para o nível de API 24 ou superior (JDK 1.8 também dá suporte a níveis de API anteriores ao 24).

Você pode continuar a usar [JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) se você estiver desenvolvendo especificamente para o nível de API 23 ou anterior.

> [!IMPORTANT]
> O Xamarin.Android não é compatível com o JDK 9.

## <a name="installation"></a>Instalação

Depois de ter instalado o xamarin. Android, execute as seguintes etapas para que você está pronto para compilar e testar aplicativos Android Wear: 

1.  Instale as ferramentas e SDK do Android necessária.
2.  Configure um dispositivo de teste.
3.  Crie seu primeiro aplicativo Android Wear.

Essas etapas são descritas nas seções a seguir.


### <a name="install-android-sdk-and-tools"></a>Instalar ferramentas e SDK do Android 

Inicie o **Gerenciador de SDK do Android**: 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Como iniciar o Gerenciador de SDK do Android no Visual Studio](installation-images/vs/sdk-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Como iniciar o Gerenciador de SDK do Android no Visual Studio para Mac](installation-images/xs/sdk-menu.png)

-----


Certifique-se de que você tenha o seguinte Android SDK e ferramentas instaladas:

* Android SDK Tools v 24.0.0 ou superior, e
* Android 4.4W (API20), ou
* Android 5.0.1 (API21) ou superior.

Se você não tiver o SDK e ferramentas instaladas o mais recente, baixe as ferramentas SDK necessárias *e* os bits de API (talvez seja necessário rolar um pouco para encontrá-los &ndash; a seleção de API é mostrada abaixo): 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Captura de tela do Gerenciador de SDK do exemplo de habilitar o Android 5.0.1 componentes](installation-images/vs/sdk-select.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Captura de tela do Gerenciador de SDK do exemplo de habilitar o Android 4.4 e 5.0.1 componentes](installation-images/xs/sdk-select.png)

-----


## <a name="configuration"></a>Configuração

Antes de poder usar testar seu aplicativo, você deve configurar um emulador Android Wear ou um dispositivo real Android Wear. 


### <a name="android-wear-emulator"></a>Emulador do Android Wear

Antes de usar um emulador Android Wear, você deve configurar um Android Wear Android Virtual AVD (dispositivo) usando o **Google Emulator Manager**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Como iniciar o Gerenciador de emulador do Android do Visual Studio](installation-images/vs/emulator-menu.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Como iniciar o Gerenciador de emulador do Android do Visual Studio para Mac](installation-images/xs/emulator-menu.png)

-----

Para obter mais informações sobre como configurar um emulador Android Wear, consulte [depurar Android Wear em um emulador](~/android/wear/deploy-test/debug-on-emulator.md).


### <a name="android-wear-device"></a>Dispositivo Android Wear

Se você tiver um dispositivo Android Wear, como um Smartwatch de Android Wear, você pode depurar o aplicativo nesse dispositivo, em vez de usar um emulador. Para obter informações sobre como desenvolver com um dispositivo Wear, consulte [depurar em um dispositivo Wear](~/android/wear/deploy-test/debug-on-device.md).


## <a name="create-your-first-android-wear-app"></a>Criar seu primeiro aplicativo Android Wear

Siga as [Olá, Wear](~/android/wear/get-started/hello-wear.md) as instruções para criar seu primeiro aplicativo de inspeção.


## <a name="packaging-your-app"></a>Empacotando seu aplicativo

Aplicativos do Android wear sempre são distribuídos com um aplicativo de telefone Android complementar. 

Quando você adiciona seu aplicativo Android Wear como uma referência ao seu aplicativo Android principal, ele é automaticamente considerado um projeto do Android Wear e gerará todos os XML e metadados necessários para você. Além disso, ele verificará que o pacote e números de versão correspondem para que você pode facilmente enviar seus aplicativos no Google Play. 

Para saber mais sobre como empacotar aplicativos de desgaste, consulte [trabalhando com o empacotamento](~/android/wear/deploy-test/packaging.md).


## <a name="related-links"></a>Links relacionados

- [SkeletonWear (amostra)](https://developer.xamarin.com/samples/SkeletonWear/)
