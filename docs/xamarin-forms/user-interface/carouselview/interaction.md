---
Título: " Xamarin.Forms interação CarouselView" Descrição: "o item atualmente exibido em um CarouselView pode ser acessado por meio das propriedades CurrentItem e Position".
MS. Prod: xamarin MS. AssetID: 854D97E5-D119-4BE2-AE7C-BD428792C992 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 02/11/2020 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-carouselview-interaction"></a>Xamarin.FormsInteração CarouselView

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)define as seguintes propriedades que controlam a interação do usuário:

- `CurrentItem`, do tipo `object` , o item atual que está sendo exibido. Essa propriedade tem um modo de associação padrão de `TwoWay` e tem um `null` valor quando não há dados a serem exibidos.
- `CurrentItemChangedCommand`, do tipo `ICommand` , que é executado quando o item atual é alterado.
- `CurrentItemChangedCommandParameter`, do tipo `object`, que é o parâmetro passado para `CurrentItemChangedCommand`.
- `IsBounceEnabled`, do tipo `bool` , que especifica se o `CarouselView` irá devolver um limite de conteúdo. O valor padrão é `true`.
- `IsSwipeEnabled`, do tipo `bool` , que determina se um gesto de dedo vai alterar o item exibido. O valor padrão é `true`.
- `Position`, do tipo `int` , o índice do item atual na coleção subjacente. Essa propriedade tem um modo de associação padrão de `TwoWay` e tem um valor de 0 quando não há dados a serem exibidos.
- `PositionChangedCommand`, do tipo `ICommand` , que é executado quando a posição é alterada.
- `PositionChangedCommandParameter`, do tipo `object`, que é o parâmetro passado para `PositionChangedCommand`.
- `VisibleViews`, do tipo `ObservableCollection<View>` , que é uma propriedade somente leitura que contém os objetos para os itens que estão visíveis no momento.

Todas essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

[`CarouselView`](xref:Xamarin.Forms.CarouselView)define um `CurrentItemChanged` evento que é disparado quando a `CurrentItem` propriedade é alterada, seja devido à rolagem do usuário ou quando um aplicativo define a propriedade. O `CurrentItemChangedEventArgs` objeto que acompanha o `CurrentItemChanged` evento tem duas propriedades, ambas do tipo `object` :

- `PreviousItem`– o item anterior, após a alteração da propriedade.
- `CurrentItem`– o item atual, após a alteração da propriedade.

[`CarouselView`](xref:Xamarin.Forms.CarouselView)também define um `PositionChanged` evento que é disparado quando a `Position` propriedade é alterada, seja devido à rolagem do usuário ou quando um aplicativo define a propriedade. O `PositionChangedEventArgs` objeto que acompanha o `PositionChanged` evento tem duas propriedades, ambas do tipo `int` :

- `PreviousPosition`– a posição anterior, após a alteração da propriedade.
- `CurrentPosition`– a posição atual, após a alteração da propriedade.

## <a name="respond-to-the-current-item-changing"></a>Responder à alteração do item atual

Quando o item exibido atualmente é alterado, a `CurrentItem` propriedade será definida como o valor do item. Quando essa propriedade é alterada, o `CurrentItemChangedCommand` é executado com o valor de `CurrentItemChangedCommandParameter` que está sendo passado para o `ICommand` . `Position`Em seguida, a propriedade é atualizada e o `CurrentItemChanged` evento é acionado.

> [!IMPORTANT]
> A `Position` propriedade muda quando a `CurrentItem` propriedade é alterada. Isso fará com que o `PositionChangedCommand` seja executado e o `PositionChanged` evento seja acionado.

### <a name="event"></a>Evento

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

Neste exemplo, o `OnCurrentItemChanged` manipulador de eventos é executado quando o `CurrentItemChanged` evento é acionado:

```csharp
void OnCurrentItemChanged(object sender, CurrentItemChangedEventArgs e)
{
    Monkey previousItem = e.PreviousItem as Monkey;
    Monkey currentItem = e.CurrentItem as Monkey;
}
```

Neste exemplo, o `OnCurrentItemChanged` manipulador de eventos expõe os itens anteriores e atuais:

[![Captura de tela de um CarouselView com itens anteriores e atuais, no iOS e no Android](interaction-images/current-item-events.png "CarouselView com itens atuais e anteriores")](interaction-images/current-item-events-large.png#lightbox "CarouselView com itens atuais e anteriores")

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

Neste exemplo, a `CurrentItemChangedCommand` propriedade é associada à `ItemChangedCommand` propriedade, passando o valor da `CurrentItem` propriedade para ela como um argumento. O `ItemChangedCommand` pode então responder à alteração do item atual, conforme necessário:

```csharp
public ICommand ItemChangedCommand => new Command<Monkey>((item) =>
{
    PreviousMonkey = CurrentMonkey;
    CurrentMonkey = item;
});
```

Neste exemplo, os `ItemChangedCommand` objetos de atualização que armazenam os itens anteriores e atuais.

## <a name="respond-to-the-position-changing"></a>Responder à alteração da posição

Quando o item exibido atualmente é alterado, a `Position` propriedade será definida como o índice do item atual na coleção subjacente. Quando essa propriedade é alterada, o `PositionChangedCommand` é executado com o valor de `PositionChangedCommandParameter` que está sendo passado para o `ICommand` . `PositionChanged`Em seguida, o evento é disparado. Se a `Position` propriedade tiver sido alterada de forma programática, a [`CarouselView`](xref:Xamarin.Forms.CarouselView) será rolada para o item que corresponde ao `Position` valor.

> [!NOTE]
> Definir a `Position` propriedade como 0 fará com que o primeiro item na coleção subjacente seja exibido.

### <a name="event"></a>Evento

O exemplo de XAML a seguir mostra um [`CarouselView`](xref:Xamarin.Forms.CarouselView) que usa um manipulador de eventos para responder à `Position` alteração da propriedade:

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

Neste exemplo, o `OnPositionChanged` manipulador de eventos é executado quando o `PositionChanged` evento é acionado:

```csharp
void OnPositionChanged(object sender, PositionChangedEventArgs e)
{
    int previousItemPosition = e.PreviousPosition;
    int currentItemPosition = e.CurrentPosition;
}
```

Neste exemplo, o `OnCurrentItemChanged` manipulador de eventos expõe as posições anterior e atual:

[![Captura de tela de um CarouselView com posições anteriores e atuais, no iOS e no Android](interaction-images/current-position-events.png "CarouselView com as posições atual e anterior")](interaction-images/current-position-events-large.png#lightbox "CarouselView com as posições atual e anterior")

### <a name="command"></a>Comando

O exemplo XAML a seguir mostra um [`CarouselView`](xref:Xamarin.Forms.CarouselView) que usa um comando para responder à `Position` alteração da propriedade:

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

Neste exemplo, a `PositionChangedCommand` propriedade é associada à `PositionChangedCommand` propriedade, passando o valor da `Position` propriedade para ela como um argumento. O `PositionChangedCommand` pode então responder à alteração da posição, conforme necessário:

```csharp
public ICommand PositionChangedCommand => new Command<int>((position) =>
{
    PreviousPosition = CurrentPosition;
    CurrentPosition = position;
});
```

Neste exemplo, os `PositionChangedCommand` objetos de atualização que armazenam as posições anteriores e atuais.

## <a name="preset-the-current-item"></a>Predefinir o item atual

O item atual em um [`CarouselView`](xref:Xamarin.Forms.CarouselView) pode ser definido de forma programática definindo a `CurrentItem` propriedade para o item. O exemplo de XAML a seguir mostra um `CarouselView` que escolhe previamente o item atual:

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
> A `CurrentItem` propriedade tem um modo de associação padrão de `TwoWay` .

Os `CarouselView.CurrentItem` dados de propriedade são associados à `CurrentItem` Propriedade do modelo de exibição conectado, que é do tipo `Monkey` . Por padrão, uma `TwoWay` associação é usada para que, se o usuário alterar o item atual, o valor da `CurrentItem` propriedade será definido como o objeto atual `Monkey` . A `CurrentItem` propriedade é definida na `MonkeysViewModel` classe:

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

Neste exemplo, a `CurrentItem` propriedade é definida como o quarto item da `Monkeys` coleção:

[![Captura de tela de um CarouselView com item predefinido, no iOS e no Android](interaction-images/preset-item.png "CarouselView com item predefinido")](interaction-images/preset-item-large.png#lightbox "CarouselView com item predefinido")

## <a name="preset-the-position"></a>Predefina a posição

O item exibido [`CarouselView`](xref:Xamarin.Forms.CarouselView) pode ser definido de forma programática definindo a `Position` propriedade como o índice do item na coleção subjacente. O exemplo de XAML a seguir mostra um `CarouselView` que define o item exibido:

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
> A `Position` propriedade tem um modo de associação padrão de `TwoWay` .

Os `CarouselView.Position` dados de propriedade são associados à `Position` Propriedade do modelo de exibição conectado, que é do tipo `int` . Por padrão, uma `TwoWay` associação é usada para que, se o usuário rolar pelo [`CarouselView`](xref:Xamarin.Forms.CarouselView) , o valor da `Position` propriedade será definido como o índice do item exibido. A `Position` propriedade é definida na `MonkeysViewModel` classe:

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

Neste exemplo, a `Position` propriedade é definida como o quarto item da `Monkeys` coleção:

[![Captura de tela de um CarouselView com posição predefinida em iOS e Android](interaction-images/preset-position.png "CarouselView com posição predefinida")](interaction-images/preset-position-large.png#lightbox "CarouselView com posição predefinida")

## <a name="define-visual-states"></a>Definir estados visuais

[`CarouselView`](xref:Xamarin.Forms.CarouselView)define quatro estados visuais:

- `CurrentItem`representa o estado visual do item exibido no momento.
- `PreviousItem`representa o estado visual do item exibido anteriormente.
- `NextItem`representa o estado visual do próximo item.
- `DefaultItem`representa o estado visual do restante dos itens.

Esses Estados visuais podem ser usados para iniciar alterações visuais nos itens exibidos pelo [`CarouselView`](xref:Xamarin.Forms.CarouselView) .

O exemplo de XAML a seguir mostra como definir `CurrentItem` os `PreviousItem` `NextItem` Estados visuais,, e `DefaultItem` :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="CurrentItem">
                            <VisualState.Setters>
                                <Setter Property="Scale"
                                        Value="1.1" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="PreviousItem">
                            <VisualState.Setters>
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="NextItem">
                            <VisualState.Setters>
                                <Setter Property="Opacity"
                                        Value="0.5" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="DefaultItem">
                            <VisualState.Setters>
                                <Setter Property="Opacity"
                                        Value="0.25" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateManager.VisualStateGroups>

                <!-- Item template content -->
                <Frame HasShadow="true">
                    ...
                </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

Neste exemplo, o `CurrentItem` estado visual especifica que o item atual exibido pelo [`CarouselView`](xref:Xamarin.Forms.CarouselView) terá sua [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriedade alterada de seu valor padrão de 1 para 1,1. Os `PreviousItem` `NextItem` Estados e visuais especificam que os itens em torno do item atual serão exibidos com um [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) valor de 0,5. O `DefaultItem` estado visual especifica que o restante dos itens exibidos pelo `CarouselView` será exibido com um `Opacity` valor de 0,25.

> [!NOTE]
> Como alternativa, os Estados visuais podem ser definidos em um [`Style`](xref:Xamarin.Forms.Style) que tenha um [`TargetType`](xref:Xamarin.Forms.Style.TargetType) valor de propriedade que seja o tipo do elemento raiz do [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) , que é definido como o `ItemTemplate` valor da propriedade.

As capturas de tela a seguir mostram os `CurrentItem` `PreviousItem` Estados do Visual, e `NextItem` :

[![Captura de tela de um CarouselView usando estados visuais, no iOS e no Android](interaction-images/visual-states.png "Estados visuais do CarouselView")](interaction-images/visual-states-large.png#lightbox "Estados visuais do CarouselView")

Para obter mais informações sobre os Estados visuais, consulte [ Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="clear-the-current-item"></a>Limpar o item atual

A `CurrentItem` propriedade pode ser desmarcada definindo-a, ou o objeto ao qual ela se vincula, para `null` .

## <a name="disable-bounce"></a>Desabilitar o retorno

Por padrão, o [`CarouselView`](xref:Xamarin.Forms.CarouselView) resalta os itens em limites de conteúdo. Isso pode ser desabilitado definindo a `IsBounceEnabled` propriedade como `false` .

## <a name="disable-swipe-interaction"></a>Desabilitar a interação do passe o dedo

Por padrão, o [`CarouselView`](xref:Xamarin.Forms.CarouselView) permite aos usuários percorrer itens usando um gesto de passar o dedo. Essa interação de passar o dedo pode ser desabilitada definindo a `IsSwipeEnabled` propriedade como `false` .

## <a name="related-links"></a>Links relacionados

- [CarouselView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Xamarin.FormsGerenciador de estado visual](~/xamarin-forms/user-interface/visual-state-manager.md)
