---
title: Emulador de linha de comando
ms.prod: xamarin
ms.assetid: E592AA32-5E83-B7E5-1753-12416551B23C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 11/05/2018
ms.openlocfilehash: 35e10ffc20e075e0245c7e42f7fd0aff24de4abb
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73021576"
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

Você pode descobrir mais informações sobre parâmetros extras no site do Android aqui -[https://developer.android.com/studio/run/emulator-commandline](https://developer.android.com/studio/run/emulator-commandline)
