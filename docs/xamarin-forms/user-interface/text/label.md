---
title: Rótulo do Xamarin. Forms
description: Este artigo explica como usar a classe rótulo Xamarin. Forms para exibir texto único e de várias linhas em aplicativos.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/09/2020
ms.openlocfilehash: 6ce4e39986afb7444e7d126e9789760d9311ca45
ms.sourcegitcommit: 737c7fbbe8aed1f33110a5217d7e6d6ef3e5b785
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793216"
---
# <a name="xamarinforms-label"></a>Rótulo do Xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Exibir texto no Xamarin. Forms_

A [`Label`](xref:Xamarin.Forms.Label) exibição é usada para exibir texto, de linha única e de várias linhas. Os rótulos podem ter decorações de texto, texto colorido e usar fontes personalizadas (famílias, tamanhos e opções).

## <a name="text-decorations"></a>Decorações de texto

As decorações de texto sublinhado e tachado podem ser [`Label`](xref:Xamarin.Forms.Label) aplicadas `Label.TextDecorations` a instâncias definindo a propriedade `TextDecorations` como um ou mais membros de enumeração:

- `None`
- `Underline`
- `Strikethrough`

O exemplo XAML a seguir demonstra como `Label.TextDecorations` definir a propriedade:

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

As capturas de tela a `TextDecorations` seguir mostram os membros [`Label`](xref:Xamarin.Forms.Label) de enumeração aplicados às instâncias:

![Rótulos com decorações de texto](label-images/label-textdecorations.png)

> [!NOTE]
> As decorações de texto também podem ser [`Span`](xref:Xamarin.Forms.Span) aplicadas a instâncias. Para obter mais informações sobre `Span` a classe, consulte [texto formatado](#Formatted_Text).

## <a name="character-spacing"></a>Espaçamento de caracteres

O espaçamento de caracteres pode [`Label`](xref:Xamarin.Forms.Label) ser aplicado a instâncias `Label.CharacterSpacing` definindo a propriedade `double` como um valor:

```xaml
<Label Text="Character spaced text"
       CharacterSpacing="10" />
```

Este é o código C# equivalente:

```csharp
Label label = new Label { Text = "Character spaced text", CharacterSpacing = 10 };
```

O resultado é que os caracteres no texto exibidos pelas unidades [`Label`](xref:Xamarin.Forms.Label) independentes de `CharacterSpacing` dispositivo espaçadas são separadas.

## <a name="new-lines"></a>Novas linhas

Há duas técnicas principais para forçar o texto em um [`Label`](xref:Xamarin.Forms.Label) em uma nova linha, do XAML:

1. Use o caractere de alimentação de linha Unicode, que&amp;é "#10;".
1. Especifique o texto usando a sintaxe do *elemento de propriedade* .

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

No C#, o texto pode ser forçado em uma nova linha com o caractere "\n":

```csharp
Label label = new Label { Text = "First line\nSecond line" };
```

## <a name="colors"></a>Cores

Os rótulos podem ser definidos para usar uma cor de texto personalizada por [`TextColor`](xref:Xamarin.Forms.Label.TextColor) meio da propriedade vinculável.

É necessário ter cuidado especial para garantir que as cores sejam utilizáveis em cada plataforma. Como cada plataforma tem padrões diferentes para cores de texto e de plano de fundo, você precisará ter cuidado ao escolher um padrão que funcione em cada um.

O exemplo XAML a seguir define a cor do texto `Label`de um:

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

As capturas de tela a seguir mostram o resultado `TextColor` da definição da propriedade:

![Exemplo de TextColor de rótulo](label-images/textcolor.png)

Para obter mais informações sobre cores, consulte [cores](~/xamarin-forms/user-interface/colors.md).

## <a name="fonts"></a>Fontes

Para obter mais informações sobre como especificar fontes `Label`em um, consulte [fontes](~/xamarin-forms/user-interface/text/fonts.md).

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Truncamento e encapsulamento

Os `LineBreakMode` rótulos podem ser definidos para manipular texto que não caiba em uma linha de várias maneiras, expostos pela propriedade. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode)é uma enumeração com os seguintes valores:

- **HeadTruncation** &ndash; trunca o cabeçalho do texto, mostrando o final.
- **CharacterWrap** &ndash; quebra o texto em uma nova linha com um limite de caracteres.
- **MiddleTruncation** &ndash; exibe o início e o fim do texto, com o meio substituir por uma elipse.
- O **nowrap** &ndash; não quebra o texto, exibindo apenas o número de texto que pode caber em uma linha.
- **TailTruncation** &ndash; mostra o início do texto, truncando o final.
- O **WordWrap** &ndash; quebra o texto no limite da palavra.

## <a name="display-a-specific-number-of-lines"></a>Exibir um número específico de linhas

O número de linhas exibidas por um [`Label`](xref:Xamarin.Forms.Label) pode ser especificado definindo a `Label.MaxLines` Propriedade como um `int` valor:

- Quando `MaxLines` é-1, que é o valor padrão, o `Label` respeita o valor da [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) propriedade para mostrar apenas uma linha, possivelmente truncada ou todas as linhas com todo o texto.
- Quando `MaxLines` é 0, o `Label` não é exibido.
- Quando `MaxLines` é 1, o resultado é idêntico à definição da [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) Propriedade como [`NoWrap`](xref:Xamarin.Forms.LineBreakMode), [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode), [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode)ou [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode). No entanto `Label` , o respeitará o valor [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) da propriedade com relação ao posicionamento de uma elipse, se aplicável.
- Quando `MaxLines` for maior que 1, o `Label` será exibido até o número especificado de linhas, respeitando o valor da [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) Propriedade com relação ao posicionamento de uma elipse, se aplicável. No entanto, `MaxLines` definir a propriedade com um valor maior que 1 não terá nenhum [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) efeito se a propriedade [`NoWrap`](xref:Xamarin.Forms.LineBreakMode)for definida como.

O exemplo XAML a seguir demonstra como `MaxLines` definir a propriedade [`Label`](xref:Xamarin.Forms.Label)em um:

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

As capturas de tela a seguir mostram o resultado `MaxLines` da definição da propriedade como 2, quando o texto é longo o suficiente para ocupar mais de duas linhas:

![Exemplo de MaxLines de rótulo](label-images/label-maxlines.png)

## <a name="display-html"></a>Exibir HTML

A [`Label`](xref:Xamarin.Forms.Label) classe tem uma `TextType` Propriedade, que determina se a `Label` instância deve exibir texto sem formatação ou texto HTML. Essa propriedade deve ser definida como um dos membros da `TextType` enumeração:

- `Text`indica que o `Label` exibirá texto sem formatação e é o valor padrão da `Label.TextType` propriedade.
- `Html`indica que o `Label` exibirá o texto HTML.

Portanto, [`Label`](xref:Xamarin.Forms.Label) as instâncias podem exibir HTML definindo a `Label.TextType` Propriedade como `Html`e a `Label.Text` Propriedade como uma cadeia de caracteres HTML:

```csharp
Label label = new Label
{
    Text = "This is <strong style=\"color:red\">HTML</strong> text.",
    TextType = TextType.Html
};
```

No exemplo acima, os caracteres de aspas duplas no HTML precisam ser ignorados usando o `\` símbolo.

Em XAML, as cadeias de caracteres HTML podem se tornar ilegíveis porque, adicionalmente, escapar os `<` símbolos e `>` :

```xaml
<Label Text="This is &lt;strong style=&quot;color:red&quot;&gt;HTML&lt;/strong&gt; text."
       TextType="Html"  />
```

Como alternativa, para maior legibilidade, o HTML pode ser embutido em uma `CDATA` seção:

```xaml
<Label TextType="Html">
    <![CDATA[
    This is <strong style="color:red">HTML</strong> text.
    ]]>
</Label>
```

Neste exemplo, a `Label.Text` propriedade é definida como a cadeia de caracteres HTML que está embutida na `CDATA` seção. Isso funciona porque a `Text` propriedade é `ContentProperty` para a `Label` classe.

As capturas de tela a [`Label`](xref:Xamarin.Forms.Label) seguir mostram um HTML de exibição:

![Capturas de tela de um rótulo que exibe HTML, no iOS e no Android](label-images/label-html.png)

> [!IMPORTANT]
> A exibição de HTML [`Label`](xref:Xamarin.Forms.Label) em um é limitada às marcas HTML com suporte na plataforma subjacente.

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Texto formatado

Os rótulos expõem uma [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) propriedade que permite a apresentação do texto com várias fontes e cores na mesma exibição.

A `FormattedText` propriedade é do tipo [`FormattedString`](xref:Xamarin.Forms.FormattedString), que consiste em uma ou mais [`Span`](xref:Xamarin.Forms.Span) instâncias, definidas por meio [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) da propriedade. As propriedades `Span` a seguir podem ser usadas para definir a aparência visual:

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor)– a cor do plano de fundo de span.
- `CharacterSpacing`, do tipo `double`, é o espaçamento entre os `Span` caracteres do texto.
- [`Font`](xref:Xamarin.Forms.Span.Font)– a fonte do texto na extensão.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes)– os atributos de fonte para o texto no span.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily)– a família de fontes à qual a fonte do texto no trecho pertence.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize)– o tamanho da fonte para o texto no intervalo.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor)– a cor do texto no intervalo. Esta propriedade é obsoleta e foi substituída pela `TextColor` propriedade.
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight)-o multiplicador a ser aplicado à altura de linha padrão da extensão. Para obter mais informações, consulte [altura da linha](#line-height).
- [`Style`](xref:Xamarin.Forms.Span.Style)– o estilo a ser aplicado ao SPAN.
- [`Text`](xref:Xamarin.Forms.Span.Text)– o texto da extensão.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor)– a cor do texto no intervalo.
- `TextDecorations`-as decorações a serem aplicadas ao texto na extensão. Para obter mais informações, consulte [decorações de texto](#text-decorations).

As [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor)propriedades [`Text`](xref:Xamarin.Forms.Span.Text), e [`Text`](xref:Xamarin.Forms.Span.Text) vinculáveis têm um modo de associação padrão [`OneWay`](xref:Xamarin.Forms.BindingMode)de. Para obter mais informações sobre esse modo de ligação, consulte [o modo de associação padrão](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode) no guia de [modo de ligação](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md) .

Além disso, a [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) propriedade pode ser usada para definir uma coleção de reconhecedores de gesto que responderão a gestos [`Span`](xref:Xamarin.Forms.Span)no.

> [!NOTE]
> Não é possível exibir HTML em um [`Span`](xref:Xamarin.Forms.Span).

O exemplo XAML a seguir demonstra `FormattedText` uma propriedade que consiste em [`Span`](xref:Xamarin.Forms.Span) três instâncias:

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
> A [`Text`](xref:Xamarin.Forms.Span.Text) propriedade de um `Span` pode ser definida por meio da vinculação de dados. Para obter mais informações, confira [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Observe que um [`Span`](xref:Xamarin.Forms.Span) também pode responder a qualquer gesto que seja adicionado à coleção do [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) span. Por exemplo, um [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) foi adicionado ao segundo `Span` nos exemplos de código acima. Portanto, quando isso `Span` for tocado, `TapGestureRecognizer` o responderá executando o `ICommand` definido pela [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) propriedade. Para obter mais informações sobre os reconhecedores de gesto, consulte [gestos do Xamarin. Forms](~/xamarin-forms/app-fundamentals/gestures/index.md).

As capturas de tela a seguir mostram o resultado `FormattedString` da definição da `Span` Propriedade como três instâncias:

![Exemplo de FormattedText de rótulo](label-images/formattedtext.png)

## <a name="line-height"></a>Altura da linha

A altura vertical de um [`Label`](xref:Xamarin.Forms.Label) e um [`Span`](xref:Xamarin.Forms.Span) pode ser personalizada definindo a [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) propriedade ou [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) como um `double` valor. No iOS e no Android, esses valores são multiplicadores da altura da linha original e, no Plataforma Universal do Windows (UWP) `Label.LineHeight` , o valor da propriedade é um multiplicador do tamanho da fonte do rótulo.

> [!NOTE]
>
> - No iOS, as [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) propriedades [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) e alteram a altura da linha do texto que cabe em uma única linha e o texto que é quebrado em várias linhas.
> - No Android, as [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) propriedades [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) e só alteram a altura da linha do texto que é quebrado em várias linhas.
> - No UWP, a [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) propriedade altera a altura da linha do texto que é quebrado em várias linhas e a [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) propriedade não tem efeito.

O exemplo XAML a seguir demonstra como [`LineHeight`](xref:Xamarin.Forms.Label.LineHeight) definir a propriedade [`Label`](xref:Xamarin.Forms.Label)em um:

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

As capturas de tela a seguir mostram o resultado [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) da definição da propriedade como 1,8:

![Exemplo de LineHeight de rótulo](label-images/label-lineheight.png)

O exemplo XAML a seguir demonstra como [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) definir a propriedade [`Span`](xref:Xamarin.Forms.Span)em um:

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

As capturas de tela a seguir mostram o resultado [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) da definição da propriedade como 1,8:

![Exemplo de span LineHeight](label-images/span-lineheight.png)

## <a name="padding"></a>Preenchimento

O preenchimento representa o espaço entre um elemento e seus elementos filho e é usado para separar o elemento de seu próprio conteúdo. O preenchimento pode ser aplicado [`Label`](xref:Xamarin.Forms.Label) a instâncias definindo a `Label.Padding` Propriedade como um [`Thickness`](xref:Xamarin.Forms.Thickness) valor:

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
> No iOS, quando um [`Label`](xref:Xamarin.Forms.Label) é criado que define a `Padding` Propriedade, o preenchimento será aplicado e o valor de preenchimento poderá ser atualizado mais tarde. No entanto, `Label` quando um é criado e não `Padding` define a propriedade, tentar defini-la mais tarde não terá nenhum efeito.
>
> No Android e no Plataforma Universal do Windows, o `Padding` valor da propriedade pode ser especificado quando `Label` o é criado, ou mais tarde.

Para obter mais informações sobre preenchimento, consulte [margens e preenchimento](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="hyperlinks"></a>Hiperlinks

O texto exibido por [`Label`](xref:Xamarin.Forms.Label) e [`Span`](xref:Xamarin.Forms.Span) as instâncias podem ser transformados em hiperlinks com a seguinte abordagem:

1. Defina as `TextColor` propriedades `TextDecoration` e do [`Label`](xref:Xamarin.Forms.Label) ou [`Span`](xref:Xamarin.Forms.Span).
1. Adicione um [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) à coleção de [`Label`](xref:Xamarin.Forms.Label) ou [`Span`](xref:Xamarin.Forms.Span), cuja [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) propriedade é vinculada a um `ICommand`e cuja [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) propriedade contém a URL a ser aberta.
1. Defina o `ICommand` que será executado pelo [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer).
1. Escreva o código que será executado pelo `ICommand`.

O exemplo de código a seguir, extraído do exemplo de [demonstrações de hiperlink](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) , mostra um [`Label`](xref:Xamarin.Forms.Label) cujo [`Span`](xref:Xamarin.Forms.Span) conteúdo está definido de várias instâncias:

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

Neste exemplo, a primeira e terceira [`Span`](xref:Xamarin.Forms.Span) instâncias compõem texto, enquanto o segundo `Span` representa um hiperlink tappable. Ele tem sua cor de texto definida como azul e tem uma decoração de texto sublinhado. Isso cria a aparência de um hiperlink, conforme mostrado nas seguintes capturas de tela:

[![Hiperlinks](label-images/hyperlinks-small.png "Hiperlinks")](label-images/hyperlinks-large.png#lightbox)

Quando o hiperlink for tocado, o [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) responderá executando o `ICommand` definido por sua [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) propriedade. Além disso, a URL especificada pela [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) propriedade será passada para o `ICommand` como um parâmetro.

O code-behind da página XAML contém a `TapCommand` implementação:

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

O `TapCommand` executa o `Launcher.OpenAsync` método, passando o valor [`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) da propriedade como um parâmetro. O `Launcher.OpenAsync` método é fornecido pelo Xamarin. Essentials e abre a URL em um navegador da Web. Portanto, o efeito geral é que, quando o hiperlink é tocado na página, um navegador da Web é exibido e a URL associada ao hiperlink é navegada para.

### <a name="creating-a-reusable-hyperlink-class"></a>Criando uma classe de hiperlink reutilizável

A abordagem anterior para criar um hiperlink requer escrever código repetitivo toda vez que você precisar de um hiperlink em seu aplicativo. No entanto, [`Label`](xref:Xamarin.Forms.Label) as [`Span`](xref:Xamarin.Forms.Span) classes e podem ser subclasses para `HyperlinkLabel` criar `HyperlinkSpan` classes e, com o reconhecedor de gestos e o código de formatação de texto adicionado lá.

O exemplo de código a seguir, extraído do exemplo de [demonstrações de hiperlink](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) , mostra uma `HyperlinkSpan` classe:

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

A `HyperlinkSpan` classe define uma `Url` Propriedade e associada [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), e o construtor define a aparência do hiperlink e o [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) que responderá quando o hiperlink for tocado. Quando um `HyperlinkSpan` for tocado, o `TapGestureRecognizer` responderá executando o `Launcher.OpenAsync` método para abrir a URL, especificada pela `Url` Propriedade, em um navegador da Web.

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

As seções anteriores abordaram [`Label`](xref:Xamarin.Forms.Label) as [`Span`](xref:Xamarin.Forms.Span) configurações e propriedades em uma base por instância. No entanto, conjuntos de propriedades podem ser agrupados em um estilo que é aplicado consistentemente a uma ou várias exibições. Isso pode aumentar a legibilidade do código e facilitar a implementação das alterações de design. Para obter mais informações, consulte [estilos](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Links relacionados

- [Texto (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Hiperlinks (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [Criando aplicativos móveis com Xamarin. Forms, capítulo 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [API de Rótulo](xref:Xamarin.Forms.Label)
- [API de span](xref:Xamarin.Forms.Span)
