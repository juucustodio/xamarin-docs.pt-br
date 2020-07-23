---
title: Demonstração do ciclo de vida do aplicativo para Xamarin. iOS
description: Este documento examina vários eventos de ciclo de vida manipulados pelo representante do aplicativo em um aplicativo iOS, demonstrando quando e como esses eventos são tratados.
ms.prod: xamarin
ms.assetid: 5C8AACA6-49F8-4C6D-99C3-5F443C01B230
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/17/2018
ms.openlocfilehash: bb3fd0623d0361a42c573cf2b2bcb8249d32181c
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86933156"
---
# <a name="application-lifecycle-demo-for-xamarinios"></a>Demonstração do ciclo de vida do aplicativo para Xamarin. iOS

Este artigo e [código de exemplo](https://docs.microsoft.com/samples/xamarin/ios-samples/lifecycledemo) demonstra os quatro Estados do aplicativo no Ios e a função dos `AppDelegate` métodos de notificação da aplicação de quando os Estados são alterados. O aplicativo imprimirá atualizações no console sempre que o aplicativo alterar o estado:

[![O aplicativo de exemplo](application-lifecycle-demo-images/image3-sml.png)](application-lifecycle-demo-images/image3.png#lightbox)

[![O aplicativo imprimirá atualizações no console sempre que o aplicativo mudar de estado](application-lifecycle-demo-images/image4.png)](application-lifecycle-demo-images/image4.png#lightbox)

## <a name="walkthrough"></a>Passo a passo

1. Abra o projeto de **ciclo de vida** na solução **LifecycleDemo** .
1. Abra a `AppDelegate` classe. O registro em log foi adicionado aos métodos do ciclo de vida para indicar quando o estado do aplicativo foi alterado:

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

1. Inicie o aplicativo no simulador ou no dispositivo. `OnActivated`será chamado quando o aplicativo for iniciado. O aplicativo agora está no estado _ativo_ .
1. Pressione o botão Início no simulador ou dispositivo para colocar o aplicativo em segundo plano. `OnResignActivation`e `DidEnterBackground` será chamado como o aplicativo faz a transição de `Active` para `Inactive` e para o `Backgrounded` estado. Como não há nenhum código de aplicativo definido para ser executado em segundo plano, o aplicativo é considerado _suspenso_ na memória.
1. Navegue de volta para o aplicativo para trazê-lo de volta para o primeiro plano. `WillEnterForeground`e `OnActivated` ambos serão chamados:

    ![Alterações de estado impressas no console](application-lifecycle-demo-images/image4.png)

    A linha de código a seguir no controlador de exibição é executada quando o aplicativo insere o primeiro plano do plano de fundo e altera o texto exibido na tela:

    ```csharp
    UIApplication.Notifications.ObserveWillEnterForeground ((sender, args) => {
        label.Text = "Welcome back!";
    });
    ```

1. Pressione o botão **página inicial** para colocar o aplicativo em segundo plano. Em seguida, toque duas vezes no botão **página inicial** para abrir o alternador de aplicativo. No iPhone X, passe o dedo para cima na parte inferior da tela:

    [![O alternador de aplicativo](application-lifecycle-demo-images/app-switcher-sml.png "O alternador de aplicativo")](application-lifecycle-demo-images/app-switcher.png#lightbox)
  
1. Localize o aplicativo no alternador de aplicativo e deslize para cima para removê-lo (no iOS 11, com uma prensa longa até que os ícones vermelhos apareçam no canto):

    [![Passe o dedo para cima para remover um aplicativo em execução](application-lifecycle-demo-images/app-switcher-swipe-sml.png "Passe o dedo para cima para remover um aplicativo em execução")](application-lifecycle-demo-images/app-switcher-swipe.png#lightbox)

o iOS encerrará o aplicativo. Observe que `WillTerminate` não é chamado porque o aplicativo já está _suspenso_ em segundo plano.

## <a name="related-links"></a>Links relacionados

- [LifecycleDemo (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/lifecycledemo)
