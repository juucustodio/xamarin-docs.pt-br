---
title: Rótulo do xamarin. Forms
description: Este artigo explica como usar a classe de rótulo do xamarin. Forms para exibir o texto de único e multilinha em aplicativos.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/16/2018
ms.openlocfilehash: b5069381126db0859508480df5596ed5ec85686f
ms.sourcegitcommit: 4c0093ee5d4aeb16c0e6f0c740c4796736971651
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39203030"
---
# <a name="xamarinforms-label"></a>Rótulo do xamarin. Forms

_Exibir o texto no xamarin. Forms_

O [ `Label` ](xref:Xamarin.Forms.Label) exibição é usada para exibir o texto, único e de várias linha. Rótulos podem ter fontes personalizadas (famílias, tamanhos e opções) e texto colorido.

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Truncamento e quebra automática

Rótulos podem ser definidos para lidar com o texto que não cabe em uma linha em uma das várias maneiras, expostas pelo `LineBreakMode` propriedade. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) é uma enumeração com os seguintes valores:

- **HeadTruncation** &ndash; trunca o cabeçalho do texto, mostrando o final.
- **CharacterWrap** &ndash; quebra o texto em uma nova linha em um limite de caractere.
- **MiddleTruncation** &ndash; exibe o início e fim do texto, com a substituição do meio por reticências.
- **NoWrap** &ndash; não quebra o texto, exibindo somente texto como pode cabe em uma linha.
- **TailTruncation** &ndash; mostra o início do texto, truncando final.
- **WordWrap** &ndash; quebra o texto no limite de palavra.

## <a name="fonts"></a>Fontes

Para obter mais informações sobre como especificar fontes em uma `Label`, consulte [fontes](~/xamarin-forms/user-interface/text/fonts.md).

## <a name="colors"></a>Cores

`Label`s pode ser definido para usar uma cor de texto personalizado por meio de associável [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) propriedade.

Cuidado especial é necessário para garantir que as cores poderá ser usadas em cada plataforma. Como cada plataforma tem diferentes padrões de cores de plano de fundo e texto, você precisará ter cuidado para escolher um padrão que funciona em cada um.

Use o XAML a seguir para definir a cor do texto de um rótulo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo">
    <StackLayout Padding="5,10">
      <Label TextColor="#77d065" FontSize = "20" Text="This is a label." />
    </StackLayout>
</ContentPage>
```

O código c# equivalente é:

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();

        var layout = new StackLayout { Padding = new Thickness(5,10) };
        var label = new Label { Text="This is a label.", TextColor = Color.FromHex("#77d065"), FontSize = 20 };
        layout.Children.Add(label);
        this.Content = layout;
    }
}
```

As capturas de tela a seguir mostram o resultado da configuração de `TextColor` propriedade:

![](label-images/textcolor.png "Exemplo de TextColor de rótulo")

Para obter mais informações sobre as cores, consulte [cores](~/xamarin-forms/user-interface/colors.md).

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Texto formatado

Rótulos de expõem uma [ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText) propriedade que permite que a apresentação de texto com várias fontes e cores na mesma exibição.

O `FormattedText` propriedade é do tipo [ `FormattedString` ](xref:Xamarin.Forms.FormattedString), que consiste em um ou mais [ `Span` ](xref:Xamarin.Forms.Span) instâncias, definidas por meio de [ `Spans` ](xref:Xamarin.Forms.FormattedString.Spans) propriedade . Cada `Span` possui as seguintes propriedades:

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) – a cor do plano de fundo span.
- [`Font`](xref:Xamarin.Forms.Span.Font) – a fonte do texto no trecho.
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) – os atributos de fonte para o texto no trecho.
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) – a família de fontes ao qual pertence a fonte do texto no trecho.
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize) – o tamanho da fonte para o texto no trecho.
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) – a cor do texto no trecho. Essa propriedade está obsoleta e foi substituída pelo `TextColor` propriedade.
- [`Style`](xref:Xamarin.Forms.Span.Style) – o estilo a ser aplicado para o período.
- [`Text`](xref:Xamarin.Forms.Span.Text) – o texto da marca span.
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor) – a cor do texto no trecho.

O exemplo XAML a seguir demonstra uma `FormattedText` propriedade consiste em três `Span` instâncias:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo - XAML">
    <StackLayout Padding="5,10">
        ...
        <Label>
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Red Bold, " TextColor="Red" FontAttributes="Bold" />
                    <Span Text="default, " Style="{DynamicResource BodyStyle}" />
                    <Span Text="italic small." FontAttributes="Italic" FontSize="Small" />
                </FormattedString>
            </Label.FormattedText>
        </Label>
    </StackLayout>
</ContentPage>
```

O código c# equivalente é:

```csharp
public class LabelPageCode : ContentPage
{
    public LabelPageCode ()
    {
        var layout = new StackLayout{ Padding = new Thickness (5, 10) };
        ...
        var formattedString = new FormattedString ();
        formattedString.Spans.Add (new Span{ Text = "Red bold, ", ForegroundColor = Color.Red, FontAttributes = FontAttributes.Bold });
        formattedString.Spans.Add (new Span { Text = "default, ", Style = Device.Styles.BodyStyle });
        formattedString.Spans.Add (new Span { Text = "italic small.", FontAttributes = FontAttributes.Italic, FontSize =  Device.GetNamedSize(NamedSize.Small, typeof(Label)) });

        layout.Children.Add (new Label { FormattedText = formattedString });
        this.Content = layout;
    }
}
```

> [!NOTE]
> O [ `Text` ](xref:Xamarin.Forms.Span.Text) propriedade de um `Span` pode ser definido por meio da vinculação de dados. Para obter mais informações, consulte [associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md).

As capturas de tela a seguir mostram o resultado da configuração de `FormattedString` propriedade para três `Span` instâncias:

![](label-images/formattedtext.png "Exemplo de FormattedText do rótulo")

## <a name="styling-a-label"></a>Definir o estilo de um rótulo

As seções anteriores coberto configuração [ `Label` ](xref:Xamarin.Forms.Label) propriedades em uma base por instância. No entanto, os conjuntos de propriedades podem ser agrupados em um estilo que é aplicado consistentemente a um ou vários modos de exibição. Isso pode aumentar a legibilidade do código e fazer alterações de design mais fácil de implementar. Para obter mais informações, consulte [estilos](~/xamarin-forms/user-interface/text/styles.md).

## <a name="related-links"></a>Links relacionados

- [Criação de aplicativos móveis com xamarin. Forms, capítulo 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [Texto (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [API de rótulo](xref:Xamarin.Forms.Label)
