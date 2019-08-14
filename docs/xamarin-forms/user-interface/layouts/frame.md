---
title: Quadro do Xamarin. Forms
description: A classe de quadro Xamarin. Forms é um layout usado para encapsular uma exibição ou um layout com uma borda que pode ser configurada com cores, sombra e outras opções.
ms.prod: xamarin
ms.assetId: 4E074714-0928-41C8-A468-B60E23236A8C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/06/2019
ms.openlocfilehash: 329edf455a4778efa5f6fbd1ec88cfb274c5cb49
ms.sourcegitcommit: 41a029c69925e3a9d2de883751ebfd649e8747cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68984418"
---
# <a name="xamarinforms-frame"></a>Quadro do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-frame/)

A classe Xamarin. [`Frame`](xref:Xamarin.Forms.Frame) Forms é um layout usado para encapsular uma exibição com uma borda que pode ser configurada com cores, sombra e outras opções. Quadros geralmente são usados para criar bordas em torno de controles, mas podem ser usados para criar uma interface do usuário mais complexa. Para obter mais informações, consulte [uso avançado de quadro](#advanced-frame-usage).

A captura de tela `Frame` a seguir mostra os controles no Ios e no Android:

Exemplos de quadros ["exemplos de quadros em Ios e Android" no Ios e no Android ![](frame-images/frame-cropped.png)](frame-images/frame-full.png#lightbox "")

A `Frame` classe define as seguintes propriedades:

* [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor)é um `Color` valor que determina a cor `Frame` da borda.
* [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius)é um `float` valor que determina o raio arredondado do canto.
* [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow)é um `bool` valor que determina se o quadro tem uma sombra de projeção.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa `Frame` que o pode ser o destino de associações de dados.

> [!NOTE]
> O `HasShadow` comportamento da propriedade é dependente da plataforma. O valor padrão é `true` em todas as plataformas. No entanto, nas sombras suspensas UWP não são renderizadas. As sombras de soltar são renderizadas no Android e no iOS, mas as sombras de soltar no iOS são mais escuras e ocupam mais espaço.

## <a name="create-a-frame"></a>Criar um quadro

Um `Frame` pode ser instanciado em XAML. O objeto `Frame` padrão tem um plano de fundo branco, uma sombra e nenhuma borda. Um `Frame` objeto normalmente encapsula outro controle. O exemplo a seguir mostra um `Frame` padrão encapsulando um `Label` objeto:

```xaml
<Frame>
  <Label Text="Example" />
</Frame>
```

Um `Frame` também pode ser criado no código:

```csharp
Frame defaultFrame = new Frame
{
    Content = new Label { Text = "Example" }
};
```

`Frame`os objetos podem ser personalizados com cantos arredondados, bordas coloridas e sombras projetadas definindo as propriedades no XAML. O exemplo a seguir mostra um `Frame` objeto personalizado:

```xaml
<Frame BorderColor="Orange"
       CornerRadius="10"
       HasShadow="True">
  <Label Text="Example" />
</Frame>
```

Essas propriedades de instância também podem ser definidas no código:

```csharp
Frame frame = new Frame
{
    BorderColor = Color.Orange,
    CornerRadius = 10,
    HasShadow = true,
    Content = new Label { Text = "Example" }
};
```

## <a name="advanced-frame-usage"></a>Uso avançado de quadros

A `Frame` classe é herdada de `ContentView`, o que significa que ela pode `View` conter qualquer `Layout` tipo de objeto, incluindo objetos. Essa capacidade permite que `Frame` o seja usado para criar objetos de interface do usuário complexos, como cartões.

### <a name="create-a-card-with-a-frame"></a>Criar um cartão com um quadro

A combinação `Frame` de um objeto `Layout` com um objeto, `StackLayout` como um objeto, permite a criação de uma interface do usuário mais complexa. A captura de tela a seguir mostra um cartão de exemplo `Frame` , criado usando um objeto:

Captura de tela ["captura de tela de um cartão criado com um quadro" de um cartão criado com um quadro ![](frame-images/frame-card-cropped.png)](frame-images/frame-full.png#lightbox "")

O XAML a seguir mostra como criar um cartão com a `Frame` classe:

```xaml
<Frame BorderColor="Gray"
       CornerRadius="5"
       Padding="8">
  <StackLayout>
    <Label Text="Card Example"
           FontSize="Medium"
           FontAttributes="Bold" />
    <BoxView Color="Gray"
             HeightRequest="2"
             HorizontalOptions="Fill" />
    <Label Text="Frames can wrap more complex layouts to create more complex UI components, such as this card!"/>
  </StackLayout>
</Frame>
```

Um cartão também pode ser criado no código:

```csharp
Frame cardFrame = new Frame
{
    BorderColor = Color.Gray,
    CornerRadius = 5,
    Padding = 8,
    Content = new StackLayout
    {
        Children =
        {
            new Label
            {
                Text = "Card Example",
                FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(Label)),
                FontAttributes = FontAttributes.Bold
            },
            new BoxView
            {
                Color = Color.Gray,
                HeightRequest = 2,
                HorizontalOptions = LayoutOptions.Fill
            },
            new Label
            {
                Text = "Frames can wrap more complex layouts to create more complex UI components, such as this card!"
            }
        }
    }
};
```

### <a name="round-elements"></a>Elementos arredondados

A `CornerRadius` propriedade`Frame` do controle pode ser usada para criar uma imagem de círculo. A captura de tela a seguir mostra um exemplo de uma imagem arredondada, criada usando um `Frame` objeto:

["Captura de tela de uma imagem de círculo criada com um quadro" captura de tela de uma imagem de círculo criada com um quadro ![](frame-images/circle-image-cropped.png)](frame-images/frame-full.png#lightbox "")

O XAML a seguir mostra como criar uma imagem de círculo em XAML:

```xaml
<Frame Margin="10"
       BorderColor="Black"
       CornerRadius="50"
       HeightRequest="60"
       WidthRequest="60"
       IsClippedToBounds="True"
       HorizontalOptions="Center"
       VerticalOptions="Center">
  <Image Source="outdoors.jpg"
         Aspect="AspectFill"
         Margin="-20"
         HeightRequest="100"
         WidthRequest="100" />
</Frame>
```

Uma imagem de círculo também pode ser criada no código:

```csharp
Frame circleImageFrame = new Frame
{
    Margin = 10,
    BorderColor = Color.Black,
    CornerRadius = 50,
    HeightRequest = 60,
    WidthRequest = 60,
    IsClippedToBounds = true,
    HorizontalOptions = LayoutOptions.Center,
    VerticalOptions = LayoutOptions.Center,
    Content = new Image
    {
        Source = ImageSource.FromFile("outdoors.jpg"),
        Aspect = Aspect.AspectFill,
        Margin = -20,
        HeightRequest = 100,
        WidthRequest = 100
    }
};
```

A imagem em **áreas de. jpg** deve ser adicionada a cada projeto de plataforma e como isso é feito varia de acordo com a plataforma. Para obter mais informações, consulte [imagens no Xamarin. Forms](~/xamarin-forms/user-interface/images.md).

> [!NOTE]
> Os cantos arredondados têm um comportamento ligeiramente diferente entre as plataformas. O `Image` `Frame` objeto deve ser metade da diferença entre a largura da imagem e a largura do quadro pai e deve ser negativo para centralizar a imagem uniformemente dentro do objeto. `Margin` No entanto, a largura e a altura solicitadas não são `Margin`Garantidas `HeightRequest` , `WidthRequest` portanto, as propriedades e podem precisar ser alteradas com base no tamanho da imagem e em outras opções de layout.

## <a name="related-links"></a>Links relacionados

* [Demonstrações de quadros](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-frame/)
* [Imagens no Xamarin. Forms](~/xamarin-forms/user-interface/images.md)
