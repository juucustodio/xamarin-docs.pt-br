---
title: Alterações nas Ferramentas do SDK do Android
description: Alterações no modo como o SDK do Android gerencia os níveis de API instalados e AVDs.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: 897daef3aba1166018a0ac796e9c7956c5f0c711
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761897"
---
# <a name="changes-to-the-android-sdk-tooling"></a>Alterações nas Ferramentas do SDK do Android

_Alterações no modo como o SDK do Android gerencia os níveis de API instalados e AVDs._

## <a name="changes-to-android-sdk-tooling"></a>Alterações nas ferramentas de SDK do Android

Nas versões recentes do SDK Tools para Android, o Google removeu os gerenciadores de AVD e SDK existentes em favor da nova ferramenta CLI (interface de linha de comando). O programa **Android** foi removido e os gerenciadores de GUI do Google (interface gráfica do usuário) em Visual Studio para Mac e versões mais antigas do ferramentas do Visual Studio para o Xamarin não funcionarão mais após a versão 25.2.5 do Android SDK Tools. Por exemplo, tentar usar o programa **Android** por meio da linha de comando resultará em uma mensagem de erro semelhante à seguinte:

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

As seções a seguir explicam como gerenciar os dispositivos virtuais SDK do Android e Android usando o SDK do Android 25.3.0 e posterior.

### <a name="ui-tools"></a>Ferramentas de interface do usuário

O Visual Studio e o Visual Studio para Mac agora fornecem substituições do Xamarin para os gerenciadores baseados em GUI do Google descontinuados:

- Para baixar SDK do Android ferramentas, plataformas e outros componentes necessários para desenvolver aplicativos Xamarin. Android, use o [Gerenciador de SDK do Android do xamarin](~/android/get-started/installation/android-sdk.md) em vez do Gerenciador do SDK do Google herdado.

- Para criar e configurar dispositivos virtuais Android, use a [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md) em vez do Gerenciador de emulador do Google herdado.

Essas ferramentas são funcionalmente equivalentes aos gerentes baseados em GUI do Google que substituem.

### <a name="cli-tools"></a>Ferramentas da CLI

Como alternativa, você pode usar as ferramentas da CLI para gerenciar e atualizar seus emuladores e SDK do Android. Os programas a seguir agora compõem a interface de linha de comando para as ferramentas de SDK do Android:

#### <a name="sdkmanager"></a>sdkmanager

**Adicionado em:** Android SDK Tools 25.2.3 (novembro, 2016) e superior.

Há um novo programa chamado **sdkmanager** na pasta **ferramentas/bin** do seu SDK do Android. Essa ferramenta é usada para manter o SDK do Android na linha de comando. Para obter mais informações sobre como usar essa ferramenta, consulte [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html).

#### <a name="avdmanager"></a>avdmanager

**Adicionado em:** Android SDK Tools 25.3.0 (março, 2017) e superior.

Há um novo programa chamado **avdmanager** na pasta **ferramentas/bin** do seu SDK do Android. Essa ferramenta é usada para manter o AVDs para o Android Emulator. Para obter mais informações sobre como usar essa ferramenta, consulte [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html).

### <a name="downgrading"></a>Downgrade

Você pode fazer o downgrade da versão do **Android SDK Tools** instalando uma versão anterior do SDK do Android no [site do desenvolvedor do Android](https://developer.android.com/studio/index.html).

### <a name="using-the-old-gui"></a>Usando a GUI antiga

Você ainda pode usar a GUI original executando o programa **Android** dentro da pasta **Tools** , contanto que esteja no **Android SDK Tools** versão **25.2.5** ou inferior.

## <a name="related-links"></a>Links relacionados

- [Configuração do SDK do Android](~/android/get-started/installation/android-sdk.md)
- [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)
- [Noções básicas sobre nível da API do Android](~/android/app-fundamentals/android-api-levels.md)
- [Notas de Versão das SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
