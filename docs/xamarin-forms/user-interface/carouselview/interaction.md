---
title: Interação CarouselView do Xamarin. Forms
description: O item atualmente exibido em um CarouselView pode ser acessado por meio das propriedades CurrentItem e Position.
ms.prod: xamarin
ms.assetid: 854D97E5-D119-4BE2-AE7C-BD428792C992
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/14/2019
ms.openlocfilehash: fd85876b38c21c7ff1ea85d7a61c1449395d56f5
ms.sourcegitcommit: e71474f91639bb43159b22f5d534325c3270ba93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72749798"
---
# <a name="xamarinforms-carouselview-interaction"></a>Interação CarouselView do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define as seguintes propriedades que controlam a interação do usuário:

- `CurrentItem`, do tipo `object`, o item atual que está sendo exibido. Essa propriedade tem um modo de associação padrão de `TwoWay` e tem um valor de `null` quando não há dados a serem exibidos.
- `CurrentItemChangedCommand`, do tipo `ICommand`, que é executado quando o item atual é alterado.
- `CurrentItemChangedCommandParameter`, do tipo `object`, que é o parâmetro passado para `CurrentItemChangedCommand`.
- `IsBounceEnabled`, do tipo `bool`, que especifica se o `CarouselView` irá devolver um limite de conteúdo. O valor padrão é `true`.
- `IsSwipeEnabled`, do tipo `bool`, que determina se um gesto de dedo vai alterar o item exibido. O valor padrão é `true`.
- `Position`, do tipo `int`, o índice do item atual na coleção subjacente. Essa propriedade tem um modo de associação padrão de `TwoWay` e tem um valor de 0 quando não há dados a serem exibidos.
- `PositionChangedCommand`, do tipo `ICommand`, que é executado quando a posição é alterada.
- `PositionChangedCommandParameter`, do tipo `object`, que é o parâmetro passado para `PositionChangedCommand`.

Todas essas propriedades são apoiadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), o que significa que essas propriedades podem ser o destino de vinculações de dados.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define um evento de `CurrentItemChanged` que é disparado quando a propriedade `CurrentItem` é alterada, seja devido à rolagem do usuário ou quando um aplicativo define a propriedade. O objeto `CurrentItemChangedEventArgs` que acompanha o evento `CurrentItemChanged` tem duas propriedades, ambas as `object` de tipo:

- `PreviousItem` – o item anterior, após a alteração da propriedade.
- `CurrentItem` – o item atual, após a alteração da propriedade.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) também define um evento de `PositionChanged` que é disparado quando a propriedade `Position` é alterada, seja devido à rolagem do usuário ou quando um aplicativo define a propriedade. O objeto `PositionChangedEventArgs` que acompanha o evento `PositionChanged` tem duas propriedades, ambas as `int` de tipo:

- `PreviousPosition` – a posição anterior, após a alteração da propriedade.
- `CurrentPosition` – a posição atual, após a alteração da propriedade.

## <a name="respond-to-the-current-item-changing"></a>Responder à alteração do item atual

Quando o item atualmente exibido for alterado, a propriedade `CurrentItem` será definida como o valor do item. Quando essa propriedade é alterada, o `CurrentItemChangedCommand` é executado com o valor do `CurrentItemChangedCommandParameter` que está sendo passado para o `ICommand`. A propriedade `Position` é então atualizada e o evento de `CurrentItemChanged` é acionado.

> [!IMPORTANT]
> A propriedade `Position` é alterada quando a propriedade `CurrentItem` é alterada. Isso fará com que o `PositionChangedCommand` seja executado e a `PositionChanged` acionando o evento.

### <a name="event"></a>evento

O exemplo de XAML a seguir mostra um [`CarouselView`](xref:Xamarin.Forms.CarouselView) que usa um manipulador de eventos para responder à alteração do item atual:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItemChanged="OnCurrentItemChanged">
    ...
</CarouselView>
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.CurrentItemChanged += OnCurrentItemChanged;
```

Neste exemplo, o manipulador de eventos `OnCurrentItemChanged` é executado quando o evento `CurrentItemChanged` é acionado, com o manipulador de eventos expondo os itens anteriores e atuais:

```csharp
void OnCurrentItemChanged(object sender, CurrentItemChangedEventArgs e)
{
    Monkey previousItem = e.PreviousItem as Monkey;
    Monkey currentItem = e.CurrentItem as Monkey;
}
```

### <a name="command"></a>Comando

O exemplo de XAML a seguir mostra um [`CarouselView`](xref:Xamarin.Forms.CarouselView) que usa um comando para responder à alteração do item atual:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItemChangedCommand="{Binding ItemChangedCommand}"
              CurrentItemChangedCommandParameter="{Binding Source={RelativeSource Self}, Path=CurrentItem}">
    ...
</CarouselView>
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.CurrentItemChangedCommandProperty, "ItemChangedCommand");
carouselView.SetBinding(CarouselView.CurrentItemChangedCommandParameterProperty, new Binding("CurrentItem", source: RelativeBindingSource.Self));
```

Neste exemplo, a propriedade `CurrentItemChangedCommand` é vinculada à propriedade `ItemChangedCommand`, passando o valor da propriedade `CurrentItem` para ela como um argumento. O `ItemChangedCommand` pode então responder à alteração do item atual, conforme necessário:

```csharp
public ICommand ItemChangedCommand => new Command<Monkey>((item) =>
{
    PreviousMonkey = CurrentMonkey;
    CurrentMonkey = item;
});
```

Neste exemplo, a `ItemChangedCommand` atualiza os objetos que armazenam os itens anteriores e atuais.

## <a name="respond-to-the-position-changing"></a>Responder à alteração da posição

Quando o item atualmente exibido for alterado, a propriedade `Position` será definida como o índice do item atual na coleção subjacente. Quando essa propriedade é alterada, o `PositionChangedCommand` é executado com o valor do `PositionChangedCommandParameter` que está sendo passado para o `ICommand`. O evento `PositionChanged`, em seguida, é disparado. Se a propriedade `Position` tiver sido alterada de forma programática, o [`CarouselView`](xref:Xamarin.Forms.CarouselView) será rolado para o item que corresponde ao valor `Position`.

> [!NOTE]
> Definir a propriedade `Position` como 0 fará com que o primeiro item na coleção subjacente seja exibido.

### <a name="event"></a>evento

O exemplo de XAML a seguir mostra um [`CarouselView`](xref:Xamarin.Forms.CarouselView) que usa um manipulador de eventos para responder à alteração da propriedade `Position`:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"              
              PositionChanged="OnPositionChanged">
    ...
</CarouselView>
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.PositionChanged += OnPositionChanged;
```

Neste exemplo, o manipulador de eventos `OnPositionChanged` é executado quando o evento `PositionChanged` é acionado, com o manipulador de eventos expondo as posições anterior e atual:

```csharp
void OnPositionChanged(object sender, PositionChangedEventArgs e)
{
    int previousItemPosition = e.PreviousPosition;
    int currentItemPosition = e.CurrentPosition;
}
```

### <a name="command"></a>Comando

O exemplo XAML a seguir mostra um [`CarouselView`](xref:Xamarin.Forms.CarouselView) que usa um comando para responder à alteração da propriedade `Position`:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PositionChangedCommand="{Binding PositionChangedCommand}"
              PositionChangedCommandParameter="{Binding Source={RelativeSource Self}, Path=Position}">
    ...
</CarouselView>
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.PositionChangedCommandProperty, "PositionChangedCommand");
carouselView.SetBinding(CarouselView.PositionChangedCommandParameterProperty, new Binding("Position", source: RelativeBindingSource.Self));
```

Neste exemplo, a propriedade `PositionChangedCommand` é vinculada à propriedade `PositionChangedCommand`, passando o valor da propriedade `Position` para ela como um argumento. O `PositionChangedCommand` pode então responder à alteração da posição, conforme necessário:

```csharp
public ICommand PositionChangedCommand => new Command<int>((position) =>
{
    PreviousPosition = CurrentPosition;
    CurrentPosition = position;
});
```

Neste exemplo, a `PositionChangedCommand` atualiza os objetos que armazenam as posições anteriores e atuais.

## <a name="preset-the-current-item"></a>Predefinir o item atual

O item atual em um [`CarouselView`](xref:Xamarin.Forms.CarouselView) pode ser definido de forma programática definindo a propriedade `CurrentItem` para o item. O exemplo de XAML a seguir mostra um `CarouselView` que escolhe previamente o item atual:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              CurrentItem="{Binding CurrentItem}">
    ...
</CarouselView>
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.CurrentItemProperty, "CurrentItem");
```

> [!NOTE]
> A propriedade `CurrentItem` tem um modo de associação padrão de `TwoWay`.

Os dados da propriedade `CarouselView.CurrentItem` são associados à propriedade `CurrentItem` do modelo de exibição conectado, que é do tipo `Monkey`. Por padrão, uma associação de `TwoWay` é usada para que, se o usuário alterar o item atual, o valor da propriedade `CurrentItem` será definido como o objeto de `Monkey` atual. A propriedade `CurrentItem` é definida na classe `MonkeysViewModel` e é definida como o quarto item da coleção `Monkeys`:

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    // ...
    public ObservableCollection<Monkey> Monkeys { get; private set; }

    public Monkey CurrentItem { get; set; }

    public MonkeysViewModel()
    {
        // ...
        CurrentItem = Monkeys.Skip(3).FirstOrDefault();
        OnPropertyChanged("CurrentItem");
    }
}
```

## <a name="preset-the-position"></a>Predefina a posição

O item exibido [`CarouselView`](xref:Xamarin.Forms.CarouselView) pode ser definido de forma programática definindo a propriedade `Position` como o índice do item na coleção subjacente. O exemplo de XAML a seguir mostra um `CarouselView` que define o item exibido:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              Position="{Binding Position}">
    ...
</CarouselView>
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
carouselView.SetBinding(CarouselView.PositionProperty, "Position");
```

> [!NOTE]
> A propriedade `Position` tem um modo de associação padrão de `TwoWay`.

Os dados da propriedade `CarouselView.Position` são associados à propriedade `Position` do modelo de exibição conectado, que é do tipo `int`. Por padrão, uma associação de `TwoWay` é usada para que, se o usuário rolar pela [`CarouselView`](xref:Xamarin.Forms.CarouselView), o valor da propriedade `Position` será definido como o índice do item exibido. A propriedade `Position` é definida na classe `MonkeysViewModel` e é definida como o quarto item da coleção `Monkeys`:

```csharp
public class MonkeysViewModel : INotifyPropertyChanged
{
    // ...
    public int Position { get; set; }

    public MonkeysViewModel()
    {
        // ...
        Position = 3;
        OnPropertyChanged("Position");
    }
}
```

## <a name="clear-the-current-item"></a>Limpar o item atual

A propriedade `CurrentItem` pode ser desmarcada definindo-a, ou o objeto ao qual ela se vincula, para `null`.

## <a name="disable-bounce"></a>Desabilitar o retorno

Por padrão, [`CarouselView`](xref:Xamarin.Forms.CarouselView) salta os itens em limites de conteúdo. Isso pode ser desabilitado definindo a propriedade `IsBounceEnabled` como `false`.

## <a name="disable-swipe-interaction"></a>Desabilitar a interação do passe o dedo

Por padrão, [`CarouselView`](xref:Xamarin.Forms.CarouselView) permite aos usuários percorrer itens usando um gesto de passar o dedo. Essa interação de passar o dedo pode ser desabilitada definindo a propriedade `IsSwipeEnabled` como `false`.

## <a name="related-links"></a>Links relacionados

- [CarouselView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
