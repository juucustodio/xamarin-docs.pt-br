---
title: Aplicativo de Player ao vivo do Xamarin
description: Este documento descreve o Player Live Xamarin do aplicativo, que pode ser usado para visualizar as alterações de código em tempo real no dispositivo. Ele aborda a instalação, exemplos, logs, configurações de gerenciamento de dispositivos e muito mais.
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
author: topgenorth
ms.author: toopge
ms.date: 05/14/2017
ms.openlocfilehash: 88f7f62650484007c221aa7baaa684f872e0a8e9
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794144"
---
# <a name="xamarin-live-player-app"></a>Aplicativo de Player ao vivo do Xamarin

![Recurso de visualização](~/media/shared/preview.png)

Depois de instalar o aplicativo em seu telefone, siga o [instruções de instalação](~/tools/live-player/install.md) para se conectar ao seu computador. Tente uma da [aplicativos de exemplo](~/tools/live-player/samples.md) para fazê-lo funcionar.

Na inicialização, o aplicativo Xamarin ao vivo Player tem esta aparência (no iOS e Android respectivamente):

![Captura de tela de aplicativo do Player iOS de Live](player-images/app-iphone-sml.png) ![Captura de tela de aplicativo Android do Player ao vivo](player-images/app-android-sml.png)

Quando você pressiona **par para o Visual Studio**, use a câmera para verificar o código de barras que mostra em seu computador:

![Captura de tela do scanner de código de barras do iOS](player-images/scan-iphone-sml.png) ![Captura de tela do scanner de código de barras Android](player-images/scan-android-sml.png)

Se a conexão for bem-sucedida, o código deve ser executado no dispositivo quase imediatamente (como o [exemplo Calculadora](https://developer.xamarin.com/samples/mobile/LivePlayer/BasicCalculator)):

![Aplicativo de exemplo do cálculo em execução no dispositivo](player-images/basic-calculator-iphone-sml.png)

## <a name="options"></a>Opções

Pressione o botão informações **(i)** na parte inferior do aplicativo para revelar o **opções** menu:

[![Captura de tela do menu Opções](player-images/options-sml.png)](player-images/options.png#lightbox)

### <a name="logs"></a>logs

Exibir logs para diagnosticar problemas.

### <a name="settings"></a>Configurações

- Alternar a exibição de erros de compilação e tempo de execução.
- Informações de versão.
- Envie comentários.

[![Captura de tela das configurações](player-images/settings-sml.png)](player-images/settings.png#lightbox)

## <a name="managing-devices"></a>Gerenciamento de dispositivos

Para conectar um dispositivo pela primeira vez, siga as instruções em [requisitos & instalação](~/tools/live-player/install.md). Você pode combinar vários dispositivos (por exemplo, um iOS e um Android) e gerenciá-los por meio do IDE.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

No Visual Studio, escolha **Ferramentas > Xamarin Live Player > Gerenciar dispositivos...**

![Gerenciar dispositivos de janela](player-images/manage-tools-menu-vs.png)

Esta janela permite que você faça o seguinte:

- Par de um novo dispositivo por meio do exame do código
- Como alternativa emparelhar um dispositivo, digitando o código exibido em sua tela
- Remover dispositivos existentes na lista

Você também pode acessar essa janela da lista de dispositivos.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

No Visual Studio para Mac, escolha **Ferramentas > (Xamarin Live Player) gerenciar dispositivos...**

![Gerenciar dispositivos de janela](player-images/manage-tools-menu.png)

Esta janela permite que você faça o seguinte:

- Par de um novo dispositivo por meio do exame do código
- Como alternativa emparelhar um dispositivo, digitando o código exibido em sua tela
- Remover dispositivos existentes na lista

![Gerenciar dispositivos de janela](player-images/manage.png)

Você também pode acessar essa janela da lista de dispositivos:

![Escolha o Xamarin Live Player dispositivos da lista de dispositivos](player-images/manage-device-menu.png)

-----

Se você tiver qualquer consulte problemas [limitações e solução de problemas](~/tools/live-player/troubleshooting.md).

## <a name="related-links"></a>Links relacionados

- [Limitações](~/tools/live-player/limitations.md)
- [Solução de problemas](~/tools/live-player/troubleshooting.md)
- [Exemplos do Xamarin Player ao vivo](samples.md)
