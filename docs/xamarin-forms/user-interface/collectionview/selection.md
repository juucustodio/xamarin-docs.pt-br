---
title: Seleção de CollectionView do Xamarin. Forms
description: Por padrão, a seleção CollectionView está desabilitada. No entanto, uma seleção única e múltipla pode ser habilitada.
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: a4cc237ef738edeccf66f1a91a010e4831c1c72f
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304467"
---
# <a name="xamarinforms-collectionview-selection"></a>Seleção de CollectionView do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define as seguintes propriedades que controlam a seleção de itens:

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode), do tipo [`SelectionMode`](xref:Xamarin.Forms.SelectionMode), o modo de seleção.
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem), do tipo `object`, o item selecionado na lista. Essa propriedade tem um modo de associação padrão de `TwoWay`e tem um valor de `null` quando nenhum item é selecionado.
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems), do tipo `IList<object>`, os itens selecionados na lista. Essa propriedade tem um modo de associação padrão de `OneWay`e tem um valor de `null` quando nenhum item é selecionado.
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand), do tipo `ICommand`, que é executado quando o item selecionado é alterado.
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter), do tipo `object`, que é o parâmetro que é passado para o `SelectionChangedCommand`.

Todas essas propriedades são apoiadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), o que significa que essas propriedades podem ser o destino de vinculações de dados.

Por padrão, [`CollectionView`](xref:Xamarin.Forms.CollectionView) seleção está desabilitada. No entanto, esse comportamento pode ser alterado definindo o valor da propriedade [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) como um dos membros da enumeração [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) :

- `None` – indica que os itens não podem ser selecionados. Este é o valor padrão.
- `Single` – indica que um único item pode ser selecionado, com o item selecionado sendo realçado.
- `Multiple` – indica que vários itens podem ser selecionados, com os itens selecionados sendo realçados.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define um evento de [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) que é acionado quando a propriedade [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) é alterada, devido ao usuário selecionar um item da lista ou quando um aplicativo define a propriedade. Além disso, esse evento também é acionado quando a propriedade [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) é alterada. O objeto [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) que acompanha o evento `SelectionChanged` tem duas propriedades, ambas as `IReadOnlyList<object>`de tipo:

- `PreviousSelection` – a lista de itens que foram selecionados antes da seleção ser alterada.
- `CurrentSelection` – a lista de itens selecionados após a alteração da seleção.

Além disso, [`CollectionView`](xref:Xamarin.Forms.CollectionView) tem um método `UpdateSelectedItems` que atualiza a propriedade [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) com uma lista de itens selecionados, enquanto apenas aciona uma única notificação de alteração.

## <a name="single-selection"></a>Seleção única

Quando a propriedade [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) é definida como `Single`, um único item no [`CollectionView`](xref:Xamarin.Forms.CollectionView) pode ser selecionado. Quando um item é selecionado, a propriedade [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) será definida como o valor do item selecionado. Quando essa propriedade é alterada, o [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) é executado (com o valor do [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) que está sendo passado para o `ICommand`) e o evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) é acionado.

O exemplo de XAML a seguir mostra um [`CollectionView`](xref:Xamarin.Forms.CollectionView) que pode responder à seleção de um único item:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

Neste exemplo de código, o manipulador de eventos `OnCollectionViewSelectionChanged` é executado quando o evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) é acionado, com o manipulador de eventos recuperando o item selecionado anteriormente e o item selecionado atual:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> O evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) pode ser acionado por alterações que ocorrem como resultado da alteração da propriedade [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) .

As capturas de tela a seguir mostram a seleção de item único em um [`CollectionView`](xref:Xamarin.Forms.CollectionView):

[![Captura de tela de uma lista vertical CollectionView com seleção única, no iOS e no Android](selection-images/single-selection.png "Lista vertical CollectionView com seleção única")](selection-images/single-selection-large.png#lightbox "Lista vertical CollectionView com seleção única")

## <a name="multiple-selection"></a>Seleção múltipla

Quando a propriedade [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) é definida como `Multiple`, vários itens na [`CollectionView`](xref:Xamarin.Forms.CollectionView) podem ser selecionados. Quando os itens são selecionados, a propriedade [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) será definida para os itens selecionados. Quando essa propriedade é alterada, o [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) é executado (com o valor do [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) que está sendo passado para o `ICommand`) e o evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) é acionado.

O exemplo de XAML a seguir mostra um [`CollectionView`](xref:Xamarin.Forms.CollectionView) que pode responder a seleção de vários itens:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

Neste exemplo de código, o manipulador de eventos `OnCollectionViewSelectionChanged` é executado quando o evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) é acionado, com o manipulador de eventos recuperando os itens anteriormente selecionados e os itens selecionados atuais:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> O evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) pode ser acionado por alterações que ocorrem como resultado da alteração da propriedade [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) .

As capturas de tela a seguir mostram a seleção de vários itens em um [`CollectionView`](xref:Xamarin.Forms.CollectionView):

[![Captura de tela de uma lista vertical CollectionView com seleção múltipla, no iOS e no Android](selection-images/multiple-selection.png "Lista vertical CollectionView com seleção múltipla")](selection-images/multiple-selection-large.png#lightbox "Lista vertical CollectionView com seleção múltipla")

## <a name="single-pre-selection"></a>Seleção única

Quando a propriedade [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) é definida como `Single`, um único item no [`CollectionView`](xref:Xamarin.Forms.CollectionView) pode ser pré-selecionado definindo a propriedade [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) para o item. O exemplo de XAML a seguir mostra um `CollectionView` que seleciona previamente um único item:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectedItem="{Binding SelectedMonkey}">
    ...
</CollectionView>
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemProperty, "SelectedMonkey");
```

> [!NOTE]
> A propriedade [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) tem um modo de associação padrão de `TwoWay`.

Os dados da propriedade [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) são associados à propriedade `SelectedMonkey` do modelo de exibição conectado, que é do tipo `Monkey`. Por padrão, uma associação de `TwoWay` é usada para que, se o usuário alterar o item selecionado, o valor da propriedade `SelectedMonkey` será definido como o objeto de `Monkey` selecionado. A propriedade `SelectedMonkey` é definida na classe `MonkeysViewModel` e é definida como o quarto item da coleção de `Monkeys`:

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    ...
    public ObservableCollection<Monkey> Monkeys { get; private set; }

    Monkey selectedMonkey;
    public Monkey SelectedMonkey
    {
        get
        {
            return selectedMonkey;
        }
        set
        {
            if (selectedMonkey != value)
            {
                selectedMonkey = value;
            }
        }
    }

    public MonkeysViewModel()
    {
        ...
        selectedMonkey = Monkeys.Skip(3).FirstOrDefault();
    }
    ...
}
```

Portanto, quando o [`CollectionView`](xref:Xamarin.Forms.CollectionView) for exibido, o quarto item da lista será pré-selecionado:

[![Captura de tela de uma lista vertical CollectionView com seleção única, no iOS e no Android](selection-images/single-pre-selection.png "Lista vertical CollectionView com seleção única")](selection-images/single-pre-selection-large.png#lightbox "Lista vertical CollectionView com seleção única")

## <a name="multiple-pre-selection"></a>Várias seleções prévias

Quando a propriedade [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) é definida como `Multiple`, vários itens na [`CollectionView`](xref:Xamarin.Forms.CollectionView) podem ser previamente selecionados. O exemplo de XAML a seguir mostra um `CollectionView` que habilitará a seleção prévia de vários itens:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectedItems="{Binding SelectedMonkeys}">
    ...
</CollectionView>
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemsProperty, "SelectedMonkeys");
```

> [!NOTE]
> A propriedade [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) tem um modo de associação padrão de `OneWay`.

Os dados da propriedade [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) são associados à propriedade `SelectedMonkeys` do modelo de exibição conectado, que é do tipo `ObservableCollection<object>`. A propriedade `SelectedMonkeys` é definida na classe `MonkeysViewModel` e é definida como o segundo, o quarto e o quinto itens na coleção `Monkeys`:

```csharp
namespace CollectionViewDemos.ViewModels
{
    public class MonkeysViewModel : INotifyPropertyChanged
    {
        ...
        ObservableCollection<object> selectedMonkeys;
        public ObservableCollection<object> SelectedMonkeys
        {
            get
            {
                return selectedMonkeys;
            }
            set
            {
                if (selectedMonkeys != value)
                {
                    selectedMonkeys = value;
                }
            }
        }

        public MonkeysViewModel()
        {
            ...
            SelectedMonkeys = new ObservableCollection<object>()
            {
                Monkeys[1], Monkeys[3], Monkeys[4]
            };
        }
        ...
    }
}
```

Portanto, quando o [`CollectionView`](xref:Xamarin.Forms.CollectionView) aparecer, o segundo, o quarto e o quinto itens da lista serão previamente selecionados:

[![Captura de tela de uma lista vertical CollectionView com várias seleções, no iOS e no Android](selection-images/multiple-pre-selection.png "Lista vertical CollectionView com várias seleções prévias")](selection-images/multiple-pre-selection-large.png#lightbox "Lista vertical CollectionView com várias seleções prévias")

## <a name="clear-selections"></a>Limpar seleções

As propriedades [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) e [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) podem ser limpas definindo-as ou os objetos aos quais eles se associam, para `null`.

## <a name="change-selected-item-color"></a>Alterar a cor do item selecionado

[`CollectionView`](xref:Xamarin.Forms.CollectionView) tem uma [`VisualState`](xref:Xamarin.Forms.VisualState) `Selected` que pode ser usada para iniciar uma alteração visual no item selecionado no `CollectionView`. Um caso de uso comum para essa `VisualState` é alterar a cor do plano de fundo do item selecionado, que é mostrado no exemplo de XAML a seguir:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="Grid">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="Normal" />
                        <VisualState x:Name="Selected">
                            <VisualState.Setters>
                                <Setter Property="BackgroundColor"
                                        Value="LightSkyBlue" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>
    <StackLayout Margin="20">
        <CollectionView ItemsSource="{Binding Monkeys}"
                        SelectionMode="Single">
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <Grid Padding="10">
                        ...
                    </Grid>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </StackLayout>
</ContentPage>
```

> [!IMPORTANT]
> O [`Style`](xref:Xamarin.Forms.Style) que contém o `Selected` `VisualState` deve ter um valor de propriedade [`TargetType`](xref:Xamarin.Forms.Style.TargetType) que seja o tipo do elemento raiz do [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que é definido como o valor da propriedade `ItemTemplate`.

Neste exemplo, o valor da propriedade [`Style.TargetType`](xref:Xamarin.Forms.Style.TargetType) é definido como `Grid` porque o elemento raiz da [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) é um [`Grid`](xref:Xamarin.Forms.Grid). O `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) especifica que quando um item no [`CollectionView`](xref:Xamarin.Forms.CollectionView) for selecionado, o [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) do item será definido como `LightSkyBlue`:

[![Captura de tela de uma lista vertical CollectionView com uma cor de seleção única personalizada, no iOS e no Android](selection-images/single-selection-color.png "Lista vertical CollectionView com uma cor de seleção única personalizada")](selection-images/single-selection-color-large.png#lightbox "Lista vertical CollectionView com uma cor de seleção única personalizada")

Para obter mais informações sobre os Estados visuais, consulte [Gerenciador de estado visual do Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-selection"></a>Desabilitar seleção

[`CollectionView`](xref:Xamarin.Forms.CollectionView) seleção está desabilitada por padrão. No entanto, se uma `CollectionView` tiver a seleção habilitada, ela poderá ser desabilitada definindo a propriedade [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) como `None`:

```xaml
<CollectionView ...
                SelectionMode="None" />
```

Este é o código C# equivalente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    SelectionMode = SelectionMode.None
};
```

Quando a propriedade [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) é definida como `None`, os itens na [`CollectionView`](xref:Xamarin.Forms.CollectionView) não podem ser selecionados, a propriedade [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) permanecerá `null`e o evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) não será disparado.

> [!NOTE]
> Quando um item tiver sido selecionado e a propriedade [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) for alterada de `Single` para `None`, a propriedade [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) será definida como `null` e o evento [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) será acionado com uma propriedade `CurrentSelection` vazia.

## <a name="related-links"></a>Links relacionados

- [CollectionView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Gerenciador de estado visual do Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
