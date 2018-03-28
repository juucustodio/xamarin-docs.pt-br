---
title: Alterações nas Ferramentas do SDK do Android
description: Alterações em como o SDK do Android gerencia os níveis de API e os AVDs instalado.
ms.topic: article
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: a16aa3704d9e0a63cfabde4b620452e7e2a5bf57
ms.sourcegitcommit: 20ca85ff638dbe3a85e601b5eb09b2f95bda2807
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="changes-to-the-android-sdk-tooling"></a>Alterações nas Ferramentas do SDK do Android

_Alterações em como o SDK do Android gerencia os níveis de API e os AVDs instalado._

## <a name="changes-to--android-sdk-tooling"></a>Alterações em ferramentas do SDK do Android

Em versões atuais das ferramentas do SDK para o Android, o Google removeu os gerenciadores de AVD e SDK existentes em favor de novas ferramentas de CLI (Interface de linha de comando). O primeiro **android** programa foi removido e os gerenciadores de GUI (Interface gráfica do usuário) no Visual Studio para Mac e versões mais antigas do Xamarin para Visual Studio deixará de funcionar após a versão 25.2.5 de ferramentas do SDK do Android.


![Menu Android do IDE do Visual Studio](sdk-cli-tooling-changes-images/android-ide-menu.png)

Tentativa de usar o **android** programa por meio da linha de comando resultará em uma mensagem de erro semelhante à seguinte:

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

Portanto, você precisará usar as ferramentas CLI para gerenciar e atualizar os emuladores e o SDK do Android.

### <a name="cli-tools"></a>Ferramentas CLI

Os seguintes programas fazem a interface de linha de comando para as ferramentas do SDK do Android:

#### <a name="sdkmanager"></a>sdkmanager

**Adicionado no:** ferramentas do SDK do Android 25.2.3 (novembro de 2016) e superior.

Há um novo programa chamado **sdkmanager** no **ferramentas/bin** pasta do seu Android SDK. Essa ferramenta é usada para manter o SDK do Android na linha de comando. Para obter mais informações sobre como usar essa ferramenta, consulte [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html).

#### <a name="avdmanager"></a>avdmanager

**Adicionado no:** ferramentas do SDK do Android 25.3.0 (março de 2017) e superior.

Há um novo programa chamado **avdmanager** no **ferramentas/bin** pasta do seu Android SDK. Essa ferramenta é usada para manter os AVDs para o emulador Android da Google. Para obter mais informações sobre como usar essa ferramenta, consulte [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html).

### <a name="downgrading"></a>Fazendo downgrade

Você pode fazer o downgrade seu **ferramentas do SDK do Android** versão instalando uma versão anterior do SDK do Android do [site do desenvolvedor Android](https://developer.android.com/studio/index.html).

### <a name="using-the-old-gui"></a>Usando a GUI do antiga

Você ainda pode usar a GUI do original executando o **android** programa dentro de sua **ferramentas** pasta enquanto você estiver usando **ferramentas do SDK do Android** versão **25.2.5**  ou inferior.


## <a name="related-links"></a>Links relacionados

- [Configuração do SDK do Android](~/android/get-started/installation/android-sdk.md)
- [Noções básicas sobre nível da API do Android](~/android/app-fundamentals/android-api-levels.md)
- [Notas de Versão das SDK Tools (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
