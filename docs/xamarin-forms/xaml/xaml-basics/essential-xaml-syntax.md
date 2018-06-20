---
title: Parte 2. Sintaxe XAML essenciais
description: Este artigo explica os recursos de sintaxe XAML essenciais de elementos de propriedade e propriedades anexadas.
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: ea448708403478cdaf565bc7abbed2af28c7a9bf
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268843"
---
# <a name="part-2-essential-xaml-syntax"></a>Parte 2. Sintaxe XAML essenciais

_XAML destina-se principalmente a criando e inicializando objetos. Muitas vezes, mas propriedades devem ser definidas como objetos complexos que não podem ser facilmente representados como cadeias de caracteres XML e, às vezes, as propriedades definidas por uma classe devem ser definidas em uma classe filha. Essas duas necessidades exigem os recursos de sintaxe XAML essenciais de elementos de propriedade e propriedades anexadas._

## <a name="property-elements"></a>Elementos de propriedade

Em XAML, as propriedades das classes normalmente são definidas como atributos XML:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

No entanto, há uma maneira alternativa de definir uma propriedade em XAML. Para tentar essa alternativa com `TextColor`, primeiro exclua o existente `TextColor` configuração:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

Abra o elemento vazio `Label` marca separando-lo em marcas de início e de término:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">

</Label>
```

Dentro dessas marcas, adicione marcas de início e término que consistem em nome de classe e um nome de propriedade separado por um ponto:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>

    </Label.TextColor>
</Label>
```

Defina o valor da propriedade como conteúdo dessas novas marcas, como este:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

Essas duas maneiras de especificar o `TextColor` propriedade são funcionalmente equivalentes, mas não use os dois modos para a mesma propriedade porque que seriam efetivamente definindo a propriedade duas vezes e pode ser ambígua.

Com essa nova sintaxe, pode ser introduzida terminologia úteis:

-  `Label` é um *elemento object*. É um objeto xamarin. Forms expressado como um elemento XML.
-  `Text`, `VerticalOptions`, `FontAttributes` e `FontSize` são *atributos da propriedade*. Eles são propriedades xamarin. Forms expressadas como atributos XML.
-  Nesse trecho final, `TextColor` se tornou um *elemento de propriedade*. É uma propriedade de xamarin. Forms, mas agora é um elemento XML.


A definição de propriedade elementos podem no primeiro parece ser uma violação da sintaxe XML, mas não é. O período não tem significado especial em XML. Para um decodificador XML, `Label.TextColor` é simplesmente um elemento filho normal.

Em XAML, no entanto, essa sintaxe é muito especial. Uma das regras para elementos de propriedade é que nada mais pode aparecer no `Label.TextColor` marca. O valor da propriedade é sempre definido como o conteúdo entre o elemento de propriedade marcas de início e término.

Você pode usar a sintaxe de elemento de propriedade em mais de uma propriedade:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center">
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
</Label>
```

Ou você pode usar a sintaxe de elemento de propriedade para todas as propriedades:

```xaml
<Label>
    <Label.Text>
        Hello, XAML!
    </Label.Text>
    <Label.FontAttributes>
        Bold
    </Label.FontAttributes>
    <Label.FontSize>
        Large
    </Label.FontSize>
    <Label.TextColor>
        Aqua
    </Label.TextColor>
    <Label.VerticalOptions>
        Center
    </Label.VerticalOptions>
</Label>
```

Primeiro, sintaxe de elemento de propriedade pode parecer uma substituição longos desnecessária para algo relativamente bem simples e nesses exemplos que certamente for o caso.

No entanto, a sintaxe de elemento de propriedade é essencial quando o valor de uma propriedade é muito complexo para ser expresso como uma cadeia de caracteres simple. Nas marcas de elemento de propriedade, você pode criar uma instância de outro objeto e definir suas propriedades. Por exemplo, você pode definir explicitamente uma propriedade como `VerticalOptions` para um `LayoutOptions` valor com as configurações de propriedade:

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

Outro exemplo: O `Grid` tem duas propriedades chamadas `RowDefinitions` e `ColumnDefinitions`. Essas duas propriedades são do tipo `RowDefinitionCollection` e `ColumnDefinitionCollection`, que são coleções de `RowDefinition` e `ColumnDefinition` objetos. Você precisa usar a sintaxe de elemento de propriedade para definir essas coleções.

Aqui está o início do arquivo XAML para um `GridDemoPage` classe, mostrando as marcas de elemento de propriedade para o `RowDefinitions` e `ColumnDefinitions` coleções:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>
        ...
    </Grid>
</ContentPage>
```

Observe a sintaxe abreviada para definir o dimensionamento automático células, as células de configurações de estrelas e alturas e larguras de pixel.

## <a name="attached-properties"></a>Propriedades anexadas

Você acabou de ver que o `Grid` requer que os elementos de propriedade para o `RowDefinitions` e `ColumnDefinitions` coleções para definir as linhas e colunas. No entanto, também deve haver alguma forma para o programador indicar a linha e coluna em que cada filho do `Grid` reside.

Dentro da marca para cada filho do `Grid` você especificar a linha e coluna dos filho usando os seguintes atributos:

-  `Grid.Row`
-  `Grid.Column`

Os valores padrão desses atributos são de 0. Você também pode indicar se um filho se estender por mais de uma linha ou coluna com esses atributos:

-  `Grid.RowSpan`
-  `Grid.ColumnSpan`

Esses dois atributos têm valores padrão de 1.

Aqui está o arquivo GridDemoPage.xaml completo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.GridDemoPage"
             Title="Grid Demo Page">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="100" />
        </Grid.ColumnDefinitions>

        <Label Text="Autosized cell"
               Grid.Row="0" Grid.Column="0"
               TextColor="White"
               BackgroundColor="Blue" />

        <BoxView Color="Silver"
                 HeightRequest="0"
                 Grid.Row="0" Grid.Column="1" />

        <BoxView Color="Teal"
                 Grid.Row="1" Grid.Column="0" />

        <Label Text="Leftover space"
               Grid.Row="1" Grid.Column="1"
               TextColor="Purple"
               BackgroundColor="Aqua"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two rows (or more if you want)"
               Grid.Row="0" Grid.Column="2" Grid.RowSpan="2"
               TextColor="Yellow"
               BackgroundColor="Blue"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Span two columns"
               Grid.Row="2" Grid.Column="0" Grid.ColumnSpan="2"
               TextColor="Blue"
               BackgroundColor="Yellow"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

        <Label Text="Fixed 100x100"
               Grid.Row="2" Grid.Column="2"
               TextColor="Aqua"
               BackgroundColor="Red"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />

    </Grid>
</ContentPage>
```

O `Grid.Row` e `Grid.Column` as configurações de 0 não são necessárias, mas são geralmente incluídas para fins de esclarecimento.

Aqui está como ele funciona em todas as três plataformas:

[![](essential-xaml-syntax-images/griddemo.png "Layout de grade")](essential-xaml-syntax-images/griddemo-large.png#lightbox "Layout de grade")

A julgar unicamente com a sintaxe, essas `Grid.Row`, `Grid.Column`, `Grid.RowSpan`, e `Grid.ColumnSpan` atributos aparecem como campos estáticos ou propriedades de `Grid`, mas interessante `Grid` não define nada chamado `Row`, `Column`, `RowSpan`, ou `ColumnSpan`.

Em vez disso, `Grid` define quatro propriedades vinculáveis denominadas `RowProperty`, `ColumnProperty`, `RowSpanProperty`, e `ColumnSpanProperty`. Estes são tipos especiais de propriedades vinculáveis conhecidos como *propriedades anexadas*. Eles são definidos pelo `Grid` classe ativado mas filhos do `Grid`.

Quando você deseja usar essas propriedades anexadas em código, o `Grid` classe fornece métodos estáticos denominados `SetRow`, `GetColumn`e assim por diante. Mas em XAML, essas propriedades anexadas estão definidas como atributos nos filhos do `Grid` usando nomes de propriedades simples.

Propriedades anexadas são sempre reconhecíveis nos arquivos XAML como atributos que contém uma classe e um nome de propriedade separado por um ponto. Eles são chamados *propriedades anexadas* porque eles são definidos por uma classe (neste caso, `Grid`) mas anexado a outros objetos (neste caso, os filhos do `Grid`). Durante o layout, o `Grid` pode interrogar os valores dessas propriedades anexado saber onde colocar cada filho.

O `AbsoluteLayout` classe define duas propriedades anexadas denominadas `LayoutBounds` e `LayoutFlags`. Aqui está um padrão quadriculado realizadas usando o posicionamento proporcional e recursos de dimensionamento do `AbsoluteLayout`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.AbsoluteDemoPage"
             Title="Absolute Demo Page">

    <AbsoluteLayout BackgroundColor="#FF8080">
        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 0.33, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.33, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="1, 0.67, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

        <BoxView Color="#8080FF"
                 AbsoluteLayout.LayoutBounds="0.67, 1, 0.25, 0.25"
                 AbsoluteLayout.LayoutFlags="All" />

  </AbsoluteLayout>
</ContentPage>
```

E aqui é:

[![](essential-xaml-syntax-images/absolutedemo-large.png "Layout absoluto")](essential-xaml-syntax-images/absolutedemo-large.png#lightbox "Layout absoluto")

Para algo semelhante a isso, você pode questionar a sabedoria da usando XAML. Sem dúvida, a repetição e regularidade do `LayoutBounds` retângulo sugere que ele pode ser melhor realizado no código.

Certamente, que é uma preocupação legítima e não há nenhum problema com o uso de código e a marcação de balanceamento ao definir as interfaces do usuário. É fácil definir alguns dos visuais em XAML e, em seguida, usar o construtor de arquivo code-behind para adicionar alguns mais visuais que podem ser gerados melhor em loops.

## <a name="content-properties"></a>Propriedades de Conteúdo

Nos exemplos anteriores, o `StackLayout`, `Grid`, e `AbsoluteLayout` objetos são definidos para o `Content` propriedade do `ContentPage`, e os filhos desses layouts são, na verdade, os itens na `Children` coleção. Embora essas `Content` e `Children` propriedades são têm no arquivo XAML.

Certamente você pode incluir o `Content` e `Children` propriedades como elementos de propriedade, tal como o **XamlPlusCode** exemplo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout.Children>
                <Slider VerticalOptions="CenterAndExpand"
                        ValueChanged="OnSliderValueChanged" />

                <Label x:Name="valueLabel"
                       Text="A simple Label"
                       FontSize="Large"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand" />

                <Button Text="Click Me!"
                      HorizontalOptions="Center"
                      VerticalOptions="CenterAndExpand"
                      Clicked="OnButtonClicked" />
            </StackLayout.Children>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

A pergunta real é: por que são esses elementos de propriedade *não* necessárias no arquivo XAML?

Os elementos definidos no xamarin. Forms para uso em XAML podem ter uma propriedade sinalizada no `ContentProperty` atributo da classe. Se você procurar o `ContentPage` classe na documentação do xamarin. Forms online, você verá esse atributo:

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

Isso significa que o `Content` marcas de elemento de propriedade não são necessárias. Qualquer conteúdo XML que é exibida entre o início e término `ContentPage` marcas deve ser atribuído para o `Content` propriedade.

 `StackLayout`, `Grid`, `AbsoluteLayout`, e `RelativeLayout` todos derivam `Layout<View>`, e se você procurar `Layout<T>` na documentação do xamarin. Forms, você verá outra `ContentProperty` atributo:

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

Que permite que o conteúdo do layout sejam adicionados automaticamente para o `Children` coleção sem explícita `Children` marcas de elemento de propriedade.

Outras classes também têm `ContentProperty` definições de atributo. Por exemplo, a propriedade de conteúdo de `Label` é `Text`. Verifique a documentação da API para outras pessoas.

## <a name="platform-differences-with-onplatform"></a>Diferenças de plataforma com OnPlatform

Em aplicativos de única página, é comum para definir o `Padding` propriedade na página para evitar a substituição da barra de status do iOS. No código, você pode usar o `Device.RuntimePlatform` propriedade para essa finalidade:

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

Você também pode fazer algo semelhante em XAML usando o `OnPlatform` e `On` classes. Primeiro incluir elementos de propriedade para o `Padding` propriedade na parte superior da página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

Dentro dessas marcas incluem um `OnPlatform` marca. `OnPlatform` é uma classe genérica. Você precisa especificar o argumento de tipo genérico, nesse caso, `Thickness`, que é o tipo de `Padding` propriedade. Felizmente, há um atributo XAML especificamente para definir argumentos genéricos chamados `x:TypeArguments`. Isso deve corresponder ao tipo da propriedade que você está configurando:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">

        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

`OnPlatform` tem uma propriedade denominada `Platforms` que é um `IList` de `On` objetos. Use marcas de elemento de propriedade para a propriedade:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>

            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Agora adicione `On` elementos. Para cada um conjunto de `Platform` propriedade e o `Value` propriedade de marcação para o `Thickness` propriedade:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <OnPlatform.Platforms>
                <On Platform="iOS" Value="0, 20, 0, 0" />
                <On Platform="Android" Value="0, 0, 0, 0" />
                <On Platform="UWP" Value="0, 0, 0, 0" />
            </OnPlatform.Platforms>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Essa marcação pode ser simplificada. A propriedade de conteúdo de `OnPlatform` é `Platforms`, portanto, as marcas de elemento de propriedade podem ser removidas:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android" Value="0, 0, 0, 0" />
            <On Platform="UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

O `Platform` propriedade `On` é do tipo `IList<string>`, portanto, você pode incluir várias plataformas, se os valores são os mesmos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
            <On Platform="Android, UWP" Value="0, 0, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Android e UWP estejam definidas para o valor padrão de `Padding`, que marca pode ser removida:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

Este é o modo padrão para definir um dependente de plataforma `Padding` propriedade em XAML. Se o `Value` configuração não pode ser representada por uma única cadeia de caracteres, você pode definir os elementos de propriedade para ele:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS">
                <On.Value>
                    0, 20, 0, 0
                </On.Value>
            </On>
        </OnPlatform>
    </ContentPage.Padding>
  ...
</ContentPage>
```

## <a name="summary"></a>Resumo

Com os elementos de propriedade e propriedades anexadas, grande parte da sintaxe XAML básica estabelecida. No entanto, às vezes você precisa definir propriedades para objetos de uma maneira indireta, por exemplo, de um dicionário de recursos. Essa abordagem é abordada na próxima parte, parte [3. Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md).



## <a name="related-links"></a>Links relacionados

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Parte 1. Introdução ao XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 3. Extensões de Marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Conceitos básicos da associação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. De associação de dados para o modelo MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
