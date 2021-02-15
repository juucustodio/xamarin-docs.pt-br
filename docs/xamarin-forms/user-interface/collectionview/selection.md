---
title: Xamarin.Forms Seleção CollectionView
description: Por padrão, a seleção CollectionView está desabilitada. No entanto, uma seleção única e múltipla pode ser habilitada.
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fd4ac89b60cdac8509e5c057c698ef79f7e6e969
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375337"
---
# <a name="no-locxamarinforms-collectionview-selection"></a>Xamarin.Forms Seleção CollectionView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define as seguintes propriedades que controlam a seleção de itens:

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode), do tipo [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) , o modo de seleção.
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem), do tipo `object` , o item selecionado na lista. Essa propriedade tem um modo de associação padrão de `TwoWay` e tem um `null` valor quando nenhum item é selecionado.
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems), do tipo `IList<object>` , os itens selecionados na lista. Essa propriedade tem um modo de associação padrão de `OneWay` e tem um `null` valor quando nenhum item é selecionado.
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand), do tipo `ICommand` , que é executado quando o item selecionado é alterado.
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter), do tipo `object` , que é o parâmetro que é passado para o `SelectionChangedCommand` .

Todas essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

Por padrão, a [`CollectionView`](xref:Xamarin.Forms.CollectionView) seleção está desabilitada. No entanto, esse comportamento pode ser alterado definindo o [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) valor da propriedade como um dos [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) membros da enumeração:

- `None` – indica que os itens não podem ser selecionados. Esse é o valor padrão.
- `Single` – indica que um único item pode ser selecionado, com o item selecionado sendo realçado.
- `Multiple` – indica que vários itens podem ser selecionados, com os itens selecionados sendo realçados.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define um [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento que é acionado quando a [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriedade é alterada, devido ao usuário selecionar um item da lista ou quando um aplicativo define a propriedade. Além disso, esse evento também é acionado quando a [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propriedade é alterada. O [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) objeto que acompanha o `SelectionChanged` evento tem duas propriedades, ambas do tipo `IReadOnlyList<object>` :

- `PreviousSelection` – a lista de itens que foram selecionados antes da seleção ser alterada.
- `CurrentSelection` – a lista de itens selecionados após a alteração da seleção.

Além disso, [`CollectionView`](xref:Xamarin.Forms.CollectionView) o tem um `UpdateSelectedItems` método que atualiza a [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propriedade com uma lista de itens selecionados, enquanto apenas aciona uma única notificação de alteração.

## <a name="single-selection"></a>Seleção única

Quando a [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriedade é definida como `Single` , um único item no [`CollectionView`](xref:Xamarin.Forms.CollectionView) pode ser selecionado. Quando um item é selecionado, a [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriedade será definida como o valor do item selecionado. Quando essa propriedade é alterada, o [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) é executado (com o valor do [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) que está sendo passado para o `ICommand` ) e o [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento é acionado.

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

Neste exemplo de código, o `OnCollectionViewSelectionChanged` manipulador de eventos é executado quando o [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento é acionado, com o manipulador de eventos recuperando o item selecionado anteriormente e o item selecionado atual:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (e.PreviousSelection.FirstOrDefault() as Monkey)?.Name;
    string current = (e.CurrentSelection.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> O [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento pode ser acionado por alterações que ocorrem como resultado da alteração da [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriedade.

As capturas de tela a seguir mostram a seleção de item único em um [`CollectionView`](xref:Xamarin.Forms.CollectionView) :

[![Captura de tela de uma lista vertical CollectionView com seleção única, no iOS e no Android](selection-images/single-selection.png "Lista vertical CollectionView com seleção única")](selection-images/single-selection-large.png#lightbox "Lista vertical CollectionView com seleção única")

## <a name="multiple-selection"></a>Seleção múltipla

Quando a [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriedade é definida como `Multiple` , vários itens no [`CollectionView`](xref:Xamarin.Forms.CollectionView) podem ser selecionados. Quando os itens são selecionados, a [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propriedade será definida para os itens selecionados. Quando essa propriedade é alterada, o [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) é executado (com o valor do [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) que está sendo passado para o `ICommand` ) e o [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento é acionado.

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

Neste exemplo de código, o `OnCollectionViewSelectionChanged` manipulador de eventos é executado quando o [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento é acionado, com o manipulador de eventos recuperando os itens anteriormente selecionados e os itens selecionados atuais:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var previous = e.PreviousSelection;
    var current = e.CurrentSelection;
    ...
}
```

> [!IMPORTANT]
> O [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento pode ser acionado por alterações que ocorrem como resultado da alteração da [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriedade.

As capturas de tela a seguir mostram a seleção de vários itens em um [`CollectionView`](xref:Xamarin.Forms.CollectionView) :

[![Captura de tela de uma lista vertical CollectionView com seleção múltipla, no iOS e no Android](selection-images/multiple-selection.png "Lista vertical CollectionView com seleção múltipla")](selection-images/multiple-selection-large.png#lightbox "Lista vertical CollectionView com seleção múltipla")

## <a name="single-pre-selection"></a>Seleção única

Quando a [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriedade é definida como `Single` , um único item no [`CollectionView`](xref:Xamarin.Forms.CollectionView) pode ser pré-selecionado definindo a [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriedade para o item. O exemplo de XAML a seguir mostra um `CollectionView` que seleciona previamente um único item:

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
> A [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriedade tem um modo de associação padrão de `TwoWay` .

Os [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) dados de propriedade são associados à `SelectedMonkey` Propriedade do modelo de exibição conectado, que é do tipo `Monkey` . Por padrão, uma `TwoWay` associação é usada para que, se o usuário alterar o item selecionado, o valor da `SelectedMonkey` propriedade será definido como o objeto selecionado `Monkey` . A `SelectedMonkey` propriedade é definida na `MonkeysViewModel` classe e é definida como o quarto item da `Monkeys` coleção:

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

Portanto, quando [`CollectionView`](xref:Xamarin.Forms.CollectionView) aparece, o quarto item da lista é pré-selecionado:

[![Captura de tela de uma lista vertical CollectionView com seleção única, no iOS e no Android](selection-images/single-pre-selection.png "Lista vertical CollectionView com seleção única")](selection-images/single-pre-selection-large.png#lightbox "Lista vertical CollectionView com seleção única")

## <a name="multiple-pre-selection"></a>Várias seleções prévias

Quando a [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriedade é definida como `Multiple` , vários itens no [`CollectionView`](xref:Xamarin.Forms.CollectionView) podem ser previamente selecionados. O exemplo de XAML a seguir mostra um `CollectionView` que permitirá a seleção prévia de vários itens:

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
> A [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propriedade tem um modo de associação padrão de `OneWay` .

Os [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) dados de propriedade são associados à `SelectedMonkeys` Propriedade do modelo de exibição conectado, que é do tipo `ObservableCollection<object>` . A `SelectedMonkeys` propriedade é definida na `MonkeysViewModel` classe e é definida para o segundo, o quarto e o quinto itens na `Monkeys` coleção:

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

Portanto, quando [`CollectionView`](xref:Xamarin.Forms.CollectionView) aparece, o segundo, o quarto e o quinto itens da lista são previamente selecionados:

[![Captura de tela de uma lista vertical CollectionView com várias seleções, no iOS e no Android](selection-images/multiple-pre-selection.png "Lista vertical CollectionView com várias seleções prévias")](selection-images/multiple-pre-selection-large.png#lightbox "Lista vertical CollectionView com várias seleções prévias")

## <a name="clear-selections"></a>Limpar seleções

As [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) Propriedades e podem ser limpas definindo-as, ou os objetos aos quais eles se associam, ao `null` .

## <a name="change-selected-item-color"></a>Alterar a cor do item selecionado

[`CollectionView`](xref:Xamarin.Forms.CollectionView) tem um `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) que pode ser usado para iniciar uma alteração visual no item selecionado no `CollectionView` . Um caso de uso comum para isso `VisualState` é alterar a cor do plano de fundo do item selecionado, que é mostrado no exemplo de XAML a seguir:

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
> O [`Style`](xref:Xamarin.Forms.Style) que contém o `Selected` `VisualState` deve ter um [`TargetType`](xref:Xamarin.Forms.Style.TargetType) valor de propriedade que é o tipo do elemento raiz do [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , que é definido como o `ItemTemplate` valor da propriedade.

Neste exemplo, o [`Style.TargetType`](xref:Xamarin.Forms.Style.TargetType) valor da propriedade é definido como `Grid` porque o elemento raiz do [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) é um [`Grid`](xref:Xamarin.Forms.Grid) . O `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) especifica que quando um item no [`CollectionView`](xref:Xamarin.Forms.CollectionView) é selecionado, o [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) do item será definido como `LightSkyBlue` :

[![Captura de tela de uma lista vertical CollectionView com uma cor de seleção única personalizada, no iOS e no Android](selection-images/single-selection-color.png "Lista vertical CollectionView com uma cor de seleção única personalizada")](selection-images/single-selection-color-large.png#lightbox "Lista vertical CollectionView com uma cor de seleção única personalizada")

Para obter mais informações sobre os Estados visuais, consulte [ Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-selection"></a>Desabilitar seleção

[`CollectionView`](xref:Xamarin.Forms.CollectionView) a seleção é desabilitada por padrão. No entanto, se uma `CollectionView` opção tiver a seleção habilitada, ela poderá ser desabilitada definindo a [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriedade como `None` :

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

Quando a [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriedade é definida como `None` , os itens em [`CollectionView`](xref:Xamarin.Forms.CollectionView) não podem ser selecionados, a [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) Propriedade permanecerá `null` e o [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento não será acionado.

> [!NOTE]
> Quando um item tiver sido selecionado e a [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriedade for alterada de `Single` para `None` , a [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriedade será definida como `null` e o [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento será acionado com uma propriedade vazia `CurrentSelection` .

## <a name="related-links"></a>Links relacionados

- [CollectionView (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin.Forms Gerenciador de estado visual](~/xamarin-forms/user-interface/visual-state-manager.md)