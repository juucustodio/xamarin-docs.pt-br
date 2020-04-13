---
title: Alterações nas Ferramentas do SDK do Android
description: Altera a forma como o Android SDK gerencia os níveis de API e AVDs instalados.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/21/2018
ms.openlocfilehash: 4c559a76d7354fd957d065717ef14d91591d1be0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019503"
---
# <a name="changes-to-the-android-sdk-tooling"></a>Alterações nas Ferramentas do SDK do Android

_Altera a forma como o Android SDK gerencia os níveis de API e AVDs instalados._

## <a name="changes-to-android-sdk-tooling"></a>Alterações nas ferramentas do Android SDK

Em versões recentes das Ferramentas SDK para Android, o Google removeu os gerentes de AVD e SDK existentes em favor das novas ferramentas CLI (Command Line Interface). O programa **android** foi removido e os gerentes de GUI (Graphical User Interface) do Google GUI (Graphical User Interface) no Visual Studio para Mac e versões mais antigas do Visual Studio Tools para Xamarin não funcionarão mais na versão 25.2.5 do Android SDK Tools. Por exemplo, tentar usar o programa **android** através da linha de comando resultará em uma mensagem de erro como a seguinte:

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

As seções a seguir explicam como gerenciar os Dispositivos Virtuais Android SDK e Android usando o Android SDK 25.3.0 e posterior.

### <a name="ui-tools"></a>Ferramentas de iu

O Visual Studio e o Visual Studio for Mac agora fornecem substituições de Xamarin para os gerentes descontinuados baseados em GUI do Google:

- Para baixar as ferramentas, plataformas e outros componentes do Android SDK para o desenvolvimento de aplicativos Xamarin.Android, use o [Xamarin Android SDK Manager](~/android/get-started/installation/android-sdk.md) em vez do Legado Google SDK Manager.

- Para criar e configurar dispositivos virtuais Android, use o [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md) em vez do Gerenciador de Emuladores do Google legado.

Essas ferramentas são funcionalmente equivalentes aos gerentes baseados em GUI do Google que substituem.

### <a name="cli-tools"></a>Ferramentas CLI

Alternativamente, você pode usar ferramentas CLI para gerenciar e atualizar seus emuladores e Android SDK. Os seguintes programas agora compõem a interface de linha de comando para as ferramentas android SDK:

#### <a name="sdkmanager"></a>sdkmanager

**Adicionado em:** Android SDK Tools 25.2.3 (novembro, 2016) e superior.

Há um novo programa chamado **sdkmanager** na pasta **de ferramentas/bin** do seu Android SDK. Esta ferramenta é usada para manter o Android SDK na linha de comando. Para obter mais informações sobre como usar esta ferramenta, consulte [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html).

#### <a name="avdmanager"></a>avdmanager

**Adicionado em:** Android SDK Tools 25.3.0 (março de 2017) e superior.

Há um novo programa chamado **avdmanager** na pasta **de ferramentas/bin** do seu Android SDK. Esta ferramenta é usada para manter os AVDs para o Emulador Android. Para obter mais informações sobre o uso desta ferramenta, consulte [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html).

### <a name="downgrading"></a>Desclassificação

Você pode fazer o downgrade da sua versão **do Android SDK Tools** instalando uma versão anterior do Android SDK no site do Android [Developer](https://developer.android.com/studio/index.html).

### <a name="using-the-old-gui"></a>Usando a GUI antiga

Você ainda pode usar a GUI original executando o programa **android** dentro de sua pasta **de ferramentas,** desde que você esteja no **Android SDK Tools** versão **25.2.5** ou inferior.

## <a name="related-links"></a>Links relacionados

- [Configuração do SDK do Android](~/android/get-started/installation/android-sdk.md)
- [Gerenciador de dispositivos Android](~/android/get-started/installation/android-emulator/device-manager.md)
- [Noções básicas sobre nível da API do Android](~/android/app-fundamentals/android-api-levels.md)
- [Notas de Versão das SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
