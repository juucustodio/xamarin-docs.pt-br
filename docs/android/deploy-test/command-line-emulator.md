---
title: Emulador de linha de comando
ms.prod: xamarin
ms.assetid: E592AA32-5E83-B7E5-1753-12416551B23C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 11/05/2018
ms.openlocfilehash: 995b0783604f752915daaa77a8362899ac61e174
ms.sourcegitcommit: f541a92b4f896474f6a5467ccff2028dafa6fee7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2018
ms.locfileid: "50983582"
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

Encontre mais informações sobre parâmetros adicionais no site do Android aqui – [https://developer.android.com/studio/run/emulator-commandline](https://developer.android.com/studio/run/emulator-commandline)
