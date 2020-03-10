---
title: Parte 2. Sintaxe essencial do XAML
description: Este artigo explica os recursos de sintaxe XAML essencial de elementos de propriedade e propriedades anexadas.
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: f79a07a04eddeea1441f7938fdef210a37fb920a
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78918465"
---
# <a name="part-2-essential-xaml-syntax"></a>Parte 2. Sintaxe essencial do XAML

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_O XAML é basicamente projetado para instanciar e inicializar objetos. Mas, muitas vezes, as propriedades devem ser definidas para objetos complexos que não podem ser facilmente representadas como cadeias de caracteres XML e, às vezes, as propriedades definidas por uma classe devem ser definidas em uma classe filho. Essas duas necessidades exigem os recursos de sintaxe XAML essenciais dos elementos de propriedade e das propriedades anexadas._

## <a name="property-elements"></a>Elementos de propriedade

No XAML, as propriedades das classes normalmente são definidas como atributos XML:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

No entanto, há uma maneira alternativa de definir uma propriedade em XAML. Para tentar essa alternativa com `TextColor`, primeiro exclua a configuração de `TextColor` existente:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

Abra a marca de `Label` de elemento vazio separando-a em marcas de início e fim:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">

</Label>
```

Dentro dessas marcas, adicione marcas de início e término que consistem em nome de classe e um nome de propriedade separados por um período:

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

Essas duas maneiras de especificar a propriedade `TextColor` são funcionalmente equivalentes, mas não usam as duas maneiras para a mesma propriedade porque isso efetivamente definiria a propriedade duas vezes e pode ser ambíguo.

Com essa nova sintaxe, terminologia úteis pode ser introduzida:

- `Label` é um *elemento Object*. Ele é um objeto de xamarin. Forms expressado como um elemento XML.
- `Text`, `VerticalOptions`, `FontAttributes` e `FontSize` são *atributos de propriedade*. Elas são expressadas como atributos XML de propriedades de xamarin. Forms.
- Nesse trecho final, `TextColor` se tornou um *elemento de propriedade*. É uma propriedade de xamarin. Forms, mas agora é um elemento XML.

A definição de propriedade elementos podem parecer a princípio ser uma violação da sintaxe XML, mas não é. O período não tem nenhum significado especial em XML. Para um decodificador XML, `Label.TextColor` é simplesmente um elemento filho normal.

No XAML, no entanto, essa sintaxe é muito especial. Uma das regras para elementos de propriedade é que nada mais pode aparecer na marca de `Label.TextColor`. O valor da propriedade é sempre definido como o conteúdo entre o elemento de propriedade marcas de início e término.

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

A princípio, sintaxe de elemento de propriedade pode parecer uma substituição longos desnecessária para algo bastante comparativamente simples, e nesses exemplos, certamente, esse é o caso.

No entanto, a sintaxe de elemento de propriedade se torna essencial quando o valor de uma propriedade é muito complexo para ser expresso como uma cadeia de caracteres simple. Dentro das marcas de elemento de propriedade, você pode criar uma instância de outro objeto e defina suas propriedades. Por exemplo, você pode definir explicitamente uma propriedade como `VerticalOptions` para um valor de `LayoutOptions` com configurações de propriedade:

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

Outro exemplo: o `Grid` tem duas propriedades chamadas `RowDefinitions` e `ColumnDefinitions`. Essas duas propriedades são do tipo `RowDefinitionCollection` e `ColumnDefinitionCollection`, que são coleções de objetos `RowDefinition` e `ColumnDefinition`. Você precisa usar a sintaxe de elemento de propriedade para definir essas coleções.

Aqui está o início do arquivo XAML para uma classe `GridDemoPage`, mostrando as marcas de elemento de propriedade para as coleções `RowDefinitions` e `ColumnDefinitions`:

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

Observe a sintaxe abreviada para a definição de células de dimensionamento automático, as células de configurações em estrela e alturas e larguras de pixel.

## <a name="attached-properties"></a>Propriedades Anexadas

Você acabou de ver que a `Grid` requer elementos de propriedade para as coleções `RowDefinitions` e `ColumnDefinitions` para definir as linhas e colunas. No entanto, também deve haver alguma maneira para o programador indicar a linha e a coluna em que cada filho da `Grid` reside.

Dentro da marca de cada filho do `Grid` você especifica a linha e a coluna do filho usando os seguintes atributos:

- `Grid.Row`
- `Grid.Column`

Os valores padrão desses atributos são 0. Você também pode indicar se um filho abrange mais de uma linha ou coluna com esses atributos:

- `Grid.RowSpan`
- `Grid.ColumnSpan`

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

As configurações de `Grid.Row` e `Grid.Column` de 0 não são necessárias, mas geralmente são incluídas para fins de clareza.

Aqui está o que se parece com:

[Layout de grade de ![](essential-xaml-syntax-images/griddemo.png)](essential-xaml-syntax-images/griddemo-large.png#lightbox)

Julgamento apenas da sintaxe, esses `Grid.Row`, `Grid.Column`, `Grid.RowSpan`e atributos de `Grid.ColumnSpan` parecem ser campos estáticos ou propriedades de `Grid`, mas curiosamente, o `Grid` não define nada chamado `Row`, `Column`, `RowSpan`ou `ColumnSpan`.

Em vez disso, `Grid` define quatro propriedades vinculáveis chamadas `RowProperty`, `ColumnProperty`, `RowSpanProperty`e `ColumnSpanProperty`. Esses são tipos especiais de propriedades vinculáveis conhecidas como *Propriedades anexadas*. Eles são definidos pela classe `Grid`, mas definidos em filhos da `Grid`.

Quando você deseja usar essas propriedades anexadas no código, a classe `Grid` fornece métodos estáticos chamados `SetRow`, `GetColumn`e assim por diante. Mas, em XAML, essas propriedades anexadas são definidas como atributos nos filhos do `Grid` usando nomes de propriedades simples.

Propriedades anexadas são sempre reconhecidas em arquivos XAML como atributos, contendo uma classe e um nome de propriedade separados por um período. Elas são chamadas de *Propriedades anexadas* porque são definidas por uma classe (nesse caso, `Grid`), mas anexadas a outros objetos (nesse caso, filhos da `Grid`). Durante o layout, o `Grid` pode interrogar os valores dessas propriedades anexadas para saber onde posicionar cada filho.

A classe `AbsoluteLayout` define duas propriedades anexadas chamadas `LayoutBounds` e `LayoutFlags`. Aqui está um padrão quadriculado realizado usando os recursos de posicionamento e dimensionamento proporcionais do `AbsoluteLayout`:

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

E aqui está:

[![layout absoluto](essential-xaml-syntax-images/absolutedemo-large.png)](essential-xaml-syntax-images/absolutedemo-large.png#lightbox)

Para algo como isso, você poderá questionar a sabedoria da usando XAML. Certamente, a repetição e a regularidade do retângulo de `LayoutBounds` sugerem que ele pode ser melhor realizado no código.

Certamente, que é uma preocupação legítima e não há nenhum problema com o uso de código e marcação de balanceamento, ao definir as interfaces do usuário. É fácil definir alguns dos elementos visuais em XAML e, em seguida, use o construtor do arquivo code-behind para adicionar mais alguns visuais que podem ser gerados de melhor em loops.

## <a name="content-properties"></a>Propriedades de Conteúdo

Nos exemplos anteriores, os objetos `StackLayout`, `Grid`e `AbsoluteLayout` são definidos como a propriedade `Content` da `ContentPage`, e os filhos desses layouts são realmente itens na coleção de `Children`. Ainda assim, essas propriedades `Content` e `Children` estão em qualquer lugar no arquivo XAML.

Você certamente pode incluir as propriedades `Content` e `Children` como elementos de propriedade, como no exemplo **XamlPlusCode** :

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

A pergunta real é: por que esses elementos de propriedade *não* são necessários no arquivo XAML?

Os elementos definidos no Xamarin. Forms para uso em XAML têm permissão para ter uma propriedade sinalizada no atributo `ContentProperty` na classe. Se você pesquisar a classe `ContentPage` na documentação online do Xamarin. Forms, você verá este atributo:

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

Isso significa que as marcas de elemento de propriedade `Content` não são necessárias. Qualquer conteúdo XML que aparece entre as marcas de `ContentPage` inicial e final é considerado atribuído à propriedade `Content`.

 `StackLayout`, `Grid`, `AbsoluteLayout`e `RelativeLayout` derivam de `Layout<View>`e, se Pesquisar `Layout<T>` na documentação do Xamarin. Forms, você verá outro atributo de `ContentProperty`:

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

Isso permite que o conteúdo do layout seja adicionado automaticamente à coleção de `Children` sem marcas de elemento de propriedade de `Children` explícitas.

Outras classes também têm `ContentProperty` definições de atributo. Por exemplo, a propriedade Content de `Label` é `Text`. Verifique a documentação da API para outras pessoas.

## <a name="platform-differences-with-onplatform"></a>Diferenças de plataforma com o OnPlatform

Em aplicativos de página única, é comum definir a propriedade `Padding` na página para evitar a substituição da barra de status do iOS. No código, você pode usar a propriedade `Device.RuntimePlatform` para essa finalidade:

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

Você também pode fazer algo semelhante em XAML usando as classes [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) e [`On`](xref:Xamarin.Forms.On) . Primeiro inclua os elementos de propriedade para a propriedade `Padding` próxima à parte superior da página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

Dentro dessas marcas, inclua uma marca de `OnPlatform`. `OnPlatform` é uma classe genérica. Você precisa especificar o argumento de tipo genérico, nesse caso, `Thickness`, que é o tipo de propriedade `Padding`. Felizmente, há um atributo XAML especificamente para definir argumentos genéricos chamados `x:TypeArguments`. Isso deve corresponder ao tipo da propriedade que você está configurando:

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

`OnPlatform` tem uma propriedade chamada `Platforms` que é uma `IList` de objetos `On`. Use as marcas de elemento de propriedade para essa propriedade:

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

Agora, adicione elementos `On`. Para cada uma, defina a propriedade `Platform` e a propriedade `Value` como marcação para a propriedade `Thickness`:

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

Essa marcação pode ser simplificada. A propriedade de conteúdo de `OnPlatform` é `Platforms`, portanto, essas marcas de elemento de propriedade podem ser removidas:

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

A propriedade `Platform` de `On` é do tipo `IList<string>`, portanto, você pode incluir várias plataformas se os valores forem os mesmos:

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

Como o Android e o UWP são definidos com o valor padrão de `Padding`, essa marca pode ser removida:

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

Essa é a maneira padrão de definir uma propriedade de `Padding` dependente de plataforma em XAML. Se a configuração de `Value` não puder ser representada por uma única cadeia de caracteres, você poderá definir elementos de propriedade para ela:

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

> [!NOTE]
> A extensão de marcação de `OnPlatform` também pode ser usada em XAML para personalizar a aparência da interface do usuário em uma base por plataforma. Ele fornece a mesma funcionalidade que as classes `OnPlatform` e `On`, mas com uma representação mais concisa. Para obter mais informações, consulte [extensão de marcação do onplatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform).

## <a name="summary"></a>Resumo

Com elementos de propriedade e propriedades anexadas, grande parte da sintaxe XAML básica estabelecida. No entanto, às vezes, você precisa definir propriedades para objetos de uma maneira indireta, por exemplo, a partir de um dicionário de recursos. Essa abordagem é abordada na próxima parte, parte [3. Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md).

## <a name="related-links"></a>Links relacionados

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introdução com XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 3. Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Noções básicas de ligação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Da vinculação de dados com o MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
