---
title: Aplicativo Xamarin Live Player
description: Este documento descreve o Xamarin Live Player aplicativo, que pode ser usado para visualizar alterações de código ao vivo no dispositivo. Ele aborda a instalação, amostras, logs, configurações, gerenciamento de dispositivos e muito mais.
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
author: topgenorth
ms.author: toopge
ms.date: 08/08/2017
ms.openlocfilehash: b3166aa440cbe2981d597771b360373fadc6451b
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2018
ms.locfileid: "40250952"
---
# <a name="xamarin-live-player-app"></a>Aplicativo Xamarin Live Player

![Recurso de visualização](~/media/shared/preview.png)

Depois de instalar o aplicativo em seu telefone, siga as [instruções de instalação](~/tools/live-player/install.md) para se conectar ao seu computador. Tente uma da [aplicativos de exemplo](~/tools/live-player/samples.md) fazê-lo funcionar.

Na inicialização, o aplicativo Xamarin Live Player tem esta aparência:

![Captura de tela de aplicativo Android Player ao vivo](player-images/app-android-sml.png)

Quando você pressiona **par para o Visual Studio**, use a câmera para digitalizar o código de barras mostrando em seu computador:

![Captura de tela do scanner Android](player-images/scan-android-sml.png)

Se a conexão for bem-sucedida, o código deve ser executado no dispositivo quase que imediatamente (como o [exemplo de calculadora](https://developer.xamarin.com/samples/mobile/LivePlayer/BasicCalculator)):

![Aplicativo de Calculadora de exemplo em execução no dispositivo](player-images/basic-calculator-sml.png)

## <a name="options"></a>Opções

Pressione o botão informações **(i)** na parte inferior do aplicativo para revelar as **opções** menu:

[![Captura de tela do menu de opções](player-images/options-sml.png)](player-images/options.png#lightbox)

### <a name="logs"></a>Logs

Exibir logs para diagnosticar problemas.

### <a name="settings"></a>Configurações

- Ativar/desativar exibição de erros de compilação e tempo de execução.
- Informações de versão.
- Envie comentários.

[![Captura de tela das configurações](player-images/settings-sml.png)](player-images/settings.png#lightbox)

## <a name="managing-devices"></a>Gerenciamento de dispositivos

Para conectar um dispositivo pela primeira vez, siga as instruções em [requisitos de & instalação](~/tools/live-player/install.md). Você pode combinar vários dispositivos e gerenciá-los por meio do IDE.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

No Visual Studio, escolha **Ferramentas > Xamarin Live Player > Gerenciar dispositivos...**

![Gerenciar dispositivos de janela](player-images/manage-tools-menu-vs.png)

Essa janela permite que você faça o seguinte:

- Emparelhar um dispositivo novo examinando o código
- Como alternativa, emparelhar um dispositivo digitando o código exibido em sua tela
- Remover dispositivos existentes da lista

Você também pode acessar essa janela da lista de dispositivos.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

No Visual Studio para Mac, escolha **Ferramentas > (Xamarin Live Player) gerenciar dispositivos...**

![Gerenciar dispositivos de janela](player-images/manage-tools-menu.png)

Essa janela permite que você faça o seguinte:

- Emparelhar um dispositivo novo examinando o código
- Como alternativa, emparelhar um dispositivo digitando o código exibido em sua tela
- Remover dispositivos existentes da lista

![Gerenciar dispositivos de janela](player-images/manage.png)

Você também pode acessar essa janela da lista de dispositivos:

![Escolha os dispositivos do Xamarin Live Player da lista de dispositivos](player-images/manage-device-menu.png)

-----

Se você tiver qualquer consulte problemas [limitações e solução de problemas](~/tools/live-player/troubleshooting.md).

## <a name="related-links"></a>Links relacionados

- [Limitações](~/tools/live-player/limitations.md)
- [Solução de problemas](~/tools/live-player/troubleshooting.md)
- [Exemplos do Xamarin Live Player](samples.md)
