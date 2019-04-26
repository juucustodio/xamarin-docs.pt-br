---
title: Alterações nas Ferramentas do SDK do Android
description: Alterações em como o SDK do Android gerencia o instalado níveis de API e AVDs.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: dbd3287e7c646be7fd969699eab685906a1c6c1a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61093928"
---
# <a name="changes-to-the-android-sdk-tooling"></a>Alterações nas Ferramentas do SDK do Android

_Alterações em como o SDK do Android gerencia o instalado níveis de API e AVDs._

## <a name="changes-to-android-sdk-tooling"></a>Alterações nas ferramentas do SDK do Android

Em versões recentes do SDK Tools para o Android, o Google removeu os gerenciadores de AVD e SDK existentes em favor de novas ferramentas da CLI (Interface de linha de comando). O **android** programa foi removido e os gerenciadores de Google GUI (Interface gráfica do usuário) no Visual Studio para Mac e as versões mais antigas das ferramentas do Visual Studio para Xamarin deixarão de funcionar anteriores à versão 25.2.5 de do Android SDK Tools. Por exemplo, a tentativa de usar o **android** programa por meio da linha de comando resultará em uma mensagem de erro semelhante à seguinte:

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

As seções a seguir explicam como gerenciar o SDK do Android e dispositivos virtuais Android usando o SDK do Android 25.3.0 e versões posteriores.

### <a name="ui-tools"></a>Ferramentas de interface do usuário

O Visual Studio e Visual Studio para Mac agora fornecem substituições de Xamarin para os gerenciadores de descontinuados baseado em GUI do Google:

-   Para baixar Android SDK tools, plataformas e outros componentes que você precisa para desenvolver aplicativos xamarin. Android, use o [Gerenciador de SDK do Android Xamarin](~/android/get-started/installation/android-sdk.md) em vez de Google SDK Manager herdado.

-   Para criar e configurar dispositivos virtuais Android, use o [Gerenciador de dispositivos Android](~/android/get-started/installation/android-emulator/device-manager.md) em vez de Google Emulator Manager herdado.

Essas ferramentas são funcionalmente equivalentes para o Google baseada em GUI e gerentes, eles substituem.

### <a name="cli-tools"></a>Ferramentas da CLI

Como alternativa, você pode usar as ferramentas da CLI para gerenciar e atualizar seus emuladores e o SDK do Android. Os seguintes programas agora compõem a interface de linha de comando para as ferramentas do SDK do Android:

#### <a name="sdkmanager"></a>sdkmanager

**Adicionado no:** Android SDK Tools 25.2.3 (novembro de 2016) e superior.

Há um novo programa chamado **sdkmanager** na **ferramentas/bin** pasta do SDK do Android. Essa ferramenta é usada para manter o SDK do Android na linha de comando. Para obter mais informações sobre como usar essa ferramenta, consulte [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html).

#### <a name="avdmanager"></a>avdmanager

**Adicionado no:** Android SDK Tools 25.3.0 (março de 2017) e superior.

Há um novo programa chamado **avdmanager** na **ferramentas/bin** pasta do SDK do Android. Essa ferramenta é usada para manter os AVDs para o emulador do Android. Para obter mais informações sobre como usar essa ferramenta, consulte [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html).

### <a name="downgrading"></a>Fazer downgrade

Você pode fazer o downgrade seu **do Android SDK Tools** versão instalando uma versão anterior do SDK do Android a [site do desenvolvedor Android](https://developer.android.com/studio/index.html).

### <a name="using-the-old-gui"></a>Usando a GUI antiga

Você ainda pode usar a GUI do original executando o **android** programa dentro de seu **ferramentas** pasta, desde que você estiver usando **do Android SDK Tools** versão **25.2.5**  ou inferior.


## <a name="related-links"></a>Links relacionados

- [Configuração do SDK do Android](~/android/get-started/installation/android-sdk.md)
- [Gerenciador de dispositivos Android](~/android/get-started/installation/android-emulator/device-manager.md)
- [Noções básicas sobre nível da API do Android](~/android/app-fundamentals/android-api-levels.md)
- [Notas de Versão das SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
