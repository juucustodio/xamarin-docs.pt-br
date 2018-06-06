---
title: Trabalhando com tvOS exibir controladores de divisão em Xamarin
description: Este documento descreve como trabalhar com tvOS dividir exibições em um aplicativo compilado com o Xamarin. Ele fornece uma visão geral de controladores de exibição de divisão, como usá-los com storyboards, acessar os modos de exibição mestre e de detalhes e mostrar e ocultar o modo de exibição mestre.
ms.prod: xamarin
ms.assetid: 21248CFB-5A94-4C19-B223-C72E0DC5F1D5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 2dd07cd8a4e92d6d39be50ba670441d965ed4d13
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789425"
---
# <a name="working-with-tvos-split-view-controllers-in-xamarin"></a>Trabalhando com tvOS exibir controladores de divisão em Xamarin

Um controlador de exibição de divisão apresenta e gerencia um mestre e o controlador de exibição de detalhes-lado a lado, na tela ao mesmo tempo. Exibir controladores de divisão são usados para apresentar conteúdo Focusable é persistente no modo de exibição mestre (seção menor à esquerda) e relacionados detalhes na exibição de detalhes (seção maior à direita).

[![](split-views-images/intro01.png "Modo de divisão de amostra")](split-views-images/intro01.png#lightbox)

<a name="About-Split-View-Controllers" />

## <a name="about-split-view-controllers"></a>Sobre controladores de exibição de divisão

Como mencionado acima, um controlador de exibição de divisão gerencia um mestre e um controlador de exibição de detalhes que são apresentados lado a lado, com o mestre sendo diminuir a exibição à esquerda, o detalhe da maior à direita. 

Além disso, o pode controlador do modo de exibição mestre foi ocultada ou exibida conforme necessário: 

[![](split-views-images/intro02.png "O controlador de exibição mestre ocultos")](split-views-images/intro02.png#lightbox)

Controladores de modos de exibição de divisão são geralmente usam para apresentar uma lista de conteúdo podem ser filtrado, com as categorias no modo de exibição mestre e os resultados filtrados na exibição detalhes. Isso normalmente é apresentado como uma exibição de tabela à esquerda e um [exibição de coleção](~/ios/tvos/user-interface/collection-views.md) à direita.

Ao criar uma Interface do usuário que requer um controlador de exibição de divisão, Apple sugere usando mestre e controladores de exibição de detalhes que não são alterados (somente as alterações de conteúdo, não a estrutura). Se você precisar exibir controladores de desconexão, é melhor usar um controlador de navegação como a base do controlador de exibição que precisa ser alterado (mestre ou detalhes).

Apple tem as seguintes sugestões para trabalhar com os controladores de exibição de divisão:

- **Use a porcentagem de divisão correta** -por padrão o controlador de exibição de divisão usa um terço da tela para o controlador de exibição mestre e dois terços para o controlador de exibição de detalhes. Opcionalmente, você pode usar uma divisão 50/50. Escolha a porcentagem correta para tornar o conteúdo exibido balanceado na tela.
- **Manter a seleção principal** - enquanto o conteúdo na exibição de detalhes podem alterar resposta a seleção do usuário no modo de exibição mestre, o conteúdo de modo de exibição mestre deve ser corrigido. Além disso, você deve mostrar claramente o item selecionado atualmente no modo de exibição mestre.
- **Use um único título unificado** -normalmente, você desejará usar um título único, centralizado na exibição de detalhes, em vez de um título em detalhes e o modo de exibição mestre.

<a name="Split-View-Controllers-and-Storyboards" />

## <a name="split-view-controllers-and-storyboards"></a>Exibir controladores de divisão e Storyboards

É a maneira mais fácil trabalhar com os controladores de exibição de divisão em um aplicativo de Xamarin.tvOS para adicioná-los à interface de usuário do aplicativo usando o Designer do iOS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. No **solução preenchimento**, clique duas vezes no `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste um **exibir controladores de divisão** do **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](split-views-images/activity01.png "Um controlador de exibição de divisão")](split-views-images/activity01.png#lightbox)
1. Por padrão, o iOS Designer instalará um controlador de navegação e um controlador de exibição no modo de exibição mestre. Se isso não se ajustar os requisitos do seu aplicativo, basta excluí-los.
1. Se você remover o padrão do modo de exibição mestre, arraste um novo controlador de exibição para a superfície de design: 

    [![](split-views-images/activity02.png "Um controlador de exibição")](split-views-images/activity02.png#lightbox)
1. CTRL + clique e arraste do controlador de exibição de divisão para o novo controlador de modo de exibição mestre. 
1. Selecione **mestre** do **Menu pop-up**: 

    [![](split-views-images/activity03.png "Selecione o mestre no Menu pop-up")](split-views-images/activity03.png#lightbox)
1. O conteúdo do mestre e modos de exibição de detalhes de design: 

    [![](split-views-images/activity04.png "Exemplo de layout")](split-views-images/activity04.png#lightbox)
1. Atribuir **nomes** no **Widget guia** do **propriedades de preenchimento** para trabalhar com os controles de interface do usuário em código c#.
1. Salve suas alterações e retornar ao Visual Studio para Mac.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. No **Solution Explorer**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste um **exibir controladores de divisão** do **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](split-views-images/activity01-vs.png "Um controlador de exibição de divisão")](split-views-images/activity01-vs.png#lightbox)
1. Por padrão, o Designer do iOS irá adicionar um controlador de navegação e o controlador de exibição no modo de exibição mestre. Se isso não se ajustar os requisitos do seu aplicativo, basta excluí-los.
1. Se você remover o padrão do modo de exibição mestre, arraste um novo controlador de exibição para a superfície de design: 

    [![](split-views-images/activity02-vs.png "Um controlador de exibição")](split-views-images/activity02-vs.png#lightbox)
1. CTRL + clique e arraste do controlador de exibição de divisão para o novo controlador de modo de exibição mestre. 
1. Selecione **mestre** do **Menu pop-up**: 

    [![](split-views-images/activity03-vs.png "Selecione o mestre no Menu pop-up")](split-views-images/activity03-vs.png#lightbox)
1. O conteúdo do mestre e modos de exibição de detalhes de design: 

    [![](split-views-images/activity04.png "Layout do conteúdo")](split-views-images/activity04.png#lightbox)
1. Atribuir **nomes** no **Widget guia** do **propriedades Explorer** para trabalhar com os controles de interface do usuário em código c#.
1. Salve as alterações.
    
-----

Para obter mais informações sobre como trabalhar com Storyboards, consulte nosso [Hello, tvOS Quick Start Guide](~/ios/tvos/get-started/hello-tvos.md).

<a name="Working-with-Split-View-Controllers" />

## <a name="working-with-split-view-controllers"></a>Trabalhando com os controladores de exibição de divisão

Como mencionado acima, um controlador de exibição de divisão é geralmente usado em situações em que você está exibindo o conteúdo filtrado para o usuário. As principais categorias são exibidas à esquerda no modo de exibição mestre, e os resultados filtrados à direita na exibição de detalhes com base na seleção do usuário.

<a name="Accessing-Master-and-Detail" />

### <a name="accessing-master-and-detail"></a>Acessando mestre e detalhes

Se você precisar acessar o mestre e controladores de exibição de detalhes programaticamente, use o `ViewControllers ` propriedade do controlador de exibição de divisão. Por exemplo:

```csharp
// Gain access to master and detail view controllers
var masterController = ViewControllers [0] as MasterViewController;
var detailController = ViewControllers [1] as DetailViewController;
```

Ele é apresentado como uma matriz, onde o primeiro elemento (0) no controlador de modo de exibição mestre e o segundo elemento (1) são o detalhe.

<a name="Accessing-Detail-from-Master" />

### <a name="accessing-detail-from-master"></a>Acessar detalhes do mestre

Como você normalmente está exibindo informações detalhadas na exibição de detalhes com base na seleção do usuário no mestre, você precisará de uma maneira de acessar os detalhes do mestre.

É a maneira mais fácil de fazer isso para expor uma propriedade em sua classe de controlador de exibição mestre, por exemplo:

```csharp
public DetailViewController DetailController { get; set;}
```

No controlador de exibição de divisão, substituir o `ViewDidLoad` método tie as duas exibições e juntos. Por exemplo:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Gain access to master and detail view controllers
    var masterController = ViewControllers [0] as MasterViewController;
    var detailController = ViewControllers [1] as DetailViewController;

    // Wire-up views
    masterController.SplitViewController = this;
    masterController.DetailController = detailController;
    detailController.SplitViewController = this;
}
```

Você pode expor propriedades e métodos em seu controlador de exibição de detalhes mestre pode usar para apresentar novos dados conforme necessário.

<a name="Showing-and-Hiding-Master" />

### <a name="showing-and-hiding-master"></a>Mostrando e ocultando mestre

Opcionalmente, você pode mostrar e ocultar o controlador de exibição mestre usando o `PreferredDisplayMode` propriedade do controlador de exibição de divisão. Por exemplo:

```csharp
// Show hide split view
if (SplitViewController.DisplayMode == UISplitViewControllerDisplayMode.PrimaryHidden) {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.AllVisible;
} else {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.PrimaryHidden;
}
```

O `UISplitViewControllerDisplayMode` enum define como o controlador de exibição mestre será apresentado como um dos seguintes:

- **Automático** -tvOS controlará a apresentação do mestre e modos de exibição de detalhes.
- **PrimaryHidden** -Isso oculta o controlador de exibição mestre.
- **AllVisible** -exibe o mestre e a controladores de exibição de detalhes lado a lado. Isso é normal, apresentação de padrão.
- **PrimaryOverlay** -o controlador de exibição de detalhes se estende em e é abordado pelo mestre.

Para obter o estado de apresentação atual, use o `DisplayMode` propriedade do controlador de exibição de divisão.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou criando e trabalhando com controladores de exibição de divisão dentro de um aplicativo Xamarin.tvOS.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
