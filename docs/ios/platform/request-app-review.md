---
title: Solicitar revisão do aplicativo no Xamarin. iOS
description: Este artigo descreve o método RequestReview que a Apple adicionou ao iOS 10 e discute como implementá-lo no Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 6408e707-b7dc-4557-b931-16a4d79b8930
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 146dc66974f6e0c6e6a8f7bf9f46b570025eaead
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70280383"
---
# <a name="request-app-review-in-xamarinios"></a>Solicitar revisão do aplicativo no Xamarin. iOS

_Este artigo aborda o método RequestReview que a Apple adicionou ao iOS 10 e como implementá-lo no Xamarin. iOS._

Novo no Ios 10,3, o `RequestReview()` método permite que um aplicativo IOS peça ao usuário para classificá-lo ou examiná-lo. Quando esse método é chamado em um aplicativo de envio que o usuário instalou da loja de aplicativos, o iOS 10 manipulará todo o processo de classificação e revisão para o desenvolvedor. Como esse processo é regido pela política da loja de aplicativos, um alerta pode ou não ser exibido.

![](request-app-review-images/review01.png "Um alerta de solicitação de revisão de exemplo")

## <a name="requesting-a-rating-or-review"></a>Solicitando uma classificação ou revisão

Embora o `RequestReview()` método estático `SKStoreReviewController` da classe possa ser chamado em qualquer ponto em que faça sentido na experiência do usuário, o processo de revisão é regido e manipulado pela política da loja de aplicativos. Como resultado, esse método pode ou não exibir um alerta e nunca deve ser chamado em resposta a uma ação do usuário, como tocar em um botão.

Por exemplo, um aplicativo pode solicitar uma revisão após ter sido iniciado um determinado número de vezes ou um jogo pode solicitar uma revisão depois que o Player termina um nível.

Para solicitar uma revisão assim que um aplicativo Xamarin. Ios concluir a inicialização, faça as seguintes alterações `AppDelegate.cs` no arquivo:

```csharp
using Foundation;
using StoreKit;
using UIKit;

namespace iOSTenThree
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        ...

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Request a review from the user
            SKStoreReviewController.RequestReview ();

            return true;
        }

        ...

    }
}
```

> [!NOTE]
> A `RequestReview()` chamada em um aplicativo em desenvolvimento sempre exibirá a caixa de diálogo classificação e revisão para que possa ser testada. Isso não se aplica a aplicativos que foram distribuídos por meio de TestFlight, onde a chamada do método será ignorada.

Quando o `RequestReview()` método é chamado em um aplicativo de envio que o usuário instalou da loja de aplicativos, o Ios 10 manipulará todo o processo de classificação e revisão para o desenvolvedor. Novamente, como esse processo é regido pela política da loja de aplicativos, um alerta pode ou não ser exibido.

## <a name="linking-to-an-app-store-product-page"></a>Vinculando a uma página de produto da loja de aplicativos 

Além do novo `RequestReview` método, o desenvolvedor ainda pode fornecer um link profundo para a página de produto do aplicativo na loja de aplicativos de dentro de um aplicativo. Ao acrescentar `action=write-review` ao final da URL da página do produto, uma página será aberta onde o usuário possa escrever uma revisão do aplicativo automaticamente. 

## <a name="summary"></a>Resumo

Este artigo abordou o método RequestReview que a Apple adicionou ao iOS 10 e como implementá-lo no Xamarin. iOS.



## <a name="related-links"></a>Links relacionados

- [Exemplo de iOSTenThree](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-iostenthree/)
