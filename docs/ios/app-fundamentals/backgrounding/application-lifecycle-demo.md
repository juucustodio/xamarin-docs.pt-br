---
title: Demonstração do ciclo de vida do aplicativo para xamarin. IOS
description: Este documento examina vários eventos de ciclo de vida tratados pelo representante do aplicativo em um aplicativo iOS, demonstrando a quando e como esses eventos são manipulados.
ms.prod: xamarin
ms.assetid: 5C8AACA6-49F8-4C6D-99C3-5F443C01B230
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/17/2018
ms.openlocfilehash: 3beb511c03b328ecea824bf89355d056df003f3e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102692"
---
# <a name="application-lifecycle-demo-for-xamarinios"></a>Demonstração do ciclo de vida do aplicativo para xamarin. IOS

Este artigo e [código de exemplo](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/) demonstra os estados de quatro aplicativos no iOS e a função do `AppDelegate` métodos em notificando-o de quando os estados sejam alterados. O aplicativo imprimirá atualizações no console sempre que o aplicativo mudar de estado:

[![](application-lifecycle-demo-images/image3-sml.png "O aplicativo de exemplo")](application-lifecycle-demo-images/image3.png#lightbox)

[![](application-lifecycle-demo-images/image4.png "O aplicativo imprimirá atualizações no console sempre que o aplicativo mudar de estado")](application-lifecycle-demo-images/image4.png#lightbox)

## <a name="walkthrough"></a>Passo a passo

1. Abra o **ciclo de vida** do projeto na **LifecycleDemo** solução.
1. Abra o `AppDelegate` classe. Registro em log foi adicionado para os métodos de ciclo de vida para indicar quando o aplicativo foi alterado o estado:

    ```csharp
    public override void OnActivated(UIApplication application)
    {
        Console.WriteLine("OnActivated called, App is active.");
    }
    public override void WillEnterForeground(UIApplication application)
    {
        Console.WriteLine("App will enter foreground");
    }
    public override void OnResignActivation(UIApplication application)
    {
        Console.WriteLine("OnResignActivation called, App moving to inactive state.");
    }
    public override void DidEnterBackground(UIApplication application)
    {
        Console.WriteLine("App entering background state.");
    }
    // not guaranteed that this will run
    public override void WillTerminate(UIApplication application)
    {
        Console.WriteLine("App is terminating.");
    }
    ```

1. Inicie o aplicativo no simulador ou no dispositivo. `OnActivated` será chamado quando o aplicativo for iniciado. O aplicativo está agora na _Active_ estado.
1. Pressione o botão Home no simulador ou dispositivo para trazer o aplicativo para o plano de fundo. `OnResignActivation` e `DidEnterBackground` será chamado como as transições de aplicativo do `Active` à `Inactive` para o `Backgrounded` estado. Como não há nenhum conjunto de código do aplicativo para executar em segundo plano, o aplicativo é considerado _suspenso_ na memória.
1. Navegue de volta para o aplicativo para colocá-los de volta em primeiro plano. `WillEnterForeground` e `OnActivated` serão ambos chamadas:

    ![](application-lifecycle-demo-images/image4.png "Alterações de estado impresso no console")

    A seguinte linha de código no controlador de exibição é executada quando o aplicativo entrou em primeiro plano do plano de fundo e altera o texto exibido na tela:

    ```csharp
    UIApplication.Notifications.ObserveWillEnterForeground ((sender, args) => {
        label.Text = "Welcome back!";
    });
    ```

1. Pressione a **Home** botão para colocar o aplicativo em segundo plano. Em seguida, com um toque duplo a **Home** botão para abrir o seletor de aplicativo. No iPhone X, passe o dedo para cima na parte inferior da tela:

    [![O seletor de aplicativo](application-lifecycle-demo-images/app-switcher-sml.png "o alternador de aplicativo")](application-lifecycle-demo-images/app-switcher.png#lightbox)
  
1. Localize o aplicativo no alternador de aplicativo e passe o dedo para cima para removê-lo (no iOS pressionamento longo 11, até que os ícones vermelhos aparecem no canto):

    [![Passe o dedo para remover um aplicativo em execução](application-lifecycle-demo-images/app-switcher-swipe-sml.png "passar o dedo para remover um aplicativo em execução")](application-lifecycle-demo-images/app-switcher-swipe.png#lightbox)

encerrar o aplicativo iOS. Observe que `WillTerminate` não é chamado porque o aplicativo já _suspenso_ em segundo plano.

## <a name="related-links"></a>Links relacionados

- [LifecycleDemo (amostra)](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
