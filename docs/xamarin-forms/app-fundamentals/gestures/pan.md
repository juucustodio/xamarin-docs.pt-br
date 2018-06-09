---
title: Adicionando um reconhecedor de gestos panorâmica
description: Este artigo explica como usar um gesto de panorâmica para horizontalmente e verticalmente, arraste uma imagem, para que todo o conteúdo de imagem podem ser exibidos quando ele está sendo exibido em um visor menor do que as dimensões da imagem.
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: d3e4dfc57678ff75fb8f9761360748d94aeefcc2
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35239978"
---
# <a name="adding-a-pan-gesture-recognizer"></a>Adicionando um reconhecedor de gestos panorâmica

_O gesto de Panorâmica é usado para detectar arrastando e é implementado com a classe PanGestureRecognizer. Um cenário comum para o movimento panorâmico é horizontal e verticalmente, arraste uma imagem, para que todo o conteúdo de imagem podem ser exibidos quando ele está sendo exibido em um visor menor do que as dimensões da imagem. Isso é feito movendo a imagem dentro do visor e é demonstrado neste artigo._

## <a name="overview"></a>Visão geral

Para criar um elemento de interface do usuário arrastável com o gesto de panorâmica, crie um [ `PanGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PanGestureRecognizer/) da instância, tratar o [ `PanUpdated` ](https://developer.xamarin.com/api/event/Xamarin.Forms.PanGestureRecognizer.PanUpdated/) evento, e adicione o novo reconhecedor de gestos para o [ `GestureRecognizers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.GestureRecognizers/) coleta sobre o elemento de interface do usuário. O seguinte exemplo de código mostra uma `PanGestureRecognizer` anexado a um [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento:

```csharp
var panGesture = new PanGestureRecognizer();
panGesture.PanUpdated += (s, e) => {
  // Handle the pan
};
image.GestureRecognizers.Add(panGesture);
```

Isso também pode ser obtido em XAML, conforme mostrado no exemplo de código a seguir:

```xaml
<Image Source="MonoMonkey.jpg">
  <Image.GestureRecognizers>
    <PanGestureRecognizer PanUpdated="OnPanUpdated" />
  </Image.GestureRecognizers>
</Image>
```

O código para o `OnPanUpdated` manipulador de eventos é adicionado para o arquivo code-behind:

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  // Handle the pan
}
```

> [!NOTE]
> Panorâmica correto no Android exige o [pacote do NuGet 2.1.0-pre1 xamarin. Forms](https://www.nuget.org/packages/Xamarin.Forms/2.1.0.6501-pre1) no mínimo.

## <a name="creating-a-pan-container"></a>Criando um contêiner de Panorâmica

Esta seção contém uma classe auxiliar generalizado que executa a panorâmica de forma livre, que normalmente é adequada para navegar dentro de imagens ou mapas. Manipular o gesto de panorâmica para executar uma operação de arrastar requer uma matemática para transformar a interface do usuário. Este matemática é usada para arrastar apenas dentro dos limites do elemento de interface do usuário encapsulado. O exemplo de código a seguir mostra a classe `PanContainer`:

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

Essa classe pode ser encapsulada com um elemento de interface do usuário para que o gesto de panorâmica levarão o elemento de interface do usuário encapsulado. O exemplo do código XAML seguinte mostra o `PanContainer` encapsulamento um [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento:

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

O seguinte exemplo de código mostra como o `PanContainer` encapsula uma [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento em uma página c#:

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

Nos dois exemplos, o [ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) e [ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) propriedades são definidas como valores de largura e altura da imagem que está sendo exibida.

Quando o [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) elemento recebe um gesto de panorâmica, a imagem exibida será arrastada. A operação de arrastar é executada pelo `PanContainer.OnPanUpdated` método, que é mostrado no exemplo de código a seguir:

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

Este método atualiza o conteúdo visível do elemento de interface do usuário encapsulado, com base em gesto de panorâmica do usuário. Isso é feito usando os valores da [ `TotalX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PanUpdatedEventArgs.TotalX/) e [ `TotalY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PanUpdatedEventArgs.TotalY/) propriedades do [ `PanUpdatedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PanUpdatedEventArgs/) instância para calcular a direção e distância da panorâmica. O `App.ScreenWidth` e `App.ScreenHeight` propriedades fornecem a altura e largura do visor e são definidas para a largura da tela e valores de altura da tela do dispositivo pelos respectivos projetos específicos de plataforma. O elemento de quebra automática de usuário, em seguida, é arrastado definindo seu [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/) e [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/) propriedades para os valores calculados.

Quando o movimento panorâmico conteúdo em um elemento que não ocupa a tela inteira, a altura e largura do visor podem ser obtidos do elemento [ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/) e [ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/) propriedades.

> [!NOTE]
> Exibindo imagens de alta resolução pode aumentar significativamente a superfície de memória do aplicativo. Portanto, eles só devem ser criados quando necessário e deve ser liberado assim que o aplicativo não exige mais-los. Para saber mais, consulte [Otimizar recursos de imagem](~/xamarin-forms/deploy-test/performance.md#optimizeimages).

## <a name="summary"></a>Resumo

O gesto de Panorâmica é usado para detectar arrastando e é implementado com o [ `PanGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PanGestureRecognizer/) classe.



## <a name="related-links"></a>Links relacionados

- [PanGesture (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PanGesture/)
- [GestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/)
- [PanGestureRecognizer](https://developer.xamarin.com/api/type/Xamarin.Forms.PanGestureRecognizer/)
