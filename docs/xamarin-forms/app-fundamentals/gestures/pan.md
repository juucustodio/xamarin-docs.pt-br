---
title: Adicionando um reconhecedor de gesto de panorâmica
description: Este artigo explica como usar um gesto de panorâmica para percorrer uma imagem horizontal e verticalmente, para que todo o conteúdo da imagem pode ser exibido quando ela estiver sendo exibida em um visor menor do que as dimensões da imagem.
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 73e312a1af56091a7e579d3fcbcea810ee0efb1e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "68820970"
---
# <a name="adding-a-pan-gesture-recognizer"></a>Adicionando um reconhecedor de gesto de panorâmica

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)

_O gesto pan é usado para detectar o movimento dos dedos ao redor da `PanGestureRecognizer` tela e aplicar esse movimento ao conteúdo, e é implementado com a classe. Um cenário comum para o gesto da panela é panorâmica horizontal e verticalmente de uma imagem, de modo que todo o conteúdo da imagem possa ser visualizado quando ele está sendo exibido em uma porta de visão menor do que as dimensões da imagem. Isso é feito movendo a imagem dentro do viewport, e é demonstrado neste artigo._

Para tornar um elemento de interface de usuário [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) movível [`PanUpdated`](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated) com o gesto da panela, [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) crie uma instância, manuseie o evento e adicione o novo reconhecimento de gestos à coleção no elemento interface do usuário. O exemplo de `PanGestureRecognizer` código a [`Image`](xref:Xamarin.Forms.Image) seguir mostra um anexo a um elemento:

```csharp
var panGesture = new PanGestureRecognizer();
panGesture.PanUpdated += (s, e) => {
  // Handle the pan
};
image.GestureRecognizers.Add(panGesture);
```

Isso também pode ser feito em XAML, conforme mostrado no exemplo de código a seguir:

```xaml
<Image Source="MonoMonkey.jpg">
  <Image.GestureRecognizers>
    <PanGestureRecognizer PanUpdated="OnPanUpdated" />
  </Image.GestureRecognizers>
</Image>
```

O código para o manipulador de eventos `OnPanUpdated` é adicionado ao arquivo code-behind:

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  // Handle the pan
}
```

## <a name="creating-a-pan-container"></a>Criando um contêiner de panorâmica

Esta seção contém uma classe auxiliar generalizada que executa a panorâmica de forma livre, o que normalmente é adequado para navegar em imagens ou mapas. Manipular o gesto de panorâmica para executar esta operação requer alguns cálculos matemáticos para transformar a interface do usuário. Esses cálculos são usados para percorrer apenas os limites do elemento de interface do usuário encapsulado. O exemplo de código a seguir mostra a classe `PanContainer`:

```csharp
public class PanContainer : ContentView
{
  double x, y;

  public PanContainer ()
  {
    // Set PanGestureRecognizer.TouchPoints to control the
    // number of touch points needed to pan
    var panGesture = new PanGestureRecognizer ();
    panGesture.PanUpdated += OnPanUpdated;
    GestureRecognizers.Add (panGesture);
  }

  void OnPanUpdated (object sender, PanUpdatedEventArgs e)
  {
    ...
  }
}
```

Essa classe pode ser encapsulada em torno de um elemento de interface do usuário para que o gesto percorra o elemento de interface do usuário encapsulado. O exemplo de código XAML a seguir mostra o `PanContainer` embrulho de um [`Image`](xref:Xamarin.Forms.Image) elemento:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:PanGesture"
             x:Class="PanGesture.HomePage">
    <ContentPage.Content>
        <AbsoluteLayout>
            <local:PanContainer>
                <Image Source="MonoMonkey.jpg" WidthRequest="1024" HeightRequest="768" />
            </local:PanContainer>
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

O exemplo de código `PanContainer` a [`Image`](xref:Xamarin.Forms.Image) seguir mostra como o elemento envolve um elemento em uma página C#:

```csharp
public class HomePageCS : ContentPage
{
  public HomePageCS ()
  {
    Content = new AbsoluteLayout {
      Padding = new Thickness (20),
      Children = {
        new PanContainer {
          Content = new Image {
            Source = ImageSource.FromFile ("MonoMonkey.jpg"),
            WidthRequest = 1024,
            HeightRequest = 768
          }
        }
      }
    };
  }
}
```

Em ambos os [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) exemplos, as propriedades e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) propriedades são definidas para os valores de largura e altura da imagem que está sendo exibida.

Quando [`Image`](xref:Xamarin.Forms.Image) o elemento recebe um gesto de panela, a imagem exibida será garimpada. A panorâmica é realizada pelo método `PanContainer.OnPanUpdated`, que é mostrado no exemplo de código a seguir:

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  switch (e.StatusType) {
  case GestureStatus.Running:
    // Translate and ensure we don't pan beyond the wrapped user interface element bounds.
    Content.TranslationX =
      Math.Max (Math.Min (0, x + e.TotalX), -Math.Abs (Content.Width - App.ScreenWidth));
    Content.TranslationY =
      Math.Max (Math.Min (0, y + e.TotalY), -Math.Abs (Content.Height - App.ScreenHeight));
    break;

  case GestureStatus.Completed:
    // Store the translation applied during the pan
    x = Content.TranslationX;
    y = Content.TranslationY;
    break;
  }
}
```

Esse método atualiza o conteúdo visível do elemento de interface do usuário encapsulado, com base no gesto de panorâmica do usuário. Isso é conseguido usando os [`TotalX`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX) [`TotalY`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY) valores [`PanUpdatedEventArgs`](xref:Xamarin.Forms.PanUpdatedEventArgs) e propriedades da instância para calcular a direção e a distância da panela. As propriedades `App.ScreenWidth` e `App.ScreenHeight` fornecem a altura e largura do visor e são definidas com os valores da largura e da altura da tela do dispositivo pelos respectivos projetos específicos da plataforma. O elemento do usuário embrulhado é [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) então garimpado definindo suas propriedades para os valores calculados.

Ao panorâmica de conteúdo em um elemento que não ocupa a tela cheia, a altura [`Height`](xref:Xamarin.Forms.VisualElement.Height) e [`Width`](xref:Xamarin.Forms.VisualElement.Width) a largura da porta de exibição podem ser obtidas a partir do elemento e propriedades.

> [!NOTE]
> Exibir imagens de alta resolução pode aumentar significativamente o volume de memória de um aplicativo. Portanto, elas só devem ser criadas quando necessário e devem ser liberadas assim que o aplicativo não precisar mais delas. Para saber mais, consulte [Otimizar recursos de imagem](~/xamarin-forms/deploy-test/performance.md#optimize-image-resources).

## <a name="related-links"></a>Links relacionados

- [PanGesture (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)
