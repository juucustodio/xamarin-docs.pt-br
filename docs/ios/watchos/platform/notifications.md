---
title: Notificações do watchOS no Xamarin
description: Este documento descreve como trabalhar com notificações do watchOS no Xamarin. Ele aborda a criação de controladores de notificação, a geração de notificações e o teste de notificações.
ms.prod: xamarin
ms.assetid: 0BC1306E-0713-4592-996E-7530CCF281E7
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: c8275811cb61aaa1a033ca414e9abd4d20ecc873
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435426"
---
# <a name="watchos-notifications-in-xamarin"></a>Notificações do watchOS no Xamarin

Observe que os aplicativos podem receber notificações se o aplicativo iOS que o contém dá suporte a eles. Há uma manipulação de notificação interna, portanto, você não *precisa* adicionar o suporte de notificação adicional descrito abaixo. no entanto, se você quiser personalizar o comportamento de notificação e a aparência, continue lendo.

Consulte o documento de [notificações do IOS](~/ios/platform/user-notifications/deprecated/index.md) para obter mais informações sobre como adicionar suporte de notificação ao aplicativo Ios em sua solução.

## <a name="creating-notification-controllers"></a>Criando controladores de notificação

Nos controladores de notificações de storyboard, há um tipo especial de transição disparando-os. Quando você arrasta um novo **controlador de interface de notificação** para um storyboard, ele terá automaticamente um transição anexado:

![Um novo controlador de interface de notificação com um transição anexado](notifications-images/notification-storyboard1.png)

Quando a transição de notificação é selecionada, você pode editar suas propriedades:

![O transição de notificação selecionado](notifications-images/notification-storyboard2.png)

Depois de personalizar o controlador, ele pode ser semelhante a este exemplo do WatchKitCatalog:

![As propriedades de notificação](notifications-images/notifications-segue.png)

Há dois tipos de notificação:

- Exibição estática de **aparência curta** não rolável definida pelo sistema.

- Exibição de **aparência longa** e personalizável definida por você! Uma versão mais simples, estática e uma versão dinâmica mais complexa podem ser especificadas.

### <a name="short-look-notification-controller"></a>Controlador de notificação de aparência curta

A interface do usuário de aparência curta consiste apenas no ícone do aplicativo, no nome do aplicativo e na cadeia de caracteres do título da notificação.

Se o usuário não ignorar a notificação, o sistema alternará automaticamente para uma notificação de aparência longa que fornece mais informações.

### <a name="long-look-notification-controller"></a>Controlador de notificação de aparência longa

O sistema operacional decide se a exibição estática ou dinâmica deve ser exibida com base em vários fatores. Você deve fornecer uma interface estática e, opcionalmente, também pode incluir uma interface dinâmica para notificações.

#### <a name="static"></a>Estático

A exibição estática deve ser simples e rápida de exibir.

![A exibição estática](notifications-images/notification-static.png)

#### <a name="dynamic"></a>Dinâmico

O modo de exibição dinâmico pode exibir mais dados e fornecer mais interatividade.

![A exibição dinâmica](notifications-images/notification-dynamic.png)

## <a name="generating-notifications"></a>Gerando notificações

As notificações podem vir de um servidor remoto ou podem ser geradas localmente no aplicativo iOS.

Consulte a [explicação sobre notificações do IOS](~/ios/platform/user-notifications/deprecated/local-notifications-in-ios-walkthrough.md) para obter um exemplo de como gerar notificações locais.

As notificações locais devem ter o `AlertTitle` conjunto a ser exibido no Apple Watch-a `AlertTitle` cadeia de caracteres é exibida na interface de aparência curta. O `AlertTitle` e o `AlertBody` são exibidos na lista de notificações; e o `AlertBody` é exibido na interface de aparência longa.

Esta captura de tela mostra o `AlertTitle` que está sendo exibido na lista de notificações e o `AlertBody` exibido na interface de aparência longa:

![Esta captura de tela mostra o AlertTitle que está sendo exibido na lista de notificações](notifications-images/watch-notificationslist-sml.png) ![O AlertBody exibido na interface de aparência longa](notifications-images/watch-notificationcontroller-sml.png)

## <a name="testing-notifications"></a>Testando notificações

As notificações (locais e remotas) só podem ser testadas corretamente em um dispositivo, no entanto, elas podem ser simuladas usando um arquivo **. JSON** no simulador de Ios.

### <a name="testing-on-apple-watch"></a>Testando no Apple Watch

Ao testar as notificações em um Apple Watch, lembre-se de que a [documentação da Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html) afirma o seguinte:

> Quando uma das notificações locais ou remotas de seu aplicativo chega no iPhone do usuário, o iOS decide se a notificação será exibida no iPhone ou no Apple Watch.

Isso é alluding ao fato de que o iOS decide se uma notificação será exibida no iPhone ou no relógio. Se o iPhone emparelhado estiver ativo quando uma notificação for recebida, a notificação provavelmente será exibida no iPhone e *não* será roteada para o relógio.

Para garantir que a notificação apareça na inspeção, desative a tela do iPhone (pressionando o botão de energia uma vez) ou deixe-a entrar em suspensão. Se a inspeção emparelhada estiver no intervalo, tiver energia e estiver sendo gasta em seu pulso, a notificação será roteada para lá e aparecerá na inspeção (acompanhada de um sutil).

### <a name="testing-on-the-ios-simulator"></a>Teste no simulador de iOS

Você *deve* fornecer uma carga JSON de teste ao testar o modo de notificação no simulador de Ios. Defina o caminho na janela **argumentos de execução personalizada** em Visual Studio para Mac.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Visual Studio para Mac exibirá opções adicionais quando uma extensão de inspeção for definida como o **projeto de inicialização**.
Clique com o botão direito do mouse no projeto de extensão de inspeção e escolha **executar com > parâmetros personalizados...**:

[![Executando com propriedades personalizadas](notifications-images/runwith-customparams-sml.png)](notifications-images/runwith-customparams.png#lightbox)

Isso abre a janela **argumentos de execução** que contém uma guia **WatchKit** . Selecione **notificação** e forneça uma carga JSON e, em seguida, pressione **executar** para iniciar o aplicativo Watch no simulador:

[![Selecionar padrão de carga de notificação](notifications-images/runwith-execargs-sml.png)](notifications-images/runwith-execargs.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Para definir a carga de notificação de teste no Visual Studio, clique com o botão direito do mouse na extensão de inspeção para editar as **Propriedades do projeto**. Vá para a seção de **depuração** e selecione um arquivo JSON de notificações na lista (ele listará automaticamente todos os arquivos JSON incluídos no projeto).

[![Selecionar um arquivo JSON de notificações](notifications-images/runwith-execargs-sml-vs.png)](notifications-images/runwith-execargs-vs.png#lightbox)

Quando a extensão de inspeção for o **projeto de inicialização**, o Visual Studio exibirá opções adicionais, conforme mostrado abaixo. Escolha uma das opções de **notificação** para iniciar o aplicativo Watch no modo de **notificação** (usando o arquivo JSON selecionado na janela Propriedades):

![O menu do dispositivo](notifications-images/runwith-vs.png)

-----

O controlador de notificação padrão tem esta aparência ao testar o simulador com o arquivo JSON de carga padrão:

![Uma notificação de exemplo](notifications-images/notification-debug-sml.png)

Também é possível usar a linha de [comando](~/ios/watchos/troubleshooting.md#command_line) para iniciar o simulador de Ios.

### <a name="example-notification-payload"></a>Conteúdo de notificação de exemplo

No exemplo de [catálogo do kit de inspeção](/samples/xamarin/ios-samples/watchos-watchkitcatalog) , há um arquivo JSON de carga de exemplo **NotificationPayload.jsem** (listado abaixo).

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

## <a name="related-links"></a>Links Relacionados

- [WatchKitCatalog (exemplo)](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Documentos de notificações do kit de inspeção da Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)