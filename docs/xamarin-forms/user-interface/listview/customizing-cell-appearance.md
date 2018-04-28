---
title: Aparência de célula
description: Explore as opções de apresentação de dados, aproveitando a conveniência de ListView.
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: 37ecc76d9774b3f375af92f2a00c6c687358f065
ms.sourcegitcommit: a69439ad4c9fd0abe759143687d3b23582573d90
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="cell-appearance"></a>Aparência de célula

ListView apresenta listas roláveis que podem ser personalizadas com o uso de `ViewCell`s. `ViewCells` pode ser usado para exibir texto e imagens, que indica um estado de verdadeiro/falso e receber entrada do usuário.

Há duas abordagens para obter a aparência desejada de ListView células:

- **[Personalizando células internas](#Built_in_Cells)**  &ndash; implementação mais fácil e melhor desempenho às custas de personalização.
- **[Criando células personalizadas](#customcells)**  &ndash; mais controle sobre o resultado final, mas tem o potencial de problemas de desempenho, se não implementado corretamente.

<a name="Built_in_Cells" />

## <a name="built-in-cells"></a>Criado em células
Xamarin. Forms vem com células internas que funcionam para muitos aplicativos simples:

- **TextCell** &ndash; para exibir o texto
- **ImageCell** &ndash; para exibir uma imagem com texto.

Duas células adicionais, [ `SwitchCell` ](~/xamarin-forms/user-interface/tableview.md#switchcell) e [ `EntryCell` ](~/xamarin-forms/user-interface/tableview.md#entrycell) estão disponíveis, mas eles não são usados com `ListView`. Consulte [ `TableView` ](~/xamarin-forms/user-interface/tableview.md) para obter mais informações sobre essas células.

<a name="TextCell" />

### <a name="textcell"></a>TextCell

[`TextCell`](http://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) é uma célula para exibir o texto, opcionalmente com uma segunda linha como texto de detalhes.

TextCells são renderizados como controles nativos em tempo de execução, para que o desempenho é muito bom em comparação com um personalizado `ViewCell`. TextCells são personalizáveis, permitindo que você defina:

- `Text` &ndash; o texto que é mostrado na primeira linha, em fonte grande.
- `Detail` &ndash; o texto que é exibido sob a primeira linha em uma fonte menor.
- `TextColor` &ndash; a cor do texto.
- `DetailColor` &ndash; a cor do texto de detalhes

![](customizing-cell-appearance-images/text-cell-default.png "Exemplo de TextCell padrão")

![](customizing-cell-appearance-images/text-cell-custom.png "Exemplo de TextCell personalizado")

<a name="ImageCell" />

### <a name="imagecell"></a>ImageCell

[`ImageCell`](http://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/), como `TextCell`, pode ser usado para exibir texto e texto detalhado secundário e oferece excelente desempenho por meio de controles nativo de cada plataforma. `ImageCell` difere `TextCell` exibir uma imagem à esquerda do texto.

`ImageCell` é útil quando você precisa exibir uma lista de dados com um aspecto visual, como uma lista de contatos ou filmes. ImageCells são personalizáveis, permitindo que você defina:

- `Text` &ndash; o texto que é mostrado na primeira linha, em fontes grandes
- `Detail` &ndash; o texto que é exibido sob a primeira linha em uma fonte menor
- `TextColor` &ndash; a cor do texto
- `DetailColor` &ndash; a cor do texto de detalhes
- `ImageSource` &ndash; a imagem a ser exibida ao lado do texto

![](customizing-cell-appearance-images/image-cell-default.png "Exemplo de ImageCell padrão")

![](customizing-cell-appearance-images/image-cell-custom.png "Exemplo de ImageCell personalizado")

<a name="customcells" />

## <a name="custom-cells"></a>Células personalizadas
Quando as células internas não fornecerem o layout necessário, células personalizadas implementado o layout necessário. Por exemplo, você talvez queira apresentar uma célula com dois rótulos que têm o mesmo peso. Um `TextCell` seria suficiente porque a `TextCell` tem um rótulo que for menor. A maioria das personalizações de célula adicionam dados somente leitura adicionais (como rótulos adicionais, imagens ou outras informações de exibição).

Todas as células personalizadas devem derivar de [ `ViewCell` ](http://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/), a mesma classe base que todos os a célula interna tipos de uso.

Xamarin. Forms 2 introduziu um novo [comportamento do cache](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy) no `ListView` controle que pode ser definida para melhorar o desempenho de rolagem para alguns tipos de células personalizados.

Este é um exemplo de uma célula personalizado:

![](customizing-cell-appearance-images/custom-cell.png "Exemplo de célula personalizado")

### <a name="xaml"></a>XAML
O XAML para criar o layout acima está abaixo:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="demoListView.ImageCellPage">
    <ContentPage.Content>
        <ListView  x:Name="listView">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout BackgroundColor="#eee"
                        Orientation="Vertical">
                            <StackLayout Orientation="Horizontal">
                                <Image Source="{Binding image}" />
                                <Label Text="{Binding title}"
                                TextColor="#f35e20" />
                                <Label Text="{Binding subtitle}"
                                HorizontalOptions="EndAndExpand"
                                TextColor="#503026" />
                            </StackLayout>
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

O XAML acima está fazendo muito. Vamos dividi-lo:

- A célula personalizada está aninhada em uma `DataTemplate`, que está dentro de `ListView.ItemTemplate`. Este é o mesmo processo usando uma outra célula.
- `ViewCell` é o tipo de célula personalizado. O filho do `DataTemplate` elemento deve ser do ou derivar do tipo `ViewCell`.
- Observe que dentro de `ViewCell`, layout é gerenciado por um `StackLayout`. Esse layout permite personalizar a cor de plano de fundo. Observe que qualquer propriedade de `StackLayout` que é vinculável pode ser associado dentro de uma célula personalizada, apesar de que não é mostrada aqui.

### <a name="cnum"></a>C&num;

A especificação de uma célula personalizada em c# é um pouco mais detalhado do que o equivalente em XAML. Vamos analisar:

Primeiro, definir uma classe de célula personalizado, com `ViewCell` como a classe base:

```csharp
public class CustomCell : ViewCell
    {
        public CustomCell()
        {
            //instantiate each of our views
            var image = new Image ();
            StackLayout cellWrapper = new StackLayout ();
            StackLayout horizontalLayout = new StackLayout ();
            Label left = new Label ();
            Label right = new Label ();

            //set bindings
            left.SetBinding (Label.TextProperty, "title");
            right.SetBinding (Label.TextProperty, "subtitle");
            image.SetBinding (Image.SourceProperty, "image");

            //Set properties for desired design
            cellWrapper.BackgroundColor = Color.FromHex ("#eee");
            horizontalLayout.Orientation = StackOrientation.Horizontal;
            right.HorizontalOptions = LayoutOptions.EndAndExpand;
            left.TextColor = Color.FromHex ("#f35e20");
            right.TextColor = Color.FromHex ("503026");

            //add views to the view hierarchy
            horizontalLayout.Children.Add (image);
            horizontalLayout.Children.Add (left);
            horizontalLayout.Children.Add (right);
            cellWrapper.Children.Add (horizontalLayout);
            View = cellWrapper;
        }
    }
```

O construtor para a página com o `ListView`, definir o ListView `ItemTemplate` propriedade para um novo `DataTemplate`:

```csharp
public partial class ImageCellPage : ContentPage
    {
        public ImageCellPage ()
        {
            InitializeComponent ();
            listView.ItemTemplate = new DataTemplate (typeof(CustomCell));
        }
    }
```

Observe que o construtor para `DataTemplate` usa um tipo. O operador typeof obtém o tipo CLR para `CustomCell`.

<a name="binding-context-changes" />

### <a name="binding-context-changes"></a>Alterações de contexto de associação

Ao associar a um tipo de célula personalizado [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) instâncias, os controles de interface de usuário exibindo o `BindableProperty` valores devem usar o [ `OnBindingContextChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.OnBindingContextChanged()/) substituição para definir os dados a serem exibidos no cada célula, em vez do construtor de célula, conforme demonstrado no exemplo de código a seguir:

```csharp
public class CustomCell : ViewCell
{
    Label nameLabel, ageLabel, locationLabel;

    public static readonly BindableProperty NameProperty =
        BindableProperty.Create ("Name", typeof(string), typeof(CustomCell), "Name");
    public static readonly BindableProperty AgeProperty =
        BindableProperty.Create ("Age", typeof(int), typeof(CustomCell), 0);
    public static readonly BindableProperty LocationProperty =
        BindableProperty.Create ("Location", typeof(string), typeof(CustomCell), "Location");

    public string Name {
        get { return(string)GetValue (NameProperty); }
        set { SetValue (NameProperty, value); }
    }

    public int Age {
        get { return(int)GetValue (AgeProperty); }
        set { SetValue (AgeProperty, value); }
    }

    public string Location {
        get { return(string)GetValue (LocationProperty); }
        set { SetValue (LocationProperty, value); }
    }
    ...

    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();

        if (BindingContext != null) {
            nameLabel.Text = Name;
            ageLabel.Text = Age.ToString ();
            locationLabel.Text = Location;
        }
    }
}
```

O [ `OnBindingContextChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.OnBindingContextChanged()/) substituição será chamada quando o [ `BindingContextChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.BindableObject.BindingContextChanged/) evento aciona em resposta ao valor da [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) alteração de propriedade. Portanto, quando o `BindingContext` for alterada, os controles de interface de usuário exibindo o [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) valores devem definir seus dados. Observe que o `BindingContext` devem ser verificados para um `null` valor, pois isso pode ser definido por xamarin. Forms para coleta de lixo, que por sua vez resultará no `OnBindingContextChanged` substituir que está sendo chamado.

Como alternativa, os controles de interface do usuário pode vincular ao [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) instâncias para exibir seus valores, o que elimina a necessidade de substituir o `OnBindingContextChanged` método.

> [!NOTE]
> Ao substituir `OnBindingContextChanged`, certifique-se de que a classe base `OnBindingContextChanged` método é chamado de modo que representantes registrados recebam o `BindingContextChanged` evento.

Em XAML, o tipo de célula personalizado de associação de dados pode ser obtida conforme mostrado no exemplo de código a seguir:

```xaml
<ListView x:Name="listView">
    <ListView.ItemTemplate>
        <DataTemplate>
            <local:CustomCell Name="{Binding Name}" Age="{Binding Age}" Location="{Binding Location}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Isso vincula o `Name`, `Age`, e `Location` propriedades vinculáveis no `CustomCell` instância, como o `Name`, `Age`, e `Location` propriedades de cada objeto na coleção subjacente.

A associação equivalente em c# é mostrada no exemplo de código a seguir:

```csharp
var customCell = new DataTemplate (typeof(CustomCell));
customCell.SetBinding (CustomCell.NameProperty, "Name");
customCell.SetBinding (CustomCell.AgeProperty, "Age");
customCell.SetBinding (CustomCell.LocationProperty, "Location");

var listView = new ListView {
    ItemsSource = people,
    ItemTemplate = customCell
};
```

No iOS e Android, se o [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) estiver Reciclando elementos e a célula personalizada usa um renderizador personalizado, o renderizador personalizado corretamente deve implementar a notificação de alteração de propriedade. Quando as células são reutilizadas seus valores de propriedade serão alterado quando o contexto de associação é atualizado para que uma célula disponível, com `PropertyChanged` eventos que está sendo gerados. Para obter mais informações, consulte [Personalizando uma ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md). Para obter mais informações sobre a reciclagem de célula, consulte [estratégia de cache](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy).

## <a name="related-links"></a>Links relacionados

- [Criado em células (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BuiltInCells)
- [Células personalizadas (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/CustomCells)
- [Associação de contexto alterado (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BindingContextChanged)
