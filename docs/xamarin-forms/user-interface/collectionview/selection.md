---
title: Seleção de CollectionView do Xamarin. Forms
description: Por padrão, a seleção CollectionView está desabilitada. No entanto, uma seleção única e múltipla pode ser habilitada.
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: f1a3e8bb8959588e64339f70268370440f356be9
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68738969"
---
# <a name="xamarinforms-collectionview-selection"></a>Seleção de CollectionView do Xamarin. Forms

![](~/media/shared/preview.png "Esta API está atualmente em pré-lançamento")

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define as seguintes propriedades que controlam a seleção de itens:

- [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode), do tipo [`SelectionMode`](xref:Xamarin.Forms.SelectionMode), o modo de seleção.
- [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem), do tipo `object`, o item selecionado na lista. Essa propriedade tem um modo de associação padrão `TwoWay`de e tem um `null` valor quando nenhum item é selecionado.
- [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems), do tipo `IList<object>`, os itens selecionados na lista. Essa propriedade tem um modo de associação padrão `OneWay`de e tem um `null` valor quando nenhum item é selecionado.
- [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand), do tipo `ICommand`, que é executado quando o item selecionado é alterado.
- [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter), do tipo `object`, que é o parâmetro que é passado para o `SelectionChangedCommand`.

Todas essas propriedades são apoiadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), o que significa que essas propriedades podem ser o destino de vinculações de dados.

Por padrão, [`CollectionView`](xref:Xamarin.Forms.CollectionView) a seleção está desabilitada. No entanto, esse comportamento pode ser alterado definindo [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) o valor da propriedade como um [`SelectionMode`](xref:Xamarin.Forms.SelectionMode) dos membros da enumeração:

- `None`– indica que os itens não podem ser selecionados. Este é o valor padrão.
- `Single`– indica que um único item pode ser selecionado, com o item selecionado sendo realçado.
- `Multiple`– indica que vários itens podem ser selecionados, com os itens selecionados sendo realçados.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define um [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento que é acionado quando [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) a propriedade é alterada, devido ao usuário selecionar um item da lista ou quando um aplicativo define a propriedade. Além disso, esse evento também é acionado quando [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) a propriedade é alterada. O [`SelectionChangedEventArgs`](xref:Xamarin.Forms.SelectionChangedEventArgs) objeto que acompanha o `SelectionChanged` evento tem duas propriedades, ambas do tipo `IReadOnlyList<object>`:

- `PreviousSelection`– a lista de itens que foram selecionados antes da seleção ser alterada.
- `CurrentSelection`– a lista de itens selecionados após a alteração da seleção.

## <a name="single-selection"></a>Seleção única

Quando a [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriedade é definida como `Single`, [`CollectionView`](xref:Xamarin.Forms.CollectionView) um único item no pode ser selecionado. Quando um item é selecionado, a [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriedade será definida como o valor do item selecionado. Quando essa propriedade é alterada, [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) o é executado (com o valor [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) do que está sendo passado `ICommand`para o) e [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) o evento é acionado.

O exemplo de XAML a seguir [`CollectionView`](xref:Xamarin.Forms.CollectionView) mostra um que pode responder à seleção de um único item:

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

Neste exemplo de código, o `OnCollectionViewSelectionChanged` manipulador de eventos é executado quando [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) o evento é acionado, com o manipulador de eventos recuperando o item selecionado anteriormente e o item selecionado atual:

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

As capturas de tela a seguir mostram a seleção [`CollectionView`](xref:Xamarin.Forms.CollectionView)de item único em um:

[ ![Captura de tela de uma lista vertical CollectionView com seleção única, em lista vertical de CollectionView e Android](selection-images/single-selection.png "com seleção única") ] (selection-images/single-selection-large.png#lightbox "Lista vertical CollectionView com seleção única")

## <a name="multiple-selection"></a>Seleção múltipla

Quando a [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriedade é definida como `Multiple` [`CollectionView`](xref:Xamarin.Forms.CollectionView) , vários itens no podem ser selecionados. Quando os itens são selecionados, [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) a propriedade será definida para os itens selecionados. Quando essa propriedade é alterada, [`SelectionChangedCommand`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommand) o é executado (com o valor [`SelectionChangedCommandParameter`](xref:Xamarin.Forms.SelectableItemsView.SelectionChangedCommandParameter) do que está sendo passado `ICommand`para o) e [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) o evento é acionado.

O exemplo de XAML a seguir [`CollectionView`](xref:Xamarin.Forms.CollectionView) mostra um que pode responder a seleção de vários itens:

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

Neste exemplo de código, o `OnCollectionViewSelectionChanged` manipulador de eventos é executado quando [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) o evento é acionado, com o manipulador de eventos recuperando os itens anteriormente selecionados e os itens selecionados atuais:

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

As capturas de tela a seguir mostram a seleção [`CollectionView`](xref:Xamarin.Forms.CollectionView)de vários itens em um:

[ ![Captura de tela de uma lista vertical CollectionView com seleção múltipla, em](selection-images/multiple-selection.png "lista vertical de CollectionView e Android com seleção múltipla") ] (selection-images/multiple-selection-large.png#lightbox "Lista vertical CollectionView com seleção múltipla")

## <a name="single-pre-selection"></a>Seleção única

Quando a [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriedade é definida como `Single`, [`CollectionView`](xref:Xamarin.Forms.CollectionView) um único item no pode ser pré-selecionado definindo a [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriedade para o item. O exemplo de XAML a seguir `CollectionView` mostra um que seleciona previamente um único item:

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
> A [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriedade tem um modo de associação padrão `TwoWay`de.

O [ `SelectedItem` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) associa dados de propriedade para o `SelectedMonkey` propriedade do modelo de exibição conectado, o que é do tipo `Monkey`. Por padrão, uma `TwoWay` associação é usada para que, se o usuário alterar o item selecionado, o valor `SelectedMonkey` da propriedade será definido como o objeto selecionado `Monkey` . A `SelectedMonkey` propriedade é definida `MonkeysViewModel` na classe e é definida como `Monkeys` o quarto item da coleção:

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

[ ![Captura de tela de uma lista vertical de CollectionView com seleção única, na lista vertical de CollectionView do Android e Ios](selection-images/single-pre-selection.png "com seleção única") ] (selection-images/single-pre-selection-large.png#lightbox "Lista vertical CollectionView com seleção única")

## <a name="multiple-pre-selection"></a>Várias seleções prévias

Quando a [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriedade é definida como `Multiple` [`CollectionView`](xref:Xamarin.Forms.CollectionView) , vários itens no podem ser previamente selecionados. O exemplo de XAML a seguir `CollectionView` mostra um que permitirá a seleção prévia de vários itens:

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
> A [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) propriedade tem um modo de associação padrão `OneWay`de.

O [ `SelectedItems` ](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) associa dados de propriedade para o `SelectedMonkeys` propriedade do modelo de exibição conectado, o que é do tipo `ObservableCollection<object>`. A `SelectedMonkeys` propriedade é definida `MonkeysViewModel` na classe e é definida para o segundo, o quarto `Monkeys` e o quinto itens na coleção:

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

[ ![Captura de tela de uma lista vertical de CollectionView com várias seleções, na lista vertical de CollectionView do Android e Ios](selection-images/multiple-pre-selection.png "com várias seleções prévias") ] (selection-images/multiple-pre-selection-large.png#lightbox "Lista vertical CollectionView com várias seleções prévias")

## <a name="clearing-selections"></a>Limpando seleções

As [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriedades [`SelectedItems`](xref:Xamarin.Forms.SelectableItemsView.SelectedItems) e podem ser limpas definindo-as, ou os objetos aos quais eles se associam, ao `null`.

## <a name="change-selected-item-color"></a>Alterar a cor do item selecionado

[`CollectionView`](xref:Xamarin.Forms.CollectionView)tem um `Selected` [`VisualState`](xref:Xamarin.Forms.VisualState) que pode ser usado para iniciar uma alteração visual no item selecionado no `CollectionView`. Um caso de uso comum para `VisualState` isso é alterar a cor do plano de fundo do item selecionado, que é mostrado no exemplo de XAML a seguir:

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
> O [`Style`](xref:Xamarin.Forms.Style) que contém o `Selected`deveter um [`TargetType`valorde](xref:Xamarin.Forms.Style.TargetType) propriedade que é o tipo do elemento raiz do `ItemTemplate` [,queédefinidocomoovalordapropriedade.`DataTemplate`](xref:Xamarin.Forms.DataTemplate) `VisualState`

Neste exemplo, o valor [`Style.TargetType`](xref:Xamarin.Forms.Style.TargetType) da propriedade é definido como `Grid` porque [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) o elemento raiz do é um [`Grid`](xref:Xamarin.Forms.Grid). O `Selected` [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) [especificaque`CollectionView`](xref:Xamarin.Forms.CollectionView) quando um item no é selecionado, o do item será definido como `LightSkyBlue`: [`VisualState`](xref:Xamarin.Forms.VisualState)

[ ![Captura de tela de uma lista vertical CollectionView com uma cor de seleção única personalizada, em](selection-images/single-selection-color.png "lista vertical de CollectionView e Android com uma cor de seleção única personalizada") ] (selection-images/single-selection-color-large.png#lightbox "Lista vertical CollectionView com uma cor de seleção única personalizada")

Para obter mais informações sobre os Estados visuais, consulte [Gerenciador de estado visual do Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-selection"></a>Desabilitar seleção

[`CollectionView`](xref:Xamarin.Forms.CollectionView)a seleção é desabilitada por padrão. No entanto, `CollectionView` se uma opção tiver a seleção habilitada, ela poderá [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) ser desabilitada definindo a propriedade como `None`:

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

Quando a [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriedade é definida como `None`, os itens em [`CollectionView`](xref:Xamarin.Forms.CollectionView) não podem ser selecionados, [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) a propriedade permanecerá `null`e o [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento não será acionado.

> [!NOTE]
> Quando um item tiver sido selecionado e a [`SelectionMode`](xref:Xamarin.Forms.SelectableItemsView.SelectionMode) propriedade for alterada de `Single` para `None`, a [`SelectedItem`](xref:Xamarin.Forms.SelectableItemsView.SelectedItem) propriedade será definida como `null` e o [`SelectionChanged`](xref:Xamarin.Forms.SelectableItemsView.SelectionChanged) evento será acionado com uma propriedade vazia `CurrentSelection` .

## <a name="related-links"></a>Links relacionados

- [CollectionView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Gerenciador de estado visual do Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md)
