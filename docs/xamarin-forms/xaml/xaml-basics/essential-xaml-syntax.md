---
title: Parte 2. Sintaxe essencial do XAML
description: Este artigo explica os recursos de sintaxe XAML essenciais dos elementos de propriedade e propriedades anexadas.
ms.prod: xamarin
ms.assetid: 4022F1DC-3802-4635-A553-688ABD3F0D5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 23d24ab7477bb7d9e95e4d78f25f334ae13a8ea2
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368512"
---
# <a name="part-2-essential-xaml-syntax"></a>Parte 2. Sintaxe essencial do XAML

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/xamlsamples)

_O XAML é basicamente projetado para instanciar e inicializar objetos. Mas, muitas vezes, as propriedades devem ser definidas para objetos complexos que não podem ser facilmente representadas como cadeias de caracteres XML e, às vezes, as propriedades definidas por uma classe devem ser definidas em uma classe filho. Essas duas necessidades exigem os recursos de sintaxe XAML essenciais dos elementos de propriedade e das propriedades anexadas._

## <a name="property-elements"></a>Elementos de propriedade

Em XAML, as propriedades das classes normalmente são definidas como atributos XML:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large"
       TextColor="Aqua" />
```

No entanto, há uma maneira alternativa de definir uma propriedade em XAML. Para tentar essa alternativa com `TextColor` , primeiro exclua a `TextColor` configuração existente:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large" />
```

Abra a marca de elemento vazio `Label` , separando-a em marcas de início e fim:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">

</Label>
```

Dentro dessas marcas, adicione marcas de início e término que consistem no nome da classe e um nome de propriedade separados por um ponto:

```xaml
<Label Text="Hello, XAML!"
       VerticalOptions="Center"
       FontAttributes="Bold"
       FontSize="Large">
    <Label.TextColor>

    </Label.TextColor>
</Label>
```

Defina o valor da propriedade como o conteúdo dessas novas marcas, desta forma:

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

Essas duas maneiras de especificar a `TextColor` propriedade são funcionalmente equivalentes, mas não usam as duas maneiras para a mesma propriedade porque isso efetivamente definiria a propriedade duas vezes e pode ser ambíguo.

Com essa nova sintaxe, uma terminologia prática pode ser introduzida:

- `Label` é um  *elemento Object*. É um Xamarin.Forms objeto expresso como um elemento XML.
- `Text`,  `VerticalOptions` `FontAttributes` e  `FontSize` são atributos de  *Propriedade*. Elas são Xamarin.Forms Propriedades expressas como atributos XML.
- Nesse trecho final, `TextColor` tornou-se um  *elemento Property*. É uma Xamarin.Forms propriedade, mas agora é um elemento XML.

A definição dos elementos de propriedade pode parecer, na primeira parte, uma violação da sintaxe XML, mas não é. O período não tem significado especial em XML. Para um decodificador XML, `Label.TextColor` é simplesmente um elemento filho normal.

No XAML, no entanto, essa sintaxe é muito especial. Uma das regras para elementos de propriedade é que nada mais pode aparecer na `Label.TextColor` marca. O valor da propriedade é sempre definido como conteúdo entre as marcas de início e término do elemento de propriedade.

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

A princípio, a sintaxe de elemento de propriedade pode parecer uma substituição de longo prazo desnecessária para algo comparativamente bem simples e, nesses exemplos, que é certamente o caso.

No entanto, a sintaxe de elemento de propriedade se torna essencial quando o valor de uma propriedade é muito complexo para ser expresso como uma cadeia de caracteres simples. Dentro das marcas de elemento de propriedade, você pode criar uma instância de outro objeto e definir suas propriedades. Por exemplo, você pode definir explicitamente uma propriedade, como `VerticalOptions` um `LayoutOptions` valor com configurações de propriedade:

```xaml
<Label>
    ...
    <Label.VerticalOptions>
        <LayoutOptions Alignment="Center" />
    </Label.VerticalOptions>
</Label>
```

Outro exemplo: o `Grid` tem duas propriedades chamadas `RowDefinitions` e `ColumnDefinitions` . Essas duas propriedades são do tipo `RowDefinitionCollection` e `ColumnDefinitionCollection` , que são coleções de `RowDefinition` `ColumnDefinition` objetos e. Você precisa usar a sintaxe do elemento de propriedade para definir essas coleções.

Aqui está o início do arquivo XAML para uma `GridDemoPage` classe, mostrando as marcas de elemento de propriedade para `RowDefinitions` as `ColumnDefinitions` coleções e:

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

Observe a sintaxe abreviada para definir células de dimensionamento automático, células de larguras e alturas de pixel e configurações de estrela.

## <a name="attached-properties"></a>Propriedades Anexadas

Você acabou de ver que o `Grid` exige elementos de propriedade para `RowDefinitions` as `ColumnDefinitions` coleções e para definir as linhas e colunas. No entanto, também deve haver alguma maneira para o programador indicar a linha e a coluna em que cada filho do `Grid` reside.

Dentro da marca de cada filho de `Grid` , você especifica a linha e a coluna do filho usando os seguintes atributos:

- `Grid.Row`
- `Grid.Column`

Os valores padrão desses atributos são 0. Você também pode indicar se um filho abrange mais de uma linha ou coluna com estes atributos:

- `Grid.RowSpan`
- `Grid.ColumnSpan`

Esses dois atributos têm valores padrão de 1.

Aqui está o arquivo GridDemoPage. XAML completo:

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

As `Grid.Row` `Grid.Column` configurações e de 0 não são necessárias, mas geralmente são incluídas para fins de clareza.

A aparência é a seguinte:

[![Layout de grade](essential-xaml-syntax-images/griddemo.png)](essential-xaml-syntax-images/griddemo-large.png#lightbox)

Julgamento unicamente da sintaxe, esses `Grid.Row` atributos,, `Grid.Column` `Grid.RowSpan` e `Grid.ColumnSpan` parecem ser campos estáticos ou propriedades de `Grid` , mas curiosamente o suficiente, não `Grid` define nada chamado `Row` , `Column` , `RowSpan` ou `ColumnSpan` .

Em vez disso, `Grid` define quatro propriedades vinculáveis chamadas `RowProperty` , `ColumnProperty` , `RowSpanProperty` e `ColumnSpanProperty` . Esses são tipos especiais de propriedades vinculáveis conhecidas como *Propriedades anexadas*. Eles são definidos pela `Grid` classe, mas definidos em filhos do `Grid` .

Quando você deseja usar essas propriedades anexadas no código, a `Grid` classe fornece métodos estáticos chamados `SetRow` , `GetColumn` e assim por diante. Mas, em XAML, essas propriedades anexadas são definidas como atributos nos filhos do `Grid` usando nomes de propriedades simples.

As propriedades anexadas são sempre reconhecíveis em arquivos XAML como atributos contendo uma classe e um nome de propriedade separados por um ponto. Elas são chamadas de *Propriedades anexadas* porque são definidas por uma classe (nesse caso, `Grid` ), mas anexadas a outros objetos (nesse caso, filhos do `Grid` ). Durante o layout, o `Grid` pode interrogar os valores dessas propriedades anexadas para saber onde posicionar cada filho.

A `AbsoluteLayout` classe define duas propriedades anexadas chamadas `LayoutBounds` e `LayoutFlags` . Aqui está um padrão quadriculado realizado usando o posicionamento proporcional e os recursos de dimensionamento de `AbsoluteLayout` :

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

[![Layout absoluto](essential-xaml-syntax-images/absolutedemo-large.png)](essential-xaml-syntax-images/absolutedemo-large.png#lightbox)

Para algo assim, você pode questionar a sabedoria do uso de XAML. Certamente, a repetição e a regularidade do `LayoutBounds` retângulo sugerem que ele pode ser melhor realizado no código.

Essa é certamente uma preocupação legítima, e não há problema com o balanceamento do uso de código e marcação ao definir suas interfaces de usuário. É fácil definir alguns dos visuais em XAML e, em seguida, usar o construtor do arquivo code-behind para adicionar mais elementos visuais que podem ser gerados melhor em loops.

## <a name="content-properties"></a>Propriedades de Conteúdo

Nos exemplos anteriores, os `StackLayout` objetos, `Grid` e `AbsoluteLayout` são definidos como a `Content` Propriedade do `ContentPage` , e os filhos desses layouts são realmente itens na `Children` coleção. Ainda assim `Content` , essas `Children` Propriedades e estão em qualquer lugar no arquivo XAML.

Você certamente pode incluir as `Content` `Children` Propriedades e como elementos de propriedade, como no exemplo de **XamlPlusCode** :

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

Elementos definidos em Xamarin.Forms para uso em XAML têm permissão para ter uma propriedade sinalizada no `ContentProperty` atributo na classe. Se você pesquisar a `ContentPage` classe na Xamarin.Forms documentação online, verá este atributo:

```csharp
[Xamarin.Forms.ContentProperty("Content")]
public class ContentPage : TemplatedPage
```

Isso significa que as `Content` marcas de elemento de propriedade não são necessárias. Qualquer conteúdo XML que aparece entre as marcas inicial e final `ContentPage` é considerado como sendo atribuído à `Content` propriedade.

 `StackLayout`, `Grid` , `AbsoluteLayout` e `RelativeLayout` todos derivam de `Layout<View>` e, se você Pesquisar `Layout<T>` na  Xamarin.Forms documentação, verá outro `ContentProperty` atributo:

```csharp
[Xamarin.Forms.ContentProperty("Children")]
public abstract class Layout<T> : Layout ...
```

Isso permite que o conteúdo do layout seja adicionado automaticamente à `Children` coleção sem marcas explícitas de `Children` elemento de propriedade.

Outras classes também têm `ContentProperty` definições de atributo. Por exemplo, a propriedade Content de `Label` é `Text` . Verifique a documentação da API para outras pessoas.

## <a name="platform-differences-with-onplatform"></a>Diferenças de plataforma com o onplatform

Em aplicativos de página única, é comum definir a `Padding` Propriedade na página para evitar a substituição da barra de status do Ios. No código, você pode usar a `Device.RuntimePlatform` propriedade para essa finalidade:

```csharp
if (Device.RuntimePlatform == Device.iOS)
{
    Padding = new Thickness(0, 20, 0, 0);
}
```

Você também pode fazer algo semelhante em XAML usando as [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) [`On`](xref:Xamarin.Forms.On) classes e. Primeiro inclua os elementos de propriedade para a `Padding` Propriedade próxima à parte superior da página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="...">

    <ContentPage.Padding>

    </ContentPage.Padding>
    ...
</ContentPage>
```

Dentro dessas marcas, inclua uma `OnPlatform` marca. `OnPlatform` é uma classe genérica. Você precisa especificar o argumento de tipo genérico, nesse caso, `Thickness` , que é o tipo de `Padding` propriedade. Felizmente, há um atributo XAML especificamente para definir argumentos genéricos chamados `x:TypeArguments` . Isso deve corresponder ao tipo da propriedade que você está definindo:

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

`OnPlatform` tem uma propriedade chamada `Platforms` que é um `IList` dos `On` objetos. Use marcas de elemento de propriedade para essa propriedade:

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

Agora, adicione `On` elementos. Para cada uma, defina a `Platform` propriedade e a `Value` propriedade como marcação para a `Thickness` Propriedade:

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

Essa marcação pode ser simplificada. A propriedade Content de `OnPlatform` é `Platforms` , portanto, essas marcas de elemento de propriedade podem ser removidas:

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

A `Platform` propriedade de `On` é do tipo `IList<string>` , de modo que você pode incluir várias plataformas se os valores forem os mesmos:

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

Como o Android e o UWP são definidos com o valor padrão de `Padding` , essa marca pode ser removida:

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

Essa é a maneira padrão de definir uma propriedade dependente de plataforma `Padding` em XAML. Se a `Value` configuração não puder ser representada por uma única cadeia de caracteres, você poderá definir elementos de propriedade para ela:

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
> A `OnPlatform` extensão de marcação também pode ser usada em XAML para personalizar a aparência da interface do usuário em uma base por plataforma. Ele fornece a mesma funcionalidade das `OnPlatform` classes e `On` , mas com uma representação mais concisa. Para obter mais informações, consulte [extensão de marcação do onplatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

## <a name="summary"></a>Resumo

Com elementos de propriedade e propriedades anexadas, grande parte da sintaxe XAML básica foi estabelecida. No entanto, às vezes, você precisa definir propriedades para objetos de maneira indireta, por exemplo, de um dicionário de recursos. Essa abordagem é abordada na próxima parte, parte [3. Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md).

## <a name="related-links"></a>Links Relacionados

- [XamlSamples](/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Parte 1. Introdução com XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 3. Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Noções básicas de ligação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Da vinculação de dados com o MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)