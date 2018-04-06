---
title: Emulador de linha de comando
ms.prod: xamarin
ms.assetid: E592AA32-5E83-B7E5-1753-12416551B23C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: b1ca1c2b441a9c9ca26de5668f318312bea156a3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="command-line-emulator"></a>Emulador de linha de comando


## <a name="running-the-android-emulator-from-the-command-line"></a>Executar o Android Emulator da linha de comando

Para habilitar a execução do Android Emulator da linha de comando, você pode usar a ferramenta "emulador" fornecida pelo SDK do Android. Essa ferramenta pode ser usada para executar o emulador do Terminal no OS X ou do prompt de comando em um computador Windows.

Para iniciar um Android Emulator específico, execute o seguinte comando do diretório de ferramentas no local do SDK do Android (tal como C:\android-sdk-windows\tools):

No Windows

```cmd
emulator.exe -avd NameOfYourEmulator -partition-size 512
```

No macOS

```bash
./emulator -avd NameOfYourEmulator -partition-size 512
```

O motivo da necessidade do tamanho da partição é permitir que o emulador tenha bastante espaço para obter a plataforma Xamarin.Android instalada no emulador, já que por padrão, o tamanho do emulador é pequeno.

Encontre mais informações sobre parâmetros adicionais no site do Android aqui – [http://developer.android.com/guide/developing/tools/emulator.html](http://developer.android.com/guide/developing/tools/emulator.html)
