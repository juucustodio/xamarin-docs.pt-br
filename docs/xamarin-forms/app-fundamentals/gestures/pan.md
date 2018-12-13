---
title: Adicionando um reconhecedor de gesto de panorâmica
description: Este artigo explica como usar um gesto de panorâmica para percorrer uma imagem horizontal e verticalmente, para que todo o conteúdo da imagem pode ser exibido quando ela estiver sendo exibida em um visor menor do que as dimensões da imagem.
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 59e9f4c61bda86faa5a55d70ef91411adb14da6d
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2018
ms.locfileid: "38996800"
---
# <a name="adding-a-pan-gesture-recognizer"></a>Adicionando um reconhecedor de gesto de panorâmica

_O gesto de panorâmica é usado para detectar a movimentação dos dedos na tela e aplicar essa movimentação ao conteúdo e é implementado com a classe `PanGestureRecognizer`. Um cenário comum para o gesto de panorâmica é percorrer uma imagem horizontal e verticalmente, para que todo o conteúdo da imagem pode ser exibido quando ela estiver sendo exibida em um visor menor do que as dimensões da imagem. Isso é feito movendo a imagem dentro do visor e é demonstrado neste artigo._

Para que seja possível mover um elemento da interface do usuário com um gesto de panorâmica, crie uma instância de [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer), manipule o evento [`PanUpdated`](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated) e adicione o novo reconhecedor de gestos à coleção [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) no elemento de interface do usuário. O exemplo de código a seguir mostra um `PanGestureRecognizer` anexado a um elemento [`Image`](xref:Xamarin.Forms.Image):

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

> [!NOTE]
> Para usar a panorâmica corretamente no Android, é necessário ter no mínimo o [Pacote NuGet 2.1.0-pre1 do Xamarin.Forms](https://www.nuget.org/packages/Xamarin.Forms/2.1.0.6501-pre1).

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

Essa classe pode ser encapsulada em torno de um elemento de interface do usuário para que o gesto percorra o elemento de interface do usuário encapsulado. O exemplo de código XAML a seguir mostra o `PanContainer` encapsulando um elemento [`Image`](xref:Xamarin.Forms.Image):

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

O exemplo de código a seguir mostra como o `PanContainer` encapsula um elemento [`Image`](xref:Xamarin.Forms.Image) em uma página de C#:

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

Nos dois exemplos, as propriedades [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) são definidas como os valores de largura e altura da imagem que está sendo exibida.

Quando o elemento [`Image`](xref:Xamarin.Forms.Image) receber um gesto de panorâmica, a imagem exibida será deslocada. A panorâmica é realizada pelo método `PanContainer.OnPanUpdated`, que é mostrado no exemplo de código a seguir:

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

Esse método atualiza o conteúdo visível do elemento de interface do usuário encapsulado, com base no gesto de panorâmica do usuário. Isso é feito usando os valores das propriedades [`TotalX`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX) e [`TotalY`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY) da instância de [`PanUpdatedEventArgs`](xref:Xamarin.Forms.PanUpdatedEventArgs) para calcular a direção e a distância da panorâmica. As propriedades `App.ScreenWidth` e `App.ScreenHeight` fornecem a altura e largura do visor e são definidas com os valores da largura e da altura da tela do dispositivo pelos respectivos projetos específicos da plataforma. O elemento de usuário encapsulado, então, é percorrido definindo suas propriedades [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) e [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) como os valores calculados.

Quando o movimento panorâmico no conteúdo de um elemento que não ocupa a tela inteira, a altura e a largura do visor podem ser obtidas das propriedades [`Height`](xref:Xamarin.Forms.VisualElement.Height) e [`Width`](xref:Xamarin.Forms.VisualElement.Width) do elemento.

> [!NOTE]
> Exibir imagens de alta resolução pode aumentar significativamente o volume de memória de um aplicativo. Portanto, elas só devem ser criadas quando necessário e devem ser liberadas assim que o aplicativo não precisar mais delas. Para saber mais, consulte [Otimizar recursos de imagem](~/xamarin-forms/deploy-test/performance.md#optimizeimages).

## <a name="related-links"></a>Links relacionados

- [PanGesture (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PanGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)
