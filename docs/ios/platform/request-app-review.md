---
title: "Solicitação de revisão de aplicativo"
description: "Este artigo aborda o método RequestReview que Apple adicionado iOS 10 e como implementá-la em xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: 6408e707-b7dc-4557-b931-16a4d79b8930
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: 2e63f2c47bbcd6da0f0d5370ebfc231d19a10e7d
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="request-app-review"></a>Solicitação de revisão de aplicativo

_Este artigo aborda o método RequestReview que Apple adicionado iOS 10 e como implementá-la em xamarin._

Novo para o iOS 10.3, o `RequestReview()` método permite que um aplicativo iOS pedir ao usuário para classificar ou examiná-la. Quando este método é chamado em um aplicativo de envio que o usuário instalou da loja de aplicativos, o iOS 10 lidar com a classificação de inteira e rever o processo para o desenvolvedor. Porque esse processo é controlado pela política da loja de aplicativos, um alerta pode ou não pode ser exibido.

![](request-app-review-images/review01.png "Um alerta de solicitação de revisão de exemplo")

## <a name="requesting-a-rating-or-review"></a>Solicitando uma classificação ou revisão

Enquanto o `RequestReview()` método estático do `SKStoreReviewController` classe pode ser chamada a qualquer momento em que faz sentido na experiência do usuário, o processo de revisão é controlado e tratado pela política da loja de aplicativos. Como resultado, esse método pode ou não pode exibir um alerta e nunca deve ser chamado em resposta a uma ação do usuário, como ao tocar em um botão.

Por exemplo, um aplicativo pode solicitar uma revisão depois que ele foi iniciado um determinado número de vezes ou um jogo pode solicitar uma revisão após o player de um nível.

Para solicitações de uma revisão assim que um aplicativo xamarin concluir a inicialização, faça as seguintes alterações para o `AppDelegate.cs` arquivo:

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
> Chamando `RequestReview()` em um desenvolvimento insuficiente aplicativo sempre exibirá a classificação e examinar a caixa de diálogo para que ela possa ser testada. Isso não se aplica a aplicativos distribuídos por meio de TestFlight, em que a chamada do método será ignorada.

Quando o `RequestReview()` método é chamado em um aplicativo de envio que o usuário instalou da App Store, iOS 10 tratará todo o processo de revisão e de classificação para o desenvolvedor. Novamente, porque esse processo é controlado pela política da loja de aplicativos, um alerta pode ou não pode ser exibido.

## <a name="linking-to-an-app-store-product-page"></a>Vincular a uma página de produto da loja de aplicativos 

Além da nova `RequestReview` método, o desenvolvedor ainda pode fornecer um link direto com a página de produto do aplicativo na loja de aplicativos de dentro de um aplicativo. Anexando `action=write-review` ao final da URL da página de produto, uma página será aberta onde o usuário pode escrever uma revisão do aplicativo automaticamente. 

## <a name="summary"></a>Resumo

Este artigo abordou o método RequestReview que Apple adicionado iOS 10 e como implementá-la em xamarin.



## <a name="related-links"></a>Links relacionados

- [Exemplo de iOSTenThree](https://developer.xamarin.com/samples/ios/iOS10/iOSTenThree)
