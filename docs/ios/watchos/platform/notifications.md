---
title: watchOS notificações no Xamarin
description: Este documento descreve como trabalhar com as notificações de watchOS no Xamarin. Ele aborda a criação controladores de notificação, gerar notificações e notificações de teste.
ms.prod: xamarin
ms.assetid: 0BC1306E-0713-4592-996E-7530CCF281E7
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 9ca50171e43ef98e5b4e5fbd7bd236f74d35da8f
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286771"
---
# <a name="watchos-notifications-in-xamarin"></a>watchOS notificações no Xamarin

Aplicativos do Watch podem receber notificações se o aplicativo iOS contém ofereça suporte a eles. Não há manipulação de notificação internos para que você não fizer isso *precisa* para adicionar o suporte de notificação adicionais descrito abaixo, no entanto se você deseja personalizar o comportamento de notificação e aparência, em seguida, continue lendo.

Consulte a [notificações iOS](~/ios/platform/user-notifications/deprecated/index.md) doc para obter mais informações sobre como adicionar suporte à notificação no aplicativo iOS na sua solução.

## <a name="creating-notification-controllers"></a>Criação de controladores de notificação

No storyboard a controladores de notificações têm um tipo especial de segue disparando-os. Quando você arrasta uma nova **controlador de Interface de notificação** em um storyboard automaticamente terá um segue anexado:

![](notifications-images/notification-storyboard1.png "Um novo controlador de Interface de notificação com um segue anexado")

Quando a notificação segue é selecionado. você pode editar suas propriedades:

![](notifications-images/notification-storyboard2.png "A notificação segue selecionado")

Depois que você tiver personalizado o controlador, ele poderá parecer com este exemplo da WatchKitCatalog:

![](notifications-images/notifications-segue.png "As propriedades de notificação")


Há dois tipos de notificação:

- Exibição estática de **aparência curta** não rolável definida pelo sistema.

- **A consulta longa** - rolável, visão personalizada definida por você! Uma versão mais simples e estática e uma versão dinâmica mais complexa podem ser especificados.

### <a name="short-look-notification-controller"></a>Controlador de notificação de aparência curto

A interface do usuário de aparência curto consiste apenas o ícone do aplicativo, o nome do aplicativo e a cadeia de caracteres do título de notificação.

Se o usuário não ignorar a notificação, o sistema automaticamente alternará para uma notificação de aparência longa que fornece mais informações.


### <a name="long-look-notification-controller"></a>Controlador de notificação de análise longa

O sistema operacional decide se deve mostrar a exibição estática ou dinâmica com base em uma série de fatores. Você deve fornecer uma interface estática e pode, opcionalmente, também incluem uma interface dinâmica para notificações.

#### <a name="static"></a>Estático

O modo de exibição estático deve ser simples e rápida de exibir.

![](notifications-images/notification-static.png "O modo de exibição estático")

#### <a name="dynamic"></a>Dinâmico

O modo de exibição dinâmico pode exibir mais dados e fornecer mais interatividade.

![](notifications-images/notification-dynamic.png "O modo de exibição dinâmico")


## <a name="generating-notifications"></a>Gerar notificações

As notificações podem vir de um servidor remoto ([serviço de notificações por Push da Apple](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), ou APNS) ou podem ser gerados localmente no aplicativo do iOS.

Consulte a [instruções passo a passo de notificações do iOS](~/ios/platform/user-notifications/deprecated/local-notifications-in-ios-walkthrough.md) para obter um exemplo de como gerar notificações locais e o [WatchNotifications exemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-watchnotifications) para obter um exemplo de trabalho.

Notificações locais devem ter o `AlertTitle` definido para ser exibido sobre o Apple Watch - o `AlertTitle` cadeia de caracteres é exibida na interface do curto aparência. Tanto a `AlertTitle` e `AlertBody` são exibidos na lista de notificações; e o `AlertBody` é exibido na interface de análise longa.

Nesta captura de tela mostra a `AlertTitle` que está sendo exibido na lista de notificações e o `AlertBody` exibido na interface de aparência longa (usando o [código de exemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-watchnotifications)):

![](notifications-images/watch-notificationslist-sml.png "Esta captura de tela mostra o que está sendo exibido na lista de notificações de AlertTitle") ![](notifications-images/watch-notificationcontroller-sml.png "AlertBody o exibido na interface de análise longa")

## <a name="testing-notifications"></a>Notificações de testes

Notificações (locais e remotas) podem ser adequadamente testadas somente em um dispositivo, no entanto, eles podem ser simulados usando um **. JSON** arquivo no simulador de iOS.

### <a name="testing-on-apple-watch"></a>Um teste no Apple Watch

Ao testar as notificações em um Apple Watch, lembre-se de que [documentação da Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html) afirma o seguinte:

> Quando uma das notificações de seu aplicativo local ou remoto chega no iPhone do usuário, o iOS decide se Exibir notificação no iPhone ou na Apple Watch.

Isso é alluding ao fato de se iOS decide se uma notificação será exibida no iPhone ou no relógio. Se o iPhone emparelhado estiver ativo quando uma notificação é recebida, a notificação é provavelmente a ser exibido no iPhone e *não* roteadas para o relógio.

Para garantir que a notificação será exibida no watch, desative a tela de iPhone (uma vez pressionando o botão de energia) ou deixar que ele entra em suspensão. Se o Watch emparelhado está no intervalo, tem capacidade e estiver sendo usado em sua mão, a notificação será ser roteado para lá e aparecem no Watch (acompanhado por um sutis).

### <a name="testing-on-the-ios-simulator"></a>Teste no iOS Simulator

Você *deve* fornecem uma carga JSON de teste quando o modo de notificação de teste no simulador de iOS. Defina o caminho na **argumentos de execução personalizado** janela no Visual Studio para Mac.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

O Visual Studio para Mac exibirá as opções adicionais quando uma extensão de inspeção é definida como o **projeto de inicialização**.
Clique com botão direito no projeto de extensão de inspeção e escolha **executar com > parâmetros personalizados...** :

[![](notifications-images/runwith-customparams-sml.png "Executando com propriedades personalizadas")](notifications-images/runwith-customparams.png#lightbox)

Isso abre o **argumentos de execução** janela que contém uma **WatchKit** guia. Selecione **notificação** e forneça uma carga JSON, em seguida, pressione **Execute** para iniciar o aplicativo watch no simulador:

[![](notifications-images/runwith-execargs-sml.png "Selecione o padrão de carga de notificação")](notifications-images/runwith-execargs.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para definir a carga de notificação de teste no atalho do Visual Studio na extensão de inspeção para editar a **propriedades do projeto**. Vá para o **depurar** seção e selecione um arquivo JSON de notificações na lista (ele listará automaticamente todos os arquivos JSON incluídos no projeto).

[![](notifications-images/runwith-execargs-sml-vs.png "Selecione um arquivo JSON de notificações")](notifications-images/runwith-execargs-vs.png#lightbox)

Quando a extensão de inspeção de variáveis é o **projeto de inicialização**, Visual Studio exibirá as opções adicionais, conforme mostrado abaixo. Escolha uma da **notificação** opções para iniciar o aplicativo watch no **notificação** modo (usando o arquivo JSON selecionado na janela Propriedades):

![](notifications-images/runwith-vs.png "Menu do dispositivo")

-----

O controlador de notificação padrão é semelhante a quando os testes no simulador com o arquivo JSON de carga padrão:

![](notifications-images/notification-debug-sml.png "Um exemplo de notificação")

Também é possível usar o [linha de comando](~/ios/watchos/troubleshooting.md#command_line) para iniciar o simulador de iOS.

### <a name="example-notification-payload"></a>Exemplo de carga de notificação

No [catálogo do Kit de inspeção](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) existe amostra é um exemplo de arquivo JSON de carga **NotificationPayload.json** (listados abaixo).

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

- [WatchNotifications (notificações locais) (amostra)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-watchnotifications)
- [WatchKitCatalog (amostra)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Documentos de notificações do Kit de inspeção da Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)
