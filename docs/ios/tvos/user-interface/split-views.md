---
title: Trabalhar com controladores de exibição de divisão no Xamarin do tvOS
description: Este documento descreve como trabalhar com tvOS Dividir modos de exibição em um aplicativo criado com o Xamarin. Ele fornece uma visão geral dos controladores de exibição de divisão, como usá-los com storyboards, acessar os modos de exibição mestre e de detalhes e mostrando e ocultando o modo de exibição mestre.
ms.prod: xamarin
ms.assetid: 21248CFB-5A94-4C19-B223-C72E0DC5F1D5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 775fe01122440378f68973549d46e3010400614e
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830858"
---
# <a name="working-with-tvos-split-view-controllers-in-xamarin"></a>Trabalhar com controladores de exibição de divisão no Xamarin do tvOS

Um controlador de exibição de divisão apresenta e gerencia um mestre e o controlador de exibição de detalhes lado a lado, na tela ao mesmo tempo. Controladores de exibição de divisão são usadas para apresentar o conteúdo persistente, focalizável no modo de exibição mestre (seção menor à esquerda) e relacionadas detalhes na exibição de detalhes (a seção maior à direita).

[![](split-views-images/intro01.png "Modo de divisão de amostra")](split-views-images/intro01.png#lightbox)

<a name="About-Split-View-Controllers" />

## <a name="about-split-view-controllers"></a>Sobre controladores de exibição de divisão

Como mencionado acima, um controlador de exibição de divisão gerencia um mestre e um controlador de exibição de detalhes que são apresentados lado a lado, com o mestre que está sendo diminuir a exibição à esquerda, o detalhe da maior à direita. 

Além disso, a lata de controlador de exibição mestre foi ocultada ou exibida conforme necessário: 

[![](split-views-images/intro02.png "O controlador de exibição mestre oculto")](split-views-images/intro02.png#lightbox)

Controladores de modos de exibição de divisão são geralmente usam para apresentar uma lista de conteúdo que podem ser filtrada, com as categorias no modo de exibição mestre e os resultados filtrados na exibição detalhes. Isso normalmente é apresentado como uma exibição de tabela à esquerda e um [exibição de coleção](~/ios/tvos/user-interface/collection-views.md) à direita.

Ao criar uma Interface do usuário que requer um controlador de exibição de divisão, a Apple sugere o uso de mestre e controladores de exibição de detalhes que não são alterados (somente as alterações de conteúdo, não a estrutura). Se você precisar desconexão controladores de exibição, é melhor usar um controlador de navegação como a base de controlador de exibição que precisa ser alterada (mestre ou detalhes).

Apple tem as sugestões a seguir para trabalhar com controladores de exibição de divisão:

- **Use a porcentagem correta de divisão** -por padrão o controlador de exibição de divisão usa um terço da tela para o controlador de exibição mestre e dois terços para o controlador de exibição de detalhes. Opcionalmente, você pode usar uma divisão 50/50. Escolha a porcentagem correta para tornar seu conteúdo seja exibido balanceado na tela.
- **Manter a seleção principal** – enquanto o conteúdo na exibição de detalhe podem alterar resposta a seleção de um usuário no modo de exibição mestre, o conteúdo de modo de exibição mestre deve ser corrigido. Além disso, você deve mostrar claramente o item selecionado atualmente no modo de exibição mestre.
- **Usar uma única, unificada título** -normalmente, você desejará usar um título único e centralizado na exibição de detalhes, em vez de um título em detalhes e o modo de exibição mestre.

<a name="Split-View-Controllers-and-Storyboards" />

## <a name="split-view-controllers-and-storyboards"></a>Controladores de exibição de divisão e Storyboards

A maneira mais fácil trabalhar com controladores de exibição de divisão em um aplicativo xamarin. tvos é adicioná-los à interface de usuário do aplicativo usando o Designer do iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. No **painel de soluções**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste uma **controladores de exibição de divisão** da **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](split-views-images/activity01.png "Um controlador de exibição de divisão")](split-views-images/activity01.png#lightbox)
1. Por padrão, o Designer do iOS instalará um controlador de navegação e um controlador de exibição no modo de exibição mestre. Se isso não se ajusta os requisitos do aplicativo, basta excluí-las.
1. Se você remover o modo de exibição mestre padrão, arraste um novo controlador de exibição para a superfície de design: 

    [![](split-views-images/activity02.png "Um controlador de exibição")](split-views-images/activity02.png#lightbox)
1. CTRL + clique e arraste do controlador de exibição de divisão para o novo controlador de exibição mestre. 
1. Selecione **Master** da **Menu pop-up**: 

    [![](split-views-images/activity03.png "Selecione o mestre no Menu pop-up")](split-views-images/activity03.png#lightbox)
1. O conteúdo do mestre e modos de exibição de detalhes de design: 

    [![](split-views-images/activity04.png "Exemplo de layout")](split-views-images/activity04.png#lightbox)
1. Atribua **nomes** na **Widget guia** da **painel de propriedades** para trabalhar com os controles de interface do usuário em C# código.
1. Salve as alterações e retorne ao Visual Studio para Mac.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. No **Gerenciador de soluções**, clique duas vezes o `Main.storyboard` de arquivo e abri-lo para edição.
1. Arraste uma **controladores de exibição de divisão** da **caixa de ferramentas** e solte-o no modo de exibição: 

    [![](split-views-images/activity01-vs.png "Um controlador de exibição de divisão")](split-views-images/activity01-vs.png#lightbox)
1. Por padrão, o Designer do iOS adicionará um controlador de navegação e o controlador de exibição no modo de exibição mestre. Se isso não se ajusta os requisitos do aplicativo, basta excluí-las.
1. Se você remover o modo de exibição mestre padrão, arraste um novo controlador de exibição para a superfície de design: 

    [![](split-views-images/activity02-vs.png "Um controlador de exibição")](split-views-images/activity02-vs.png#lightbox)
1. CTRL + clique e arraste do controlador de exibição de divisão para o novo controlador de exibição mestre. 
1. Selecione **Master** da **Menu pop-up**: 

    [![](split-views-images/activity03-vs.png "Selecione o mestre no Menu pop-up")](split-views-images/activity03-vs.png#lightbox)
1. O conteúdo do mestre e modos de exibição de detalhes de design: 

    [![](split-views-images/activity04.png "Layout do conteúdo")](split-views-images/activity04.png#lightbox)
1. Atribua **nomes** no **guia Widget** do **Gerenciador de propriedades** para trabalhar com os controles de interface do usuário em C# código.
1. Salve as alterações.
    
-----

Para obter mais informações sobre como trabalhar com Storyboards, consulte nosso [Olá, guia de início rápido do tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Working-with-Split-View-Controllers" />

## <a name="working-with-split-view-controllers"></a>Trabalhando com os controladores de exibição de divisão

Como mencionado acima, um controlador de exibição de divisão é geralmente usado em situações em que você está exibindo o conteúdo filtrado para o usuário. As principais categorias são exibidas à esquerda no modo de exibição mestre e os resultados filtrados à direita no modo de exibição de detalhes com base na seleção do usuário.

<a name="Accessing-Master-and-Detail" />

### <a name="accessing-master-and-detail"></a>Acessando o mestre e detalhes

Se você precisar acessar o mestre e controladores de exibição de detalhes de forma programática, use o `ViewControllers` propriedade do controlador de exibição de divisão. Por exemplo:

```csharp
// Gain access to master and detail view controllers
var masterController = ViewControllers [0] as MasterViewController;
var detailController = ViewControllers [1] as DetailViewController;
```

Ela é apresentada como uma matriz, onde o primeiro elemento (0) no controlador de exibição mestre e o segundo elemento (1) são o detalhe.

<a name="Accessing-Detail-from-Master" />

### <a name="accessing-detail-from-master"></a>Acessando os detalhes do mestre

Como você geralmente está exibindo informações detalhadas na exibição de detalhes com base na seleção do usuário no mestre, você precisará de uma maneira de acessar os detalhes do servidor mestre.

A maneira mais fácil de fazer isso é expor uma propriedade em sua classe de controlador de exibição mestre, por exemplo:

```csharp
public DetailViewController DetailController { get; set;}
```

No controlador de exibição de divisão, substituir o `ViewDidLoad` método e empate a dois modos de exibição juntos. Por exemplo:

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
- **AllVisible** -exibe o mestre e a controladores de exibição de detalhes lado a lado. Isso é normal, apresentação padrão.
- **PrimaryOverlay** -o controlador de exibição de detalhes se estende em e é abordado pelo mestre.

Para obter o estado de apresentação atual, use o `DisplayMode` propriedade do controlador de exibição de divisão.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo abordou a projetar e trabalhar com controladores de exibição de divisão dentro de um aplicativo xamarin. tvos.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
