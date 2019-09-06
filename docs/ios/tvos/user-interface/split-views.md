---
title: Trabalhando com controladores de exibição de divisão tvOS no Xamarin
description: Este documento descreve como trabalhar com exibições de divisão do tvOS em um aplicativo criado com o Xamarin. Ele fornece uma visão geral de alto nível dos controladores de exibição de divisão, como usá-los com storyboards, acessar as exibições mestre e de detalhes e mostrar e ocultar o modo de exibição mestre.
ms.prod: xamarin
ms.assetid: 21248CFB-5A94-4C19-B223-C72E0DC5F1D5
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 9f770eaf3fcb68c17a7692e5b6433081234951e6
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286371"
---
# <a name="working-with-tvos-split-view-controllers-in-xamarin"></a>Trabalhando com controladores de exibição de divisão tvOS no Xamarin

Um controlador de exibição de divisão apresenta e gerencia um controlador de exibição mestre e de detalhes lado a lado, na tela ao mesmo tempo. Os controladores de exibição de divisão são usados para apresentar conteúdo persistente e de foco no modo de exibição mestre (a seção menor à esquerda) e detalhes relacionados na exibição de detalhes (a seção maior à direita).

[![](split-views-images/intro01.png "Exibição de divisão de exemplo")](split-views-images/intro01.png#lightbox)

<a name="About-Split-View-Controllers" />

## <a name="about-split-view-controllers"></a>Sobre os controladores de exibição de divisão

Como mencionado acima, um controlador de exibição de divisão gerencia um controlador de exibição mestre e de detalhes que é apresentado lado a lado, com o mestre sendo a exibição menor à esquerda, o detalhe o maior à direita. 

Além disso, o controlador de modo de exibição mestre pode ser ocultado ou mostrado conforme necessário: 

[![](split-views-images/intro02.png "O controlador do modo de exibição mestre oculto")](split-views-images/intro02.png#lightbox)

Os controladores de modos de exibição de divisão geralmente são usados para apresentar uma lista de conteúdo filtrável, com as categorias no modo de exibição mestre e os resultados filtrados na exibição de detalhes. Normalmente, isso é apresentado como uma exibição de tabela à esquerda e uma [exibição de coleção](~/ios/tvos/user-interface/collection-views.md) à direita.

Ao criar uma interface do usuário que requer um controlador de exibição de divisão, a Apple sugere usar controladores de exibição mestre e de detalhes que não são alterados (somente o conteúdo muda, não a estrutura). Se você precisar alternar os controladores de exibição, é melhor usar um controlador de navegação como a base do controlador de exibição que precisa ser alterado (mestre ou detalhes).

A Apple tem as seguintes sugestões para trabalhar com controladores de exibição de divisão:

- **Usar a porcentagem de divisão correta** – por padrão, o controlador de exibição de divisão usa um terço da tela para o controlador de modo de exibição mestre e dois terços para o controlador de exibição de detalhes. Opcionalmente, você pode usar uma divisão 50/50. Escolha a porcentagem correta para fazer com que seu conteúdo pareça balanceado na tela.
- **Persistir a seleção principal** -embora o conteúdo no modo de exibição de detalhes possa mudar seja resposta à seleção de um usuário no modo de exibição mestre, o conteúdo do modo de exibição mestre deve ser corrigido. Além disso, você deve mostrar claramente o item atualmente selecionado no modo de exibição mestre.
- **Use um único título unificado** – normalmente, você desejará usar um único título centralizado na exibição de detalhes, em vez de um título no modo de exibição de detalhes e mestre.

<a name="Split-View-Controllers-and-Storyboards" />

## <a name="split-view-controllers-and-storyboards"></a>Dividir os controladores de exibição e os storyboards

A maneira mais fácil de trabalhar com os controladores de exibição de divisão em um aplicativo Xamarin. tvOS é adicioná-los à interface do usuário do aplicativo usando o designer do iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. No **painel de soluções**, clique duas vezes no arquivo `Main.storyboard` e abra-o para edição.
1. Arraste um dos **controladores de exibição de divisão** da caixa de **ferramentas** e solte-o na exibição: 

    [![](split-views-images/activity01.png "Um controlador de exibição de divisão")](split-views-images/activity01.png#lightbox)
1. Por padrão, o designer do iOS instalará um controlador de navegação e um controlador de exibição no modo de exibição mestre. Se isso não atender aos requisitos do seu aplicativo, simplesmente exclua-os.
1. Se você remover o modo de exibição mestre padrão, arraste um novo controlador de exibição para a superfície de design: 

    [![](split-views-images/activity02.png "Um controlador de exibição")](split-views-images/activity02.png#lightbox)
1. Controle-clique e arraste do controlador de exibição de divisão para o novo controlador de exibição mestre. 
1. Selecione **mestre** no **menu pop-up**: 

    [![](split-views-images/activity03.png "Selecione mestre no menu pop-up")](split-views-images/activity03.png#lightbox)
1. Crie o conteúdo de suas exibições mestre e de detalhes: 

    [![](split-views-images/activity04.png "Exemplo de layout")](split-views-images/activity04.png#lightbox)
1. Atribua **nomes** na **Guia do widget** do **painel de propriedades** para trabalhar com os controles da interface do C# usuário no código.
1. Salve as alterações e retorne ao Visual Studio para Mac.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. No **Gerenciador de soluções**, clique duas vezes no arquivo `Main.storyboard` e abra-o para edição.
1. Arraste um dos **controladores de exibição de divisão** da caixa de **ferramentas** e solte-o na exibição: 

    [![](split-views-images/activity01-vs.png "Um controlador de exibição de divisão")](split-views-images/activity01-vs.png#lightbox)
1. Por padrão, o designer do iOS adicionará um controlador de navegação e um controlador de exibição no modo de exibição mestre. Se isso não atender aos requisitos do seu aplicativo, simplesmente exclua-os.
1. Se você remover o modo de exibição mestre padrão, arraste um novo controlador de exibição para a superfície de design: 

    [![](split-views-images/activity02-vs.png "Um controlador de exibição")](split-views-images/activity02-vs.png#lightbox)
1. Controle-clique e arraste do controlador de exibição de divisão para o novo controlador de exibição mestre. 
1. Selecione **mestre** no **menu pop-up**: 

    [![](split-views-images/activity03-vs.png "Selecione mestre no menu pop-up")](split-views-images/activity03-vs.png#lightbox)
1. Crie o conteúdo de suas exibições mestre e de detalhes: 

    [![](split-views-images/activity04.png "Layout do conteúdo")](split-views-images/activity04.png#lightbox)
1. Atribua **nomes** na **Guia Widget** do Gerenciador de **Propriedades** para trabalhar com os controles de interface do C# usuário no código.
1. Salve as alterações.

-----

Para obter mais informações sobre como trabalhar com storyboards, consulte nossa [Guia de início rápido Olá, tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Working-with-Split-View-Controllers" />

## <a name="working-with-split-view-controllers"></a>Trabalhando com controladores de exibição de divisão

Como mencionado acima, um controlador de exibição de divisão geralmente é usado em situações em que você está exibindo conteúdo filtrado para o usuário. As categorias principais são exibidas à esquerda no modo de exibição mestre e os resultados filtrados à direita na exibição de detalhes com base na seleção do usuário.

<a name="Accessing-Master-and-Detail" />

### <a name="accessing-master-and-detail"></a>Acessando mestre e detalhes

Se você precisar acessar os controladores mestre e de exibição de detalhes programaticamente `ViewControllers` , use a propriedade do controlador de exibição de divisão. Por exemplo:

```csharp
// Gain access to master and detail view controllers
var masterController = ViewControllers [0] as MasterViewController;
var detailController = ViewControllers [1] as DetailViewController;
```

Ele é apresentado como uma matriz, em que o primeiro elemento (0) no controlador de modo de exibição mestre e o segundo elemento (1) são os detalhes.

<a name="Accessing-Detail-from-Master" />

### <a name="accessing-detail-from-master"></a>Acessando detalhes do mestre

Como geralmente você está exibindo informações detalhadas no modo de exibição de detalhes com base na seleção do usuário no mestre, você precisará de uma maneira de acessar os detalhes do mestre.

A maneira mais fácil de fazer isso é expor uma propriedade em sua classe de controlador de modo de exibição mestre, por exemplo:

```csharp
public DetailViewController DetailController { get; set;}
```

No controlador de exibição de divisão, substitua `ViewDidLoad` o método e vincule as duas exibições juntas. Por exemplo:

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

Você pode expor propriedades e métodos em seu controlador de exibição de detalhes que o mestre pode usar para apresentar novos dados conforme necessário.

<a name="Showing-and-Hiding-Master" />

### <a name="showing-and-hiding-master"></a>Mostrando e ocultando o mestre

Opcionalmente, você pode mostrar e ocultar o controlador de modo de exibição `PreferredDisplayMode` mestre usando a propriedade do controlador de exibição de divisão. Por exemplo:

```csharp
// Show hide split view
if (SplitViewController.DisplayMode == UISplitViewControllerDisplayMode.PrimaryHidden) {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.AllVisible;
} else {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.PrimaryHidden;
}
```

A `UISplitViewControllerDisplayMode` enumeração define como o controlador de modo de exibição mestre será apresentado como um dos seguintes:

- **Automatic** -tvOS controlará a apresentação das exibições mestre e de detalhes.
- **PrimaryHidden** -isso oculta o controlador de exibição mestre.
- **OnVisible** – exibe os controladores mestre e de exibição de detalhes lado a lado. Esta é a apresentação padrão normal.
- **PrimaryOverlay** -o controlador de exibição de detalhes se estende em e é coberto pelo mestre.

Para obter o estado da apresentação atual, use `DisplayMode` a propriedade do controlador de exibição de divisão.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou a criação e o trabalho com controladores de exibição de divisão dentro de um aplicativo Xamarin. tvOS.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
