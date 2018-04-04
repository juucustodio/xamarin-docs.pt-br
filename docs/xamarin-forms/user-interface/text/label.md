---
title: Rotular
description: Exibir o texto no xamarin. Forms
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: c1056626b336dd9b6ce265ab693ceed2a24eae0f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="label"></a>Rotular

_Exibir o texto no xamarin. Forms_

O `Label` exibição é usada para exibir o texto, a único e multilinha. Rótulos podem ter fontes personalizadas (famílias, tamanhos e opções) e cor do texto. Este artigo aborda os seguintes tópicos:

- **[O truncamento e encapsulamento](#Truncation_and_Wrapping)**  &ndash; truncamento e opções para lidar com situações em que o texto não couber em uma linha de quebra.
- **[Fonte](#Font)**  &ndash; opções de fonte.
- **[Cor](#Color)**  &ndash; opções de cor do texto de rótulo.
- **[Texto formatado](#Formatted_Text)**  &ndash; opções de exibição de texto com vários embutido de formatos/estilos.

## <a name="styling-label"></a>Estilo de rótulo

As seções a seguir abrangem propriedades de configuração do `Label` manualmente em uma base por instância. Observe que conjuntos de propriedades pode ser agrupado em um estilo consistente é aplicado a um ou vários modos de exibição. Isso pode aumentar a legibilidade do código e fazer alterações de design mais fácil de implementar. Consulte [estilos](~/xamarin-forms/user-interface/text/styles.md) para obter mais informações.

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>O truncamento e encapsulamento

Rótulos podem ser definidos para tratar o texto que não cabem em uma linha em uma das várias maneiras, expostas pelo `LineBreakMode` propriedade. [`LineBreakMode`](https://developer.xamarin.com/api/type/Xamarin.Forms.LineBreakMode/) é uma enumeração das seguintes opções:

- **HeadTruncation** &ndash; trunca o cabeçalho do texto, mostrando o final.
- **CharacterWrap** &ndash; quebra o texto em uma nova linha em um limite de caractere.
- **MiddleTruncation** &ndash; exibe o início e fim do texto, com a substituição intermediária por reticências.
- **Sem quebra automática** &ndash; não quebra o texto, exibindo somente texto que pode cabe em uma única linha.
- **TailTruncation** &ndash; mostra o início do texto, truncando final.
- **WordWrap** &ndash; quebra o texto no limite do word.

## <a name="font"></a>Fonte

Consulte [trabalhando com fontes](~/xamarin-forms/user-interface/text/fonts.md) para obter mais informações.

## <a name="color"></a>Cor

`Label`s pode ser definido para usar uma cor de texto personalizado por meio de associável `TextColor` propriedade.

Cuidado especial é necessário para assegurar que as cores poderá ser usadas em cada plataforma. Como cada plataforma tem diferentes padrões de cores de plano de fundo e texto, você precisará ter cuidado para escolher um padrão que funciona em cada um.

Use o código a seguir para definir a cor do texto de um rótulo:

No código:

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var label = new Label { Text="This is a label.", TextColor = Color.FromHex("#77d065"), FontSize = 20 };
        layout.Children.Add(label);
    }
}
```

Em XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.LabelPage"
Title="Label Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
      <Label TextColor="#77d065" FontSize = "20" Text="This is a label." />
    </StackLayout>
  </ContentPage.Content>
</ContentPage>
```

![](label-images/textcolor.png "Exemplo de TextColor de rótulo")

<a name="Formatted_Text" />

## <a name="formatted-text"></a>Texto formatado

Rótulos de expõem um `FormattedText` propriedade que permite que você apresente o texto com várias fontes e cores na mesma exibição.

O `FormattedText` é de propriedade do tipo [ `FormattedString` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FormattedString/). Cadeias de caracteres formatadas são compostas de um ou mais `Span`s, cada um com as seguintes propriedades:

- **BackgroundColor** &ndash; pode ser usado para definir uma cor de plano de fundo, por exemplo, para obter um efeito de marca-texto.
- **FontAttributes** &ndash; pode ser definida como negrito, itálico ou nenhum deles.
- **FontFamily** &ndash; define a fonte a ser usada.
- **FontSize** &ndash; define o tamanho do texto.
- **ForegroundColor** &ndash; define a cor do texto.
- **Texto** &ndash; o texto a ser exibida.

O código c# a seguir demonstra um rótulo em que a primeira palavra está em negrito e a última palavra é vermelha:

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
    var label = new Label { FontSize = 20 };
    var s = new FormattedString ();
    s.Spans.Add (new Span{ Text = "Red Bold", FontAttributes = FontAttributes.Bold });
    s.Spans.Add (new Span{ Text = "Default" });
    s.Spans.Add (new Span{ Text = "italic small", FontSize =  Device.GetNamedSize(NamedSize.Small, typeof(Label)), FontAttributes = FontAttributes.Italic});
    label.FormattedText = s;
        layout.Children.Add(label);
    }
}
```

Em XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.LabelPage"
Title="Label Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
      <Label FontSize=20>
        <Label.FormattedText>
          <FormattedString>
            <Span Text="Red Bold" ForegroundColor="Red" FontAttributes="Bold" />
            <Span Text="Default" />
            <Span Text="italic small" FontAttributes="Italic" FontSize="Small" />
          </FormattedString>
        </Label.FormattedText>
      </Label>
    </StackLayout>
  </ContentPage.Content>
</ContentPage>
```

![](label-images/formattedtext.png "Exemplo de FormattedText de rótulo")


## <a name="related-links"></a>Links relacionados

- [Criando aplicativos móveis com o xamarin. Forms, capítulo 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [Texto (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [API de rótulo](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)
