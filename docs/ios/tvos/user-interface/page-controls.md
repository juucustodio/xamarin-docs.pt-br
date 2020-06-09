---
title: Trabalhando com controles de página tvOS no Xamarin
description: Este documento descreve como trabalhar com controles de página tvOS em um aplicativo criado com o Xamarin. Ele fornece uma descrição de alto nível dos controles de página, discute como configurá-los em storyboards e examina como responder a eventos de alteração de página.
ms.prod: xamarin
ms.assetid: 19198D46-7BBE-4D04-9BFA-7D1C5C9F9FC6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 9a45e87165101a8e8afcfc51d15d085982b6499e
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84569927"
---
# <a name="working-with-tvos-page-controls-in-xamarin"></a>Trabalhando com controles de página tvOS no Xamarin

Às vezes, talvez seja necessário exibir uma série de páginas ou imagens em seu aplicativo Xamarin. tvOS. Um controle de página foi projetado para mostrar claramente em qual página um usuário está fora do número máximo de páginas. Um controle de página exibe uma série de pontos em relação a um plano de fundo de forma escura e oval. A página atual exibirá um ponto preenchido, todas as outras páginas serão mostradas como pontos vazios. O controle de página cortará os pontos mais externos se houver muitos para caber em sua área de plano de fundo.

[![](page-controls-images/page01.png "Sample Page control")](page-controls-images/page01.png#lightbox)

Um controle de página em um elemento não interativo projetado para fornecer comentários somente para o usuário. Você precisará adicionar outros controles para alterar o número da página atual (como gestos ou botões).

A Apple tem as seguintes sugestões ao usar um controle de página:

- **Usar somente em coleções completas** – os controles de página funcionam melhor em um ambiente de tela inteira para exibir várias páginas que existem em uma única coleção.
- **Limitar o número de páginas que os** controles de página funcionam melhor para dez (10) ou menos páginas e um máximo de vinte (20) páginas. Para mais de vinte páginas, considere usar uma [exibição de coleção](~/ios/tvos/user-interface/collection-views.md) e exibir as páginas em uma grade.

<a name="Page-Controls-and-Storyboards"></a>

## <a name="page-controls-and-storyboards"></a>Controles de página e storyboards

A maneira mais fácil de trabalhar com controles de página em um aplicativo Xamarin. tvOS é adicioná-los à interface do usuário do aplicativo usando o designer do iOS.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. No **painel de soluções**, clique duas vezes no `Main.storyboard` arquivo e abra-o para edição.
1. Arraste um **controle de página** da **caixa de ferramentas** e solte-o na exibição:

    [![](page-controls-images/page02.png "A Page Control")](page-controls-images/page02.png#lightbox)
1. Na **Guia Widget** da **painel de propriedades**, você pode ajustar várias propriedades do controle de página, como a **página atual** e o **número de páginas**:

    [![](page-controls-images/page03.png "The Widget Tab")](page-controls-images/page03.png#lightbox)
1. Em seguida, adicione controles ou gestos à exibição para voltar e avançar pela coleção de páginas.
1. Por fim, atribua **nomes** aos controles para que você possa respondê-los em código C#. Por exemplo:

    [![](page-controls-images/page04.png "Name the control")](page-controls-images/page04.png#lightbox)
1. Salve suas alterações.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. No **Gerenciador de soluções**, clique duas vezes no `Main.storyboard` arquivo e abra-o para edição.
1. Arraste um **controle de página** da **caixa de ferramentas** e solte-o na exibição:

    [![](page-controls-images/page02-vs.png "A Page Control")](page-controls-images/page02-vs.png#lightbox)
1. Na **Guia Widget** do Gerenciador de **Propriedades**, você pode ajustar várias propriedades do controle de página, como a **página atual** e o **número de páginas**:

    [![](page-controls-images/page03-vs.png "The Widget tab")](page-controls-images/page03-vs.png#lightbox)
1. Em seguida, adicione controles ou gestos à exibição para voltar e avançar pela coleção de páginas.
1. Por fim, atribua **nomes** aos controles para que você possa respondê-los em código C#. Por exemplo:

    [![](page-controls-images/page04-vs.png "Name the control")](page-controls-images/page04-vs.png#lightbox)
1. Salve suas alterações.

-----

> [!IMPORTANT]
> Embora seja possível atribuir eventos como `TouchUpInside` a um elemento de interface do usuário (como um UIButton) no designer do IOS, ele nunca será chamado porque o Apple TV não tem uma tela sensível ao toque ou dá suporte a eventos de toque. Você sempre deve usar o `Primary Action` evento ao criar manipuladores de eventos para elementos da interface do usuário do tvOS.

Edite o arquivo do controlador de exibição (exemplo `ViewController.cs` ) e adicione o código para manipular as páginas que estão sendo alteradas. Por exemplo:

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Computed Properties
        public nint PageNumber { get; set; } = 0;
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            PageView.Pages = 6;
            ShowCat ();
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void NextCat (UIBarButtonItem sender) {

            // Display next Cat
            if (++PageNumber > 5) {
                PageNumber = 5;
            }
            ShowCat();

        }

        partial void PreviousCat (UIBarButtonItem sender) {
            // Display previous cat
            if (--PageNumber < 0) {
                PageNumber = 0;
            }
            ShowCat();
        }
        #endregion

        #region Private Methods
        private void ShowCat() {

            // Adjust UI
            PreviousButton.Enabled = (PageNumber > 0);
            NextButton.Enabled = (PageNumber < 5);
            PageView.CurrentPage = PageNumber;

            // Display new cat
            CatView.Image = UIImage.FromFile(string.Format("Cat{0:00}.jpg",PageNumber+1));
        }
        #endregion
    }
}
```

Vamos examinar com mais detalhes as duas propriedades do controle de página. Primeiro, para especificar o número máximo de páginas, use o seguinte:

```csharp
PageView.Pages = 6;
```

Para alterar o número de página atual, use o seguinte código:

```csharp
PageView.CurrentPage = PageNumber;
```

A `CurrentPage` propriedade é zero (0) com base, portanto, a primeira página será zero e a última será uma menos a quantidade máxima de páginas.

Para obter mais informações sobre como trabalhar com storyboards, consulte nossa [Guia de início rápido Olá, tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Summary"></a>

## <a name="summary"></a>Resumo

Este artigo abordou a criação e o trabalho com o controle de página dentro de um aplicativo Xamarin. tvOS.

## <a name="related-links"></a>Links relacionados

- [Exemplos do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
