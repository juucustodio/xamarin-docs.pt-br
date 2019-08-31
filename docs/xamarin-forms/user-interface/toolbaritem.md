---
title: ToolbarItem Xamarin. Forms
description: A classe ToolbarItem é um tipo especial de botão usado na barra de navegação de um aplicativo.
ms.prod: xamarin
ms.assetId: CC737D54-0280-46BD-A2BC-A0FB67DDD6A1
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/29/2019
ms.openlocfilehash: b361bc988b66d38fc33ceb866a2c5ec83153f8d0
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200018"
---
# <a name="xamarinforms-toolbaritem"></a>ToolbarItem Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)

A classe Xamarin. [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) Forms é um tipo especial de botão que pode ser adicionado à coleção `Page` de `ToolbarItems` um objeto. Cada `ToolbarItem` objeto será exibido como um botão na barra de navegação do aplicativo. Uma `ToolbarItem` instância pode ter um ícone e aparecer como um item de menu primário ou secundário. A `ToolbarItem` classe é herdada de [`MenuItem`](xref:Xamarin.Forms.MenuItem).

As capturas de tela `ToolbarItem` a seguir mostram objetos na barra de navegação no Ios e no Android:

!["Captura de tela de demonstração do ToolbarItem no Android e IOS"](toolbaritem-images/toolbaritem-device-screenshot.png "Captura de tela de demonstração do ToolbarItem no Android e Ios")

A `ToolbarItem` classe define as seguintes propriedades:

* [`Order`](xref:Xamarin.Forms.ToolbarItem.Order)é um `ToolbarItemOrder` valor de enumeração que determina se `ToolbarItem` a instância é exibida no menu primário ou secundário.
* [`Priority`](xref:Xamarin.Forms.ToolbarItem.Priority)é um `integer` valor que determina a ordem de exibição dos itens na `Page` `ToolbarItems` coleção de um objeto.

A `ToolbarItem` classe herda as seguintes propriedades normalmente usadas `MenuItem` da classe:

* [`Command`](xref:Xamarin.Forms.MenuItem.Command)é um `ICommand` que permite a vinculação de ações do usuário, como toques ou cliques de dedos, a comandos definidos em um ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)é um `object` que especifica o parâmetro que deve ser passado para o `Command`.
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource)é um `ImageSource` valor que determina o ícone de exibição em `ToolbarItem` um objeto.
* [`Text`](xref:Xamarin.Forms.MenuItem.Text)é um `string` que determina o texto de exibição em `ToolbarItem` um objeto.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos para que `ToolbarItem` uma instância possa ser o destino de associações de dados.

> [!NOTE]
> Uma alternativa para criar uma barra de [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) ferramentas a partir de objetos [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) é definir a propriedade anexada como uma classe de layout que contenha várias exibições. Para obter mais informações, consulte [exibindo modos de exibição na barra de navegação](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md#displaying-views-in-the-navigation-bar).

## <a name="create-a-toolbaritem"></a>Criar um ToolbarItem

Um `ToolbarItem` objeto pode ser instanciado em XAML. As `Text` propriedades `IconImageSource` e podem ser definidas para determinar como o botão é exibido na barra de navegação. O exemplo a seguir mostra como criar uma `ToolbarItem` instância de um com algumas propriedades comuns definidas:

```xaml
<ToolbarItem Text="Example Item"
             IconImageSource="example_icon.png"
             Order="Primary"
             Priority="0" />
```

Este exemplo resultará em um `ToolbarItem` objeto que tem texto, um ícone e aparece primeiro na área da barra de navegação principal. Um `ToolbarItem` também pode ser criado no código e adicionado `ToolbarItems` à coleção:

```csharp
ToolbarItem item = new ToolbarItem
{
    Text = "Example Item",
    IconImageSource = ImageSource.FromFile("example_icon.png"),
    Order = ToolbarItemOrder.Primary,
    Priority = 0
};

// "this" refers to a Page object
this.ToolbarItems.Add(item);
```

O arquivo representado pelo `string`, fornecido como a `IconImageSource` Propriedade, deve existir em cada projeto de plataforma.

> [!NOTE]
> Os ativos de imagem são tratados de forma diferente em cada plataforma. Um `ImageSource` pode vir de fontes, incluindo um arquivo local ou recurso incorporado, um URI ou um fluxo. Para obter mais informações sobre como `IconImageSource` definir a propriedade e as imagens no xamarin. Forms, consulte [imagens no xamarin. Forms](~/xamarin-forms/user-interface/images.md).

## <a name="define-button-behavior"></a>Definir comportamento do botão

A `ToolbarItem` classe herda o `Clicked` evento da `MenuItem` classe. Um manipulador de eventos pode ser anexado ao `Clicked` evento para reagir a toques ou cliques em `ToolbarItem` instâncias em XAML:

```xaml
<ToolbarItem ...
             Clicked="OnItemClicked" />
```

Um manipulador de eventos também pode ser anexado no código:

```csharp
ToolbarItem item = new ToolbarItem { ... }
item.Clicked += OnItemClicked;
```

Os exemplos anteriores referenciaram um manipulador de `OnItemClicked` eventos. O código a seguir mostra uma implementação de exemplo:

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    ToolbarItem item = (ToolbarItem)sender;
    messageLabel.Text = $"You clicked the \"{item.Text}\" toolbar item.";
}
```

`ToolbarItem`os objetos também podem usar `Command` as `CommandParameter` Propriedades e para reagir à entrada do usuário sem manipuladores de eventos. Para obter mais informações sobre `ICommand` a interface e a vinculação de dados MVVM, consulte [comportamento MVVM do Xamarin. Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md#define-menuitem-behavior-with-mvvm).

## <a name="primary-and-secondary-menus"></a>Menus primários e secundários

O `ToolbarItemOrder` enum tem `Default`os `Primary`valores, `Secondary` e.

Quando a `Order` propriedade for definida como `Primary`, o `ToolbarItem` objeto será exibido na barra de navegação principal em todas as plataformas. `ToolbarItem`os objetos são priorizados sobre o título da página, que será truncado para liberar espaço para os itens. As capturas de tela `ToolbarItem` a seguir mostram objetos no menu principal no Ios e no Android:

!["Captura de tela de menu primário do ToolbarItem Android e IOS"](toolbaritem-images/toolbaritem-primary-menu.png "Captura de tela do menu principal do ToolbarItem no Android e Ios")

Quando a `Order` propriedade é definida como `Secondary`, o comportamento varia entre as plataformas. No UWP e no Android, `Secondary` o menu itens aparece como três pontos que podem ser tocados ou clicados para revelar os itens em uma lista vertical. No Ios, o `Secondary` menu itens é exibido abaixo da barra de navegação como uma lista horizontal. As capturas de tela a seguir mostram um menu secundário no iOS e no Android:

!["Captura de tela de menu secundário ToolbarItem Android e IOS"](toolbaritem-images/toolbaritem-secondary-menu.png "Captura de tela de menu secundário ToolbarItem no Android e Ios")

> [!WARNING]
> O comportamento do `ToolbarItem` ícone em objetos que `Order` têm sua propriedade `Secondary` definida como está inconsistente entre plataformas. Evite definir a `IconImageSource` Propriedade em itens que aparecem no menu secundário.

## <a name="related-links"></a>Links relacionados

* [Demonstrações do ToolbarItem](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)
* [Imagens no Xamarin. Forms](~/xamarin-forms/user-interface/images.md)
* [Xamarin. Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md)
