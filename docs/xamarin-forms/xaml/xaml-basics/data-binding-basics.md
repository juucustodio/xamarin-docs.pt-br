---
title: Parte 4. Noções básicas de vinculação de dados
description: Associações de dados permitem que as propriedades de dois objetos a serem vinculadas para que a alteração de uma causa uma alteração no outro.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 342288C3-BB4C-4924-B178-72E112D777BA
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: aa576e4af5d804eb5b8d78643ffa762b8a15c6dc
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171762"
---
# <a name="part-4-data-binding-basics"></a>Parte 4. Noções básicas de vinculação de dados

_Associações de dados permitem que as propriedades de dois objetos a serem vinculadas para que a alteração de uma causa uma alteração no outro. Essa é uma ferramenta muito valiosa, e enquanto as associações de dados podem ser definidas inteiramente no código, XAML fornece atalhos e conveniência. Consequentemente, uma das extensões de marcação mais importantes no xamarin. Forms é associação._

## <a name="data-bindings"></a>Associações de dados

Associações de dados se conectar a propriedades de dois objetos, chamados de *fonte* e o *destino*. No código, são necessárias duas etapas: O `BindingContext` propriedade do objeto de destino deve ser definida para o objeto de origem e o `SetBinding` método (geralmente usado em conjunto com o `Binding` classe) deve ser chamado no objeto de destino para associar uma propriedade de que objeto a uma propriedade do objeto de origem.

A propriedade de destino deve ser uma propriedade vinculável, o que significa que o objeto de destino deve derivar de `BindableObject`. A documentação do xamarin. Forms online indica quais propriedades são propriedades associáveis. Uma propriedade de `Label` , como `Text` associado com a propriedade associável `TextProperty`.

Na marcação, você também deve executar as mesmas duas etapas que são necessárias no código, exceto pelo fato do `Binding` extensão de marcação ocupa o lugar do `SetBinding` chamar e o `Binding` classe.

No entanto, quando você define vinculações de dados no XAML, há várias maneiras de definir o `BindingContext` do objeto de destino. Às vezes, ele é definido do arquivo code-behind, às vezes, usando um `StaticResource` ou `x:Static` extensão de marcação e às vezes, como o conteúdo de `BindingContext` marcas de elemento de propriedade.

Associações são usadas com mais frequência para conectar os elementos visuais de um programa com um modelo de dados subjacente, geralmente em uma realização da arquitetura MVVM (Model-View-ViewModel) do aplicativo, conforme discutido em [parte 5. De associações de dados a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), mas outros cenários são possíveis.

## <a name="view-to-view-bindings"></a>Associações de exibição para exibição

Você pode definir associações de dados para vincular as propriedades de dois modos de exibição na mesma página. Nesse caso, você definir a `BindingContext` do objeto de destino usando o `x:Reference` extensão de marcação.

Aqui está um arquivo XAML que contém um `Slider` e duas `Label` modos de exibição, um dos quais é girado, o `Slider` valor e outro que exibe o `Slider` valor:

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

O `Slider` contém uma `x:Name` atributo que é referenciado pelas duas `Label` exibições usando o `x:Reference` extensão de marcação.

O `x:Reference` extensão de associação define uma propriedade chamada `Name` para definir como o nome do elemento referenciado, nesse caso, `slider`. No entanto, o `ReferenceExtension` classe que define o `x:Reference` extensão de marcação também define um `ContentProperty` de atributo para `Name`, que significa que não é explicitamente necessário. Apenas para a variedade, a primeira `x:Reference` inclui "nome =", mas não o segundo:

```csharp
BindingContext="{x:Reference Name=slider}"
…
BindingContext="{x:Reference slider}"
```

O `Binding` extensão de marcação em si pode ter várias propriedades, assim como o `BindingBase` e `Binding` classe. O `ContentProperty` para `Binding` é `Path`, mas o "caminho =" parte da extensão de marcação pode ser omitido se o caminho é o primeiro item no `Binding` extensão de marcação. O exemplo primeiro tem "caminho =", mas o segundo exemplo omite a ele:

```csharp
Rotation="{Binding Path=Value}"
…
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

As propriedades podem estar em uma linha ou separados em várias linhas:

```csharp
Text="{Binding Value,
               StringFormat='The angle is {0:F0} degrees'}"
```

Fazer o que for conveniente.

Observe que o `StringFormat` propriedade na segunda `Binding` extensão de marcação. No xamarin. Forms, as associações não executam as conversões de tipo implícito, e se você precisar exibir um objeto de cadeia de caracteres não como uma cadeia de caracteres, você deve fornecer um conversor de tipo ou usar `StringFormat`. Nos bastidores, estático `String.Format` método é usado para implementar `StringFormat`. Que é potencialmente um problema, porque as especificações de formatação .NET envolvem chaves, que também são usadas para delimitar as extensões de marcação. Isso cria um risco de confundir o analisador XAML. Para evitar isso, coloque toda a cadeia de caracteres de formatação em aspas simples:

```csharp
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Aqui está o programa em execução:

[![](data-binding-basics-images/sliderbinding.png "Associações de exibição para exibição")](data-binding-basics-images/sliderbinding-large.png#lightbox "associações de exibição para exibição ")

## <a name="the-binding-mode"></a>O modo de ligação

Uma única exibição pode ter associações de dados em várias de suas propriedades. No entanto, cada modo de exibição pode ter apenas um `BindingContext`, portanto, várias associações de dados nessa exibição devem todas as propriedades do mesmo objeto de referência.

A solução para esse e outros problemas envolve a `Mode` propriedade, que é definida como um membro do `BindingMode` enumeração:

- `Default`
- `OneWay` — valores que são transferidos da origem ao destino
- `OneWayToSource` — valores que são transferidos do destino para a fonte
- `TwoWay` — valores são transferidos ambas as direções entre origem e destino

O programa a seguir demonstra um uso comum de `OneWayToSource` e `TwoWay` modos de ligação. Quatro `Slider` modos de exibição destinam-se ao controle de `Scale`, `Rotate`, `RotateX`, e `RotateY` propriedades de um `Label`. A princípio, parece como se essas quatro propriedades do `Label` deve ser destinos de vinculação de dados, porque cada um está sendo definida um `Slider`. No entanto, o `BindingContext` de `Label` pode ser apenas um objeto, e há quatro controles deslizantes diferentes.

Por esse motivo, todas as associações são definidas aparentemente com versões anteriores maneiras: os `BindingContext` de cada um dos quatro controles deslizantes é definido como o `Label`, e as associações são definidas no `Value` propriedades dos controles deslizantes. Usando o `OneWayToSource` e `TwoWay` modos, essas `Value` propriedades podem definir as propriedades da fonte, que são os `Scale`, `Rotate`, `RotateX`, e `RotateY` propriedades do `Label`:

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

As associações em três da `Slider` modos de exibição são `OneWayToSource`, o que significa que o `Slider` valor faz com que uma alteração na propriedade do seu `BindingContext`, que é o `Label` chamado `label`. Esses três `Slider` modos de exibição causam alterações para o `Rotate`, `RotateX`, e `RotateY` propriedades do `Label`.

No entanto, a associação para o `Scale` é de propriedade `TwoWay`. Isso ocorre porque o `Scale` propriedade tem um valor padrão de 1 e usando um `TwoWay` associação faz com que o `Slider` inicial do valor a ser definido em 1 em vez de 0. Se essa associação foram `OneWayToSource`, o `Scale` propriedade inicialmente seria definida como 0 do `Slider` valor padrão. O `Label` não seria visível e que pode causar alguma confusão ao usuário.

 [![](data-binding-basics-images/slidertransforms.png "Com versões anteriores associações")](data-binding-basics-images/slidertransforms-large.png#lightbox "com versões anteriores associações")

 > [!NOTE]
 > O [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe também tem [ `ScaleX` ](xref:Xamarin.Forms.VisualElement.ScaleX) e [ `ScaleY` ](xref:Xamarin.Forms.VisualElement.ScaleY) propriedades, que dimensionarem o `VisualElement` no eixo x e y respectivamente.

## <a name="bindings-and-collections"></a>Associações e coleções

Nada ilustra o poder do XAML e associações de dados melhores do que com um modelo `ListView`.

`ListView` define uma `ItemsSource` propriedade do tipo `IEnumerable`, e exibe os itens dessa coleção. Esses itens podem ser objetos de qualquer tipo. Por padrão, `ListView` usa o `ToString` método de cada item para exibir o item. Às vezes, é isso que você deseja, mas em muitos casos, `ToString` retorna somente o nome de classe totalmente qualificado do objeto.

No entanto, os itens a `ListView` coleção pode ser exibida como quiser com o uso de um *modelo*, que envolve uma classe que deriva de `Cell`. O modelo for clonado para todos os itens a `ListView`, e as associações de dados que foram definidas no modelo são transferidas para os clones individuais.

Com muita frequência, você desejará criar uma célula personalizada para esses itens usando o `ViewCell` classe. Esse processo é um pouco confuso no código, mas no XAML torna-se muito simples.

O XamlSamples projeto há também uma classe chamada `NamedColor`. Cada `NamedColor` objeto tem `Name` e `FriendlyName` propriedades do tipo `string`e um `Color` propriedade do tipo `Color`. Além disso, `NamedColor` tem 141 campos estáticos somente leitura do tipo `Color` correspondente para as cores definidas no xamarin. Forms `Color` classe. Um construtor estático cria um `IEnumerable<NamedColor>` coleção que contém `NamedColor` objetos correspondentes a esses campos estáticos e o atribui a seu público estático `All` propriedade.

Configuração estática `NamedColor.All` propriedade para o `ItemsSource` de uma `ListView` é fácil usando o `x:Static` extensão de marcação:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ListView ItemsSource="{x:Static local:NamedColor.All}" />

</ContentPage>
```

A exibição resultante estabelece que os itens são realmente do tipo `XamlSamples.NamedColor`:

[![](data-binding-basics-images/listview1.png "Associando a uma coleção")](data-binding-basics-images/listview1-large.png#lightbox "associando a uma coleção")

Não é o máximo de informações, mas o `ListView` é rolável e selecionável.

Para definir um modelo para os itens, você desejará separar a `ItemTemplate` a propriedade como um elemento de propriedade e defini-lo como um `DataTemplate`, que, em seguida, referencia um `ViewCell`. Para o `View` propriedade do `ViewCell` você pode definir um layout de uma ou mais exibições para exibir cada item. Aqui está um exemplo simples:

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

O `Label` é definido como o `View` propriedade do `ViewCell`. (O `ViewCell.View` marcas não são necessárias porque o `View` é a propriedade de conteúdo do `ViewCell`.) Essa marcação exibe a `FriendlyName` propriedade de cada `NamedColor` objeto:

[![](data-binding-basics-images/listview2.png "Associando a uma coleção com um DataTemplate")](data-binding-basics-images/listview2-large.png#lightbox "associando a uma coleção com um DataTemplate")

Muito melhor. Agora tudo o que precisamos é aprimorar o modelo de item com mais informações e a cor real. Para dar suporte a esse modelo, alguns valores e os objetos foram definidos no dicionário de recursos da página:

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

Observe o uso de `OnPlatform` para definir o tamanho de um `BoxView` e a altura do `ListView` linhas. Embora os valores para todas as plataformas são os mesmos, a marcação pode facilmente ser adaptada para outros valores ajustar a exibição.

## <a name="binding-value-converters"></a>Conversores de valor de associação

Anterior **demonstração de ListView** arquivo XAML exibe o indivíduo `R`, `G`, e `B` propriedades do xamarin. Forms `Color` estrutura. Essas propriedades são do tipo `double` e intervalo de 0 a 1. Se você quiser exibir os valores hexadecimais, você não pode simplesmente usar `StringFormat` com uma especificação de formatação "X2". Isso funciona apenas para números inteiros e Além disso, o `double` valores precisam ser multiplicado por 255.

Esse pequeno problema foi resolvido com uma *conversor de valor*, também chamado um *conversor de associação*. Essa é uma classe que implementa o `IValueConverter` interface, o que significa que ele tem dois métodos chamados `Convert` e `ConvertBack`. O `Convert` método é chamado quando um valor é transferido da origem para destino; o `ConvertBack` método é chamado para transferências de destino a fonte no `OneWayToSource` ou `TwoWay` associações:

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

O `ConvertBack` método não desempenham um papel nesse programa porque as associações são apenas uma maneira de origem ao destino.

Uma associação faz referência a um conversor de associação com o `Converter` propriedade. Um conversor de associação também pode aceitar um parâmetro especificado com o `ConverterParameter` propriedade. Para alguns versatilidade, isso é como o multiplicador é especificado. O conversor de associação verifica se o parâmetro de conversor para válido `double` valor.

O conversor é instanciado no dicionário de recursos para que ele possa ser compartilhado entre várias associações:

```xaml
<local:DoubleToIntConverter x:Key="intConverter" />
```

Associações de dados de três fazer referência a essa única instância. Observe que o `Binding` extensão de marcação contém inserida `StaticResource` extensão de marcação:

```xaml
<Label Text="{Binding Color.R,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```

Aqui está o resultado:

[![](data-binding-basics-images/listview3.png "Associando a uma coleção com um DataTemplate e conversores")](data-binding-basics-images/listview3-large.png#lightbox "para uma coleção com um DataTemplate e conversores de associação")

O `ListView` é bastante sofisticados em lidar com alterações que podem ocorrer dinamicamente nos dados subjacentes, mas somente se você seguir etapas específicas. Se a coleção de itens atribuídos à `ItemsSource` propriedade do `ListView` alterações durante o tempo de execução — que, se itens podem ser adicionados a ou removido da coleção — use um `ObservableCollection` classe para esses itens. `ObservableCollection` implementa o `INotifyCollectionChanged` interface, e `ListView` instalará um manipulador para o `CollectionChanged` eventos.

Se as propriedades dos itens em si são alteradas durante o tempo de execução, então os itens na coleção devem implementar o `INotifyPropertyChanged` alterações de interface e o sinal para valores de propriedade usando o `PropertyChanged` eventos. Isso é demonstrado na próxima parte desta série, [parte 5. De associação de dados a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Resumo

Associações de dados fornecem um mecanismo poderoso para vincular as propriedades entre dois objetos dentro de uma página ou entre objetos visuais e os dados subjacentes. Mas quando o aplicativo começa a trabalhar com fontes de dados, um padrão de arquitetura de aplicativo populares começa a surgir como um paradigma úteis. Isso é abordado em [parte 5. De associações de dados a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).



## <a name="related-links"></a>Links relacionados

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Parte 1. Introdução ao XAML (amostra)](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintaxe essencial de XAML (amostra)](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Extensões de marcação de XAML (amostra)](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 5. De associação de dados a MVVM (amostra)](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
