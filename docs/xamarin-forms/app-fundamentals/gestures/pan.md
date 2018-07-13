---
title: Adicionando um reconhecedor de gestos de Panorâmica
description: Este artigo explica como usar um gesto de panorâmica para horizontalmente e verticalmente, arraste uma imagem, para que todo o conteúdo de imagem pode ser exibido quando ele está sendo exibido em um visor menor do que as dimensões da imagem.
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
ms.openlocfilehash: 45c0a1452916f193236e5ba741f8e8e19b6691aa
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996800"
---
# <a name="adding-a-pan-gesture-recognizer"></a>Adicionando um reconhecedor de gestos de Panorâmica

_O gesto de Panorâmica é usado para detectar arrastando e é implementado com a classe PanGestureRecognizer. Um cenário comum para o movimento panorâmico é horizontal e verticalmente, arraste uma imagem, para que todo o conteúdo de imagem pode ser exibido quando ele está sendo exibido em um visor menor do que as dimensões da imagem. Isso é feito ao mover a imagem dentro do visor e é demonstrado neste artigo._

## <a name="overview"></a>Visão geral

Para criar um elemento de interface do usuário arrastável com o gesto de panorâmica, crie uma [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) da instância, lidar com o [ `PanUpdated` ](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated) evento, e adicione o reconhecedor de gestos de novo para o [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) coleta sobre o elemento de interface do usuário. O seguinte exemplo de código mostra uma `PanGestureRecognizer` anexados a uma [ `Image` ](xref:Xamarin.Forms.Image) elemento:

```csharp
var panGesture = new PanGestureRecognizer();
panGesture.PanUpdated += (s, e) => {
  // Handle the pan
};
image.GestureRecognizers.Add(panGesture);
```

Isso também pode ser obtido no XAML, conforme mostrado no exemplo de código a seguir:

```xaml
<Image Source="MonoMonkey.jpg">
  <Image.GestureRecognizers>
    <PanGestureRecognizer PanUpdated="OnPanUpdated" />
  </Image.GestureRecognizers>
</Image>
```

O código para o `OnPanUpdated` manipulador de eventos é adicionado ao arquivo code-behind:

```csharp
void OnPanUpdated (object sender, PanUpdatedEventArgs e)
{
  // Handle the pan
}
```

> [!NOTE]
> Panorâmica correto no Android exige o [pacote do NuGet do xamarin. Forms 2.1.0-pre1](https://www.nuget.org/packages/Xamarin.Forms/2.1.0.6501-pre1) no mínimo.

## <a name="creating-a-pan-container"></a>Criar um contêiner de Panorâmica

Esta seção contém uma classe auxiliar generalizado que executa a panorâmica de forma livre, que normalmente é adequada para navegar em imagens ou mapas. Tratando o gesto de panorâmica para executar uma operação de arrastar requer um pouco de matemática para transformar a interface do usuário. Este matemática é usada para arrastar apenas dentro dos limites do elemento de interface do usuário encapsulado. O exemplo de código a seguir mostra a classe `PanContainer`:

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

Essa classe pode ser encapsulada em torno de um elemento de interface do usuário para que o gesto de panorâmica arrastar o elemento de interface de usuário encapsulado. Mostra o exemplo de código XAML abaixo de `PanContainer` encapsulando um [ `Image` ](xref:Xamarin.Forms.Image) elemento:

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

O seguinte exemplo de código mostra como o `PanContainer` encapsula uma [ `Image` ](xref:Xamarin.Forms.Image) elemento em uma página do c#:

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

Nos dois exemplos, o [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) e [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) propriedades são definidas como valores de largura e altura da imagem que está sendo exibida.

Quando o [ `Image` ](xref:Xamarin.Forms.Image) elemento recebe um gesto de panorâmica, a imagem exibida será ser arrastada. A operação de arrastar é executada pelo `PanContainer.OnPanUpdated` método, que é mostrado no exemplo de código a seguir:

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

Esse método atualiza o conteúdo visível do elemento de interface do usuário encapsulado, com base em um gesto de panorâmica do usuário. Isso é feito usando os valores da [ `TotalX` ](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX) e [ `TotalY` ](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY) propriedades do [ `PanUpdatedEventArgs` ](xref:Xamarin.Forms.PanUpdatedEventArgs) instância para calcular a direção e distância da panorâmica. O `App.ScreenWidth` e `App.ScreenHeight` propriedades forneça a altura e largura do visor e definir a largura da tela e os valores de altura de tela do dispositivo pelos respectivos projetos específicos da plataforma. O elemento de usuário encapsulado, em seguida, é arrastado definindo sua [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) e [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) propriedades para os valores calculados.

Quando o movimento panorâmico conteúdo em um elemento que não ocupa a tela inteira, a altura e largura do visor podem ser obtidos do elemento [ `Height` ](xref:Xamarin.Forms.VisualElement.Height) e [ `Width` ](xref:Xamarin.Forms.VisualElement.Width) propriedades.

> [!NOTE]
> Exibir imagens de alta resolução pode aumentar significativamente o volume de memória do aplicativo. Portanto, eles só devem ser criados quando necessário e deve ser liberado assim que o aplicativo não exige mais-los. Para saber mais, consulte [Otimizar recursos de imagem](~/xamarin-forms/deploy-test/performance.md#optimizeimages).

## <a name="summary"></a>Resumo

O gesto de Panorâmica é usado para detectar arrastando e é implementado com o [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) classe.



## <a name="related-links"></a>Links relacionados

- [PanGesture (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/PanGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)
