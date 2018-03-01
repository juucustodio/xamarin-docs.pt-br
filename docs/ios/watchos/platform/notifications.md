---
title: "Notificações"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0BC1306E-0713-4592-996E-7530CCF281E7
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 0f1d968dcee0cb9b6cd0cee8fa60be4f4dbb2833
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="notifications"></a>Notificações

Assista aos aplicativos podem receber notificações se o aplicativo do iOS contendo oferece suporte a eles. Há notificação internos tratamento; portanto, você não *necessário* para adicionar o suporte de notificação adicional descrito abaixo, porém se você deseja personalizar o comportamento de notificação e aparência, em seguida, continue lendo.

Consulte o [iOS notificações](~/ios/platform/user-notifications/deprecated/index.md) documento para obter mais informações sobre como adicionar suporte de notificação para o aplicativo do iOS na sua solução.

## <a name="creating-notification-controllers"></a>Criação de controladores de notificação

No storyboard a controladores de notificações têm um tipo especial de atender o disparo-los. Quando você arrasta um novo **controlador de Interface de notificação** para um storyboard automaticamente terá um segue anexado:

![](notifications-images/notification-storyboard1.png "Um novo controlador de Interface de notificação com um segue anexado")

Quando a notificação de atender é selecionado. você pode editar suas propriedades:

![](notifications-images/notification-storyboard2.png "A notificação atender selecionado")

Depois que você tiver personalizado o controlador ele pode se parecer com este exemplo da WatchKitCatalog:

![](notifications-images/notifications-segue.png "As propriedades de notificação")


Há dois tipos de notificação:

- **Aparência curto** -não rolável exibição estática definida pelo sistema.

- **A consulta longa** - rolável, personalizável exibição definida por você! Uma versão mais simples, estática e uma versão dinâmica mais complexa podem ser especificados.

### <a name="short-look-notification-controller"></a>Controlador de notificação de consulta curto

A interface do usuário curto aparência consiste apenas o ícone de aplicativo, nome do aplicativo e a cadeia de caracteres do título de notificação.

Se o usuário não ignorar a notificação, o sistema alternará automaticamente em uma notificação de consulta longa que fornece mais informações.


### <a name="long-look-notification-controller"></a>Controlador de notificação de consulta longa

O sistema operacional decide se deve mostrar a exibição estática ou dinâmica com base em uma série de fatores. Você deve fornecer uma interface estática e pode, opcionalmente, também incluem uma interface dinâmica para notificações.

#### <a name="static"></a>Estático

O modo de exibição estático deve ser simples e rápido exibir.

![](notifications-images/notification-static.png "O modo de exibição estático")

#### <a name="dynamic"></a>Dinâmico

O modo de exibição dinâmico pode exibir mais dados e fornecer interatividade mais.

![](notifications-images/notification-dynamic.png "O modo de exibição dinâmico")


## <a name="generating-notifications"></a>Geração de notificações

As notificações podem vir de um servidor remoto ([serviço de notificações por Push da Apple](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html), ou APNS) ou podem ser gerados localmente no aplicativo do iOS.

Consulte o [iOS passo a passo de notificações](~/ios/platform/user-notifications/deprecated/local-notifications-in-ios-walkthrough.md) para obter um exemplo de como gerar notificações locais e o [WatchNotifications exemplo](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchNotifications/) para obter um exemplo de funcionamento.

Notificações locais devem ter o `AlertTitle` definido para ser exibido sobre o Apple Watch - o `AlertTitle` cadeia de caracteres é exibida na interface do aparência curto. Tanto o `AlertTitle` e `AlertBody` são exibidos na lista de notificações; e o `AlertBody` é exibido na interface de consulta longa.

Esta captura de tela mostra a `AlertTitle` que está sendo exibido na lista de notificações e o `AlertBody` exibido na interface de consulta longa (usando o [código de exemplo](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchNotifications/)):

![](notifications-images/watch-notificationslist-sml.png "Esta captura de tela mostra o que está sendo exibido na lista de notificações de AlertTitle") ![ ] (notifications-images/watch-notificationcontroller-sml.png "AlertBody o exibido na interface de consulta longa")

## <a name="testing-notifications"></a>Notificações de teste

Notificações (locais e remotas) podem ser adequadamente testadas somente em um dispositivo, no entanto, eles podem ser simulados usando um **. JSON** arquivo no simulador iOS.

### <a name="testing-on-apple-watch"></a>Teste no Apple Watch

Ao testar as notificações em um Apple Watch, lembre-se de que [documentação da Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html) estados a seguir:

> Quando uma das notificações de local ou remoto do seu aplicativo chega no iPhone do usuário, o iOS decide se deseja exibir a notificação no iPhone ou sobre o Apple Watch.

Isso é alluding ao fato de que iOS decide se uma notificação será exibida no iPhone ou no relógio. Se o iPhone par está ativo quando a notificação é recebida, a notificação é provável a ser exibida no iPhone e *não* roteado para inspeção.

Para garantir que a notificação é exibida no relógio, desative a tela de iPhone (uma vez pressionando o botão de energia) ou permitir que ele entrar em suspensão. Se emparelhados inspeção estiver no intervalo, energia e está sendo gasto em seu pulso, a notificação será roteada existe e aparecem no relógio (acompanhado por um sutis).

### <a name="testing-on-the-ios-simulator"></a>O simulador de iOS de teste

Você *deve* fornecer uma carga JSON de teste ao modo de notificação de teste no simulador iOS. Defina o caminho no **argumentos de execução personalizado** janela no Visual Studio para Mac.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

O Visual Studio para Mac exibirá as opções adicionais quando uma extensão de inspeção é definida como o **projeto de inicialização**.
Clique com botão direito no projeto de extensão inspeção e escolha **executar com > parâmetros personalizados...** :
    
[![](notifications-images/runwith-customparams-sml.png "Executando com propriedades personalizadas")](notifications-images/runwith-customparams.png)
    
Isso abre o **argumentos de execução** janela que contém um **WatchKit** guia. Selecione **notificação** e fornecer uma carga JSON, em seguida, pressione **Execute** para iniciar o aplicativo watch no simulador:
    
[![](notifications-images/runwith-execargs-sml.png "Selecione o padrão de carga de notificação")](notifications-images/runwith-execargs.png)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Para definir a carga de notificação de teste no atalho do Visual Studio na extensão de inspeção para editar o **propriedades do projeto**. Vá para o **depurar** seção e selecione um arquivo JSON de notificações na lista (ela listará automaticamente todos os arquivos JSON incluídos no projeto).
    
[![](notifications-images/runwith-execargs-sml-vs.png "Selecione um arquivo JSON de notificações")](notifications-images/runwith-execargs-vs.png)

Quando é a extensão de inspecionar o **projeto de inicialização**, Visual Studio exibirá as opções adicionais, conforme mostrado abaixo. Escolha uma da **notificação** opções para iniciar o aplicativo de inspeção **notificação** modo (usando o arquivo JSON selecionado na janela de propriedades):
    
![](notifications-images/runwith-vs.png "O menu de dispositivo")

-----

O controlador de notificação padrão é semelhante a testar no simulador com o arquivo JSON de carga padrão:

![](notifications-images/notification-debug-sml.png "Uma notificação de exemplo")

Também é possível usar o [linha de comando](~/ios/watchos/troubleshooting.md#command_line) para iniciar o simulador de iOS.

### <a name="example-notification-payload"></a>Carga de notificação de exemplo

No [inspecionar Kit catálogo](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/) existe exemplo é um exemplo de arquivo carga JSON **NotificationPayload.json** (listados abaixo).

```csharp
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

- [WatchNotifications (notificações locais) (amostra)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchNotifications/)
- [WatchKitCatalog (exemplo)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [Documentos de notificações do Kit de inspeção da Apple](https://developer.apple.com/library/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/BasicSupport.html)
