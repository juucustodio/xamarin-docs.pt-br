---
title: "Trabalhando com controle de página"
description: "Este artigo aborda criando e trabalhando com controle de página dentro de um aplicativo Xamarin.tvOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 19198D46-7BBE-4D04-9BFA-7D1C5C9F9FC6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: f77eac8179f9e368e767bb4b586ccaa3f93e40a3
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="working-with-page-control"></a>Trabalhando com controle de página

_Este artigo aborda criando e trabalhando com controle de página dentro de um aplicativo Xamarin.tvOS._

Às vezes, talvez seja necessário exibir uma série de páginas ou imagens em seu aplicativo Xamarin.tvOS. Um controle de página foi projetado para mostrar claramente a página de um usuário estiver no número máximo de páginas. Um controle de página exibe uma série de pontos em um escuro, oval em forma de plano de fundo. A página atual será exibido um ponto preenchido, todas as outras páginas mostram como pontos vazios. O controle de página recortará os pontos mais externos, se houver muito grande para caber na área de seu plano de fundo.

[![](page-controls-images/page01.png "Exemplo de controle de página")](page-controls-images/page01.png#lightbox)

Um controle de página em um elemento não interativo projetado para fornecer comentários para o usuário somente. Você precisará adicionar outros controles para alterar o número da página atual (como botões ou gestos).

Apple tem as sugestões a seguir ao usar um controle de página:

- **Use em coleções completo somente** -controles de página funcionam melhor em um ambiente de tela inteira para exibir várias páginas que existem em uma única coleção.
- **Limitar o número de páginas** -controles de página funcionam melhor para páginas de dez (10) ou menos e um máximo de páginas de vinte (20). Para mais de vinte páginas, considere o uso de um [exibição de coleção](~/ios/tvos/user-interface/collection-views.md) e exibir as páginas em uma grade.

<a name="Page-Controls-and-Storyboards" />

## <a name="page-controls-and-storyboards"></a>Controles de página e Storyboards

É a maneira mais fácil trabalhar com controles de página em um aplicativo de Xamarin.tvOS para adicioná-los à interface de usuário do aplicativo usando o Designer do iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

    
1. No **solução preenchimento**, clique duas vezes no `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste um **controle de página** do **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](page-controls-images/page02.png "Um controle de página")](page-controls-images/page02.png#lightbox)
1. No **Widget guia** do **propriedades de preenchimento**, você pode ajustar várias propriedades de controle de página, como seu **página atual** e **n º de páginas**: 

    [![](page-controls-images/page03.png "A guia de Widget")](page-controls-images/page03.png#lightbox)
1. Em seguida, adicione controles ou gestos para a exibição para retroceder e Avançar pela coleção de páginas.
1. Por fim, atribuir **nomes** para os controles para que você pode responder a eles no código c#. Por exemplo: 

    [![](page-controls-images/page04.png "Nome do controle")](page-controls-images/page04.png#lightbox)
1. Salve as alterações.
    

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

    
1. No **Solution Explorer**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste um **controle de página** do **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](page-controls-images/page02-vs.png "Um controle de página")](page-controls-images/page02-vs.png#lightbox)
1. No **Widget guia** do **propriedades Explorer**, você pode ajustar várias propriedades de controle de página, como seu **página atual** e **n º de páginas**: 

    [![](page-controls-images/page03-vs.png "A guia de Widget")](page-controls-images/page03-vs.png#lightbox)
1. Em seguida, adicione controles ou gestos para a exibição para retroceder e Avançar pela coleção de páginas.
1. Por fim, atribuir **nomes** para os controles para que você pode responder a eles no código c#. Por exemplo: 

    [![](page-controls-images/page04-vs.png "Nome do controle")](page-controls-images/page04-vs.png#lightbox)
1. Salve as alterações.
    

-----

> [!IMPORTANT]
> **Observação:** enquanto é possível atribuir eventos como `TouchUpInside` para um elemento de interface do usuário (como um UIButton) no Designer do iOS, ele nunca será chamado porque Apple TV não tem um toque de tela ou oferecer suporte a eventos de toque. Você sempre deve usar o `Primary Action` eventos durante a criação de manipuladores de eventos para tvOS elementos da interface do usuário.




Editar seu controlador de exibição (exemplo `ViewController.cs`) e adicione o código para manipular as páginas que está sendo alteradas. Por exemplo:

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

Vamos examinar mais detalhadamente as duas propriedades de controle de página. Primeiro, para especificar o número máximo de páginas, use o seguinte:

```csharp
PageView.Pages = 6;
```

Para alterar o número da página atual, use o seguinte código:

```csharp
PageView.CurrentPage = PageNumber;
```

O `CurrentPage` propriedade é zero (0) baseada, portanto, a primeira página será zero e o último será um menos o número máximo de páginas.

Para obter mais informações sobre como trabalhar com Storyboards, consulte nosso [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou criando e trabalhando com controle de página dentro de um aplicativo Xamarin.tvOS.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
