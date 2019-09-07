---
title: Aplicativo Xamarin Live Player
description: Este documento descreve o Xamarin Live Player aplicativo, que pode ser usado para visualizar as alterações de código ativas no dispositivo. Ele aborda a instalação, os exemplos, os logs, as configurações, o gerenciamento de dispositivos e muito mais.
ms.prod: xamarin
ms.assetid: A7EB73C1-38D7-46C5-9AF6-4C571C168BE7
author: conceptdev
ms.author: crdun
ms.date: 06/13/2019
ms.openlocfilehash: d4cf596d70be746e85f21bace3e5ddf662076e6d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772322"
---
# <a name="xamarin-live-player-app"></a>Aplicativo Xamarin Live Player

![Versão prévia do recurso](~/media/shared/preview.png)

> [!WARNING]
> A visualização do Xamarin Live Player foi encerrada. O aplicativo não está mais disponível. As instruções a seguir são fornecidas para os clientes que continuam a usar a visualização com o Visual Studio 2017.

> [!TIP]
> Você pode usar o [Visualizar XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) no Visual Studio 2019 ou Visual Studio para Mac para exibir os designs de tela ao editá-los.

Na inicialização, o aplicativo Xamarin Live Player tem esta aparência:

![Captura de tela do aplicativo Android do Live Player](player-images/app-android-sml.png)

Quando você pressiona o **par para o Visual Studio**, use a câmera para digitalizar a exibição do código de barras no computador:

![Captura de tela do scanner de código de barras do Android](player-images/scan-android-sml.png)

Se a conexão for bem-sucedida, o código deverá ser executado no dispositivo quase imediatamente (como o [exemplo de calculadora](https://github.com/xamarin/mobile-samples/tree/master/LivePlayer/BasicCalculator):

![Exemplo de aplicativo de calculadora em execução no dispositivo](player-images/basic-calculator-sml.png)

## <a name="options"></a>Opções

Pressione o botão de informações **(i)** na parte inferior do aplicativo para revelar o menu de **Opções** :

[![Captura de tela do menu de opções](player-images/options-sml.png)](player-images/options.png#lightbox)

### <a name="logs"></a>Logs

Exibir logs para diagnosticar problemas.

### <a name="settings"></a>Configurações

- Alternar exibição de erros de compilação e tempo de execução.
- Informações de versão.
- Enviar comentários.

[![Captura de tela das configurações](player-images/settings-sml.png)](player-images/settings.png#lightbox)

## <a name="managing-devices"></a>Gerenciando dispositivos

Para conectar um dispositivo pela primeira vez, siga as instruções em [requisitos & configuração](~/tools/live-player/install.md). Você pode emparelhar vários dispositivos e gerenciá-los por meio do IDE.

# <a name="visual-studio-2017tabwindows"></a>[Visual Studio 2017](#tab/windows)

No Visual Studio, escolha **ferramentas > Xamarin Live Player > gerenciar dispositivos...**

![Janela gerenciar dispositivos](player-images/manage-tools-menu-vs.png)

Esta janela permite que você faça o seguinte:

- Emparelhar um novo dispositivo examinando o código
- Como alternativa, Emparelhe um dispositivo digitando o código exibido na tela
- Remover dispositivos existentes da lista

Você também pode acessar essa janela na lista de dispositivos.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Em Visual Studio para Mac, escolha **ferramentas > (Xamarin Live Player) gerenciar dispositivos...**

![Janela gerenciar dispositivos](player-images/manage-tools-menu.png)

Esta janela permite que você faça o seguinte:

- Emparelhar um novo dispositivo examinando o código
- Como alternativa, Emparelhe um dispositivo digitando o código exibido na tela
- Remover dispositivos existentes da lista

![Janela gerenciar dispositivos](player-images/manage.png)

Você também pode acessar essa janela na lista de dispositivos:

![Escolha Xamarin Live Player dispositivos na lista de dispositivos](player-images/manage-device-menu.png)

-----

Se você tiver problemas, consulte [limitações e solução de problemas](~/tools/live-player/troubleshooting.md).

## <a name="related-links"></a>Links relacionados

- [Solução de problemas](~/tools/live-player/troubleshooting.md)
