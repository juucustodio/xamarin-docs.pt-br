---
title: Opções de layout emXamarin.Forms
description: Cada Xamarin.Forms exibição tem as propriedades horizontaloptions e verticaloptions, do tipo layoutoptions. Este artigo explica o efeito que cada valor de Layoutoptions tem sobre o alinhamento e a expansão de uma exibição.
ms.prod: xamarin
ms.assetid: 7CAB5631-5153-4DEF-8AD7-C6011CE44307
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1a3b9db435de49c438f458d1c4d85d3f81bbf749
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86930670"
---
# <a name="layout-options-in-xamarinforms"></a>Opções de layout emXamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutoptions)

_Cada Xamarin.Forms exibição tem as propriedades horizontaloptions e verticaloptions, do tipo layoutoptions. Este artigo explica o efeito que cada valor de Layoutoptions tem sobre o alinhamento e a expansão de uma exibição._

## <a name="overview"></a>Visão geral

A [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) estrutura encapsula duas preferências de layout:

- **Alinhamento** – o alinhamento preferencial da exibição, que determina sua posição e tamanho dentro de seu layout pai.
- **Expansão** – usada apenas por um [`StackLayout`](xref:Xamarin.Forms.StackLayout) e indica se a exibição deve usar espaço extra, se estiver disponível.

Essas preferências de layout podem ser aplicadas a um [`View`](xref:Xamarin.Forms.View) , em relação ao seu pai, definindo [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) a [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propriedade ou do `View` como um dos campos públicos da [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) estrutura. Os campos públicos são os seguintes:

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

Os `Start` `Center` campos,, e `End` `Fill` são usados para definir o alinhamento do modo de exibição dentro do layout pai:

- Para alinhamento horizontal, [`Start`](xref:Xamarin.Forms.LayoutOptions.Start) posiciona o [`View`](xref:Xamarin.Forms.View) no lado esquerdo do layout pai e, para alinhamento vertical, ele posiciona o `View` na parte superior do layout pai.
- Para alinhamento horizontal e vertical, [`Center`](xref:Xamarin.Forms.LayoutOptions.Center) centraliza horizontal ou verticalmente o [`View`](xref:Xamarin.Forms.View) .
- Para alinhamento horizontal, [`End`](xref:Xamarin.Forms.LayoutOptions.End) posiciona o [`View`](xref:Xamarin.Forms.View) no lado direito do layout pai e, para alinhamento vertical, ele posiciona o `View` na parte inferior do layout pai.
- Para alinhamento horizontal, [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) garante que o [`View`](xref:Xamarin.Forms.View) preencha a largura do layout pai e para alinhamento vertical, garante que o `View` preencha a altura do layout pai.

Os `StartAndExpand` valores,, `CenterAndExpand` `EndAndExpand` e `FillAndExpand` são usados para definir a preferência de alinhamento e se a exibição ocupará mais espaço se estiver disponível no pai [`StackLayout`](xref:Xamarin.Forms.StackLayout) .

> [!NOTE]
> O valor padrão das propriedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) de uma exibição é [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill).

## <a name="alignment"></a>Alinhamento

O alinhamento controla como uma exibição é posicionada dentro de seu layout pai quando o layout pai contém espaço não utilizado (ou seja, o layout pai é maior do que o tamanho combinado de todos os seus filhos).

Um [`StackLayout`](xref:Xamarin.Forms.StackLayout) só respeita os `Start` `Center` campos,, `End` e `Fill` [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) em exibições filho que estão na direção oposta à `StackLayout` orientação. Portanto, as exibições filhas dentro de um verticalmente orientado `StackLayout` podem definir suas [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) Propriedades como um dos `Start` `Center` campos,, `End` ou `Fill` . Da mesma forma, as exibições filhas em um horizontalmente orientado `StackLayout` podem definir suas [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriedades como um dos `Start` `Center` campos,, `End` ou `Fill` .

A não [`StackLayout`](xref:Xamarin.Forms.StackLayout) respeita os `Start` campos, `Center` , `End` e `Fill` [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) em exibições filho que estão na mesma direção que a `StackLayout` orientação. Portanto, um orientado verticalmente `StackLayout` ignora os `Start` `Center` campos,, `End` ou `Fill` se eles são definidos nas [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriedades de exibições filho. Da mesma forma, um orientado horizontalmente `StackLayout` ignora os `Start` `Center` campos,, `End` ou `Fill` se eles são definidos nas [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) Propriedades de exibições filho.

> [!NOTE]
> [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)geralmente substitui as solicitações de tamanho especificadas usando as [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) Propriedades e.

O exemplo de código XAML a seguir demonstra um verticalmente orientado [`StackLayout`](xref:Xamarin.Forms.StackLayout) onde cada filho [`Label`](xref:Xamarin.Forms.Label) define sua [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) propriedade como um dos quatro campos de alinhamento da [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) estrutura:

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <Label Text="Start" BackgroundColor="Gray" HorizontalOptions="Start" />
  <Label Text="Center" BackgroundColor="Gray" HorizontalOptions="Center" />
  <Label Text="End" BackgroundColor="Gray" HorizontalOptions="End" />
  <Label Text="Fill" BackgroundColor="Gray" HorizontalOptions="Fill" />
</StackLayout>
```

O código C# equivalente é mostrado abaixo:

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new Label { Text = "Start", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Start },
    new Label { Text = "Center", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Center },
    new Label { Text = "End", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.End },
    new Label { Text = "Fill", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Fill }
  }
};
```

O código resulta no layout mostrado nas seguintes capturas de tela:

[![Opções de layout de alinhamento](layout-options-images/alignment.png)](layout-options-images/alignment-large.png#lightbox "Opções de layout de alinhamento")

## <a name="expansion"></a>Expansão

A expansão controla se uma exibição ocupará mais espaço, se disponível, em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Se o `StackLayout` contiver espaço não utilizado (ou seja, o `StackLayout` for maior que o tamanho combinado de todos os seus filhos), o espaço não utilizado será compartilhado igualmente por todas as exibições filhas que solicitam a expansão, definindo suas [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriedades ou como um [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) campo que usa o `AndExpand` sufixo. Observe que quando todo o espaço no `StackLayout` é usado, as opções de expansão não têm nenhum efeito.

Um [`StackLayout`](xref:Xamarin.Forms.StackLayout) só pode expandir exibições filho na direção de sua orientação. Portanto, um verticalmente orientado `StackLayout` pode expandir exibições filho que definem suas [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriedades para um dos `StartAndExpand` `CenterAndExpand` campos,, `EndAndExpand` ou `FillAndExpand` , se o `StackLayout` contiver espaço não utilizado. Da mesma forma, um orientado horizontalmente `StackLayout` pode expandir exibições filho que definem suas [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) Propriedades para um dos `StartAndExpand` `CenterAndExpand` campos,, `EndAndExpand` ou `FillAndExpand` , se o `StackLayout` contiver espaço não utilizado.

Um [`StackLayout`](xref:Xamarin.Forms.StackLayout) não pode expandir exibições filho na direção oposta à sua orientação. Portanto, em um verticalmente orientado `StackLayout` , definir a [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) propriedade em uma exibição filho [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand) terá o mesmo efeito que definir a propriedade como [`Start`](xref:Xamarin.Forms.LayoutOptions.Start) .

> [!NOTE]
> Observe que a habilitação da expansão não altera o tamanho de uma exibição, a menos que ela use [`LayoutOptions.FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) .

O exemplo de código XAML a seguir demonstra um verticalmente orientado [`StackLayout`](xref:Xamarin.Forms.StackLayout) onde cada filho [`Label`](xref:Xamarin.Forms.Label) define sua [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propriedade para um dos quatro campos de expansão da [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) estrutura:

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Start" BackgroundColor="Gray" VerticalOptions="StartAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Center" BackgroundColor="Gray" VerticalOptions="CenterAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="End" BackgroundColor="Gray" VerticalOptions="EndAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Fill" BackgroundColor="Gray" VerticalOptions="FillAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
</StackLayout>
```

O código C# equivalente é mostrado abaixo:

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "StartAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.StartAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "CenterAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.CenterAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "EndAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.EndAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
    new Label { Text = "FillAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.FillAndExpand },
    new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 }
  }
};
```

O código resulta no layout mostrado nas seguintes capturas de tela:

[![Opções de layout de expansão](layout-options-images/expansion.png)](layout-options-images/expansion-large.png#lightbox "Opções de layout de expansão")

Cada um [`Label`](xref:Xamarin.Forms.Label) ocupa a mesma quantidade de espaço dentro do [`StackLayout`](xref:Xamarin.Forms.StackLayout) . No entanto, somente o último `Label`, que define sua propriedade [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) como [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) tem um tamanho diferente. Além disso, cada `Label` um é separado por um pequeno vermelho [`BoxView`](xref:Xamarin.Forms.BoxView) , o que permite que o espaço `Label` ocupado seja facilmente exibido.

## <a name="summary"></a>Resumo

Este artigo explicou o efeito que cada [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) valor de estrutura tem sobre o alinhamento e a expansão de uma exibição, em relação ao seu pai. Os `Start` `Center` campos,, `End` e `Fill` são usados para definir o alinhamento do modo de exibição dentro do layout pai, e os `StartAndExpand` campos,, e `CenterAndExpand` `EndAndExpand` `FillAndExpand` são usados para definir a preferência de alinhamento e para determinar se a exibição ocupará mais espaço, se disponível, em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) .

## <a name="related-links"></a>Links Relacionados

- [Layoutoptions (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutoptions)
- [LayoutOptions](xref:Xamarin.Forms.LayoutOptions)
