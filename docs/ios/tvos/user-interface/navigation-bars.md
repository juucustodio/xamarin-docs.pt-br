---
title: Trabalhando com barras de navegação do tvOS no Xamarin
description: Este documento descreve como trabalhar com barras de navegação em um aplicativo tvOS criado com o Xamarin. Ele aborda a configuração de barras de navegação em um storyboard e a resposta a eventos desses botões.
ms.prod: xamarin
ms.assetid: 74E396B7-87F0-46F7-BC6C-827DB8884C97
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 0f3c91e175e5ccdefeaf3d6c9c83e9eb3e012e14
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86935389"
---
# <a name="working-with-tvos-navigation-bars-in-xamarin"></a>Trabalhando com barras de navegação do tvOS no Xamarin

As barras de navegação podem ser adicionadas à parte superior das exibições para exibir um título e botões de barra de navegação opcionais. Normalmente, eles são usados quando o usuário navegou de uma página principal, como uma exibição de tabela, uma coleção ou um menu para uma subexibição mostrando os detalhes do item selecionado.

[![Barra de navegação de exemplo](navigation-bars-images/navbar01.png)](navigation-bars-images/navbar01.png#lightbox)

Além do título (exibido no centro), as barras de navegação podem conter um ou mais botões da barra de navegação ( `UIBarButtonItem` ) nos lados esquerdo e direito da barra.

> [!IMPORTANT]
> As barras de navegação são totalmente transparentes por padrão. Deve-se ter cuidado para garantir que o conteúdo da barra de navegação permaneça legível sobre o conteúdo abaixo dele. Por exemplo, quando o conteúdo em uma exibição de tabela ou coleção rola sobre ele.

<a name="Navigation-Bars-and-Storyboards"></a>

## <a name="navigation-bars-and-storyboards"></a>Barras de navegação e storyboards

A maneira mais fácil de trabalhar com barras de navegação em um aplicativo Xamarin. tvOS é adicioná-las à interface do usuário do aplicativo usando o designer do iOS.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Na **painel de soluções**, clique duas vezes no `Main.storyboard` arquivo e abra-o para edição.
1. Arraste uma **barra de navegação** da **caixa de ferramentas** e solte-a na exibição na parte superior da tela:

    [![Uma barra de navegação](navigation-bars-images/navbar02.png)](navigation-bars-images/navbar02.png#lightbox)
1. Clique duas vezes na **barra de navegação** para selecionar o **item de navegação**. Na guia **widget** da **painel de propriedades**, você pode definir o **título**:

    [![Definir o título](navigation-bars-images/navbar03.png)](navigation-bars-images/navbar03.png#lightbox)
1. Em seguida, você pode adicionar um ou mais **itens de botão de barra** para qualquer fim da barra:

    [![Um item de botão de barra](navigation-bars-images/navbar04.png)](navigation-bars-images/navbar04.png#lightbox)
1. Por fim, conecte os **itens do botão de barra** a ações na guia **eventos** do **Gerenciador de propriedades**:

    [![Uma ação de item de botão de barra](navigation-bars-images/navbar05.png)](navigation-bars-images/navbar05.png#lightbox)
1. Salve as alterações.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Na **Gerenciador de soluções**, clique duas vezes no `Main.storyboard` arquivo e abra-o para edição.
1. Arraste uma **barra de navegação** da **caixa de ferramentas** e solte-a na exibição na parte superior da tela:

    [![Uma barra de navegação](navigation-bars-images/navbar02-vs.png)](navigation-bars-images/navbar02-vs.png#lightbox)
1. Clique duas vezes na **barra de navegação** para selecionar o **item de navegação**. Na guia **widget** do Gerenciador de **Propriedades**, você pode definir o **título**:

    [![Definir o título](navigation-bars-images/navbar03-vs.png)](navigation-bars-images/navbar03-vs.png#lightbox)
1. Em seguida, você pode adicionar um ou mais **itens de botão de barra** para qualquer fim da barra:

    [![Itens de botão de barra](navigation-bars-images/navbar04-vs.png)](navigation-bars-images/navbar04-vs.png#lightbox)
1. Por fim, conecte os **itens do botão de barra** a ações na guia **eventos** do **Gerenciador de propriedades**:

    [![Ações de item de botão de barra](navigation-bars-images/navbar05-vs.png)](navigation-bars-images/navbar05-vs.png#lightbox)
1. Salve as alterações.

-----

> [!IMPORTANT]
> Embora seja possível atribuir eventos como `TouchUpInside` a um elemento de interface do usuário (como um UIButton) no designer do IOS, ele nunca será chamado porque o Apple TV não tem uma tela sensível ao toque ou dá suporte a eventos de toque. Você sempre deve usar o `Primary Action` evento ao criar manipuladores de eventos para elementos da interface do usuário do tvOS.

O código a seguir fornece um exemplo de manipuladores de eventos em três BarButtonItems diferentes: `ShowFirstHotel` , `ShowSecondHotel` e `ShowThirdHotel` . Quando cada item é clicado, a imagem de plano de fundo `HotelImage` é alterada. Isso é editado no arquivo do controlador de exibição (exemplo `ViewController.cs` ):

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void ShowFirstHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel01.jpg");
        }

        partial void ShowSecondHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel02.jpg");
        }

        partial void ShowThirdHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel03.jpg");
        }
        #endregion
    }
}
```

Desde que a propriedade de um botão `Enabled` seja `true` e não seja coberta por outro controle ou exibição, ela pode se tornar o item em foco usando o Siri remoto.

Para obter mais informações sobre como trabalhar com storyboards, consulte nossa [Guia de início rápido Olá, tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Summary"></a>

## <a name="summary"></a>Resumo

Este artigo abordou a criação e o trabalho com barras de navegação dentro de um aplicativo Xamarin. tvOS.

## <a name="related-links"></a>Links Relacionados

- [Exemplos do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
