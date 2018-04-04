---
title: Parte 4. Noções básicas de associação de dados
description: Associações de dados permitem que propriedades de dois objetos a serem vinculados para que a alteração de uma causa uma alteração no outro. Essa é uma ferramenta muito valiosa e enquanto associações de dados podem ser definidas inteiramente no código, XAML fornece atalhos e conveniência. Consequentemente, uma das extensões de marcação mais importantes no xamarin. Forms é associação.
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 342288C3-BB4C-4924-B178-72E112D777BA
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: 2aa6fd2f54c09921621a12af9401a6f84ae37ffa
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="part-4-data-binding-basics"></a>Parte 4. Noções básicas de associação de dados

_Associações de dados permitem que propriedades de dois objetos a serem vinculados para que a alteração de uma causa uma alteração no outro. Essa é uma ferramenta muito valiosa e enquanto associações de dados podem ser definidas inteiramente no código, XAML fornece atalhos e conveniência. Consequentemente, uma das extensões de marcação mais importantes no xamarin. Forms é associação._

## <a name="data-bindings"></a>Associações de dados

Propriedades de dois objetos, chamados de conexão de associações de dados de *fonte* e *destino*. No código, duas etapas são necessárias: O `BindingContext` propriedade do objeto de destino deve ser definida para o objeto de origem e o `SetBinding` método (geralmente usado em conjunto com o `Binding` classe) deve ser chamado no objeto de destino para associar uma propriedade que objeto a uma propriedade do objeto de origem.

A propriedade de destino deve ser uma propriedade ligável, o que significa que o objeto de destino deve ser derivado de `BindableObject`. A documentação online do xamarin. Forms indica as propriedades vinculáveis. Uma propriedade de `Label` como `Text` está associado com a propriedade associável `TextProperty`.

Na marcação, você também deve executar as duas etapas mesmo que são necessárias no código, exceto pelo fato do `Binding` extensão de marcação assume o lugar do `SetBinding` chamar e `Binding` classe.

No entanto, quando você definir associações de dados em XAML, há várias maneiras de definir o `BindingContext` do objeto de destino. Às vezes, ele é definido do arquivo code-behind, às vezes, usando um `StaticResource` ou `x:Static` extensão de marcação e às vezes, como o conteúdo de `BindingContext` marcas de elemento de propriedade.

Associações são usadas com mais frequência para conectar os elementos visuais de um programa com um modelo de dados subjacente, normalmente em uma realização da arquitetura do aplicativo MVVM (Model-View-ViewModel), conforme discutido em [parte 5. De associações de dados para o modelo MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md), mas outros cenários são possíveis.

## <a name="view-to-view-bindings"></a>Associações de exibição para exibição

Você pode definir associações de dados para vincular as propriedades dos dois modos de exibição na mesma página. Nesse caso, você configura o `BindingContext` do objeto de destino usando o `x:Reference` extensão de marcação.

Aqui está um arquivo XAML que contém um `Slider` e dois `Label` modos de exibição, um dos quais é girado pelo `Slider` valor e outro que exibe o `Slider` valor:

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

O `Slider` contém um `x:Name` atributo que é referenciado pelos dois `Label` exibições usando o `x:Reference` extensão de marcação.

O `x:Reference` extensão de associação define uma propriedade chamada `Name` para definir como o nome do elemento referenciado, nesse caso `slider`. No entanto, o `ReferenceExtension` classe que define o `x:Reference` extensão de marcação também define uma `ContentProperty` atributo `Name`, que significa que não seja explicitamente necessário. Apenas para a série, o primeiro `x:Reference` inclui "Name =", mas não o segundo:

```csharp
BindingContext="{x:Reference Name=slider}"
…
BindingContext="{x:Reference slider}"
```

O `Binding` extensão de marcação em si pode ter várias propriedades, como o `BindingBase` e `Binding` classe. O `ContentProperty` para `Binding` é `Path`, mas o "caminho =" parte da extensão de marcação pode ser omitido se o caminho é o primeiro item a `Binding` extensão de marcação. O primeiro exemplo tem "caminho =", mas o segundo exemplo omite a ela:

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

Observe o `StringFormat` propriedade no segundo `Binding` extensão de marcação. Xamarin. Forms, associações não executam qualquer conversões implícitas de tipo, e se você precisar exibir um objeto de cadeia de caracteres não como uma cadeia de caracteres, você deve fornecer um conversor de tipo ou usar `StringFormat`. Nos bastidores, estático `String.Format` método é usado para implementar `StringFormat`. Que é potencialmente um problema, porque as especificações de formatação do .NET envolvem chaves, que também são usadas para delimitar as extensões de marcação. Isso cria um risco de confundir o analisador XAML. Para evitar isso, coloque a cadeia de caracteres de formatação inteira entre aspas simples:

```csharp
Text="{Binding Value, StringFormat='The angle is {0:F0} degrees'}"
```

Aqui está o programa em execução:

[![](data-binding-basics-images/sliderbinding.png "Associações de exibição para exibição")](data-binding-basics-images/sliderbinding-large.png#lightbox "associações de exibição para exibição ")

## <a name="the-binding-mode"></a>O modo de associação 

Um único modo de exibição pode ter associações de dados em várias de suas propriedades. No entanto, cada modo de exibição pode ter apenas um `BindingContext`, portanto, várias associações de dados nessa exibição devem ser todas fazer referência às propriedades do mesmo objeto.

A solução para esse e outros problemas envolve o `Mode` propriedade, que é definida como um membro do `BindingMode` enumeração:

- `Default` 
- `OneWay` — valores são transferidos da origem para o destino
- `OneWayToSource` — valores são transferidos do destino para a origem
- `TwoWay` — valores são transferidos ambas as direções entre origem e destino

O programa a seguir demonstra um uso comum de `OneWayToSource` e `TwoWay` modos de ligação. Quatro `Slider` exibições destinam-se ao controle de `Scale`, `Rotate`, `RotateX`, e `RotateY` propriedades de um `Label`. A princípio, isso parece como se essas quatro propriedades do `Label` deve ser destinos de vinculação de dados, porque cada um está sendo definida um `Slider`. No entanto, o `BindingContext` de `Label` pode ser apenas um objeto e há quatro controles deslizantes diferente.

Por esse motivo, todas as associações são definidas no aparentemente com versões anteriores maneiras: O `BindingContext` de cada um dos quatro controles deslizantes é definido como o `Label`, e as associações são definidas no `Value` propriedades dos controles deslizantes. Usando o `OneWayToSource` e `TwoWay` modos, essas `Value` propriedades podem definir as propriedades de origem, que são o `Scale`, `Rotate`, `RotateX`, e `RotateY` propriedades do `Label`:

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

As associações em três do `Slider` modos de exibição são `OneWayToSource`, o que significa que o `Slider` valor faz com que uma alteração na propriedade do seu `BindingContext`, que é o `Label` chamado `label`. Esses três `Slider` exibições causam alterações para o `Rotate`, `RotateX`, e `RotateY` propriedades da `Label`.

No entanto, a associação para o `Scale` é de propriedade `TwoWay`. Isso ocorre porque o `Scale` propriedade tem um valor padrão de 1 e usando um `TwoWay` associação faz com que o `Slider` inicial valor a ser definido para 1 em vez de 0. Se essa associação `OneWayToSource`, o `Scale` propriedade inicialmente deve ser definida como 0 do `Slider` valor padrão. O `Label` não será visível e que pode causar alguma confusão ao usuário.

 [![](data-binding-basics-images/slidertransforms.png "Versões anteriores associações")](data-binding-basics-images/slidertransforms-large.png#lightbox "com versões anteriores associações")

## <a name="bindings-and-collections"></a>Associações e coleções

Nada ilustra o poder das associações de dados e XAML melhores do que um modelo `ListView`.

`ListView` define uma `ItemsSource` propriedade do tipo `IEnumerable`, e exibe os itens na coleção. Esses itens podem ser objetos de qualquer tipo. Por padrão, `ListView` usa o `ToString` método de cada item para exibir esse item. Às vezes, isso é o que você deseja, em muitos casos, `ToString` retorna somente o nome de classe totalmente qualificado do objeto.

No entanto, os itens a `ListView` coleção pode ser exibida como desejar através do uso de um *modelo*, que envolve uma classe que deriva de `Cell`. O modelo é clonado para cada item a `ListView`, e as associações de dados que foram definidas no modelo são transferidas para os clones individuais.

Frequentemente, você desejará criar uma célula personalizada para esses itens usando o `ViewCell` classe. Esse processo é um pouco confuso no código, mas em XAML torna-se muito simples.

O XamlSamples projeto há uma classe chamada `NamedColor`. Cada `NamedColor` objeto tem `Name` e `FriendlyName` propriedades do tipo `string`e um `Color` propriedade do tipo `Color`. Além disso, `NamedColor` tem 141 campos estáticos somente leitura do tipo `Color` correspondente às cores definidas no xamarin. Forms `Color` classe. Cria um construtor estático um `IEnumerable<NamedColor>` coleção que contém `NamedColor` objetos correspondentes a esses campos estáticos e atribui a seu público estático `All` propriedade.

Configuração estática `NamedColor.All` propriedade para o `ItemsSource` de um `ListView` é fácil usando o `x:Static` extensão de marcação:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ListViewDemoPage"
             Title="ListView Demo Page">

    <ListView ItemsSource="{x:Static local:NamedColor.All}" />

</ContentPage>
```

A exibição resultante estabelece os itens são realmente do tipo `XamlSamples.NamedColor`:

[![](data-binding-basics-images/listview1.png "Associação a uma coleção")](data-binding-basics-images/listview1-large.png#lightbox "associação a uma coleção")

Não é mais informações, mas o `ListView` é rolável e selecionável.

Para definir um modelo para os itens, você desejará separar o `ItemTemplate` propriedade como um elemento de propriedade e defina-o como um `DataTemplate`, que referencia, em seguida, um `ViewCell`. Para o `View` propriedade o `ViewCell` você pode definir um layout de uma ou mais exibições para exibir cada item. Aqui está um exemplo simples:

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

O `Label` é definido como o `View` propriedade o `ViewCell`. (O `ViewCell.View` marcas não são necessárias porque o `View` é a propriedade de conteúdo do `ViewCell`.) Essa marcação exibe o `FriendlyName` propriedade de cada `NamedColor` objeto:

[![](data-binding-basics-images/listview2.png "Associação a uma coleção com um DataTemplate")](data-binding-basics-images/listview2-large.png#lightbox "associação a uma coleção com um DataTemplate")

Muito melhor. Agora tudo o que é necessário é melhore o modelo de item com mais informações e a cor real. Para dar suporte a esse modelo, alguns valores e os objetos foram definidos no dicionário de recursos da página:

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

    <ListView ItemsSource="{x:Static local:NamedColor.All}">
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

Observe o uso de `OnPlatform` para definir o tamanho de um `BoxView` e a altura do `ListView` linhas. Embora os valores para todas as plataformas de três forem iguais, a marcação pode ser facilmente adaptada para outros valores ajustar a exibição. 

## <a name="binding-value-converters"></a>Conversores de valor de associação

Anterior **ListView demonstração** arquivo XAML exibe o indivíduo `R`, `G`, e `B` propriedades do xamarin. Forms `Color` estrutura. Essas propriedades são do tipo `double` e intervalo de 0 a 1. Se você deseja exibir os valores hexadecimais, você não pode simplesmente usar `StringFormat` com uma especificação de formatação "X2". Isso funciona somente para inteiros e Além disso, o `double` valores precisam ser multiplicado por 255.

Esse pequeno problema foi resolvido com uma *conversor de valor*, também chamado um *conversor de associação*. Esta é uma classe que implementa o `IValueConverter` interface, o que significa que ele tem dois métodos chamados `Convert` e `ConvertBack`. O `Convert` método é chamado quando um valor é transferido da origem para o destino; o `ConvertBack` método é chamado para transferências de destino a fonte em `OneWayToSource` ou `TwoWay` associações:

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

O `ConvertBack` método não desempenha uma função neste programa porque as associações são apenas uma maneira de origem ao destino. 

Uma associação faz referência a um conversor de associação com o `Converter` propriedade. Um conversor de associação também pode aceitar um parâmetro especificado com o `ConverterParameter` propriedade. Para alguns versatilidade, isso é como o multiplicador é especificado. O conversor de associação verifica se o parâmetro conversor válido `double` valor.

O conversor é instanciado no dicionário de recursos para que ele possa ser compartilhado entre várias associações:

```xaml
<local:DoubleToIntConverter x:Key="intConverter" />
```

Associações de três dados fazem referência a essa instância única. Observe que o `Binding` extensão de marcação contém inserida `StaticResource` extensão de marcação:

```xaml
<Label Text="{Binding Color.R,
                      Converter={StaticResource intConverter},
                      ConverterParameter=255,
                      StringFormat='R={0:X2}'}" />
```

Aqui está o resultado:

[![](data-binding-basics-images/listview3.png "Associação a uma coleção com um DataTemplate e conversores")](data-binding-basics-images/listview3-large.png#lightbox "associação a uma coleção com um DataTemplate e conversores")

O `ListView` é bastante sofisticado lidar com as alterações que podem ocorrer dinamicamente em subjacente dados, mas somente se seguir etapas específicas. Se a coleção de itens atribuídos ao `ItemsSource` propriedade do `ListView` alterações durante o tempo de execução — que se itens podem ser adicionados a ou removido da coleção — use um `ObservableCollection` classe para esses itens. `ObservableCollection` implementa o `INotifyCollectionChanged` interface, e `ListView` instalará um manipulador para o `CollectionChanged` evento.

Se alteram propriedades de itens se durante o tempo de execução, os itens na coleção devem implementar a `INotifyPropertyChanged` alterações de interface e o sinal para valores de propriedade usando o `PropertyChanged` evento. Isso é demonstrado na próxima parte desta série, [parte 5. De associação de dados para o modelo MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Resumo

Associações de dados fornecem um mecanismo eficiente para vincular as propriedades entre dois objetos em uma página ou entre objetos visuais e dados subjacentes. Mas quando o aplicativo começa a trabalhar com fontes de dados, um padrão de arquitetura de aplicativos populares começa a surgir como um paradigma de útil. Isso é abordado em [parte 5. De associações de dados para o modelo MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).



## <a name="related-links"></a>Links relacionados

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Parte 1. Guia de Introdução com XAML (exemplo)](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Essenciais de sintaxe XAML (exemplo)](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Extensões de marcação XAML (exemplo)](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 5. De associação de dados a MVVM (exemplo)](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
