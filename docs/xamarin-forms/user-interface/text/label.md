---
title: Rótulo do xamarin. Forms
description: Este artigo explica como usar a classe de rótulo do xamarin. Forms para exibir o texto de único e multilinha em aplicativos.
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: ce602a84ea1024dc22298a3ec1567a9a34ad4a82
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995960"
---
# <a name="xamarinforms-label"></a>Rótulo do xamarin. Forms

_Exibir o texto no xamarin. Forms_

O `Label` exibição é usada para exibir o texto, único e de várias linha. Rótulos podem ter fontes personalizadas (famílias, tamanhos e opções) e texto colorido. Este artigo aborda os seguintes tópicos:

- **[Truncamento e disposição](#Truncation_and_Wrapping)**  &ndash; truncamento e opções para lidar com situações em que o texto pode não cabe em uma única linha de quebra.
- **[Fonte](#Font)**  &ndash; as opções de fonte.
- **[Cor](#Color)**  &ndash; as opções de cor do texto de rótulo.
- **[Texto formatado](#Formatted_Text)**  &ndash; opções para exibir o texto com vários embutido de formatos/estilos.

## <a name="styling-label"></a>Rótulo de estilo

As seções a seguir abordam a propriedades de configuração do `Label` manualmente em uma base por instância. Observe que os conjuntos de propriedades pode ser agrupado em um estilo que é aplicado consistentemente a um ou vários modos de exibição. Isso pode aumentar a legibilidade do código e fazer alterações de design mais fácil de implementar. Ver [estilos](~/xamarin-forms/user-interface/text/styles.md) para obter mais informações.

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>Truncamento e quebra automática

Rótulos podem ser definidos para lidar com o texto que não cabe em uma linha em uma das várias maneiras, expostas pelo `LineBreakMode` propriedade. [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) é uma enumeração das seguintes opções:

- **HeadTruncation** &ndash; trunca o cabeçalho do texto, mostrando o final.
- **CharacterWrap** &ndash; quebra o texto em uma nova linha em um limite de caractere.
- **MiddleTruncation** &ndash; exibe o início e fim do texto, com a substituição do meio por reticências.
- **NoWrap** &ndash; não quebra o texto, exibindo somente texto como pode cabe em uma linha.
- **TailTruncation** &ndash; mostra o início do texto, truncando final.
- **WordWrap** &ndash; quebra o texto no limite de palavra.

## <a name="font"></a>Fonte

Ver [trabalhando com fontes](~/xamarin-forms/user-interface/text/fonts.md) para obter mais informações.

## <a name="color"></a>Cor

`Label`s pode ser definido para usar uma cor de texto personalizado por meio de associável `TextColor` propriedade.

Cuidado especial é necessário para garantir que as cores poderá ser usadas em cada plataforma. Como cada plataforma tem diferentes padrões de cores de plano de fundo e texto, você precisará ter cuidado para escolher um padrão que funciona em cada um.

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

No XAML:

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

Rótulos de expõem um `FormattedText` propriedade que permite apresentar texto com várias fontes e cores na mesma exibição.

O `FormattedText` propriedade é do tipo [ `FormattedString` ](xref:Xamarin.Forms.FormattedString). Cadeias de caracteres formatadas são compostas de um ou mais `Span`s, cada um com as seguintes propriedades:

- **BackgroundColor** &ndash; pode ser usado para definir uma cor de plano de fundo, por exemplo, para conseguir um efeito de marca-texto.
- **FontAttributes** &ndash; pode ser definida como negrito, itálico ou nenhum.
- **FontFamily** &ndash; define a fonte a ser usado.
- **FontSize** &ndash; define o tamanho do texto.
- **ForegroundColor** &ndash; define a cor do texto.
- **Texto** &ndash; o texto a ser apresentado.

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

No XAML:

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

![](label-images/formattedtext.png "Exemplo de FormattedText do rótulo")


## <a name="related-links"></a>Links relacionados

- [Criação de aplicativos móveis com xamarin. Forms, capítulo 3](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [Texto (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [API de rótulo](xref:Xamarin.Forms.Label)
