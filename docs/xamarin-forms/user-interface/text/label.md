---
title: Rótulo Xamarin.Forms
description: Este artigo explica como usar a classe Xamarin.Forms Label para exibir texto único e multi-linha em aplicativos.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/09/2020
ms.openlocfilehash: bed6b8a774ecb352427f16b78d10e50821f92701
ms.sourcegitcommit: 765b69ed451a0f48625ea597c3f39de95f3ae693
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80987589"
---
# <a name="xamarinforms-label"></a>Rótulo Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Exibir texto em Xamarin.Forms_

A [`Label`](xref:Xamarin.Forms.Label) exibição é usada para exibir texto, tanto single quanto multi-line. Os rótulos podem ter decorações de texto, texto colorido e usar fontes personalizadas (famílias, tamanhos e opções).

## <a name="text-decorations"></a>Decorações de texto

Sublinhar e fazer a decoração de [`Label`](xref:Xamarin.Forms.Label) texto pode `Label.TextDecorations` ser aplicada `TextDecorations` às instâncias definindo a propriedade para um ou mais membros de enumeração:

- `None`
- `Underline`
- `Strikethrough`

O exemplo XAML a `Label.TextDecorations` seguir demonstra a configuração da propriedade:

```xaml
<Label Text="This is underlined text." TextDecorations="Underline"  />
<Label Text="This is text with strikethrough." TextDecorations="Strikethrough" />
<Label Text="This is underlined text with strikethrough." TextDecorations="Underline, Strikethrough" />
```

Este é o código C# equivalente:

```csharp
var underlineLabel = new Label { Text = "This is underlined text.", TextDecorations = TextDecorations.Underline };
var strikethroughLabel = new Label { Text = "This is text with strikethrough.", TextDecorations = TextDecorations.Strikethrough };
var bothLabel = new Label { Text = "This is underlined text with strikethrough.", TextDecorations = TextDecorations.Underline | TextDecorations.Strikethrough };
```

As capturas de `TextDecorations` tela a seguir [`Label`](xref:Xamarin.Forms.Label) mostram os membros de enumeração aplicados às instâncias:

![Rótulos com Decorações de Texto](label-images/label-textdecorations.png)

> [!NOTE]
> Decorações de texto também [`Span`](xref:Xamarin.Forms.Span) podem ser aplicadas a instâncias. Para obter mais `Span` informações sobre a classe, consulte [Texto Formatado](#Formatted_Text).

## <a name="character-spacing"></a>Espaçamento de caracteres

O espaçamento de [`Label`](xref:Xamarin.Forms.Label) caracteres pode `Label.CharacterSpacing` ser `double` aplicado às instâncias definindo a propriedade como um valor:

```xaml
<Label Text="Character spaced text"
       CharacterSpacing="10" />
```

Este é o código C# equivalente:

```csharp
Label label = new Label { Text = "Character spaced text", CharacterSpacing = 10 };
```

O resultado é que os caracteres [`Label`](xref:Xamarin.Forms.Label) no `CharacterSpacing` texto exibido sao unidades independentes de dispositivos espaçados separadas.

## <a name="new-lines"></a>Novas linhas

Existem duas técnicas principais [`Label`](xref:Xamarin.Forms.Label) para forçar o texto em uma nova linha, da XAML:

1. Use o caractere de feed de linha unicode, que é "&#10;".
1. Especifique seu texto usando a sintaxe do elemento de *propriedade.*

O código a seguir mostra um exemplo de ambas as técnicas:

```xaml
<!-- Unicode line feed character -->
<Label Text="First line &#10; Second line" />

<!-- Property element syntax -->
<Label>
    <Label.Text>
        First line
        Second line
    </Label.Text>
</Label>
```

Em C#, o texto pode ser forçado a uma nova linha com o caractere "\n":

```csharp
Label label = new Label { Text = "First line\nSecond line" };
```

## <a name="colors"></a>Cores

Os rótulos podem ser definidos para usar [`TextColor`](xref:Xamarin.Forms.Label.TextColor) uma cor de texto personalizada através da propriedade vinculável.

É necessário um cuidado especial para garantir que as cores sejam utilizáveis em cada plataforma. Como cada plataforma tem padrões diferentes para cores de texto e de fundo, você precisará ter cuidado para escolher um padrão que funcione em cada um.

O exemplo XAML a seguir `Label`define a cor do texto de :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo">
    <StackLayout Padding="5,10">
      <Label TextColor="#77d065" FontSize = "20" Text="This is a green label." />
    </StackLayout>
</ContentPage>
```

Este é o código C# equivalente:

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();

        var layout = new StackLayout { Padding = new Thickness(5,10) };
        var label = new Label { Text="This is a green label.", TextColor = Color.FromHex("#77d065"), FontSize = 20 };
        layout.Children.Add(label);
        this.Content = layout;
    }
}
```

As capturas de tela a `TextColor` seguir mostram o resultado da configuração da propriedade:

![Exemplo de marcação texto-de-texto](label-images/textcolor.png)

Para obter mais informações sobre cores, consulte [Cores](~/xamarin-forms/user-interface/colors.md).

## <a name="fonts"></a>Fontes

Para obter mais informações sobre `Label`como especificar fontes em um , consulte [Fontes](~/xamarin-forms/user-interface/text/fonts.md).

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Truncação e embrulho

Os rótulos podem ser definidos para lidar com textos que não podem `LineBreakMode` caber em uma linha de várias maneiras, expostos pela propriedade. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode)é uma enumeração com os seguintes valores:

- **HeadTruncation** &ndash; trunca a cabeça do texto, mostrando o fim.
- **CharacterWrap** &ndash; envolve texto em uma nova linha em um limite de caracteres.
- **MiddleTruncation** &ndash; exibe o início e o fim do texto, com a substituição do meio por uma elipse.
- **O NoWrap** &ndash; não envolve texto, exibindo apenas o máximo de texto que pode caber em uma linha.
- **TailTruncation** &ndash; mostra o início do texto, truncando o final.
- **WordWrap** &ndash; envolve texto no limite da palavra.

## <a name="display-a-specific-number-of-lines"></a>Exibir um número específico de linhas

O número de linhas [`Label`](xref:Xamarin.Forms.Label) exibidas por um `Label.MaxLines` pode ser `int` especificado definindo a propriedade como um valor:

- Quando `MaxLines` é -1, que é `Label` o seu valor [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) padrão, o respeito o valor da propriedade para mostrar apenas uma linha, possivelmente truncada, ou todas as linhas com todo o texto.
- Quando `MaxLines` é 0, o `Label` não é exibido.
- Quando `MaxLines` é 1, o resultado [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) é [`NoWrap`](xref:Xamarin.Forms.LineBreakMode)idêntico [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode) [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode)ao [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode)de definir a propriedade para , , ou . No entanto, o `Label` vai [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) respeitar o valor do imóvel no que diz respeito à colocação de uma elipse, se aplicável.
- Quando `MaxLines` for maior que `Label` 1, o exibirá até o número especificado [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) de linhas, respeitando o valor da propriedade no que diz respeito à colocação de uma elipse, se aplicável. No entanto, definir a `MaxLines` propriedade para um [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) valor maior [`NoWrap`](xref:Xamarin.Forms.LineBreakMode)que 1 não tem efeito se a propriedade for definida como .

O exemplo XAML a `MaxLines` seguir [`Label`](xref:Xamarin.Forms.Label)demonstra a configuração da propriedade em um:

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       MaxLines="2" />
```

Este é o código C# equivalente:

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  MaxLines = 2
};
```

As capturas de tela a `MaxLines` seguir mostram o resultado da configuração da propriedade para 2, quando o texto é longo o suficiente para ocupar mais de 2 linhas:

![Exemplo de marcação MaxLines](label-images/label-maxlines.png)

## <a name="display-html"></a>Exibir HTML

A [`Label`](xref:Xamarin.Forms.Label) classe `TextType` tem uma propriedade, `Label` que determina se a instância deve exibir texto simples ou texto HTML. Esta propriedade deve ser definida como `TextType` um dos membros da enumeração:

- `Text`indica que `Label` o exibirá texto simples e `Label.TextType` é o valor padrão da propriedade.
- `Html`indica que `Label` o texto será exibido HTML.

[`Label`](xref:Xamarin.Forms.Label) Portanto, as instâncias podem `Label.TextType` exibir `Html`HTML definindo a propriedade para , e a `Label.Text` propriedade para uma seqüência HTML:

```csharp
Label label = new Label
{
    Text = "This is <strong style=\"color:red\">HTML</strong> text.",
    TextType = TextType.Html
};
```

No exemplo acima, os caracteres de citação dupla `\` no HTML devem ser escapados usando o símbolo.

No XAML, as strings HTML podem se tornar `<` ilegíveis devido à fuga adicional dos símbolos: `>`

```xaml
<Label Text="This is &lt;strong style=&quot;color:red&quot;&gt;HTML&lt;/strong&gt; text."
       TextType="Html"  />
```

Alternativamente, para maior legibilidade, o HTML `CDATA` pode ser inforrado em uma seção:

```xaml
<Label TextType="Html">
    <![CDATA[
    This is <strong style="color:red">HTML</strong> text.
    ]]>
</Label>
```

Neste exemplo, `Label.Text` a propriedade é definida como a seqüência HTML que é inforrada na seção. `CDATA` Isso funciona `Text` porque a `ContentProperty` propriedade `Label` é para a classe.

As capturas de [`Label`](xref:Xamarin.Forms.Label) tela a seguir mostram um HTML exibido:

![Capturas de tela de um rótulo exibindo HTML, no iOS e Android](label-images/label-html.png)

> [!IMPORTANT]
> A exibição [`Label`](xref:Xamarin.Forms.Label) de HTML em um é limitada às tags HTML que são suportadas pela plataforma subjacente.

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Texto formatado

Os rótulos [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) expõem uma propriedade que permite a apresentação de texto com várias fontes e cores na mesma exibição.

O `FormattedText` imóvel é [`FormattedString`](xref:Xamarin.Forms.FormattedString)do tipo , [`Span`](xref:Xamarin.Forms.Span) que compreende uma [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) ou mais instâncias, definidas através do imóvel. As `Span` seguintes propriedades podem ser usadas para definir a aparência visual:

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor)– a cor do fundo de extensão.
- `CharacterSpacing`, do `double`tipo , é o espaçamento entre caracteres do `Span` texto.
- [`Font`](xref:Xamarin.Forms.Span.Font)– a fonte para o texto no intervalo.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes)– os atributos de fonte para o texto no período.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily)– a família de fontes à qual a fonte para o texto no intervalo pertence.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize)– o tamanho da fonte para o texto no intervalo.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor)– a cor do texto no vão. Esta propriedade está obsoleta e foi substituída pela `TextColor` propriedade.
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight)- o multiplicador para aplicar à altura da linha padrão do vão. Para obter mais informações, consulte [Altura de linha](#line-height).
- [`Style`](xref:Xamarin.Forms.Span.Style)– o estilo para aplicar ao vão.
- [`Text`](xref:Xamarin.Forms.Span.Text)– o texto do período.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor)– a cor do texto no vão.
- `TextDecorations`- as decorações para aplicar ao texto no vão. Para obter mais informações, consulte [Decorações de Texto](#text-decorations).

As [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) [`Text`](xref:Xamarin.Forms.Span.Text)propriedades [`Text`](xref:Xamarin.Forms.Span.Text) , e vinculáveis têm [`OneWay`](xref:Xamarin.Forms.BindingMode)um modo de vinculação padrão de . Para obter mais informações sobre esse modo de vinculação, consulte [O modo de vinculação padrão](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode) no guia Modo de [Vinculação.](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md)

Além disso, [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) a propriedade pode ser usada para definir uma coleção de [`Span`](xref:Xamarin.Forms.Span)reconhecimentos de gestos que responderão a gestos no .

> [!NOTE]
> Não é possível exibir HTML [`Span`](xref:Xamarin.Forms.Span)em um .

O exemplo XAML `FormattedText` a seguir demonstra [`Span`](xref:Xamarin.Forms.Span) uma propriedade que consiste em três instâncias:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo - XAML">
    <StackLayout Padding="5,10">
        ...
        <Label LineBreakMode="WordWrap">
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Red Bold, " TextColor="Red" FontAttributes="Bold" />
                    <Span Text="default, " Style="{DynamicResource BodyStyle}">
                        <Span.GestureRecognizers>
                            <TapGestureRecognizer Command="{Binding TapCommand}" />
                        </Span.GestureRecognizers>
                    </Span>
                    <Span Text="italic small." FontAttributes="Italic" FontSize="Small" />
                </FormattedString>
            </Label.FormattedText>
        </Label>
    </StackLayout>
</ContentPage>
```

Este é o código C# equivalente:

```csharp
public class LabelPageCode : ContentPage
{
    public LabelPageCode ()
    {
        var layout = new StackLayout{ Padding = new Thickness (5, 10) };
        ...
        var formattedString = new FormattedString ();
        formattedString.Spans.Add (new Span{ Text = "Red bold, ", ForegroundColor = Color.Red, FontAttributes = FontAttributes.Bold });

        var span = new Span { Text = "default, " };
        span.GestureRecognizers.Add(new TapGestureRecognizer { Command = new Command(async () => await DisplayAlert("Tapped", "This is a tapped Span.", "OK")) });
        formattedString.Spans.Add(span);
        formattedString.Spans.Add (new Span { Text = "italic small.", FontAttributes = FontAttributes.Italic, FontSize =  Device.GetNamedSize(NamedSize.Small, typeof(Label)) });

        layout.Children.Add (new Label { FormattedText = formattedString });
        this.Content = layout;
    }
}
```

> [!IMPORTANT]
> A [`Text`](xref:Xamarin.Forms.Span.Text) propriedade `Span` de um pode ser definida através da vinculação de dados. Para obter mais informações, confira [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Observe que [`Span`](xref:Xamarin.Forms.Span) um também pode responder a quaisquer gestos [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) que são adicionados à coleção do span. Por exemplo, [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) a foi adicionada `Span` ao segundo nos exemplos de código acima. Portanto, quando `Span` este for `TapGestureRecognizer` tocado, o testamento `ICommand` responderá [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) executando o definido pela propriedade. Para obter mais informações sobre reconhecimentos de gestos, consulte [Xamarin.Forms Gestures](~/xamarin-forms/app-fundamentals/gestures/index.md).

As capturas de tela a `FormattedString` seguir `Span` mostram o resultado da definição da propriedade em três instâncias:

![Exemplo de texto formatado pelo rótulo](label-images/formattedtext.png)

## <a name="line-height"></a>Altura da linha

A altura vertical [`Label`](xref:Xamarin.Forms.Label) de [`Span`](xref:Xamarin.Forms.Span) a e a [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) pode [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) ser `double` personalizada definindo a propriedade ou para um valor. No iOS e Android esses valores são multiplicadores da altura da linha `Label.LineHeight` original, e na Universal Windows Platform (UWP) o valor da propriedade é um multiplicador do tamanho da fonte da etiqueta.

> [!NOTE]
>
> - No iOS, [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) as propriedades alteram a altura da linha de texto que se encaixa em uma única linha, e o texto que envolve várias linhas.
> - No Android, [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) as propriedades e as propriedades só alteram a altura da linha de texto que envolve várias linhas.
> - No UWP, [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) a propriedade altera a altura da linha de [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) texto que envolve várias linhas, e a propriedade não tem efeito.

O exemplo XAML a [`LineHeight`](xref:Xamarin.Forms.Label.LineHeight) seguir [`Label`](xref:Xamarin.Forms.Label)demonstra a configuração da propriedade em um:

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       LineHeight="1.8" />
```

Este é o código C# equivalente:

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  LineHeight = 1.8
};
```

As capturas de tela a [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) seguir mostram o resultado da configuração da propriedade para 1.8:

![Exemplo de altura da linha do rótulo](label-images/label-lineheight.png)

O exemplo XAML a [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) seguir [`Span`](xref:Xamarin.Forms.Span)demonstra a configuração da propriedade em um:

```xaml
<Label LineBreakMode="WordWrap">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In a tincidunt sem. Phasellus mollis sit amet turpis in rutrum. Sed aliquam ac urna id scelerisque. "
                  LineHeight="1.8"/>
            <Span Text="Nullam feugiat sodales elit, et maximus nibh vulputate id."
                  LineHeight="1.8" />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

Este é o código C# equivalente:

```csharp
var formattedString = new FormattedString();
formattedString.Spans.Add(new Span
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In a tincidunt sem. Phasellus mollis sit amet turpis in rutrum. Sed aliquam ac urna id scelerisque. ",
  LineHeight = 1.8
});
formattedString.Spans.Add(new Span
{
  Text = "Nullam feugiat sodales elit, et maximus nibh vulputate id.",
  LineHeight = 1.8
});
var label = new Label
{
  FormattedText = formattedString,
  LineBreakMode = LineBreakMode.WordWrap
};
```

As capturas de tela a [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) seguir mostram o resultado da configuração da propriedade para 1.8:

![Exemplo de altura da linha de extensão](label-images/span-lineheight.png)

## <a name="padding"></a>Preenchimento

O preenchimento representa o espaço entre um elemento e seus elementos infantis, e é usado para separar o elemento de seu próprio conteúdo. O preenchimento pode ser [`Label`](xref:Xamarin.Forms.Label) aplicado às `Label.Padding` instâncias [`Thickness`](xref:Xamarin.Forms.Thickness) definindo a propriedade como um valor:

```xaml
<Label Padding="10">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Lorem ipsum" />
            <Span Text="dolor sit amet." />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

Este é o código C# equivalente:

```csharp
FormattedString formattedString = new FormattedString();
formattedString.Spans.Add(new Span
{
  Text = "Lorem ipsum"
});
formattedString.Spans.Add(new Span
{
  Text = "dolor sit amet."
});
Label label = new Label
{
    FormattedText = formattedString,
    Padding = new Thickness(20)
};
```

> [!IMPORTANT]
> No iOS, [`Label`](xref:Xamarin.Forms.Label) quando for criado `Padding` um que define a propriedade, o preenchimento será aplicado e o valor do preenchimento poderá ser atualizado posteriormente. No entanto, quando um `Label` é criado `Padding` que não define a propriedade, tentar defini-la mais tarde não terá efeito.
>
> No Android e na Universal `Padding` Windows Platform, o `Label` valor da propriedade pode ser especificado quando o for criado, ou posterior.

Para obter mais informações sobre o preenchimento, consulte [Margens e Preenchimento](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="hyperlinks"></a>Hiperlinks

O texto exibido [`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span) por e instâncias pode ser transformado em hiperlinks com a seguinte abordagem:

1. Defina `TextColor` `TextDecoration` as propriedades [`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span)do ou .
1. Adicione [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) a [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) à coleção [`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span)do [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) ou , cuja `ICommand`propriedade [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) se liga a a , e cuja propriedade contém a URL para abrir.
1. Defina `ICommand` o que será [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)executado pelo .
1. Escreva o código que será `ICommand`executado pelo .

O exemplo de código a seguir, extraído da [`Label`](xref:Xamarin.Forms.Label) amostra [Hyperlink Demos,](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) mostra um cujo conteúdo é definido a partir de várias [`Span`](xref:Xamarin.Forms.Span) instâncias:

```xaml
<Label>
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Alternatively, click " />
            <Span Text="here"
                  TextColor="Blue"
                  TextDecorations="Underline">
                <Span.GestureRecognizers>
                    <TapGestureRecognizer Command="{Binding TapCommand}"
                                          CommandParameter="https://docs.microsoft.com/xamarin/" />
                </Span.GestureRecognizers>
            </Span>
            <Span Text=" to view Xamarin documentation." />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

Neste exemplo, a primeira [`Span`](xref:Xamarin.Forms.Span) e a terceira `Span` instâncias compreendem texto, enquanto a segunda representa um hiperlink tappable. Ele tem sua cor de texto definida como azul, e tem uma decoração de texto sublinhado. Isso cria a aparência de um hiperlink, como mostrado nas capturas de tela a seguir:

[![Hiperlinks](label-images/hyperlinks-small.png "Hiperlinks")](label-images/hyperlinks-large.png#lightbox)

Quando o hiperlink é [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) tocado, o responderá executando o `ICommand` definido por sua [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) propriedade. Além disso, a URL [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) especificada pela propriedade `ICommand` será passada para o parâmetro.

O código-traseiro da página XAML contém a `TapCommand` implementação:

```csharp
public partial class MainPage : ContentPage
{
    // Launcher.OpenAsync is provided by Xamarin.Essentials.
    public ICommand TapCommand => new Command<string>(async (url) => await Launcher.OpenAsync(url));

    public MainPage()
    {
        InitializeComponent();
        BindingContext = this;
    }
}
```

O `TapCommand` executa `Launcher.OpenAsync` o método, passando o valor da [`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) propriedade como parâmetro. O `Launcher.OpenAsync` método é fornecido pelo Xamarin.Essentials e abre a URL em um navegador da Web. Portanto, o efeito geral é que quando o hiperlink é tocado na página, um navegador da Web aparece e a URL associada ao hiperlink é navegada.

### <a name="creating-a-reusable-hyperlink-class"></a>Criando uma classe de hiperlink reutilizável

A abordagem anterior para criar um hiperlink requer escrever código repetitivo toda vez que você precisar de um hiperlink em seu aplicativo. No entanto, [`Span`](xref:Xamarin.Forms.Span) tanto as classes quanto `HyperlinkLabel` `HyperlinkSpan` as [`Label`](xref:Xamarin.Forms.Label) classes podem ser subclassificadas para criar e classes, com o código de reconhecimento de gestos e formatação de texto adicionado lá.

O exemplo de código a seguir, extraído da `HyperlinkSpan` amostra [Hyperlink Demos,](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) mostra uma classe:

```csharp
public class HyperlinkSpan : Span
{
    public static readonly BindableProperty UrlProperty =
        BindableProperty.Create(nameof(Url), typeof(string), typeof(HyperlinkSpan), null);

    public string Url
    {
        get { return (string)GetValue(UrlProperty); }
        set { SetValue(UrlProperty, value); }
    }

    public HyperlinkSpan()
    {
        TextDecorations = TextDecorations.Underline;
        TextColor = Color.Blue;
        GestureRecognizers.Add(new TapGestureRecognizer
        {
            // Launcher.OpenAsync is provided by Xamarin.Essentials.
            Command = new Command(async () => await Launcher.OpenAsync(Url))
        });
    }
}
```

A `HyperlinkSpan` classe define `Url` uma propriedade [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)e associada, e o construtor [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) define a aparência do hiperlink e o que responderá quando o hiperlink for tocado. Quando `HyperlinkSpan` a é grampeada, a `TapGestureRecognizer` `Launcher.OpenAsync` vontade responderá executando o método `Url` para abrir a URL, especificada pela propriedade, em um navegador da Web.

A `HyperlinkSpan` classe pode ser consumida adicionando uma instância da classe ao XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:HyperlinkDemo"
             x:Class="HyperlinkDemo.MainPage">
    <StackLayout>
        ...
        <Label>
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Alternatively, click " />
                    <local:HyperlinkSpan Text="here"
                                         Url="https://docs.microsoft.com/appcenter/" />
                    <Span Text=" to view AppCenter documentation." />
                </FormattedString>
            </Label.FormattedText>
        </Label>
    </StackLayout>
</ContentPage>
```

## <a name="styling-labels"></a>Rótulos de estilo

As seções anteriores cobriam a configuração [`Label`](xref:Xamarin.Forms.Label) e [`Span`](xref:Xamarin.Forms.Span) as propriedades por instância. No entanto, conjuntos de propriedades podem ser agrupados em um estilo que é consistentemente aplicado a uma ou muitas visualizações. Isso pode aumentar a legibilidade do código e tornar as alterações de design mais fáceis de implementar. Para obter mais informações, consulte [Styles](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Links relacionados

- [Texto (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Hiperlinks (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [Criando aplicativos móveis com Xamarin.Forms, Capítulo 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [API de Rótulo](xref:Xamarin.Forms.Label)
- [Span API](xref:Xamarin.Forms.Span)
