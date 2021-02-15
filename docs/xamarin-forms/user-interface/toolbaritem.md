---
title: Xamarin.Forms ToolbarItem
description: A classe ToolbarItem é um tipo especial de botão usado na barra de navegação de um aplicativo.
ms.prod: xamarin
ms.assetId: CC737D54-0280-46BD-A2BC-A0FB67DDD6A1
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 07/29/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 59954a16cbf1fb01c974968848596138469ab9b2
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371203"
---
# <a name="no-locxamarinforms-toolbaritem"></a>Xamarin.Forms ToolbarItem

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)

A Xamarin.Forms [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) classe é um tipo especial de botão que pode ser adicionado à `Page` coleção de um objeto `ToolbarItems` . Cada `ToolbarItem` objeto será exibido como um botão na barra de navegação do aplicativo. Uma `ToolbarItem` instância pode ter um ícone e aparecer como um item de menu primário ou secundário. A `ToolbarItem` classe é herdada de [`MenuItem`](xref:Xamarin.Forms.MenuItem) .

As capturas de tela a seguir mostram `ToolbarItem` objetos na barra de navegação no Ios e no Android:

!["Captura de tela de demonstração do ToolbarItem no Android e iOS"](toolbaritem-images/toolbaritem-device-screenshot.png "Captura de tela de demonstração do ToolbarItem no Android e iOS")

A `ToolbarItem` classe define as seguintes propriedades:

* [`Order`](xref:Xamarin.Forms.ToolbarItem.Order) é um `ToolbarItemOrder` valor de enumeração que determina se a `ToolbarItem` instância é exibida no menu primário ou secundário.
* [`Priority`](xref:Xamarin.Forms.ToolbarItem.Priority) é um `integer` valor que determina a ordem de exibição dos itens na `Page` coleção de um objeto `ToolbarItems` .

A `ToolbarItem` classe herda as seguintes propriedades normalmente usadas da `MenuItem` classe:

* [`Command`](xref:Xamarin.Forms.MenuItem.Command) é um `ICommand` que permite a vinculação de ações do usuário, como toques ou cliques de dedos, a comandos definidos em um ViewModel.
* [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) é um `object` que especifica o parâmetro que deve ser passado para o `Command` .
* [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) é um `ImageSource` valor que determina o ícone de exibição em um `ToolbarItem` objeto.
* [`Text`](xref:Xamarin.Forms.MenuItem.Text) é um `string` que determina o texto de exibição em um `ToolbarItem` objeto.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos para que uma `ToolbarItem` instância possa ser o destino de associações de dados.

> [!NOTE]
> Uma alternativa para criar uma barra de ferramentas a partir de [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) objetos é definir a [`NavigationPage.TitleView`](xref:Xamarin.Forms.NavigationPage.TitleViewProperty) Propriedade anexada como uma classe de layout que contenha várias exibições. Para obter mais informações, consulte [exibindo modos de exibição na barra de navegação](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md#displaying-views-in-the-navigation-bar).

## <a name="create-a-toolbaritem"></a>Criar um ToolbarItem

Um `ToolbarItem` objeto pode ser instanciado em XAML. As `Text` `IconImageSource` Propriedades e podem ser definidas para determinar como o botão é exibido na barra de navegação. O exemplo a seguir mostra como criar uma instância de um `ToolbarItem` com algumas propriedades comuns definidas e adicioná-lo à `ContentPage` coleção de uma `ToolbarItems` :

```xaml
<ContentPage.ToolbarItems>
    <ToolbarItem Text="Example Item"
                 IconImageSource="example_icon.png"
                 Order="Primary"
                 Priority="0" />
</ContentPage.ToolbarItems>
```

Este exemplo resultará em um `ToolbarItem` objeto que tem texto, um ícone e aparece primeiro na área da barra de navegação principal. Um `ToolbarItem` também pode ser criado no código e adicionado à `ToolbarItems` coleção:

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

O arquivo representado pelo `string` , fornecido como a `IconImageSource` propriedade, deve existir em cada projeto de plataforma.

> [!NOTE]
> Os ativos de imagem são tratados de forma diferente em cada plataforma. Um `ImageSource` pode vir de fontes, incluindo um arquivo local ou recurso incorporado, um URI ou um fluxo. Para obter mais informações sobre como definir a `IconImageSource` propriedade e as imagens no Xamarin.Forms , consulte [imagens em Xamarin.Forms ](~/xamarin-forms/user-interface/images.md).

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

Os exemplos anteriores referenciaram um `OnItemClicked` manipulador de eventos. O código a seguir mostra uma implementação de exemplo:

```csharp
void OnItemClicked(object sender, EventArgs e)
{
    ToolbarItem item = (ToolbarItem)sender;
    messageLabel.Text = $"You clicked the \"{item.Text}\" toolbar item.";
}
```

`ToolbarItem` os objetos também podem usar `Command` as `CommandParameter` Propriedades e para reagir à entrada do usuário sem manipuladores de eventos. Para obter mais informações sobre a `ICommand` interface e a vinculação de dados MVVM, veja [ Xamarin.Forms comportamento MVVM MenuItem](~/xamarin-forms/user-interface/menuitem.md#define-menuitem-behavior-with-mvvm).

## <a name="enable-or-disable-a-toolbaritem-at-runtime"></a>Habilitar ou desabilitar um ToolbarItem em tempo de execução

Para habilitar a desabilitação de um `ToolbarItem` em tempo de execução, associe sua `Command` Propriedade a uma `ICommand` implementação e verifique se um `canExecute` delegado habilita e desabilita o `ICommand` conforme apropriado.

Para obter mais informações, consulte [habilitar ou desabilitar um MenuItem em tempo de execução](menuitem.md#enable-or-disable-a-menuitem-at-runtime).

## <a name="primary-and-secondary-menus"></a>Menus primários e secundários

O `ToolbarItemOrder` enum tem `Default` os `Primary` valores, `Secondary` e.

Quando a `Order` propriedade for definida como `Primary` , o `ToolbarItem` objeto será exibido na barra de navegação principal em todas as plataformas. `ToolbarItem` os objetos são priorizados sobre o título da página, que será truncado para liberar espaço para os itens. As capturas de tela a seguir mostram `ToolbarItem` objetos no menu principal no Ios e no Android:

!["Captura de tela de menu primário do ToolbarItem Android e iOS"](toolbaritem-images/toolbaritem-primary-menu.png "Captura de tela do menu principal do ToolbarItem no Android e iOS")

Quando a `Order` propriedade é definida como `Secondary` , o comportamento varia entre as plataformas. No UWP e no Android, o `Secondary` menu itens aparece como três pontos que podem ser tocados ou clicados para revelar os itens em uma lista vertical. No iOS, o `Secondary` menu itens é exibido abaixo da barra de navegação como uma lista horizontal. As capturas de tela a seguir mostram um menu secundário no iOS e no Android:

!["Captura de tela de menu secundário ToolbarItem Android e iOS"](toolbaritem-images/toolbaritem-secondary-menu.png "Captura de tela de menu secundário ToolbarItem no Android e iOS")

> [!WARNING]
> O comportamento do ícone em `ToolbarItem` objetos que têm sua `Order` propriedade definida como `Secondary` está inconsistente entre plataformas. Evite definir a `IconImageSource` propriedade em itens que aparecem no menu secundário.

## <a name="related-links"></a>Links relacionados

* [Demonstrações do ToolbarItem](/samples/xamarin/xamarin-forms-samples/userinterface-toolbaritem/)
* [Imagens em Xamarin.Forms](~/xamarin-forms/user-interface/images.md)
* [Xamarin.Forms MenuItem](~/xamarin-forms/user-interface/menuitem.md)