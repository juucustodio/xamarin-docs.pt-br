---
title: Seleção de CollectionView de xamarin. Forms
description: Por padrão, a seleção de CollectionView está desabilitada. No entanto, a seleção única e múltipla pode ser habilitada.
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: dc01cf6bea9fe614cbfb53dcc4417ffb0e602c6f
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67512761"
---
# <a name="xamarinforms-collectionview-selection"></a>Seleção de CollectionView de xamarin. Forms

![](~/media/shared/preview.png "Esta API está atualmente em pré-lançamento")

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) Define as seguintes propriedades que controlam a seleção de item:

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode), do tipo [ `SelectionMode` ](xref:Xamarin.Forms.SelectionMode), o modo de seleção.
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem), do tipo `object`, o item selecionado na lista. Essa propriedade tem um modo de associação padrão do `TwoWay`e tem um `null` valor quando nenhum item está selecionado.
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems), do tipo `IList<object>`, os itens selecionados na lista. Essa propriedade tem um modo de associação padrão do `OneWay`e tem um `null` valor quando nenhum item estiver selecionado.
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand), do tipo `ICommand`, que é executado quando o item selecionado é alterado.
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter), do tipo `object`, que é o parâmetro que é passado para o `SelectionChangedCommand`.

Todas essas propriedades são apoiadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), o que significa que essas propriedades podem ser o destino de vinculações de dados.

Por padrão, [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) seleção está desabilitada. No entanto, esse comportamento pode ser alterado definindo a [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) valor de propriedade a um dos [ `SelectionMode` ](xref:Xamarin.Forms.SelectionMode) membros de enumeração:

- `None` – indica que os itens não podem ser selecionados. Este é o valor padrão.
- `Single` – indica que um único item pode ser selecionado, com o item selecionado que está sendo realçado.
- `Multiple` – indica que vários itens podem ser selecionados, com os itens selecionados que está sendo realçados.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define uma [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento que é disparado quando o [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriedade é alterada, seja porque o usuário selecionar um item da lista, ou quando um aplicativo define a propriedade. Além disso, esse evento também é disparado quando o [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) alterações de propriedade. O [ `SelectionChangedEventArgs` ](xref:Xamarin.Forms.SelectionChangedEventArgs) objeto que acompanha o `SelectionChanged` evento tem duas propriedades, ambos do tipo `IReadOnlyList<object>`:

- `PreviousSelection` – a lista de itens que foram selecionados, antes que a seleção é alterada.
- `CurrentSelection` – a lista de itens selecionados, após a alteração da seleção.

## <a name="single-selection"></a>Seleção única

Quando o [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) estiver definida como `Single`, um único item no [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) podem ser selecionados. Quando um item é selecionado, o [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriedade será definida como o valor do item selecionado. Quando essa propriedade é alterada, o [ `SelectionChangedCommand` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) é executada (com o valor da [ `SelectionChangedCommandParameter` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) sendo passado para o `ICommand`) e o [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento é acionado.

A exemplo XAML a seguir mostra uma [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) que pode responder a seleção de item único:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

Neste exemplo de código, o `OnCollectionViewSelectionChanged` manipulador de eventos é executado quando o [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento é acionado, com o manipulador de eventos ao recuperar o item selecionado anteriormente e o item selecionado atual:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> O [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) eventos podem ser acionados por alterações que ocorrem como resultado da alteração os [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriedade.

As capturas de tela a seguir mostram a seleção de item único em uma [ `CollectionView` ](xref:Xamarin.Forms.CollectionView):

[![Captura de tela de uma lista vertical de CollectionView com seleção única, no iOS e Android](selection-images/single-selection.png "CollectionView de lista vertical com seleção única")](selection-images/single-selection-large.png#lightbox "CollectionView de lista vertical com único seleção")

## <a name="multiple-selection"></a>Seleção múltipla

Quando o [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) estiver definida como `Multiple`, vários itens no [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) podem ser selecionados. Quando itens são selecionados, o [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propriedade será definida para os itens selecionados. Quando essa propriedade é alterada, o [ `SelectionChangedCommand` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) é executada (com o valor da [ `SelectionChangedCommandParameter` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) sendo passado para o `ICommand`) e o [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento é acionado.

A exemplo XAML a seguir mostra uma [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) que pode responder a seleção múltipla do item:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

Neste exemplo de código, o `OnCollectionViewSelectionChanged` manipulador de eventos é executado quando o [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento é acionado, com o manipulador de eventos ao recuperar os itens selecionados anteriormente e os itens selecionados atuais:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> O [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) eventos podem ser acionados por alterações que ocorrem como resultado da alteração os [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriedade.

As capturas de tela a seguir mostram várias seleção de item em uma [ `CollectionView` ](xref:Xamarin.Forms.CollectionView):

[![Captura de tela de uma lista vertical de CollectionView com seleção múltipla, no iOS e Android](selection-images/multiple-selection.png "CollectionView de lista vertical com seleção múltipla")](selection-images/multiple-selection-large.png#lightbox "CollectionView de lista vertical com seleção múltipla")

## <a name="single-pre-selection"></a>Pré-seleção de única

Quando o [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) estiver definida como `Single`, um único item no [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) previamente selecionadas, definindo o [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriedade para o item. A exemplo XAML a seguir mostra um `CollectionView` que pré-seleciona um único item:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectedItem="{Binding SelectedMonkey}">
    ...
</CollectionView>
```

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemProperty, "SelectedMonkey");
```

> [!NOTE]
> O [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriedade tem um modo de associação padrão do `TwoWay`.

O [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) associa dados de propriedade para o `SelectedMonkey` propriedade do modelo de exibição conectado, o que é do tipo `Monkey`. Por padrão, uma `TwoWay` associação é usada para que, se o usuário altera o item selecionado, o valor da `SelectedMonkey` propriedade será definida para selecionado `Monkey` objeto. O `SelectedMonkey` propriedade está definida na `MonkeysViewModel` classe e é definido como o quarto item do `Monkeys` coleção:

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

Portanto, quando o [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) for exibida, o quarto item na lista é pré-selecionada:

[![Captura de tela de uma lista vertical de CollectionView com pré-seleção de única, no iOS e Android](selection-images/single-pre-selection.png "CollectionView de lista vertical com pré-seleção de única")](selection-images/single-pre-selection-large.png#lightbox "lista vertical de CollectionView com a pré-seleção de única")

## <a name="multiple-pre-selection"></a>Seleção múltipla de pré-lançamento

Quando o [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) estiver definida como `Multiple`, vários itens no [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) pode ser pré-selecionado. A exemplo XAML a seguir mostra um `CollectionView` que permitirá a pré-seleção de vários itens:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}"
                SelectionMode="Multiple"
                SelectedItems="{Binding SelectedMonkeys}">
    ...
</CollectionView>
```

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Multiple
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemsProperty, "SelectedMonkeys");
```

> [!NOTE]
> O [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propriedade tem um modo de associação padrão do `OneWay`.

O [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) associa dados de propriedade para o `SelectedMonkeys` propriedade do modelo de exibição conectado, o que é do tipo `ObservableCollection<object>`. O `SelectedMonkeys` propriedade está definida no `MonkeysViewModel` classe e é definido como o segundo, quarto e quinto itens o `Monkeys` coleção:

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

Portanto, quando o [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) for exibida, o segundo, em quarto lugar, e o quintas itens na lista estão pré-selecionados:

[![Captura de tela de uma lista vertical de CollectionView com seleção múltipla de pré-lançamento, no iOS e Android](selection-images/multiple-pre-selection.png "CollectionView de lista vertical com seleção múltipla de pré-lançamento")](selection-images/multiple-pre-selection-large.png#lightbox "CollectionView vertical lista de seleção múltipla de pré-lançamento")

## <a name="clearing-selections"></a>Limpar seleções

O [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) e [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propriedades podem ser apagadas, definindo-os ou os objetos que eles se ligam ao, `null`.

## <a name="change-selected-item-color"></a>Alterar a cor do item selecionado

[`CollectionView`](xref:Xamarin.Forms.CollectionView) tem um `Selected` [ `VisualState` ](xref:Xamarin.Forms.VisualState) que pode ser usado para iniciar uma alteração visual para o item selecionado no `CollectionView`. Caso de uso de um comum para isso `VisualState` é alterar a cor do plano de fundo do item selecionado, que é mostrado no exemplo XAML a seguir:

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
> O [ `Style` ](xref:Xamarin.Forms.Style) que contém o `Selected` `VisualState` deve ter uma [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) valor da propriedade que é o tipo de elemento raiz do [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate), que é definido como o `ItemTemplate` valor da propriedade.

Neste exemplo, o [ `Style.TargetType` ](xref:Xamarin.Forms.Style.TargetType) o valor da propriedade é definido como `Grid` porque o elemento raiz do [ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView.ItemTemplate) é uma [ `Grid` ](xref:Xamarin.Forms.Grid). O `Selected` [ `VisualState` ](xref:Xamarin.Forms.VisualState) Especifica que quando um item no [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) for selecionada, o [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) do item será definido como `LightSkyBlue`:

[![Captura de tela de uma lista vertical de CollectionView com uma cor personalizada de seleção única, no iOS e Android](selection-images/single-selection-color.png "CollectionView de lista vertical com uma cor personalizada de seleção única") ] (selection-images/single-selection-color-large.png#lightbox " Lista vertical de CollectionView com uma cor personalizada de seleção única")

Para obter mais informações sobre estados visuais, consulte [Gerenciador de estado Visual do xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-selection"></a>Desabilitar seleção

[`CollectionView`](xref:Xamarin.Forms.CollectionView) seleção é desabilitada por padrão. No entanto, se um `CollectionView` tem seleção habilitada, ele pode ser desabilitado definindo o [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriedade para `None`:

```xaml
<CollectionView ...
                SelectionMode="None" />
```

O código C# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    SelectionMode = SelectionMode.None
};
```

Quando o [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) estiver definida como `None`, os itens no [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) não podem ser selecionadas, o [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) será de propriedade permanecem `null`e o [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento não será disparado.

> [!NOTE]
> Quando um item foi selecionado e o [ `SelectionMode` ](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriedade é alterada de `Single` para `None`, o [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriedade será definida como `null` e o [ `SelectionChanged` ](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento será acionado com um vazio `CurrentSelection` propriedade.

## <a name="related-links"></a>Links relacionados

- [CollectionView (amostra)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)
- [Xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)
