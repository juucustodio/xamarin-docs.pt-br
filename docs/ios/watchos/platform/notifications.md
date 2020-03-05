---
title: watchOS notificações no Xamarin
description: Este documento descreve como trabalhar com as notificações de watchOS no Xamarin. Ele aborda a criação controladores de notificação, gerar notificações e notificações de teste.
ms.prod: xamarin
ms.assetid: 0BC1306E-0713-4592-996E-7530CCF281E7
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 85a55967446da5cf89e8ce19dadf88d0de16d80a
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291864"
---
# <a name="watchos-notifications-in-xamarin"></a>watchOS notificações no Xamarin

Aplicativos do Watch podem receber notificações se o aplicativo iOS contém ofereça suporte a eles. Há uma manipulação de notificação interna, portanto, você não *precisa* adicionar o suporte de notificação adicional descrito abaixo. no entanto, se você quiser personalizar o comportamento de notificação e a aparência, continue lendo.

Consulte o documento de [notificações do IOS](~/ios/platform/user-notifications/deprecated/index.md) para obter mais informações sobre como adicionar suporte de notificação ao aplicativo Ios em sua solução.

## <a name="creating-notification-controllers"></a>Criação de controladores de notificação

No storyboard a controladores de notificações têm um tipo especial de segue disparando-os. Quando você arrasta um novo **controlador de interface de notificação** para um storyboard, ele terá automaticamente um transição anexado:

![](notifications-images/notification-storyboard1.png "A new Notification Interface Controller with a segue attached")

Quando a notificação segue é selecionado. você pode editar suas propriedades:

![](notifications-images/notification-storyboard2.png "The notification segue selected")

Depois que você tiver personalizado o controlador, ele poderá parecer com este exemplo da WatchKitCatalog:

![](notifications-images/notifications-segue.png "The Notification Properties")

Há dois tipos de notificação:

- Exibição estática de **aparência curta** não rolável definida pelo sistema.

- Exibição de **aparência longa** e personalizável definida por você! Uma versão mais simples e estática e uma versão dinâmica mais complexa podem ser especificados.

### <a name="short-look-notification-controller"></a>Controlador de notificação de aparência curto

A interface do usuário de aparência curto consiste apenas o ícone do aplicativo, o nome do aplicativo e a cadeia de caracteres do título de notificação.

Se o usuário não ignorar a notificação, o sistema automaticamente alternará para uma notificação de aparência longa que fornece mais informações.

### <a name="long-look-notification-controller"></a>Controlador de notificação de análise longa

O sistema operacional decide se deve mostrar a exibição estática ou dinâmica com base em uma série de fatores. Você deve fornecer uma interface estática e pode, opcionalmente, também incluem uma interface dinâmica para notificações.

#### <a name="static"></a>Estático

O modo de exibição estático deve ser simples e rápida de exibir.

![](notifications-images/notification-static.png "The static view")

#### <a name="dynamic"></a>Dinâmico

O modo de exibição dinâmico pode exibir mais dados e fornecer mais interatividade.

![](notifications-images/notification-dynamic.png "The dynamic view")

## <a name="generating-notifications"></a>Gerar notificações

As notificações podem vir de um servidor remoto ou podem ser geradas localmente no aplicativo iOS.

Consulte a [explicação sobre notificações do IOS](~/ios/platform/user-notifications/deprecated/local-notifications-in-ios-walkthrough.md) para obter um exemplo de como gerar notificações locais.

As notificações locais devem ter os `AlertTitle` definidos para serem exibidos no Apple Watch-a cadeia de caracteres `AlertTitle` é exibida na interface de aparência curta. Os `AlertTitle` e `AlertBody` são exibidos na lista de notificações; e o `AlertBody` é exibido na interface de aparência longa.

Esta captura de tela mostra o `AlertTitle` que está sendo exibido na lista de notificações e o `AlertBody` exibido na interface de aparência longa:

![](notifications-images/watch-notificationslist-sml.png "Esta captura de tela mostra o AlertTitle que está sendo exibido na lista de notificações") ![](notifications-images/watch-notificationcontroller-sml.png "O AlertBody exibido na interface de aparência longa")

## <a name="testing-notifications"></a>Notificações de testes

As notificações (locais e remotas) só podem ser testadas corretamente em um dispositivo, no entanto, elas podem ser simuladas usando um arquivo **. JSON** no simulador de Ios.

### <a name="testing-on-apple-watch"></a>Um teste no Apple Watch

Ao testar as notificações em um Apple Watch, lembre-se de que a [documentação da Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html) afirma o seguinte:

> Quando uma das notificações de seu aplicativo local ou remoto chega no iPhone do usuário, o iOS decide se Exibir notificação no iPhone ou na Apple Watch.

Isso é alluding ao fato de se iOS decide se uma notificação será exibida no iPhone ou no relógio. Se o iPhone emparelhado estiver ativo quando uma notificação for recebida, a notificação provavelmente será exibida no iPhone e *não* será roteada para o relógio.

Para garantir que a notificação será exibida no watch, desative a tela de iPhone (uma vez pressionando o botão de energia) ou deixar que ele entra em suspensão. Se o Watch emparelhado está no intervalo, tem capacidade e estiver sendo usado em sua mão, a notificação será ser roteado para lá e aparecem no Watch (acompanhado por um sutis).

### <a name="testing-on-the-ios-simulator"></a>Teste no iOS Simulator

Você *deve* fornecer uma carga JSON de teste ao testar o modo de notificação no simulador de Ios. Defina o caminho na janela **argumentos de execução personalizada** em Visual Studio para Mac.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Visual Studio para Mac exibirá opções adicionais quando uma extensão de inspeção for definida como o **projeto de inicialização**.
Clique com o botão direito do mouse no projeto de extensão de inspeção e escolha **executar com > parâmetros personalizados...** :

[![](notifications-images/runwith-customparams-sml.png "Running with Custom Properties")](notifications-images/runwith-customparams.png#lightbox)

Isso abre a janela **argumentos de execução** que contém uma guia **WatchKit** . Selecione **notificação** e forneça uma carga JSON e, em seguida, pressione **executar** para iniciar o aplicativo Watch no simulador:

[![](notifications-images/runwith-execargs-sml.png "Select Notification Payload Default")](notifications-images/runwith-execargs.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para definir a carga de notificação de teste no Visual Studio, clique com o botão direito do mouse na extensão de inspeção para editar as **Propriedades do projeto**. Vá para a seção de **depuração** e selecione um arquivo JSON de notificações na lista (ele listará automaticamente todos os arquivos JSON incluídos no projeto).

[![](notifications-images/runwith-execargs-sml-vs.png "Select a notifications JSON file")](notifications-images/runwith-execargs-vs.png#lightbox)

Quando a extensão de inspeção for o **projeto de inicialização**, o Visual Studio exibirá opções adicionais, conforme mostrado abaixo. Escolha uma das opções de **notificação** para iniciar o aplicativo Watch no modo de **notificação** (usando o arquivo JSON selecionado na janela Propriedades):

![](notifications-images/runwith-vs.png "The Device menu")

-----

O controlador de notificação padrão é semelhante a quando os testes no simulador com o arquivo JSON de carga padrão:

![](notifications-images/notification-debug-sml.png "An example notification")

Também é possível usar a linha de [comando](~/ios/watchos/troubleshooting.md#command_line) para iniciar o simulador de Ios.

### <a name="example-notification-payload"></a>Exemplo de carga de notificação

No exemplo de [catálogo do kit de inspeção](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) , há um arquivo JSON de carga de exemplo **NotificationPayload. JSON** (listado abaixo).

```json
{
    "aps": {
        "alert": "Test message content",
        "title": "Optional title",
        "category": "myCategory"
        },

        "WatchKit Simulator Actions": [
        {
            "title": "First Button",
            "identifier": "firstButtonAction"
        }
        ],

        "customKey": "Use this file to define a testing payload for your notifications. The aps dictionary specifies the category, alert text and title. The WatchKit Simulator Actions array can provide info for one or more action buttons in addition to the standard Dismiss button. Any other top level keys are custom payload. If you have multiple such JSON files in your project, you'll be able to choose between them in when selecting to debug the notification interface of your Watch App."
    }
```

## <a name="related-links"></a>Links relacionados

- [WatchKitCatalog (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Documentos de notificações do kit de inspeção da Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)
