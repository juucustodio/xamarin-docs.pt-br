---
title: Definir o modo de seleção CollectionView de xamarin. Forms
description: Por padrão, a seleção de CollectionView está desabilitada. No entanto, a seleção única e múltipla pode ser habilitada.
ms.prod: xamarin
ms.assetid: 423D91C7-1E58-4735-9E80-58F11CDFD953
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/18/2019
ms.openlocfilehash: 441afb9348a85de61d35574bb9121c7de713a897
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58329755"
---
# <a name="set-collectionview-selection-mode"></a>Definir o modo de seleção CollectionView

![Visualizar](~/media/shared/preview.png)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

> [!IMPORTANT]
> O `CollectionView` atualmente é uma visualização e não tem algumas das suas funcionalidades planejada. Além disso, a API pode mudar conforme a implementação for concluída.

`CollectionView` Define as seguintes propriedades que controlam a seleção de item:

- `SelectionMode`, do tipo `SelectionMode`, o modo de seleção.
- `SelectedItem`, do tipo `object`, o item selecionado na lista. Essa propriedade tem um `null` valor quando nenhum item está selecionado.
- `SelectionChangedCommand`, do tipo `ICommand`, que é executado quando o item selecionado é alterado.
- `SelectionChangedCommandParameter`, do tipo `object`, que é o parâmetro que é passado para o `SelectionChangedCommand`.

Todas essas propriedades têm o respaldo [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser alvos de vinculações de dados.

Por padrão, `CollectionView` seleção está desabilitada. No entanto, esse comportamento pode ser alterado definindo a `SelectionMode` valor de propriedade a um do `SelectionMode` membros de enumeração:

- `None` – indica que os itens não podem ser selecionados. Este é o valor padrão.
- `Single` – indica que um único item pode ser selecionado, com o item selecionado que está sendo realçado.
- `Multiple` – indica que vários itens podem ser selecionados, com os itens selecionados que está sendo realçados.

`CollectionView` define uma `SelectionChanged` evento que é disparado quando o `SelectedItem` propriedade é alterada, seja porque o usuário selecionar um item da lista, ou quando um aplicativo define a propriedade. O `SelectionChangedEventArgs` objeto que acompanha o `SelectionChanged` evento tem duas propriedades, ambos do tipo `IReadOnlyList<object>`:

- `PreviousSelection` – a lista de itens que foram selecionados, antes que a seleção é alterada.
- `CurrentSelection` – a lista de itens selecionados, após a alteração da seleção.

## <a name="single-selection"></a>Seleção única

Quando o `SelectionMode` estiver definida como `Single`, um único item no `CollectionView` podem ser selecionados. Quando um item é selecionado, o `SelectedItem` propriedade será definida como o valor do item selecionado. Quando essa propriedade é alterada, o `SelectionChangedCommand` é executada (com o valor da `SelectionChangedCommandParameter` que está sendo passado para o `ICommand`) e o `SelectionChanged` evento é acionado.

A exemplo XAML a seguir mostra um `CollectionView` que pode responder a seleção de item único:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectionChanged="OnCollectionViewSelectionChanged">
    ...
</CollectionView>
```

O código c# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SelectionChanged += OnCollectionViewSelectionChanged;
```

Neste exemplo de código, o `OnCollectionViewSelectionChanged` manipulador de eventos é executado quando o `SelectionChanged` evento é acionado, com o manipulador de eventos ao recuperar o item selecionado anteriormente e o item selecionado atual:

```csharp
void OnCollectionViewSelectionChanged(object sender, SelectionChangedEventArgs e)
{
    string previous = (previousSelectedItems.FirstOrDefault() as Monkey)?.Name;
    string current = (currentSelectedItems.FirstOrDefault() as Monkey)?.Name;
    ...
}
```

> [!IMPORTANT]
> O `SelectionChanged` eventos podem ser acionados por alterações que ocorrem como resultado da alteração de `SelectionMode` propriedade.

As capturas de tela a seguir mostram a seleção de item único em um `CollectionView`:

[![Captura de tela de uma lista vertical de CollectionView com seleção única, no iOS e Android](selection-images/single-selection.png "CollectionView de lista vertical com seleção única")](selection-images/single-selection-large.png#lightbox "CollectionView de lista vertical com único seleção")

## <a name="pre-selection"></a>Pré-seleção

Quando o `SelectionMode` estiver definida como `Single`, um único item na `CollectionView` previamente selecionadas, definindo o `SelectedItem` propriedade para o item. A exemplo XAML a seguir mostra um `CollectionView` que pré-seleciona um único item:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                SelectionMode="Single"
                SelectedItem="{Binding SelectedMonkey, Mode=TwoWay}">
    ...
</CollectionView>
```

O código c# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    SelectionMode = SelectionMode.Single
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
collectionView.SetBinding(SelectableItemsView.SelectedItemProperty, "SelectedMonkey", BindingMode.TwoWay);
```

O `SelectedItem` associa dados de propriedade para o `SelectedMonkey` propriedade do modelo de exibição conectado, o que é do tipo `Monkey`. Um `TwoWay` associação é usada para que, se o usuário altera o item selecionado, o valor da `SelectedMonkey` propriedade será definida para selecionado `Monkey` objeto. O `SelectedMonkey` propriedade está definida na `MonkeysViewModel` classe e é definido como o quarto item do `Monkeys` coleção:

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

Portanto, quando o `CollectionView` for exibida, o quarto item na lista é pré-selecionada:

[![Captura de tela de uma lista vertical de CollectionView com pré-seleção de única, no iOS e Android](selection-images/single-pre-selection.png "CollectionView de lista vertical com pré-seleção de única")](selection-images/single-pre-selection-large.png#lightbox "lista vertical de CollectionView com a pré-seleção de única")

## <a name="change-selected-item-color"></a>Alterar a cor do item selecionado

`CollectionView` tem um `Selected` [ `VisualState` ](xref:Xamarin.Forms.VisualState) que pode ser usado para iniciar uma alteração visual para o item selecionado no `CollectionView`. Caso de uso de um comum para isso `VisualState` é alterar a cor do plano de fundo do item selecionado, que é mostrado no exemplo XAML a seguir:

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

Neste exemplo, o [ `Style.TargetType` ](xref:Xamarin.Forms.Style.TargetType) o valor da propriedade é definido como `Grid` porque o elemento raiz do `ItemTemplate` é um [ `Grid` ](xref:Xamarin.Forms.Grid). O `Selected` [ `VisualState` ](xref:Xamarin.Forms.VisualState) Especifica que quando um item no `CollectionView` estiver selecionada, o [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) do item será definido como `LightSkyBlue`:

[![Captura de tela de uma lista vertical de CollectionView com uma cor personalizada de seleção única, no iOS e Android](selection-images/single-selection-color.png "CollectionView de lista vertical com uma cor personalizada de seleção única") ] (selection-images/single-selection-color-large.png#lightbox " Lista vertical de CollectionView com uma cor personalizada de seleção única")

Para obter mais informações sobre estados visuais, consulte [Gerenciador de estado Visual do xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="disable-selection"></a>Desabilitar seleção

`CollectionView` seleção é desabilitada por padrão. No entanto, se um `CollectionView` tem seleção habilitada, ele pode ser desabilitado definindo o `SelectionMode` propriedade para `None`:

```xaml
<CollectionView ...
                SelectionMode="None" />
```

O código c# equivalente é:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    SelectionMode = SelectionMode.None
};
```

Quando o `SelectionMode` estiver definida como `None`, os itens na `CollectionView` não pode ser selecionado, o `SelectedItem` propriedade permanecerá `null`e o `SelectionChanged` evento não será disparado.

> [!NOTE]
> Quando um item foi selecionado e o `SelectionMode` propriedade é alterada de `Single` ao `None`, o `SelectedItem` propriedade será definida como `null` e o `SelectionChanged` evento será acionado com um vazio `CurrentSelection` propriedade.

## <a name="related-links"></a>Links relacionados

- [CollectionView (amostra)](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [Xamarin. Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)
