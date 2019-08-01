---
title: Adicionando um reconhecedor de gestos de pinçagem
description: Este artigo explica como usar o gesto de pinçagem para aplicar zoom de forma interativa a uma imagem na localização da pinçagem.
ms.prod: xamarin
ms.assetid: 832F7810-F0CF-441A-B04A-3975F3FB8B29
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: be7a145e93aa4720b38921efc895ca3f3f33edb3
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656024"
---
# <a name="adding-a-pinch-gesture-recognizer"></a>Adicionando um reconhecedor de gestos de pinçagem

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pinchgesture)

_O gesto de pinçagem é usado para aplicar zoom de forma interativa e é implementado com a classe PinchGestureRecognizer. Um cenário comum para o gesto de pinçagem é aplicar zoom de forma interativa a uma imagem na localização da pinçagem. Isso é feito dimensionando o conteúdo do visor e é demonstrado neste artigo._

Para que seja possível ampliar um elemento da interface do usuário com um gesto de pinçagem, crie uma instância de [`PinchGestureRecognizer`](xref:Xamarin.Forms.PinchGestureRecognizer), manipule o evento [`PinchUpdated`](xref:Xamarin.Forms.PinchGestureRecognizer.PinchUpdated) e adicione o novo reconhecedor de gestos à coleção [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) no elemento de interface do usuário. O exemplo de código a seguir mostra um `PinchGestureRecognizer` anexado a um elemento [`Image`](xref:Xamarin.Forms.Image):

```csharp
var pinchGesture = new PinchGestureRecognizer();
pinchGesture.PinchUpdated += (s, e) => {
  // Handle the pinch
};
image.GestureRecognizers.Add(pinchGesture);
```

Isso também pode ser feito em XAML, conforme mostrado no exemplo de código a seguir:

```xaml
<Image Source="waterfront.jpg">
  <Image.GestureRecognizers>
    <PinchGestureRecognizer PinchUpdated="OnPinchUpdated" />
  </Image.GestureRecognizers>
</Image>
```

O código para o manipulador de eventos `OnPinchUpdated` é adicionado ao arquivo code-behind:

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  // Handle the pinch
}
```

## <a name="creating-a-pinchtozoom-container"></a>Criar um contêiner de PinchToZoom

Manipular o gesto de pinçagem para executar uma operação de aplicação de zoom requer alguns cálculos matemáticos para transformar a interface do usuário. Esta seção contém uma classe auxiliar generalizada para executar os cálculos, que pode ser usada para aplicar zoom interativamente a qualquer elemento da interface do usuário. O exemplo de código a seguir mostra a classe `PinchToZoomContainer`:

```csharp
public class PinchToZoomContainer : ContentView
{
  ...

  public PinchToZoomContainer ()
  {
    var pinchGesture = new PinchGestureRecognizer ();
    pinchGesture.PinchUpdated += OnPinchUpdated;
    GestureRecognizers.Add (pinchGesture);
  }

  void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
  {
    ...
  }
}
```

Essa classe pode ser encapsulada em torno de um elemento de interface do usuário para que o gesto de pinçagem aplique zoom ao elemento de interface do usuário encapsulado. O exemplo de código XAML a seguir mostra o `PinchToZoomContainer` encapsulando um elemento [`Image`](xref:Xamarin.Forms.Image):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:PinchGesture;assembly=PinchGesture"
             x:Class="PinchGesture.HomePage">
    <ContentPage.Content>
        <Grid Padding="20">
            <local:PinchToZoomContainer>
                <local:PinchToZoomContainer.Content>
                    <Image Source="waterfront.jpg" />
                </local:PinchToZoomContainer.Content>
            </local:PinchToZoomContainer>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

O exemplo de código a seguir mostra como o `PinchToZoomContainer` encapsula um elemento [`Image`](xref:Xamarin.Forms.Image) em uma página de C#:

```csharp
public class HomePageCS : ContentPage
{
  public HomePageCS ()
  {
    Content = new Grid {
      Padding = new Thickness (20),
      Children = {
        new PinchToZoomContainer {
          Content = new Image { Source = ImageSource.FromFile ("waterfront.jpg") }
        }
      }
    };
  }
}
```

Quando o elemento [`Image`](xref:Xamarin.Forms.Image) receber um gesto de pinçagem, a imagem exibida será ampliada ou reduzida. A aplicação de zoom é realizada pelo método `PinchZoomContainer.OnPinchUpdated`, que é mostrado no exemplo de código a seguir:

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  if (e.Status == GestureStatus.Started) {
    // Store the current scale factor applied to the wrapped user interface element,
    // and zero the components for the center point of the translate transform.
    startScale = Content.Scale;
    Content.AnchorX = 0;
    Content.AnchorY = 0;
  }
  if (e.Status == GestureStatus.Running) {
    // Calculate the scale factor to be applied.
    currentScale += (e.Scale - 1) * startScale;
    currentScale = Math.Max (1, currentScale);

    // The ScaleOrigin is in relative coordinates to the wrapped user interface element,
    // so get the X pixel coordinate.
    double renderedX = Content.X + xOffset;
    double deltaX = renderedX / Width;
    double deltaWidth = Width / (Content.Width * startScale);
    double originX = (e.ScaleOrigin.X - deltaX) * deltaWidth;

    // The ScaleOrigin is in relative coordinates to the wrapped user interface element,
    // so get the Y pixel coordinate.
    double renderedY = Content.Y + yOffset;
    double deltaY = renderedY / Height;
    double deltaHeight = Height / (Content.Height * startScale);
    double originY = (e.ScaleOrigin.Y - deltaY) * deltaHeight;

    // Calculate the transformed element pixel coordinates.
    double targetX = xOffset - (originX * Content.Width) * (currentScale - startScale);
    double targetY = yOffset - (originY * Content.Height) * (currentScale - startScale);

    // Apply translation based on the change in origin.
    Content.TranslationX = targetX.Clamp (-Content.Width * (currentScale - 1), 0);
    Content.TranslationY = targetY.Clamp (-Content.Height * (currentScale - 1), 0);

    // Apply scale factor.
    Content.Scale = currentScale;
  }
  if (e.Status == GestureStatus.Completed) {
    // Store the translation delta's of the wrapped user interface element.
    xOffset = Content.TranslationX;
    yOffset = Content.TranslationY;
  }
}
```

Esse método atualiza o nível de aplicação de zoom do elemento da interface do usuário encapsulado, com base no gesto de pinçagem do usuário. Isso é feito usando os valores das propriedades [`Scale`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Scale), [`ScaleOrigin`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.ScaleOrigin) e [`Status`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Status) da instância de [`PinchGestureUpdatedEventArgs`](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs) para calcular o fator de escala a ser aplicado na origem do gesto de pinçagem. O elemento do usuário encapsulado, então, é ampliado na origem do gesto de pinçagem definindo suas propriedades [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX), [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) e [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) como os valores calculados.

## <a name="related-links"></a>Links relacionados

- [PinchGesture (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pinchgesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PinchGestureRecognizer](xref:Xamarin.Forms.PinchGestureRecognizer)
