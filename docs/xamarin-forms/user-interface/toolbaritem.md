---
title: ToolbarItem Xamarin. Forms
description: A classe ToolbarItem é um tipo especial de botão usado na barra de navegação de um aplicativo.
ms.prod: xamarin
ms.assetId: CC737D54-0280-46BD-A2BC-A0FB67DDD6A1
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/29/2019
ms.openlocfilehash: afdf9029f836ac8e55b2bb338b31f669af946c12
ms.sourcegitcommit: 6d86aac422d6ce2131930d18ada161d117c8c61b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2020
ms.locfileid: "78291603"
---
# <a name="xamarinforms-toolbaritem"></a>ToolbarItem Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)

A classe [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) Xamarin. Forms é um tipo especial de botão que pode ser adicionado à coleção de `ToolbarItems` de um objeto `Page`. Cada objeto de `ToolbarItem` aparecerá como um botão na barra de navegação do aplicativo. Uma instância de `ToolbarItem` pode ter um ícone e aparecer como um item de menu primário ou secundário. A classe de `ToolbarItem` herda de [`MenuItem`](xref:Xamarin.Forms.MenuItem).

As capturas de tela a seguir mostram `ToolbarItem` objetos na barra de navegação no iOS e no Android:

!["Captura de tela de demonstração do ToolbarItem no Android e iOS"](toolbaritem-images/toolbaritem-device-screenshot.png "Captura de tela de demonstração do ToolbarItem no Android e iOS")

A classe `ToolbarItem` define as seguintes propriedades:

* [`Order`](xref:Xamarin.Forms.ToolbarItem.Order) é um valor de enumeração `ToolbarItemOrder` que determina se a instância de `ToolbarItem` é exibida no menu primário ou secundário.
* [`Priority`](xref:Xamarin.Forms.ToolbarItem.Priority) é um valor de `integer` que determina a ordem de exibição dos itens na coleção de `ToolbarItems` de um objeto de `Page`.

A classe `ToolbarItem` herda as seguintes propriedades normalmente usadas da classe `MenuItem`:

* [`Command`](xref:Xamarin.Forms.MenuItem.Command) é um `ICommand` que permite a vinculação de ações do usuário, como toques ou cliques de dedos, a comandos definidos em um ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) é um `object` que especifica o parâmetro que deve ser passado para o `Command`.
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) é um valor de `ImageSource` que determina o ícone de exibição em um objeto `ToolbarItem`.
* [`Text`](xref:Xamarin.Forms.MenuItem.Text) é uma `string` que determina o texto de exibição em um objeto `ToolbarItem`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos para que uma instância de `ToolbarItem` possa ser o destino de associações de dados.

> [!NOTE]
> Uma alternativa para criar uma barra de ferramentas a partir de [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) objetos é definir a propriedade [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) anexada como uma classe de layout que contém várias exibições. Para obter mais informações, consulte [exibindo modos de exibição na barra de navegação](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md#displaying-views-in-the-navigation-bar).

## <a name="create-a-toolbaritem"></a>Criar um ToolbarItem

Um objeto `ToolbarItem` pode ser instanciado em XAML. As propriedades `Text` e `IconImageSource` podem ser definidas para determinar como o botão é exibido na barra de navegação. O exemplo a seguir mostra como criar uma instância de um `ToolbarItem` com algumas propriedades comuns definidas e adicioná-lo à coleção de `ToolbarItems` de um `ContentPage`:

```xaml
<ContentPage.ToolbarItems>
    <ToolbarItem Text="Example Item"
                 IconImageSource="example_icon.png"
                 Order="Primary"
                 Priority="0" />
</ContentPage.ToolbarItems>
```

Este exemplo resultará em um objeto `ToolbarItem` que tem texto, um ícone e aparece primeiro na área da barra de navegação principal. Um `ToolbarItem` também pode ser criado no código e adicionado à coleção de `ToolbarItems`:

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

O arquivo representado pelo `string`, fornecido como a propriedade `IconImageSource`, deve existir em cada projeto de plataforma.

> [!NOTE]
> Os ativos de imagem são tratados de forma diferente em cada plataforma. Um `ImageSource` pode vir de fontes, incluindo um arquivo local ou recurso incorporado, um URI ou um fluxo. Para obter mais informações sobre como definir a propriedade de `IconImageSource` e as imagens no Xamarin. Forms, consulte [imagens no xamarin. Forms](~/xamarin-forms/user-interface/images.md).

## <a name="define-button-behavior"></a>Definir comportamento do botão

A classe `ToolbarItem` herda o evento `Clicked` da classe `MenuItem`. Um manipulador de eventos pode ser anexado ao evento `Clicked` para reagir a toques ou cliques em instâncias de `ToolbarItem` em XAML:

```xaml
<ToolbarItem ...
             Clicked="OnItemClicked" />
```

Um manipulador de eventos também pode ser anexado no código:

```csharp
ToolbarItem item = new ToolbarItem { ... }
item.Clicked += OnItemClicked;
```

Os exemplos anteriores referenciaram um manipulador de eventos `OnItemClicked`. O código a seguir mostra uma implementação de exemplo:

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    ToolbarItem item = (ToolbarItem)sender;
    messageLabel.Text = $"You clicked the \"{item.Text}\" toolbar item.";
}
```

`ToolbarItem` objetos também podem usar as propriedades `Command` e `CommandParameter` para reagir à entrada do usuário sem manipuladores de eventos. Para obter mais informações sobre a interface de `ICommand` e a vinculação de dados MVVM, consulte [comportamento MVVM do Xamarin. Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md#define-menuitem-behavior-with-mvvm).

## <a name="enable-or-disable-a-toolbaritem-at-runtime"></a>Habilitar ou desabilitar um ToolbarItem em tempo de execução

Para habilitar a desabilitação de um `ToolbarItem` em tempo de execução, vincule sua propriedade `Command` a uma implementação de `ICommand` e verifique se um delegado de `canExecute` habilita e desabilita o `ICommand` conforme apropriado.

Para obter mais informações, consulte [habilitar ou desabilitar um MenuItem em tempo de execução](menuitem.md#enable-or-disable-a-menuitem-at-runtime).

## <a name="primary-and-secondary-menus"></a>Menus primários e secundários

O `ToolbarItemOrder` enum tem os valores `Default`, `Primary`e `Secondary`.

Quando a propriedade `Order` estiver definida como `Primary`, o objeto `ToolbarItem` aparecerá na barra de navegação principal em todas as plataformas. `ToolbarItem` objetos são priorizados sobre o título da página, que será truncado para liberar espaço para os itens. As capturas de tela a seguir mostram `ToolbarItem` objetos no menu principal no iOS e no Android:

!["Captura de tela de menu primário do ToolbarItem Android e iOS"](toolbaritem-images/toolbaritem-primary-menu.png "Captura de tela do menu principal do ToolbarItem no Android e iOS")

Quando a propriedade `Order` é definida como `Secondary`, o comportamento varia entre as plataformas. No UWP e no Android, o menu `Secondary` itens aparece como três pontos que podem ser tocados ou clicados para revelar os itens em uma lista vertical. No iOS, o menu `Secondary` itens é exibido abaixo da barra de navegação como uma lista horizontal. As capturas de tela a seguir mostram um menu secundário no iOS e no Android:

!["Captura de tela de menu secundário ToolbarItem Android e iOS"](toolbaritem-images/toolbaritem-secondary-menu.png "Captura de tela de menu secundário ToolbarItem no Android e iOS")

> [!WARNING]
> Comportamento de ícone em objetos `ToolbarItem` que têm sua propriedade `Order` definida como `Secondary` é inconsistente entre plataformas. Evite definir a propriedade `IconImageSource` em itens que aparecem no menu secundário.

## <a name="related-links"></a>Links relacionados

* [Demonstrações do ToolbarItem](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)
* [Imagens no Xamarin. Forms](~/xamarin-forms/user-interface/images.md)
* [MenuItem do Xamarin.Forms](~/xamarin-forms/user-interface/menuitem.md)
