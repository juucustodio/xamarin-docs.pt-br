---
title: Adicionar um reconhecedor de gesto de Pan
description: Este artigo explica como usar um gesto de panorâmica para percorrer uma imagem horizontal e verticalmente, para que todo o conteúdo da imagem pode ser exibido quando ela estiver sendo exibida em um visor menor do que as dimensões da imagem.
ms.prod: xamarin
ms.assetid: 42CBD2CF-432D-4F19-A05E-D569BB7F8713
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/21/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 401924de371efb14095499a655a42642c3032d4b
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375220"
---
# <a name="add-a-pan-gesture-recognizer"></a>Adicionar um reconhecedor de gesto de Pan

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)

_O gesto de panorâmica é usado para detectar a movimentação de dedos em toda a tela e aplicar essa movimentação ao conteúdo, e é implementado com a `PanGestureRecognizer` classe. Um cenário comum para o gesto panorâmico é deslocar horizontalmente e verticalmente uma imagem, para que todo o conteúdo da imagem possa ser exibido quando ele estiver sendo exibido em um visor menor do que as dimensões da imagem. Isso é feito movendo a imagem dentro do visor e é demonstrado neste artigo._

Para tornar um elemento de interface do usuário móvel com o gesto de panorâmica, crie uma [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) instância, manipule o [`PanUpdated`](xref:Xamarin.Forms.PanGestureRecognizer.PanUpdated) evento e adicione o novo reconhecedor de gesto à [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) coleção no elemento de interface do usuário. O exemplo de código a seguir mostra um `PanGestureRecognizer` anexado a um [`Image`](xref:Xamarin.Forms.Image) elemento:

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

Essa classe pode ser encapsulada em torno de um elemento de interface do usuário para que o gesto percorra o elemento de interface do usuário encapsulado. O exemplo de código XAML a seguir mostra a `PanContainer` disposição de um [`Image`](xref:Xamarin.Forms.Image) elemento:

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

O exemplo de código a seguir mostra como o `PanContainer` encapsula um [`Image`](xref:Xamarin.Forms.Image) elemento em uma página do C#:

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

Em ambos os exemplos, [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) as [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) Propriedades e são definidas com os valores de largura e altura da imagem que está sendo exibida.

Quando o [`Image`](xref:Xamarin.Forms.Image) elemento recebe um gesto de panorâmica, a imagem exibida será panorâmica. A panorâmica é realizada pelo método `PanContainer.OnPanUpdated`, que é mostrado no exemplo de código a seguir:

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

Esse método atualiza o conteúdo visível do elemento de interface do usuário encapsulado, com base no gesto de panorâmica do usuário. Isso é obtido usando os valores das [`TotalX`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalX) [`TotalY`](xref:Xamarin.Forms.PanUpdatedEventArgs.TotalY) Propriedades e da [`PanUpdatedEventArgs`](xref:Xamarin.Forms.PanUpdatedEventArgs) instância do para calcular a direção e a distância da panorâmica. As propriedades `App.ScreenWidth` e `App.ScreenHeight` fornecem a altura e largura do visor e são definidas com os valores da largura e da altura da tela do dispositivo pelos respectivos projetos específicos da plataforma. O elemento usuário encapsulado é, então, o movimento panorâmico definindo suas [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) Propriedades e com os valores calculados.

Ao panorâmica do conteúdo em um elemento que não ocupa a tela inteira, a altura e a largura do visor podem ser obtidas nas [`Height`](xref:Xamarin.Forms.VisualElement.Height) Propriedades e no elemento [`Width`](xref:Xamarin.Forms.VisualElement.Width) .

> [!NOTE]
> Exibir imagens de alta resolução pode aumentar significativamente o volume de memória de um aplicativo. Portanto, elas só devem ser criadas quando necessário e devem ser liberadas assim que o aplicativo não precisar mais delas. Para saber mais, consulte [Otimizar recursos de imagem](~/xamarin-forms/deploy-test/performance.md#optimize-image-resources).

## <a name="related-links"></a>Links Relacionados

- [PanGesture (amostra)](/samples/xamarin/xamarin-forms-samples/workingwithgestures-pangesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [PanGestureRecognizer](xref:Xamarin.Forms.PanGestureRecognizer)