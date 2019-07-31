---
title: Personalizando a aparência de célula do ListView
description: Este artigo explora as opções para apresentar dados em aplicativos xamarin. Forms, ao mesmo tempo, aproveitar a conveniência do controle ListView.
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: ecb9e84473778f5185276a854bfbf2fdfcbb6528
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68654843"
---
# <a name="customizing-listview-cell-appearance"></a>Personalizando a aparência de célula do ListView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)

[`ListView`](xref:Xamarin.Forms.ListView)apresenta listas roláveis, que podem ser personalizadas com o uso de `ViewCell`s. `ViewCells` pode ser usado para exibir texto e imagens, que indica um estado de verdadeiro/falso e receber entrada do usuário.

<a name="Built_in_Cells" />

## <a name="built-in-cells"></a>Criado em células
Xamarin. Forms vem com células internos que funcionam para muitos aplicativos simples:

- **TextCell** &ndash; para exibir o texto
- **ImageCell** &ndash; para exibir uma imagem com texto.

Duas células adicionais, [ `SwitchCell` ](~/xamarin-forms/user-interface/tableview.md#switchcell) e [ `EntryCell` ](~/xamarin-forms/user-interface/tableview.md#entrycell) estão disponíveis, no entanto, eles não são comumente usados com `ListView`. Ver [ `TableView` ](~/xamarin-forms/user-interface/tableview.md) para obter mais informações sobre essas células.

<a name="TextCell" />

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell) é uma célula para exibir o texto, opcionalmente com uma segunda linha como texto de detalhes.

TextCells são renderizados como controles nativos em tempo de execução, portanto, o desempenho é muito bom em comparação com um personalizado `ViewCell`. TextCells são personalizáveis, permitindo que você defina:

- `Text` &ndash; o texto que é mostrado na primeira linha, em fonte grande.
- `Detail` &ndash; o texto que é mostrado abaixo da primeira linha, em uma fonte menor.
- `TextColor` &ndash; a cor do texto.
- `DetailColor` &ndash; a cor do texto de detalhes

![](customizing-cell-appearance-images/text-cell-default.png "Exemplo de TextCell padrão")

![](customizing-cell-appearance-images/text-cell-custom.png "Exemplo de TextCell personalizado")

<a name="ImageCell" />

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell), como `TextCell`, pode ser usado para exibir texto e texto secundário detalhes e oferece excelente desempenho usando controles nativos de cada plataforma. `ImageCell` é diferente do `TextCell` em que ele exibe uma imagem à esquerda do texto.

`ImageCell` é útil quando você precisa exibir uma lista de dados com um aspecto visual, como uma lista de contatos ou filmes. ImageCells são personalizáveis, permitindo que você defina:

- `Text` &ndash; o texto que é mostrado na primeira linha, em fontes grandes
- `Detail` &ndash; o texto que é mostrado abaixo da primeira linha, em uma fonte menor
- `TextColor` &ndash; a cor do texto
- `DetailColor` &ndash; a cor do texto de detalhes
- `ImageSource` &ndash; a imagem a ser exibida ao lado do texto

![](customizing-cell-appearance-images/image-cell-default.png "Exemplo de ImageCell padrão")

![](customizing-cell-appearance-images/image-cell-custom.png "Exemplo de ImageCell personalizado")

<a name="customcells" />

## <a name="custom-cells"></a>Células personalizadas
Quando as células internas não fornecerem o layout necessário, células personalizadas implementado o layout necessário. Por exemplo, você talvez queira apresentar uma célula com dois rótulos que têm o mesmo peso. Um `TextCell` seria insuficiente porque o `TextCell` tem um rótulo que for menor. A maioria das personalizações de célula adicionar dados somente leitura adicionais (como rótulos adicionais, imagens ou outras informações de exibição).

Todas as células personalizadas devem derivar de [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), a mesma classe base que todos os internos célula tipos de uso.

Xamarin. Forms 2 introduziu um novo [comportamento de cache](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy) sobre o `ListView` controle que pode ser definida para melhorar o desempenho de rolagem para alguns tipos de células personalizadas.

Este é um exemplo de uma célula personalizada:

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

- A célula personalizada está aninhada em uma `DataTemplate`, que está dentro de `ListView.ItemTemplate`. Isso é o mesmo processo usando qualquer outra célula.
- `ViewCell` é o tipo de célula personalizado. O filho de `DataTemplate` elemento deve ser do ou derivar do tipo `ViewCell`.
- Observe que dentro de `ViewCell`, layout é gerenciado por um `StackLayout`. Esse layout nos permite personalizar a cor do plano de fundo. Observe que qualquer propriedade do `StackLayout` que é associável pode ser associado dentro de uma célula personalizada, apesar de que não é mostrada aqui.
- Dentro do `ViewCell`, o layout pode ser gerenciado por qualquer layout do Xamarin. Forms. 

### <a name="cnum"></a>C&num;

Especificar uma célula personalizada em C# é um pouco mais detalhado do que o equivalente em XAML. Vamos analisar:

Primeiro, defina uma classe de célula personalizado, com `ViewCell` como a classe base:

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

Em seu construtor para a página com o `ListView`, defina o ListView `ItemTemplate` propriedade para um novo `DataTemplate`:

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

Ao associar a um tipo de célula personalizado [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instâncias, os controles de interface do usuário exibindo as `BindableProperty` valores devem usar o [ `OnBindingContextChanged` ](xref:Xamarin.Forms.Cell.OnBindingContextChanged) substituição para definir os dados a serem exibidos no cada célula, em vez do construtor de célula, conforme demonstrado no exemplo de código a seguir:

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

O [ `OnBindingContextChanged` ](xref:Xamarin.Forms.Cell.OnBindingContextChanged) substituto será chamado quando o [ `BindingContextChanged` ](xref:Xamarin.Forms.BindableObject.BindingContextChanged) evento aciona em resposta ao valor da [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) a alteração de propriedade. Portanto, quando o `BindingContext` for alterado, os controles de interface do usuário exibindo o [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) valores devem definir seus dados. Observe que o `BindingContext` devem ser verificados para uma `null` de valor, pois isso pode ser definido pelo xamarin. Forms para coleta de lixo, que por sua vez resultará na `OnBindingContextChanged` substituir o que está sendo chamado.

Como alternativa, os controles de interface do usuário podem associar à [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instâncias para exibir seus valores, o que elimina a necessidade de substituir o `OnBindingContextChanged` método.

> [!NOTE]
> Ao substituir `OnBindingContextChanged`, certifique-se de que a classe base `OnBindingContextChanged` método é chamado para que os representantes registrados recebam o `BindingContextChanged` eventos.

No XAML, associando o tipo de célula personalizado a dados pode ser obtida conforme mostrado no exemplo de código a seguir:

```xaml
<ListView x:Name="listView">
    <ListView.ItemTemplate>
        <DataTemplate>
            <local:CustomCell Name="{Binding Name}" Age="{Binding Age}" Location="{Binding Location}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Isso vincula o `Name`, `Age`, e `Location` propriedades vinculáveis no `CustomCell` da instância, como o `Name`, `Age`, e `Location` propriedades de cada objeto na coleção subjacente.

A associação equivalente em C# é mostrada no exemplo de código a seguir:

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

No iOS e Android, se o [ `ListView` ](xref:Xamarin.Forms.ListView) reciclagem de elementos e a célula personalizada usa um renderizador personalizado, o renderizador personalizado deve implementar corretamente a notificação de alteração de propriedade. Quando as células são reutilizadas seus valores de propriedade serão alterado quando o contexto de associação é atualizado ao de uma célula disponível, com `PropertyChanged` eventos sendo gerados. Para obter mais informações, consulte [personalizar uma ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md). Para obter mais informações sobre a reciclagem de célula, consulte [estratégia de cache](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy).

## <a name="related-links"></a>Links relacionados

- [Criado em células (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Células personalizadas (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Associação de contexto alterado (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-bindingcontextchanged)
