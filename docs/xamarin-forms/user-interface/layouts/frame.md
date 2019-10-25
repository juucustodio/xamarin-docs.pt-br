---
title: Quadro do Xamarin. Forms
description: A classe de quadro Xamarin. Forms é um layout usado para encapsular uma exibição ou um layout com uma borda que pode ser configurada com cores, sombra e outras opções.
ms.prod: xamarin
ms.assetId: 4E074714-0928-41C8-A468-B60E23236A8C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/06/2019
ms.openlocfilehash: 619b29a9d65594b1badd805c3361fe1a174d7174
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "69976498"
---
# <a name="xamarinforms-frame"></a>Quadro do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/)

A classe [`Frame`](xref:Xamarin.Forms.Frame) Xamarin. Forms é um layout usado para encapsular uma exibição com uma borda que pode ser configurada com cores, sombra e outras opções. Quadros geralmente são usados para criar bordas em torno de controles, mas podem ser usados para criar uma interface do usuário mais complexa. Para obter mais informações, consulte [uso avançado de quadro](#advanced-frame-usage).

A captura de tela a seguir mostra `Frame` controles no iOS e no Android:

[![exemplos de quadro no iOS e no Android](frame-images/frame-cropped.png)](frame-images/frame-full.png#lightbox "Exemplos de quadro no iOS e no Android")

A classe `Frame` define as seguintes propriedades:

* [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor) é um valor de `Color` que determina a cor da borda de `Frame`.
* [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius) é um valor de `float` que determina o raio arredondado do canto.
* [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) é um valor `bool` que determina se o quadro tem uma sombra de projeção.

Essas propriedades têm o suporte de objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , o que significa que o `Frame` pode ser o destino de associações de dados.

> [!NOTE]
> O comportamento da propriedade `HasShadow` é dependente da plataforma. O valor padrão é `true` em todas as plataformas. No entanto, nas sombras suspensas UWP não são renderizadas. As sombras de soltar são renderizadas no Android e no iOS, mas as sombras de soltar no iOS são mais escuras e ocupam mais espaço.

## <a name="create-a-frame"></a>Criar um quadro

Um `Frame` pode ser instanciado em XAML. O objeto de `Frame` padrão tem um plano de fundo branco, uma sombra e nenhuma borda. Um objeto `Frame` normalmente encapsula outro controle. O exemplo a seguir mostra um padrão `Frame` encapsulando um objeto `Label`:

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

`Frame` objetos podem ser personalizados com cantos arredondados, bordas coloridas e sombras, definindo as propriedades no XAML. O exemplo a seguir mostra um objeto de `Frame` personalizado:

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

A classe `Frame` herda de `ContentView`, o que significa que ela pode conter qualquer tipo de objeto `View`, incluindo objetos `Layout`. Essa capacidade permite que o `Frame` seja usado para criar objetos de interface do usuário complexos, como cartões.

### <a name="create-a-card-with-a-frame"></a>Criar um cartão com um quadro

A combinação de um objeto `Frame` com um objeto `Layout`, como um objeto `StackLayout`, permite a criação de uma interface do usuário mais complexa. A captura de tela a seguir mostra um cartão de exemplo, criado usando um objeto `Frame`:

[![captura de tela de um cartão criado com um quadro](frame-images/frame-card-cropped.png)](frame-images/frame-full.png#lightbox "Captura de tela de um cartão criado com um quadro")

O XAML a seguir mostra como criar um cartão com a classe `Frame`:

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

A propriedade `CornerRadius` do controle de `Frame` pode ser usada para criar uma imagem de círculo. A captura de tela a seguir mostra um exemplo de uma imagem arredondada, criada usando um objeto `Frame`:

[![ "captura de tela de uma imagem de círculo criada com um quadro"](frame-images/circle-image-cropped.png)](frame-images/frame-full.png#lightbox "Captura de tela de uma imagem de círculo criada com um quadro")

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
> Os cantos arredondados têm um comportamento ligeiramente diferente entre as plataformas. A `Margin` do objeto de `Image` deve ser metade da diferença entre a largura da imagem e a largura do quadro pai e deve ser negativa para centralizar a imagem uniformemente dentro do objeto `Frame`. No entanto, a largura e a altura solicitadas não são garantidas, portanto, as propriedades `Margin`, `HeightRequest` e `WidthRequest` podem precisar ser alteradas com base no tamanho da imagem e em outras opções de layout.

## <a name="related-links"></a>Links relacionados

* [Demonstrações de quadros](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/)
* [Imagens no Xamarin. Forms](~/xamarin-forms/user-interface/images.md)
