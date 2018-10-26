---
title: Solicitação de revisão de aplicativo no xamarin. IOS
description: Este artigo descreve o método RequestReview que a Apple adicionada ao iOS 10 e discute como implementá-la no xamarin. IOS.
ms.prod: xamarin
ms.assetid: 6408e707-b7dc-4557-b931-16a4d79b8930
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: f72aaa781b0712e206cf02725cfc434594287f41
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109777"
---
# <a name="request-app-review-in-xamarinios"></a>Solicitação de revisão de aplicativo no xamarin. IOS

_Este artigo aborda o método RequestReview que a Apple adicionada ao iOS 10 e como implementá-la no xamarin. IOS._

Novo para o iOS 10.3, o `RequestReview()` método permite que um aplicativo iOS solicitar que o usuário avalie ou revisá-lo. Quando este método é chamado em um aplicativo de envio que o usuário instalado da Store do aplicativo, o iOS 10 lidar com a classificação de inteira e processo de revisão para o desenvolvedor. Porque esse processo é controlado por política da App Store, um alerta pode ou não pode ser exibido.

![](request-app-review-images/review01.png "Um alerta de solicitação de revisão de exemplo")

## <a name="requesting-a-rating-or-review"></a>Solicitando uma classificação ou revisão

Enquanto o `RequestReview()` método estático do `SKStoreReviewController` classe pode ser chamado em qualquer ponto em que faz sentido na experiência do usuário, o processo de revisão é controlado e manipulado pela política da App Store. Como resultado, esse método pode ou não pode exibir um alerta e nunca deve ser chamado em resposta a uma ação do usuário, como tocar em um botão.

Por exemplo, um aplicativo pode solicitar uma análise após ela ter sido iniciado de um determinado número de vezes ou um jogo pode solicitar uma análise depois que o jogador termina um nível.

Para solicitações de uma revisão, assim que um aplicativo xamarin. IOS concluir a inicialização, faça as seguintes alterações para o `AppDelegate.cs` arquivo:

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
> Chamar `RequestReview()` em um desenvolvimento corrigiremos aplicativo sempre exibirá a classificação e examine a caixa de diálogo para que ele pode ser testado. Isso não se aplica a aplicativos que foram distribuídos por meio de TestFlight, em que a chamada de método será ignorada.

Quando o `RequestReview()` método é chamado em um aplicativo de envio que o usuário instalou da App Store, iOS 10 tratará todo o processo de revisão e de classificação para o desenvolvedor. Novamente, porque esse processo é controlado por política da App Store, um alerta pode ou não pode ser exibido.

## <a name="linking-to-an-app-store-product-page"></a>Vinculando a uma página de produto da App Store 

Além da nova `RequestReview` método, o desenvolvedor ainda pode fornecer um link profundo a página de produto do aplicativo na Store do aplicativo de dentro de um aplicativo. Acrescentando `action=write-review` ao final da URL da página de produto, uma página será aberta em que o usuário pode escrever uma revisão do aplicativo automaticamente. 

## <a name="summary"></a>Resumo

Este artigo abordou o método RequestReview que a Apple adicionada ao iOS 10 e como implementá-la no xamarin. IOS.



## <a name="related-links"></a>Links relacionados

- [Exemplo de iOSTenThree](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
