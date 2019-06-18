---
title: Aplicativo Xamarin Live Player
description: Este documento descreve o Xamarin Live Player aplicativo, que pode ser usado para visualizar alterações de código ao vivo no dispositivo. Ele aborda a instalação, amostras, logs, configurações, gerenciamento de dispositivos e muito mais.
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
author: lobrien
ms.author: laobri
ms.date: 06/13/2019
ms.openlocfilehash: fce0eeae4ef5776842ea1b45c36163118042dc49
ms.sourcegitcommit: 93b1e2255d59c8ca6674485938f26bd425740dd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67157735"
---
# <a name="xamarin-live-player-app"></a>Aplicativo Xamarin Live Player

![Versão prévia do recurso](~/media/shared/preview.png)

> [!WARNING]
> O Xamarin Live Player visualização foi encerrada. O aplicativo não está mais disponível. As instruções a seguir são fornecidas para clientes que continuam a usar a visualização com o Visual Studio 2017.

> [!TIP]
> Você pode usar o [XAML pré-visualizador](~/xamarin-forms/xaml/xaml-previewer/index.md) no 2019 do Visual Studio ou Visual Studio para Mac para exibir seus designs de tela, conforme você editá-los.

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

# <a name="visual-studio-2017tabwindows"></a>[Visual Studio 2017](#tab/windows)

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

- [Solução de problemas](~/tools/live-player/troubleshooting.md)

