---
title: Alterações nas Ferramentas do SDK do Android
description: Alterações em como o SDK do Android gerencia os níveis de API e os AVDs instalado.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/21/2018
ms.openlocfilehash: 4e808736fd92fa40ecbf0c24938c0fedd7afcff9
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935445"
---
# <a name="changes-to-the-android-sdk-tooling"></a>Alterações nas Ferramentas do SDK do Android

_Alterações em como o SDK do Android gerencia os níveis de API e os AVDs instalado._

## <a name="changes-to-android-sdk-tooling"></a>Alterações em ferramentas do SDK do Android

Em versões recentes das ferramentas do SDK para o Android, o Google removeu os gerenciadores de AVD e SDK existentes em favor de novas ferramentas de CLI (Interface de linha de comando). O **android** programa foi removido e os gerenciadores de Google GUI (Interface gráfica do usuário) no Visual Studio para Mac e versões mais antigas do Visual Studio Tools para Xamarin deixará de funcionar após a versão 25.2.5 de ferramentas do SDK do Android. Por exemplo, a tentativa de usar o **android** programa por meio da linha de comando resultará em uma mensagem de erro semelhante à seguinte:

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

As seções a seguir explicam como gerenciar o SDK do Android e dispositivos Virtual Android usando o SDK do Android 25.3.0 e posterior.

### <a name="ui-tools"></a>Ferramentas de interface do usuário

Visual Studio e o Visual Studio para Mac agora fornecem Xamarin substituições para os gerenciadores de descontinuados baseado em GUI do Google:

-   Para baixar as ferramentas do SDK do Android, plataformas e outros componentes que você precisa para desenvolver aplicativos xamarin, use o [Xamarin Android SDK Manager](~/android/get-started/installation/android-sdk.md) em vez do Gerenciador de SDK herdados Google.

-   Para criar e configurar dispositivos Virtual Android, use o [Gerenciador de dispositivos Android](~/android/get-started/installation/android-emulator/device-manager.md) em vez do Gerenciador de emulador herdados Google.

Essas ferramentas são funcionalmente equivalentes para o Google baseada em GUI e gerentes, elas substituem.

### <a name="cli-tools"></a>Ferramentas CLI

Como alternativa, você pode usar ferramentas CLI para gerenciar e atualizar os emuladores e o SDK do Android. Os seguintes programas fazem a interface de linha de comando para as ferramentas do SDK do Android:

#### <a name="sdkmanager"></a>sdkmanager

**Adicionado no:** ferramentas do SDK do Android 25.2.3 (novembro de 2016) e superior.

Há um novo programa chamado **sdkmanager** no **ferramentas/bin** pasta do seu Android SDK. Essa ferramenta é usada para manter o SDK do Android na linha de comando. Para obter mais informações sobre como usar essa ferramenta, consulte [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html).

#### <a name="avdmanager"></a>avdmanager

**Adicionado no:** ferramentas do SDK do Android 25.3.0 (março de 2017) e superior.

Há um novo programa chamado **avdmanager** no **ferramentas/bin** pasta do seu Android SDK. Essa ferramenta é usada para manter os AVDs para o emulador Android. Para obter mais informações sobre como usar essa ferramenta, consulte [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html).

### <a name="downgrading"></a>Fazendo downgrade

Você pode fazer o downgrade seu **ferramentas do SDK do Android** versão instalando uma versão anterior do SDK do Android do [site do desenvolvedor Android](https://developer.android.com/studio/index.html).

### <a name="using-the-old-gui"></a>Usando a GUI do antiga

Você ainda pode usar a GUI do original executando o **android** programa dentro de sua **ferramentas** pasta enquanto você estiver usando **ferramentas do SDK do Android** versão **25.2.5**  ou inferior.


## <a name="related-links"></a>Links relacionados

- [Configuração do SDK do Android](~/android/get-started/installation/android-sdk.md)
- [Gerenciador de dispositivos Android](~/android/get-started/installation/android-emulator/device-manager.md)
- [Noções básicas sobre nível da API do Android](~/android/app-fundamentals/android-api-levels.md)
- [Notas de Versão das SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
