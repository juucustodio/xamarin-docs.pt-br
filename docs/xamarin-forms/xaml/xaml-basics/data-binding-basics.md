---
title: Parte 4. Conceitos básicos da associação de dados
description: As ligações de dados permitem que as propriedades de dois objetos sejam vinculadas para que uma alteração em um cause uma alteração no outro.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 342288C3-BB4C-4924-B178-72E112D777BA
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2dd2abed23704f6a67ac34bc828c48e7200cf99a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558708"
---
# <a name="part-4-data-binding-basics"></a>Parte 4. Conceitos básicos da associação de dados

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_As ligações de dados permitem que as propriedades de dois objetos sejam vinculadas para que uma alteração em um cause uma alteração no outro. Essa é uma ferramenta muito valiosa e, embora as ligações de dados possam ser definidas inteiramente no código, o XAML fornece atalhos e conveniência. Consequentemente, uma das extensões de marcação mais importantes no Xamarin.Forms é a associação._

## <a name="data-bindings"></a>Associações de dados

Vinculações de dados conectam Propriedades de dois objetos, chamados de *origem* e *destino*. No código, são necessárias duas etapas: a `BindingContext` Propriedade do objeto de destino deve ser definida como o objeto de origem, e o `SetBinding` método (geralmente usado em conjunto com `Binding` a classe) deve ser chamado no objeto de destino para associar uma propriedade desse objeto a uma propriedade do objeto de origem.

A propriedade de destino deve ser uma propriedade vinculável, o que significa que o objeto de destino deve derivar de `BindableObject` . A Xamarin.Forms documentação online indica quais propriedades são propriedades vinculáveis. Uma propriedade de como `Label` `Text` é associada à propriedade vinculável `TextProperty` .

Na marcação, você também deve executar as mesmas duas etapas que são necessárias no código, exceto que a `Binding` extensão de marcação assume o lugar da `SetBinding` chamada e a `Binding` classe.

No entanto, quando você define as associações de dados em XAML, há várias maneiras de definir o `BindingContext` do objeto de destino. Às vezes, ele é definido a partir do arquivo code-behind, às vezes usando uma `StaticResource` `x:Static` extensão de marcação ou e, às vezes, como o conteúdo das `BindingContext` marcas de elemento de propriedade.

As associações são usadas com mais frequência para conectar os visuais de um programa a um modelo de dados subjacente, geralmente em uma realização da arquitetura do aplicativo MVVM (Model-View-ViewModel), conforme discutido na [parte 5. De associações de dados para MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), mas outros cenários são possíveis.

## <a name="view-to-view-bindings"></a>Associações de exibição para exibição

Você pode definir associações de dados para vincular propriedades de dois modos de exibição na mesma página. Nesse caso, você define o `BindingContext` do objeto de destino usando a `x:Reference` extensão de marcação.

Aqui está um arquivo XAML que contém uma `Slider` e duas `Label` exibições, uma das quais é girada pelo `Slider` valor e outra que exibe o `Slider` valor:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderBindingsPage"
             Title="Slider Bindings Page">

    <StackLayout>
        <Label Text="ROTATION"
               BindingContext="{x:Reference Name=slider}"
               Rotation="{Binding Path=Value}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="CenterAndExpand" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
               FontAttributes="Bold"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

O `Slider` contém um `x:Name` atributo que é referenciado pelas duas `Label` exibições usando a `x:Reference` extensão de marcação.

A `x:Reference` extensão de associação define uma propriedade chamada `Name` para definir como o nome do elemento referenciado, nesse caso `slider` . No entanto, a `ReferenceExtension` classe que define a `x:Reference` extensão de marcação também define um `ContentProperty` atributo para `Name` , o que significa que ela não é explicitamente necessária. Apenas para a variedade, o primeiro `x:Reference` inclui "Name =", mas o segundo não:

```xaml
BindingContext="{x:Reference Name=slider}"
…
BindingContext="{x:Reference slider}"
```

A `Binding` própria extensão de marcação pode ter várias propriedades, assim como `BindingBase` a `Binding` classe e. O `ContentProperty` for `Binding` é `Path` , mas a parte "Path =" da extensão de marcação poderá ser omitida se o caminho for o primeiro item na `Binding` extensão de marcação. O primeiro exemplo tem "Path =", mas o segundo exemplo o omite:

```xaml
Rotation="{Binding Path=Value}"
…
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Todas as propriedades podem estar em uma linha ou separadas em várias linhas:

```xaml
Text="{Binding Value,
               StringFormat='The angle is {0:F0} degrees'}"
```

Faça tudo o que for conveniente.

Observe a `StringFormat` Propriedade na segunda `Binding` extensão de marcação. No Xamarin.Forms , as associações não executam conversões de tipo implícitas e, se você precisar exibir um objeto que não seja de cadeia de caracteres como uma cadeia de caracteres, você deve fornecer um conversor de tipo ou usar `StringFormat` . Nos bastidores, o `String.Format` método estático é usado para implementar `StringFormat` . Isso é potencialmente um problema, pois as especificações de formatação do .NET envolvem chaves, que também são usadas para delimitar extensões de marcação. Isso gera um risco de confundir o analisador XAML. Para evitar isso, coloque toda a cadeia de caracteres de formatação entre aspas simples:

```xaml
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Este é o programa em execução:

[![Associações de exibição para exibição](data-binding-basics-images/sliderbinding.png)](data-binding-basics-images/sliderbinding-large.png#lightbox)

## <a name="the-binding-mode"></a>O modo de associação

Uma única exibição pode ter associações de dados em várias de suas propriedades. No entanto, cada exibição pode ter apenas um `BindingContext` , portanto, várias associações de dados nessa exibição devem todas as propriedades de referência do mesmo objeto.

A solução para esse e outros problemas envolve a `Mode` propriedade, que é definida como um membro da `BindingMode` enumeração:

- `Default`
- `OneWay` — os valores são transferidos da origem para o destino
- `OneWayToSource` — os valores são transferidos do destino para a origem
- `TwoWay` — os valores são transferidos de ambas as formas entre a origem e o destino
- `OneTime` — os dados vão da origem para o destino, mas somente quando as `BindingContext` alterações

O programa a seguir demonstra um uso comum dos `OneWayToSource` `TwoWay` modos de vinculação e. Quatro `Slider` exibições destinam-se a controlar as `Scale` `Rotate` Propriedades,, `RotateX` e `RotateY` de um `Label` . A princípio, parece que essas quatro propriedades do `Label` devem ser destinos de vinculação de dados porque cada uma está sendo definida por um `Slider` . No entanto, o `BindingContext` de `Label` pode ser apenas um objeto, e há quatro controles deslizantes diferentes.

Por esse motivo, todas as associações são definidas de maneira aparentemente retroativa: o `BindingContext` de cada um dos quatro controles deslizantes é definido como o `Label` , e as associações são definidas nas `Value` Propriedades dos controles deslizantes. Usando os `OneWayToSource` modos e `TwoWay` , essas `Value` Propriedades podem definir as propriedades de origem, que são as `Scale` `Rotate` Propriedades,, `RotateX` e `RotateY` do `Label` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.SliderTransformsPage"
             Padding="5"
             Title="Slider Transforms Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="Auto" />
        </Grid.ColumnDefinitions>

        <!-- Scaled and rotated Label -->
        <Label x:Name="label"
               Text="TEXT"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <!-- Slider and identifying Label for Scale -->
        <Slider x:Name="scaleSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="1" Grid.Column="0"
                Maximum="10"
                Value="{Binding Scale, Mode=TwoWay}" />

        <Label BindingContext="{x:Reference scaleSlider}"
               Text="{Binding Value, StringFormat='Scale = {0:F1}'}"
               Grid.Row="1" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for Rotation -->
        <Slider x:Name="rotationSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="2" Grid.Column="0"
                Maximum="360"
                Value="{Binding Rotation, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationSlider}"
               Text="{Binding Value, StringFormat='Rotation = {0:F0}'}"
               Grid.Row="2" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationX -->
        <Slider x:Name="rotationXSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="3" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationX, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationXSlider}"
               Text="{Binding Value, StringFormat='RotationX = {0:F0}'}"
               Grid.Row="3" Grid.Column="1"
               VerticalTextAlignment="Center" />

        <!-- Slider and identifying Label for RotationY -->
        <Slider x:Name="rotationYSlider"
                BindingContext="{x:Reference label}"
                Grid.Row="4" Grid.Column="0"
                Maximum="360"
                Value="{Binding RotationY, Mode=OneWayToSource}" />

        <Label BindingContext="{x:Reference rotationYSlider}"
               Text="{Binding Value, StringFormat='RotationY = {0:F0}'}"
               Grid.Row="4" Grid.Column="1"
               VerticalTextAlignment="Center" />
    </Grid>
</ContentPage>
```

As associações em três das `Slider` exibições são `OneWayToSource` , o que significa que o `Slider` valor causa uma alteração na propriedade de seu `BindingContext` , que é o `Label` nome `label` . Essas três `Slider` exibições causam alterações nas `Rotate` `RotateX` Propriedades, e `RotateY` do `Label` .

No entanto, a associação para a `Scale` propriedade é `TwoWay` . Isso ocorre porque a `Scale` propriedade tem um valor padrão de 1 e o uso de uma `TwoWay` associação faz com que o `Slider` valor inicial seja definido como 1 em vez de 0. Se essa associação tiver sido `OneWayToSource` , a `Scale` Propriedade inicialmente seria definida como 0 a partir do `Slider` valor padrão. O `Label` não estaria visível e isso pode causar alguma confusão ao usuário.

 [![Associações anteriores](data-binding-basics-images/slidertransforms.png)](data-binding-basics-images/slidertransforms-large.png#lightbox)

 > [!NOTE]
 > A [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe também tem [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) Propriedades e, que dimensionam o `VisualElement` no eixo x e y, respectivamente.

## <a name="bindings-and-collections"></a>Associações e coleções

Nada ilustra o poder do XAML e das ligações de dados melhor do que um modelo `ListView` .

`ListView` define uma `ItemsSource` Propriedade do tipo `IEnumerable` e exibe os itens nessa coleção. Esses itens podem ser objetos de qualquer tipo. Por padrão, `ListView` o usa o `ToString` método de cada item para exibir esse item. Às vezes, isso é exatamente o que você deseja, mas em muitos casos, `ToString` retorna apenas o nome de classe totalmente qualificado do objeto.

No entanto, os itens na `ListView` coleção podem ser exibidos da maneira que você desejar por meio do uso de um *modelo*, que envolve uma classe derivada de `Cell` . O modelo é clonado para cada item no `ListView` , e as vinculações de dados que foram definidas no modelo são transferidas para os clones individuais.

Com muita frequência, você desejará criar uma célula Personalizada para esses itens usando a `ViewCell` classe. Esse processo é um pouco confuso no código, mas em XAML ele se torna muito simples.

Incluído no projeto XamlSamples é uma classe chamada `NamedColor` . Cada `NamedColor` objeto tem `Name` e `FriendlyName` Propriedades do tipo e `string` uma `Color` Propriedade do tipo `Color` . Além disso, o `NamedColor` tem 141 campos somente leitura estáticos do tipo `Color` correspondente às cores definidas na Xamarin.Forms `Color` classe. Um construtor estático cria uma `IEnumerable<NamedColor>` coleção que contém `NamedColor` objetos correspondentes a esses campos estáticos e os atribui à sua propriedade estática pública `All` .

Definir a `NamedColor.All` propriedade estática para `ItemsSource` de um `ListView` é fácil usando a extensão de `x:Static` marcação:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ListView ItemsSource="{x:Static local:NamedColor.All}" />

</ContentPage>
```

A exibição resultante estabelece que os itens são verdadeiramente do tipo `XamlSamples.NamedColor` :

[![Associação a uma coleção](data-binding-basics-images/listview1.png)](data-binding-basics-images/listview1-large.png#lightbox)

Não é muito mais informação, mas a `ListView` é rolável e selecionável.

Para definir um modelo para os itens, você desejará dividir a `ItemTemplate` propriedade como um elemento de propriedade e defini-la como um `DataTemplate` , que faz referência a uma `ViewCell` . Para a `View` Propriedade do `ViewCell` , você pode definir um layout de uma ou mais exibições para exibir cada item. Veja um exemplo simples:

```xaml
<ListView ItemsSource="{x:Static local:NamedColor.All}">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <ViewCell.View>
                    <Label Text="{Binding FriendlyName}" />
                </ViewCell.View>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

> [!NOTE]
> A fonte de associação para células e filhos de células é a `ListView.ItemsSource` coleção.

O `Label` elemento é definido como a `View` Propriedade do `ViewCell` . (As `ViewCell.View` marcas não são necessárias porque a `View` propriedade é a propriedade Content de `ViewCell` .) Essa marcação exibe a `FriendlyName` propriedade de cada `NamedColor` objeto:

[![Associando a uma coleção com um DataTemplate](data-binding-basics-images/listview2.png)](data-binding-basics-images/listview2-large.png#lightbox)

Muito melhor. Agora tudo o que é necessário é sofisticar o modelo de item com mais informações e a cor real. Para dar suporte a esse modelo, alguns valores e objetos foram definidos no dicionário de recursos da página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ContentPage.Resources>
        <ResourceDictionary>
            <OnPlatform x:Key="boxSize"
                        x:TypeArguments="x:Double">
                <On Platform="iOS, Android, UWP" Value="50" />
            </OnPlatform>

            <OnPlatform x:Key="rowHeight"
                        x:TypeArguments="x:Int32">
                <On Platform="iOS, Android, UWP" Value="60" />
            </OnPlatform>

            <local:DoubleToIntConverter x:Key="intConverter" />

        </ResourceDictionary>
    </ContentPage.Resources>

    <ListView ItemsSource="{x:Static local:NamedColor.All}"
              RowHeight="{StaticResource rowHeight}">
        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <StackLayout Padding="5, 5, 0, 5"
                                 Orientation="Horizontal"
                                 Spacing="15">

                        <BoxView WidthRequest="{StaticResource boxSize}"
                                 HeightRequest="{StaticResource boxSize}"
                                 Color="{Binding Color}" />

                        <StackLayout Padding="5, 0, 0, 0"
                                     VerticalOptions="Center">

                            <Label Text="{Binding FriendlyName}"
                                   FontAttributes="Bold"
                                   FontSize="Medium" />

                            <StackLayout Orientation="Horizontal"
                                         Spacing="0">
                                <Label Text="{Binding Color.R,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat='R={0:X2}'}" />

                                <Label Text="{Binding Color.G,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', G={0:X2}'}" />

                                <Label Text="{Binding Color.B,
                                       Converter={StaticResource intConverter},
                                       ConverterParameter=255,
                                       StringFormat=', B={0:X2}'}" />
                            </StackLayout>
                        </StackLayout>
                    </StackLayout>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

Observe o uso de `OnPlatform` para definir o tamanho de uma `BoxView` e a altura das `ListView` linhas. Embora os valores de todas as plataformas sejam os mesmos, a marcação poderia facilmente ser adaptada para outros valores para ajustar a exibição.

## <a name="binding-value-converters"></a>Associação de conversores de valor

O arquivo XAML de **demonstração do ListView** anterior exibe as `R` Propriedades individual, `G` e `B` da Xamarin.Forms `Color` estrutura. Essas propriedades são do tipo `double` e do intervalo de 0 a 1. Se você quiser exibir os valores hexadecimais, não poderá simplesmente usar `StringFormat` com uma especificação de formatação "X2". Isso funciona apenas para números inteiros e, além disso, os `double` valores precisam ser multiplicados por 255.

Esse pequeno problema foi resolvido com um *conversor de valor*, também chamado de *conversor de associação*. Essa é uma classe que implementa a `IValueConverter` interface, o que significa que ela tem dois métodos chamados `Convert` e `ConvertBack` . O `Convert` método é chamado quando um valor é transferido da origem para o destino; `ConvertBack` o método é chamado para transferências do destino para a origem `OneWayToSource` ou `TwoWay` associações:

```csharp
using System;
using System.Globalization;
using Xamarin.Forms;

namespace XamlSamples
{
    class DoubleToIntConverter : IValueConverter
    {
        public object Convert(object value, Type targetType,
                              object parameter, CultureInfo culture)
        {
            double multiplier;

            if (!Double.TryParse(parameter as string, out multiplier))
                multiplier = 1;

            return (int)Math.Round(multiplier * (double)value);
        }

        public object ConvertBack(object value, Type targetType,
                                  object parameter, CultureInfo culture)
        {
            double divider;

            if (!Double.TryParse(parameter as string, out divider))
                divider = 1;

            return ((double)(int)value) / divider;
        }
    }
}
```

O `ConvertBack` método não desempenha uma função neste programa porque as associações são apenas uma maneira de origem para destino.

Uma associação faz referência a um conversor de associação com a `Converter` propriedade. Um conversor de associação também pode aceitar um parâmetro especificado com a `ConverterParameter` propriedade. Para alguma versatilidade, é assim que o multiplicador é especificado. O conversor de associação verifica o parâmetro do conversor em busca de um `double` valor válido.

O conversor é instanciado no dicionário de recursos para que ele possa ser compartilhado entre várias associações:

```xaml
<local:DoubleToIntConverter x:Key="intConverter" />
```

Três associações de dados fazem referência a essa única instância. Observe que a `Binding` extensão de marcação contém uma `StaticResource` extensão de marcação inserida:

```xaml
<Label Text="{Binding Color.R,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```

Este é o resultado:

[![Associação a uma coleção com DataTemplate e conversores](data-binding-basics-images/listview3.png)](data-binding-basics-images/listview3-large.png#lightbox)

O `ListView` é bastante sofisticado no tratamento de alterações que podem ocorrer dinamicamente nos dados subjacentes, mas somente se você executar determinadas etapas. Se a coleção de itens atribuída à `ItemsSource` propriedade das `ListView` alterações durante o tempo de execução, ou seja, se os itens puderem ser adicionados ou removidos da coleção, use uma `ObservableCollection` classe para esses itens. `ObservableCollection` implementa a `INotifyCollectionChanged` interface e `ListView` instalará um manipulador para o `CollectionChanged` evento.

Se as propriedades dos próprios itens forem alteradas durante o tempo de execução, os itens na coleção deverão implementar as `INotifyPropertyChanged` alterações de interface e de sinal aos valores de propriedade usando o `PropertyChanged` evento. Isso é demonstrado na próxima parte desta série, [parte 5. Da vinculação de dados com o MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Resumo

As associações de dados fornecem um mecanismo poderoso para vincular propriedades entre dois objetos dentro de uma página ou entre objetos visuais e dados subjacentes. Mas quando o aplicativo começa a trabalhar com fontes de dados, um padrão de arquitetura de aplicativo popular começa a surgir como um paradigma útil. Isso é abordado na [parte 5. De associações de dados para MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="related-links"></a>Links Relacionados

- [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introdução com XAML (exemplo)](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintaxe XAML essencial (exemplo)](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Extensões de marcação XAML (exemplo)](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 5. Da vinculação de dados ao MVVM (exemplo)](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)