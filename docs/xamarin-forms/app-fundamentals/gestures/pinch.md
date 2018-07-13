---
title: Adicionando um reconhecedor de gestos de aperto
description: Este artigo explica como usar o gesto de pinçagem para executar o zoom interativa de uma imagem no local pinch.
ms.prod: xamarin
ms.assetid: 832F7810-F0CF-441A-B04A-3975F3FB8B29
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 37befdcd4ccbcd49e3cebda92d55ae6f70da2ad6
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998691"
---
# <a name="adding-a-pinch-gesture-recognizer"></a>Adicionando um reconhecedor de gestos de aperto

_O gesto de pinçagem é usado para executar o zoom interativa e é implementado com a classe PinchGestureRecognizer. Um cenário comum para o gesto de pinçagem é executar zoom interativa de uma imagem no local pinch. Isso é feito por meio do dimensionamento o conteúdo do visor e é demonstrado neste artigo._

## <a name="overview"></a>Visão geral

Para criar um elemento de interface do usuário pinçagem com o gesto de pinçagem, crie uma [ `PinchGestureRecognizer` ](xref:Xamarin.Forms.PinchGestureRecognizer) da instância, lidar com o [ `PinchUpdated` ](xref:Xamarin.Forms.PinchGestureRecognizer.PinchUpdated) evento, e adicione o reconhecedor de gestos de novo para o [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) coleta sobre o elemento de interface do usuário. O seguinte exemplo de código mostra uma `PinchGestureRecognizer` anexados a uma [ `Image` ](xref:Xamarin.Forms.Image) elemento:

```csharp
var pinchGesture = new PinchGestureRecognizer();
pinchGesture.PinchUpdated += (s, e) => {
  // Handle the pinch
};
image.GestureRecognizers.Add(pinchGesture);
```

Isso também pode ser obtido no XAML, conforme mostrado no exemplo de código a seguir:

```xaml
<Image Source="waterfront.jpg">
  <Image.GestureRecognizers>
    <PinchGestureRecognizer PinchUpdated="OnPinchUpdated" />
  </Image.GestureRecognizers>
</Image>
```

O código para o `OnPinchUpdated` manipulador de eventos é adicionado ao arquivo code-behind:

```csharp
void OnPinchUpdated (object sender, PinchGestureUpdatedEventArgs e)
{
  // Handle the pinch
}
```

## <a name="creating-a-pinchtozoom-container"></a>Criar um contêiner de PinchToZoom

Tratando o gesto de pinçagem para executar uma operação de zoom requer um pouco de matemática para transformar a interface do usuário. Esta seção contém uma classe auxiliar generalizado para executar a matemática, que pode ser usada interativamente ampliar qualquer elemento de interface do usuário. O exemplo de código a seguir mostra a classe `PinchToZoomContainer`:

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

Essa classe pode ser encapsulada em torno de um elemento de interface do usuário para que o gesto de pinçagem ampliará o elemento de interface de usuário encapsulado. Mostra o exemplo de código XAML abaixo de `PinchToZoomContainer` encapsulando um [ `Image` ](xref:Xamarin.Forms.Image) elemento:

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

O seguinte exemplo de código mostra como o `PinchToZoomContainer` encapsula uma [ `Image` ](xref:Xamarin.Forms.Image) elemento em uma página do c#:

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

Quando o [ `Image` ](xref:Xamarin.Forms.Image) elemento recebe um gesto de pinçagem, a imagem exibida será ser ampliado ou recusar. O zoom é executado pelo `PinchZoomContainer.OnPinchUpdated` método, que é mostrado no exemplo de código a seguir:

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

Esse método atualizará o nível de zoom do elemento de interface do usuário encapsulado com base em um gesto de pinçagem do usuário. Isso é feito usando os valores da [ `Scale` ](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Scale), [ `ScaleOrigin` ](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.ScaleOrigin) e [ `Status` ](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs.Status) propriedades do [ `PinchGestureUpdatedEventArgs` ](xref:Xamarin.Forms.PinchGestureUpdatedEventArgs) instância para calcular o fator de escala a ser aplicado na origem do gesto pinch. O elemento de usuário encapsulado é ampliado na origem do gesto pinch definindo sua [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX), [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY), e [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propriedades para os valores calculados.

## <a name="summary"></a>Resumo

O gesto de pinçagem é usado para executar o zoom interativa e é implementado com o [ `PinchGestureRecognizer` ](xref:Xamarin.Forms.PinchGestureRecognizer) classe.


## <a name="related-links"></a>Links relacionados

- [PinchGesture (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PinchGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PinchGestureRecognizer](xref:Xamarin.Forms.PinchGestureRecognizer)
