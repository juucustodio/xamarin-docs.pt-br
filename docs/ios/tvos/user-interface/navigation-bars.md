---
title: Trabalhando com barras de navegação no Xamarin do tvOS
description: Este documento descreve como trabalhar com barras de navegação em um aplicativo tvOS criados com o Xamarin. Ele aborda a configuração barras de navegação em um storyboard e respondendo a eventos desses botões.
ms.prod: xamarin
ms.assetid: 74E396B7-87F0-46F7-BC6C-827DB8884C97
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 81e6cfe1e532bcfa7616e35adb28b314587bafc8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106943"
---
# <a name="working-with-tvos-navigation-bars-in-xamarin"></a>Trabalhando com barras de navegação no Xamarin do tvOS

Barras de navegação pode ser adicionadas à parte superior das exibições para exibir um título e botões da barra de navegação opcionais. Normalmente, eles são usados quando o usuário navegar de uma página principal, como um modo de exibição de tabela, coleção ou Menu para um modo de exibição secundário mostrando os detalhes do item selecionado.

[![](navigation-bars-images/navbar01.png "Exemplo de barra de navegação")](navigation-bars-images/navbar01.png#lightbox)

Além disso, para o título (que é exibido no centro), barras de navegação pode conter um ou mais botões de barra de navegação (`UIBarButtonItem`) à esquerda e direita da barra.

> [!IMPORTANT]
> Barras de navegação são totalmente transparentes por padrão. Tome cuidado para garantir que o conteúdo da barra de navegação permaneça legível sobre o conteúdo abaixo dela. Por exemplo, quando o conteúdo em uma exibição de tabela ou coleção rola sob ele.

<a name="Navigation-Bars-and-Storyboards" />

## <a name="navigation-bars-and-storyboards"></a>Barras de navegação e Storyboards

A maneira mais fácil trabalhar com barras de navegação em um aplicativo xamarin. tvos é adicioná-los à interface de usuário do aplicativo usando o Designer do iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. No **painel de soluções**, clique duas vezes em `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste uma **barra de navegação** da **caixa de ferramentas** e solte-o no modo de exibição na parte superior da tela: 

    [![](navigation-bars-images/navbar02.png "Uma barra de navegação")](navigation-bars-images/navbar02.png#lightbox)
1. Clique duas vezes no **barra de navegação** para selecionar a **Item de navegação**. No **Widget** guia da **painel de propriedades**, você pode definir o **título**: 

    [![](navigation-bars-images/navbar03.png "Definir o título")](navigation-bars-images/navbar03.png#lightbox)
1. Em seguida, você pode adicionar um ou mais **itens da barra de botão** para ambas as extremidades da barra de: 

    [![](navigation-bars-images/navbar04.png "Um Item de botão da barra")](navigation-bars-images/navbar04.png#lightbox)
1. Por fim, wire-up a **itens do botão de barra** a ações na **eventos** guia do **Gerenciador de propriedades**: 

    [![](navigation-bars-images/navbar05.png "Uma barra de ação do Item de botão")](navigation-bars-images/navbar05.png#lightbox)
1. Salve as alterações.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


1. No **Gerenciador de soluções**, clique duas vezes em `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste uma **barra de navegação** da **caixa de ferramentas** e solte-o no modo de exibição na parte superior da tela: 

    [![](navigation-bars-images/navbar02-vs.png "Uma barra de navegação")](navigation-bars-images/navbar02-vs.png#lightbox)
1. Clique duas vezes no **barra de navegação** para selecionar a **Item de navegação**. No **Widget** guia da **Gerenciador de propriedades**, você pode definir o **título**: 

    [![](navigation-bars-images/navbar03-vs.png "Definir o título")](navigation-bars-images/navbar03-vs.png#lightbox)
1. Em seguida, você pode adicionar um ou mais **itens da barra de botão** para ambas as extremidades da barra de: 

    [![](navigation-bars-images/navbar04-vs.png "Uma barra itens do botão")](navigation-bars-images/navbar04-vs.png#lightbox)
1. Por fim, wire-up a **itens do botão de barra** a ações na **eventos** guia do **Gerenciador de propriedades**: 

    [![](navigation-bars-images/navbar05-vs.png "Uma barra de ações do Item de botão")](navigation-bars-images/navbar05-vs.png#lightbox)
1. Salve as alterações.


-----

> [!IMPORTANT]
> Embora seja possível atribuir eventos como `TouchUpInside` para um elemento de interface do usuário (por exemplo, um UIButton) no Designer do iOS, ele nunca será chamado como Apple TV não tem um toque de tela ou dar suporte a eventos de toque. Você sempre deve usar o `Primary Action` eventos durante a criação de manipuladores de eventos para tvOS elementos da interface do usuário.

O código a seguir fornece um exemplo de manipuladores de eventos em três BarButtonItems diferentes: `ShowFirstHotel`, `ShowSecondHotel`, e `ShowThirdHotel`. Quando cada item é clicado, a imagem de plano de fundo `HotelImage` é alterado. Isso é editado no controlador de exibição (exemplo `ViewController.cs`) arquivo:

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

Desde que um botão `Enabled` é de propriedade `true` e ele não é coberto por outro controle ou modo de exibição, ela pode se tornar o item em foco usando o Siri remoto.

Para obter mais informações sobre como trabalhar com Storyboards, consulte nosso [Olá, guia de início rápido do tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou a projetar e trabalhar com barras de navegação dentro de um aplicativo xamarin. tvos.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
