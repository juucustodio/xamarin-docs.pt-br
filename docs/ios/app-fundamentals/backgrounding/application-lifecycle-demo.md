---
title: Demonstração de ciclo de vida do aplicativo para xamarin
description: Este documento examina vários eventos de ciclo de vida manipulados pelo aplicativo delegado em um aplicativo do iOS, demonstrando quando e como esses eventos são tratados.
ms.prod: xamarin
ms.assetid: 5C8AACA6-49F8-4C6D-99C3-5F443C01B230
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 64c695065012e4bf796c219c260324d9b6278ca5
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34783578"
---
# <a name="application-lifecycle-demo-for-xamarinios"></a>Demonstração de ciclo de vida do aplicativo para xamarin

Nesta seção, vamos examinar um aplicativo que demonstra os quatro estados de aplicativo e a função do `AppDelegate` métodos notificar o aplicativo de quando estados sejam alterados. O aplicativo imprimirá atualizações no console, sempre que o aplicativo muda de estado:

 [![](application-lifecycle-demo-images/image3.png "O aplicativo de exemplo")](application-lifecycle-demo-images/image3.png#lightbox)

 [![](application-lifecycle-demo-images/image4.png "O aplicativo imprimirá atualizações no console sempre que o aplicativo muda de estado")](application-lifecycle-demo-images/image4.png#lightbox)

## <a name="walkthrough"></a>Passo a passo


  1. Abra o _ciclo de vida_ project no _LifecycleDemo_ solução.
  1. Abra o `AppDelegate` classe. Observe que, adicionamos o registro em log para os métodos de ciclo de vida para nos informar quando o aplicativo foi alterado o estado:

            ```chsarp
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

  1. Inicie o aplicativo no simulador ou no dispositivo. `OnActivated` será chamado quando o aplicativo é iniciado. O aplicativo está agora no _Active_ estado.
  1. Clique no botão página inicial no simulador ou dispositivo para que o aplicativo para o plano de fundo. `OnResignActivation` e `DidEnterBackground` será chamada como as transições de aplicativo de `Active` para `Inactive` para o `Backgrounded` estado. Como podemos não têm nosso aplicativo qualquer código a ser executado em segundo plano, o aplicativo é considerado _suspenso_ na memória.
  1. Navegue de volta para o aplicativo para trazer de volta em primeiro plano. `WillEnterForeground` e `OnActivated` ambos ser chamadas:

        ![](application-lifecycle-demo-images/image4.png "Alterações de estado impresso no console")

    Observe que nós adicionamos a seguinte linha de código para o nosso controlador exibição notificando que o aplicativo entrou em primeiro plano do plano de fundo:

        ```csharp
            UIApplication.Notifications.ObserveWillEnterForeground ((sender, args) => {
                    label.Text = "Welcome back!";
                });
        ```

1. Pressione a **início** botão para colocar o aplicativo em segundo plano. Em seguida, toque duas vezes o **início** botão para abrir o seletor de exibição do aplicativo:
    
    ![](application-lifecycle-demo-images/app-switcher-.png "O seletor de exibição do aplicativo")
  
1. Localize o aplicativo no seletor de exibição do aplicativo e passa o dedo para cima para removê-lo:
    
    ![](application-lifecycle-demo-images/app-switcher-swipe-.png "Passe o dedo para remover um aplicativo em execução") 
    
encerrar o aplicativo iOS. Tenha em mente que `WillTerminate` não é chamado porque estamos está encerrando um aplicativo que é _suspenso_ em segundo plano.

Agora que sabemos iOS aplicativo estados e transições, vamos dar uma olhada as diferentes opções disponíveis para backgrounding no iOS.



## <a name="related-links"></a>Links relacionados

- [LifecycleDemo(Part2) (exemplo)](https://developer.xamarin.com/samples/monotouch/LifecycleDemo/)
