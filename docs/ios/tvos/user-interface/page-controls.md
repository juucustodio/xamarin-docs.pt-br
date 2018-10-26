---
title: Trabalhando com controles de página no Xamarin do tvOS
description: Este documento descreve como trabalhar com controles de página tvOS em um aplicativo criado com o Xamarin. Ele fornece uma descrição detalhada dos controles de página, discute como configurá-los em storyboards e examina como responder a eventos de alteração de página.
ms.prod: xamarin
ms.assetid: 19198D46-7BBE-4D04-9BFA-7D1C5C9F9FC6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 173bc7713b5b8c330d4d4c5863bef24be8bdcb52
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107463"
---
# <a name="working-with-tvos-page-controls-in-xamarin"></a>Trabalhando com controles de página no Xamarin do tvOS

Às vezes, talvez seja necessário exibir uma série de páginas ou imagens em seu aplicativo xamarin. tvos. Um controle de página foi projetado para mostrar claramente qual página de um usuário está em fora o número máximo de páginas. Um controle de página exibe uma série de pontos em relação a um escuro, oval em forma de plano de fundo. A página atual será exibido um ponto preenchido, todas as outras páginas mostram como os pontos vazios. O controle de página recortará os pontos mais externos, se houver muitas para caber na sua área de tela de fundo.

[![](page-controls-images/page01.png "Exemplo de controle de página")](page-controls-images/page01.png#lightbox)

Um controle de página em um elemento não interativo, projetado para fornecer comentários ao usuário apenas. Você precisará adicionar outros controles para alterar o número da página atual (como gestos ou botões).

Apple tem as sugestões a seguir ao usar um controle de página:

- **Use em coleções completo somente** -controles de página funcionam melhor em um ambiente de tela inteira para exibir várias páginas que existem em uma única coleção.
- **Limitar o número de páginas** -controles de página funcionam melhor para as páginas de dez (10) ou menos e um máximo de vinte (20) páginas. Para mais de vinte páginas, considere o uso de um [exibição de coleção](~/ios/tvos/user-interface/collection-views.md) e exibindo as páginas em uma grade.

<a name="Page-Controls-and-Storyboards" />

## <a name="page-controls-and-storyboards"></a>Controles de página e Storyboards

A maneira mais fácil trabalhar com controles de página em um aplicativo xamarin. tvos é adicioná-los à interface de usuário do aplicativo usando o Designer do iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

    
1. No **painel de soluções**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste uma **controle de página** da **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](page-controls-images/page02.png "Um controle de página")](page-controls-images/page02.png#lightbox)
1. No **guia de Widget** da **painel de propriedades**, você pode ajustar várias propriedades de controle de página, como seu **página atual** e **n º de páginas**: 

    [![](page-controls-images/page03.png "Na guia de Widget")](page-controls-images/page03.png#lightbox)
1. Em seguida, adicione controles ou gestos para a exibição para retroceder e Avançar através da coleção de páginas.
1. Por fim, ceda **nomes** para os controles para que você possa responder a elas dentro de C# código. Por exemplo: 

    [![](page-controls-images/page04.png "Nomeie o controle")](page-controls-images/page04.png#lightbox)
1. Salve as alterações.
    

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    
1. No **Gerenciador de soluções**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste uma **controle de página** da **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](page-controls-images/page02-vs.png "Um controle de página")](page-controls-images/page02-vs.png#lightbox)
1. No **guia de Widget** da **Gerenciador de propriedades**, você pode ajustar várias propriedades de controle de página, como seu **página atual** e **n º de páginas**: 

    [![](page-controls-images/page03-vs.png "Na guia de Widget")](page-controls-images/page03-vs.png#lightbox)
1. Em seguida, adicione controles ou gestos para a exibição para retroceder e Avançar através da coleção de páginas.
1. Por fim, ceda **nomes** para os controles para que você possa responder a elas dentro de C# código. Por exemplo: 

    [![](page-controls-images/page04-vs.png "Nomeie o controle")](page-controls-images/page04-vs.png#lightbox)
1. Salve as alterações.
    

-----

> [!IMPORTANT]
> Embora seja possível atribuir eventos como `TouchUpInside` para um elemento de interface do usuário (por exemplo, um UIButton) no Designer do iOS, ele nunca será chamado como Apple TV não tem um toque de tela ou dar suporte a eventos de toque. Você sempre deve usar o `Primary Action` eventos durante a criação de manipuladores de eventos para tvOS elementos da interface do usuário.

Editar seu controlador de exibição (exemplo `ViewController.cs`) de arquivo e adicione o código para lidar com as páginas que está sendo alteradas. Por exemplo:

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

Vamos dar uma olhada nas duas propriedades do controle de página. Primeiro, para especificar o número máximo de páginas, use o seguinte:

```csharp
PageView.Pages = 6;
```

Para alterar o número da página atual, use o seguinte código:

```csharp
PageView.CurrentPage = PageNumber;
```

O `CurrentPage` propriedade é zero (0) com base, portanto, a primeira página será zero e o último será um menos o número máximo de páginas.

Para obter mais informações sobre como trabalhar com Storyboards, consulte nosso [Olá, guia de início rápido do tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo cobriu criando e trabalhando com controle de página dentro de um aplicativo xamarin. tvos.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
