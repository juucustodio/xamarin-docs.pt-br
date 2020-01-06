---
title: Rótulo do xamarin. Forms
description: Este artigo explica como usar a classe de rótulo do xamarin. Forms para exibir o texto de único e multilinha em aplicativos.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/28/2019
ms.openlocfilehash: 64fa15a15468a84ada3a377a9ac85bbf6310099c
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490032"
---
# <a name="xamarinforms-label"></a>Rótulo do xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_Exibir o texto no xamarin. Forms_

O [ `Label` ](xref:Xamarin.Forms.Label) exibição é usada para exibir o texto, único e de várias linha. Rótulos podem ter as decorações de texto, texto colorido e usar fontes personalizadas (famílias, tamanhos e opções).

## <a name="text-decorations"></a>Decorações de texto

Decorações de texto sublinhado e tachado podem ser aplicadas a [ `Label` ](xref:Xamarin.Forms.Label) instâncias, definindo o `Label.TextDecorations` propriedade a um ou mais `TextDecorations` membros de enumeração:

- `None`
- `Underline`
- `Strikethrough`

O exemplo XAML a seguir demonstra a configuração de `Label.TextDecorations` propriedade:

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

As capturas de tela a seguir mostram os `TextDecorations` aplicados a membros da enumeração [ `Label` ](xref:Xamarin.Forms.Label) instâncias:

![Rótulos com decorações de texto](label-images/label-textdecorations.png)

> [!NOTE]
> Decorações de texto também podem ser aplicadas a [ `Span` ](xref:Xamarin.Forms.Span) instâncias. Para obter mais informações sobre o `Span` classe, consulte [texto formatado em](#Formatted_Text).

## <a name="character-spacing"></a>Espaçamento de caracteres

O espaçamento de caracteres pode ser aplicado a instâncias de [`Label`](xref:Xamarin.Forms.Label) definindo a propriedade `Label.CharacterSpacing` como um valor `double`:

```xaml
<Label Text="Character spaced text"
       CharacterSpacing="10" />
```

Este é o código C# equivalente:

```csharp
Label label = new Label { Text = "Character spaced text", CharacterSpacing = 10 };
```

O resultado é que os caracteres no texto exibido pelo [`Label`](xref:Xamarin.Forms.Label) são espaçados `CharacterSpacing` unidades independentes de dispositivo.

## <a name="colors"></a>Cores

Rótulos podem ser definidos para usar uma cor de texto personalizado por meio de associável [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) propriedade.

Cuidado especial é necessário para garantir que as cores poderá ser usadas em cada plataforma. Como cada plataforma tem diferentes padrões de cores de plano de fundo e texto, você precisará ter cuidado para escolher um padrão que funciona em cada um.

O exemplo XAML a seguir define a cor do texto de um `Label`:

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

As capturas de tela a seguir mostram o resultado da configuração de `TextColor` propriedade:

![Exemplo de TextColor de rótulo](label-images/textcolor.png)

Para obter mais informações sobre as cores, consulte [cores](~/xamarin-forms/user-interface/colors.md).

## <a name="fonts"></a>Fontes

Para obter mais informações sobre como especificar fontes em uma `Label`, consulte [fontes](~/xamarin-forms/user-interface/text/fonts.md).

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Truncamento e quebra automática

Rótulos podem ser definidos para lidar com o texto que não cabe em uma linha em uma das várias maneiras, expostas pelo `LineBreakMode` propriedade. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) é uma enumeração com os seguintes valores:

- **HeadTruncation** &ndash; trunca o cabeçalho do texto, mostrando o final.
- **CharacterWrap** &ndash; quebra o texto em uma nova linha em um limite de caractere.
- **MiddleTruncation** &ndash; exibe o início e fim do texto, com a substituição do meio por reticências.
- **NoWrap** &ndash; não quebra o texto, exibindo somente texto como pode cabe em uma linha.
- **TailTruncation** &ndash; mostra o início do texto, truncando final.
- **WordWrap** &ndash; quebra o texto no limite de palavra.

## <a name="display-a-specific-number-of-lines"></a>Exibir um número específico de linhas

O número de linhas exibidas por um [ `Label` ](xref:Xamarin.Forms.Label) pode ser especificada definindo o `Label.MaxLines` propriedade para um `int` valor:

- Quando `MaxLines` é-1, que é o valor padrão, o `Label` respeita o valor da propriedade [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) para mostrar apenas uma linha, possivelmente truncada ou todas as linhas com todo o texto.
- Quando `MaxLines` é 0, o `Label` não é exibido.
- Quando `MaxLines` for 1, o resultado é idêntico à configuração de [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) propriedade a ser [ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode), [ `HeadTruncation` ](xref:Xamarin.Forms.LineBreakMode), [ `MiddleTruncation` ](xref:Xamarin.Forms.LineBreakMode), ou [ `TailTruncation` ](xref:Xamarin.Forms.LineBreakMode). No entanto, o `Label` respeitará o valor da [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) propriedade em relação ao posicionamento de reticências, se aplicável.
- Quando `MaxLines` é maior que 1, o `Label` exibirá até o número especificado de linhas, respeitando o valor da [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) propriedade em relação ao posicionamento de reticências, se aplicável. No entanto, definir a `MaxLines` propriedade para um valor maior que 1 não tem nenhum efeito se o [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) estiver definida como [ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode).

O exemplo XAML a seguir demonstra a configuração de `MaxLines` propriedade em um [ `Label` ](xref:Xamarin.Forms.Label):

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

As capturas de tela a seguir mostram o resultado da configuração de `MaxLines` propriedade como 2, quando o texto é longo o suficiente para ocupar mais de 2 linhas:

![Exemplo de MaxLines de rótulo](label-images/label-maxlines.png)

## <a name="display-html"></a>Exibir HTML

A classe [`Label`](xref:Xamarin.Forms.Label) tem uma propriedade `TextType`, que determina se a instância de `Label` deve exibir texto sem formatação ou texto HTML. Essa propriedade deve ser definida como um dos membros da enumeração `TextType`:

- `Text` indica que o `Label` exibirá texto sem formatação e é o valor padrão da propriedade `Label.TextType`.
- `Html` indica que o `Label` exibirá o texto HTML.

Portanto, [`Label`](xref:Xamarin.Forms.Label) instâncias podem exibir HTML definindo a propriedade `Label.TextType` como `Html`e a propriedade `Label.Text` como uma cadeia de caracteres HTML:

```csharp
Label label = new Label
{
    Text = "This is <strong style=\"color:red\">HTML</strong> text.",
    TextType = TextType.Html
};
```

No exemplo acima, os caracteres de aspas duplas no HTML precisam ser ignorados usando o símbolo de `\`.

Em XAML, as cadeias de caracteres HTML podem se tornar ilegíveis porque, adicionalmente, escapar os símbolos `<` e `>`:

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

Neste exemplo, a propriedade `Label.Text` é definida como a cadeia de caracteres HTML que é embutida na seção `CDATA`. Isso funciona porque a propriedade `Text` é a `ContentProperty` da classe `Label`.

As capturas de tela a seguir mostram uma [`Label`](xref:Xamarin.Forms.Label) exibição de HTML:

![Capturas de tela de um rótulo que exibe HTML, no iOS e no Android](label-images/label-html.png)

> [!IMPORTANT]
> A exibição de HTML em uma [`Label`](xref:Xamarin.Forms.Label) é limitada às marcas HTML com suporte na plataforma subjacente.

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Texto formatado

Os rótulos expõem uma propriedade [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) que permite a apresentação do texto com várias fontes e cores na mesma exibição.

O `FormattedText` propriedade é do tipo [ `FormattedString` ](xref:Xamarin.Forms.FormattedString), que consiste em um ou mais [ `Span` ](xref:Xamarin.Forms.Span) instâncias, definidas por meio de [ `Spans` ](xref:Xamarin.Forms.FormattedString.Spans) propriedade . O seguinte `Span` propriedades podem ser usadas para definir a aparência visual:

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) – a cor do plano de fundo span.
- `CharacterSpacing`, do tipo `double`, é o espaçamento entre os caracteres do texto do `Span`.
- [`Font`](xref:Xamarin.Forms.Span.Font) – a fonte do texto no trecho.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) – os atributos de fonte para o texto no trecho.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) – a família de fontes ao qual pertence a fonte do texto no trecho.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize) – o tamanho da fonte para o texto no trecho.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) – a cor do texto no trecho. Essa propriedade está obsoleta e foi substituída pelo `TextColor` propriedade.
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) -o multiplicador para aplicar a altura de linha padrão da extensão. Para obter mais informações, consulte [altura da linha](#line-height).
- [`Style`](xref:Xamarin.Forms.Span.Style) – o estilo a ser aplicado ao SPAN.
- [`Text`](xref:Xamarin.Forms.Span.Text) – o texto da marca span.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor) – a cor do texto no trecho.
- `TextDecorations` -as decorações para aplicar ao texto no trecho. Para obter mais informações, consulte [decorações de texto](#text-decorations).

As propriedades [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor), [`Text`](xref:Xamarin.Forms.Span.Text)e [`Text`](xref:Xamarin.Forms.Span.Text) vinculáveis têm um modo de associação padrão de [`OneWay`](xref:Xamarin.Forms.BindingMode). Para obter mais informações sobre esse modo de ligação, consulte [o modo de associação padrão](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode) no guia de [modo de ligação](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md) .

Além disso, o [ `GestureRecognizers` ](xref:Xamarin.Forms.GestureElement.GestureRecognizers) propriedade pode ser usada para definir uma coleção dos reconhecedores de gestos que irá responder a gestos no [ `Span` ](xref:Xamarin.Forms.Span).

> [!NOTE]
> Não é possível exibir HTML em um [`Span`](xref:Xamarin.Forms.Span).

O exemplo XAML a seguir demonstra uma `FormattedText` propriedade consiste em três [ `Span` ](xref:Xamarin.Forms.Span) instâncias:

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
> O [ `Text` ](xref:Xamarin.Forms.Span.Text) propriedade de um `Span` pode ser definido por meio da vinculação de dados. Para obter mais informações, confira [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md).

Observe que um [ `Span` ](xref:Xamarin.Forms.Span) também pode responder a qualquer gestos que são adicionados para o período [ `GestureRecognizers` ](xref:Xamarin.Forms.GestureElement.GestureRecognizers) coleção. Por exemplo, uma [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer) foi adicionada à segunda `Span` nos exemplos de código acima. Portanto, quando isso `Span` é tocado a `TapGestureRecognizer` responderá executando o `ICommand` definido pelo [ `Command` ](xref:Xamarin.Forms.TapGestureRecognizer.Command) propriedade. Para obter mais informações sobre os reconhecedores de gestos, consulte [xamarin. Forms gestos](~/xamarin-forms/app-fundamentals/gestures/index.md).

As capturas de tela a seguir mostram o resultado da configuração de `FormattedString` propriedade para três `Span` instâncias:

![Exemplo de FormattedText de rótulo](label-images/formattedtext.png)

## <a name="line-height"></a>Altura da linha

A altura vertical de um [ `Label` ](xref:Xamarin.Forms.Label) e uma [ `Span` ](xref:Xamarin.Forms.Span) pode ser personalizado definindo o [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) propriedade ou [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) para um `double` valor. No iOS e Android, esses valores são multiplicadores da altura da linha original e na plataforma Universal de Windows (UWP) o `Label.LineHeight` valor da propriedade é um multiplicador de tamanho da fonte do rótulo.

> [!NOTE]
>
> - No iOS, o [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) e [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) propriedades alterar a altura da linha de texto que cabem em uma única linha e o texto que é quebrada em várias linhas.
> - No Android, o [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) e [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) propriedades apenas alterar a altura da linha de texto que é quebrada em várias linhas.
> - Na UWP, o [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) propriedade altera a altura da linha de texto que é quebrada em várias linhas, e o [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) propriedade não tem nenhum efeito.

O exemplo XAML a seguir demonstra a configuração de [ `LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) propriedade em uma [ `Label` ](xref:Xamarin.Forms.Label):

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

As capturas de tela a seguir mostram o resultado da configuração de [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) propriedade para o 1.8:

![Exemplo de LineHeight de rótulo](label-images/label-lineheight.png)

O exemplo XAML a seguir demonstra a configuração de [ `LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) propriedade em uma [ `Span` ](xref:Xamarin.Forms.Span):

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

As capturas de tela a seguir mostram o resultado da configuração de [ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) propriedade para o 1.8:

![Exemplo de span LineHeight](label-images/span-lineheight.png)

## <a name="padding"></a>Enchimento

O preenchimento representa o espaço entre um elemento e seus elementos filho e é usado para separar o elemento de seu próprio conteúdo. O preenchimento pode ser aplicado a instâncias de [`Label`](xref:Xamarin.Forms.Label) definindo a propriedade `Label.Padding` como um valor [`Thickness`](xref:Xamarin.Forms.Thickness) :

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
> No iOS, quando um [`Label`](xref:Xamarin.Forms.Label) é criado que define a propriedade `Padding`, o preenchimento será aplicado e o valor de preenchimento poderá ser atualizado mais tarde. No entanto, quando uma `Label` é criada e não define a propriedade `Padding`, tentar defini-la mais tarde não terá nenhum efeito.
>
> No Android e no Plataforma Universal do Windows, o valor da propriedade `Padding` pode ser especificado quando o `Label` é criado ou posterior.

Para obter mais informações sobre preenchimento, consulte [margens e preenchimento](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="hyperlinks"></a>Hiperlinks

O texto exibido pelas instâncias [`Label`](xref:Xamarin.Forms.Label) e [`Span`](xref:Xamarin.Forms.Span) pode ser transformado em hiperlinks com a seguinte abordagem:

1. Defina as propriedades `TextColor` e `TextDecoration` do [`Label`](xref:Xamarin.Forms.Label) ou [`Span`](xref:Xamarin.Forms.Span).
1. Adicione um [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) à coleção de [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) do [`Label`](xref:Xamarin.Forms.Label) ou [`Span`](xref:Xamarin.Forms.Span), cuja propriedade [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) é associada a um `ICommand`e cuja propriedade [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) contém a URL a ser aberta.
1. Defina o `ICommand` que será executado pelo [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer).
1. Escreva o código que será executado pelo `ICommand`.

O exemplo de código a seguir, extraído do exemplo de [demonstrações de hiperlink](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) , mostra uma [`Label`](xref:Xamarin.Forms.Label) cujo conteúdo está definido de várias instâncias de [`Span`](xref:Xamarin.Forms.Span) :

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

Neste exemplo, o primeiro e o terceiro [`Span`](xref:Xamarin.Forms.Span) instâncias compõem texto, enquanto a segunda `Span` representa um hiperlink tappable. Ele tem sua cor de texto definida como azul e tem uma decoração de texto sublinhado. Isso cria a aparência de um hiperlink, conforme mostrado nas seguintes capturas de tela:

[![Hiperlinks](label-images/hyperlinks-small.png "Hiperlinks")](label-images/hyperlinks-large.png#lightbox)

Quando o hiperlink for tocado, o [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) responderá executando o `ICommand` definido por sua propriedade [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) . Além disso, a URL especificada pela propriedade [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) será passada para o `ICommand` como um parâmetro.

O code-behind da página XAML contém a implementação de `TapCommand`:

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

O `TapCommand` executa o método `Launcher.OpenAsync`, passando o valor da propriedade [`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) como um parâmetro. O método `Launcher.OpenAsync` é fornecido pelo Xamarin. Essentials e abre a URL em um navegador da Web. Portanto, o efeito geral é que, quando o hiperlink é tocado na página, um navegador da Web é exibido e a URL associada ao hiperlink é navegada para.

### <a name="creating-a-reusable-hyperlink-class"></a>Criando uma classe de hiperlink reutilizável

A abordagem anterior para criar um hiperlink requer escrever código repetitivo toda vez que você precisar de um hiperlink em seu aplicativo. No entanto, as classes [`Label`](xref:Xamarin.Forms.Label) e [`Span`](xref:Xamarin.Forms.Span) podem ser subclasseas para criar `HyperlinkLabel` e `HyperlinkSpan` classes, com o reconhecedor de gestos e o código de formatação de texto adicionado lá.

O exemplo de código a seguir, extraído do exemplo de [demonstrações de hiperlink](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) , mostra uma classe `HyperlinkSpan`:

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

A classe `HyperlinkSpan` define uma propriedade `Url` e [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)associados, e o construtor define a aparência do hiperlink e o [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) que responderá quando o hiperlink for tocado. Quando um `HyperlinkSpan` é tocado, a `TapGestureRecognizer` responderá executando o método `Launcher.OpenAsync` para abrir a URL, especificada pela propriedade `Url`, em um navegador da Web.

A classe `HyperlinkSpan` pode ser consumida adicionando uma instância da classe ao XAML:

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

## <a name="styling-labels"></a>Estilo de rótulos

As seções anteriores coberto configuração [ `Label` ](xref:Xamarin.Forms.Label) e [ `Span` ](xref:Xamarin.Forms.Span) propriedades em uma base por instância. No entanto, os conjuntos de propriedades podem ser agrupados em um estilo que é aplicado consistentemente a um ou vários modos de exibição. Isso pode aumentar a legibilidade do código e fazer alterações de design mais fácil de implementar. Para obter mais informações, consulte [estilos](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Links relacionados

- [Texto (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Hiperlinks (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [Criação de aplicativos móveis com xamarin. Forms, capítulo 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [API de Rótulo](xref:Xamarin.Forms.Label)
- [API de alcance](xref:Xamarin.Forms.Span)
