---
title: Personalizando a aparência de célula do ListView
description: Este artigo explora as opções para apresentar dados em aplicativos xamarin. Forms, ao mesmo tempo, aproveitar a conveniência do controle ListView.
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2019
ms.openlocfilehash: ab54b54c9f2f7d6d7748137ea079439b7c3ddfca
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998090"
---
# <a name="customizing-listview-cell-appearance"></a>Personalizando a aparência de célula do ListView

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)

A classe Xamarin. [`ListView`](xref:Xamarin.Forms.ListView) Forms é usada para apresentar listas roláveis, que podem ser personalizadas por meio do uso `ViewCell` de elementos. Um `ViewCell` elemento pode exibir texto e imagens, indicar um estado verdadeiro/falso e receber entrada do usuário.

## <a name="built-in-cells"></a>Criado em células
O Xamarin. Forms vem com células internas que funcionam para muitos aplicativos:

- [`TextCell`](#textcell)os controles são usados para exibir texto com uma segunda linha opcional para texto detalhado.
- [`ImageCell`](#imagecell)os controles são semelhantes `TextCell`a s, mas incluem uma imagem à esquerda do texto.
- `SwitchCell`os controles são usados para apresentar e capturar Estados on/off ou true/false.
- `EntryCell`os controles são usados para apresentar dados de texto que o usuário pode editar.

Os [`SwitchCell`](~/xamarin-forms/user-interface/tableview.md#switchcell) controles [`EntryCell`](~/xamarin-forms/user-interface/tableview.md#entrycell) e são mais comumente usados no contexto de um [`TableView`](~/xamarin-forms/user-interface/tableview.md).

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell) é uma célula para exibir o texto, opcionalmente com uma segunda linha como texto de detalhes. A captura de tela `TextCell` a seguir mostra itens no Ios e no Android:

![](customizing-cell-appearance-images/text-cell-default.png "Exemplo de TextCell padrão")

TextCells são renderizados como controles nativos em tempo de execução, portanto, o desempenho é muito bom em comparação com um personalizado `ViewCell`. Textcéls são personalizáveis, permitindo que você defina as seguintes propriedades:

- `Text` &ndash; o texto que é mostrado na primeira linha, em fonte grande.
- `Detail` &ndash; o texto que é mostrado abaixo da primeira linha, em uma fonte menor.
- `TextColor` &ndash; a cor do texto.
- `DetailColor` &ndash; a cor do texto de detalhes

A captura de tela `TextCell` a seguir mostra itens com propriedades de cores personalizadas:

![](customizing-cell-appearance-images/text-cell-custom.png "Exemplo de textcell personalizado")

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell), como `TextCell`, pode ser usado para exibir texto e texto secundário detalhes e oferece excelente desempenho usando controles nativos de cada plataforma. `ImageCell` é diferente do `TextCell` em que ele exibe uma imagem à esquerda do texto.

A captura de tela `ImageCell` a seguir mostra itens no Ios e no Android: !["Exemplo de ImageCell padrão"](customizing-cell-appearance-images/image-cell-default.png "Exemplo de ImageCell padrão")

`ImageCell` é útil quando você precisa exibir uma lista de dados com um aspecto visual, como uma lista de contatos ou filmes. `ImageCell`os s são personalizáveis, permitindo que você defina:

- `Text` &ndash; o texto que é mostrado na primeira linha, em fontes grandes
- `Detail` &ndash; o texto que é mostrado abaixo da primeira linha, em uma fonte menor
- `TextColor` &ndash; a cor do texto
- `DetailColor` &ndash; a cor do texto de detalhes
- `ImageSource` &ndash; a imagem a ser exibida ao lado do texto

A captura de tela `ImageCell` a seguir mostra itens com propriedades de cores personalizadas: !["Exemplo de ImageCell personalizado"](customizing-cell-appearance-images/image-cell-custom.png "Exemplo de ImageCell personalizado")

## <a name="custom-cells"></a>Células personalizadas
As células personalizadas permitem que você crie layouts de células que não têm suporte nas células internas. Por exemplo, você talvez queira apresentar uma célula com dois rótulos que têm o mesmo peso. Um `TextCell` seria insuficiente porque o `TextCell` tem um rótulo que for menor. A maioria das personalizações de célula adicionar dados somente leitura adicionais (como rótulos adicionais, imagens ou outras informações de exibição).

Todas as células personalizadas devem derivar de [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), a mesma classe base que todos os internos célula tipos de uso.

O Xamarin. Forms oferece um [comportamento](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy) de cache `ListView` no controle que pode melhorar o desempenho de rolagem para alguns tipos de células personalizadas.

A captura de tela a seguir mostra um exemplo de uma célula Personalizada:

!["Exemplo de célula Personalizada"](customizing-cell-appearance-images/custom-cell.png "Exemplo de célula Personalizada")

### <a name="xaml"></a>XAML
A célula Personalizada mostrada na captura de tela anterior pode ser criada com o seguinte XAML:

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

O XAML funciona da seguinte maneira:

- A célula personalizada está aninhada em uma `DataTemplate`, que está dentro de `ListView.ItemTemplate`. Esse é o mesmo processo que usar qualquer célula interna.
- `ViewCell` é o tipo de célula personalizado. O filho do `DataTemplate` elemento deve ser de, ou derivar `ViewCell` , da classe.
- Dentro do `ViewCell`, o layout pode ser gerenciado por qualquer layout do Xamarin. Forms. Neste exemplo, layout é gerenciado por um `StackLayout`, que permite que a cor do plano de fundo seja personalizada.

> [!NOTE]
> Qualquer propriedade de `StackLayout` que seja ligável pode ser associada dentro de uma célula Personalizada. No entanto, esse recurso não é mostrado no exemplo de XAML.

### <a name="code"></a>Código

Uma célula Personalizada também pode ser criada no código. Primeiro, uma classe personalizada que deriva de `ViewCell` deve ser criada:

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

No construtor da página, a `ItemTemplate` propriedade de ListView é definida como um `DataTemplate` com o `CustomCell` tipo especificado:

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

    public string Name
    {
        get { return(string)GetValue (NameProperty); }
        set { SetValue (NameProperty, value); }
    }

    public int Age
    {
        get { return(int)GetValue (AgeProperty); }
        set { SetValue (AgeProperty, value); }
    }

    public string Location
    {
        get { return(string)GetValue (LocationProperty); }
        set { SetValue (LocationProperty, value); }
    }
    ...

    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();

        if (BindingContext != null)
        {
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

var listView = new ListView
{
    ItemsSource = people,
    ItemTemplate = customCell
};
```

No iOS e Android, se o [ `ListView` ](xref:Xamarin.Forms.ListView) reciclagem de elementos e a célula personalizada usa um renderizador personalizado, o renderizador personalizado deve implementar corretamente a notificação de alteração de propriedade. Quando as células são reutilizadas seus valores de propriedade serão alterado quando o contexto de associação é atualizado ao de uma célula disponível, com `PropertyChanged` eventos sendo gerados. Para obter mais informações, consulte [personalizar uma ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md). Para obter mais informações sobre a reciclagem de célula, consulte [estratégia de cache](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy).

## <a name="related-links"></a>Links relacionados

- [Criado em células (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Células personalizadas (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Associação de contexto alterado (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-bindingcontextchanged)
