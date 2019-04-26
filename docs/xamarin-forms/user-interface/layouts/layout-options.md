---
title: Opções de layout no xamarin. Forms
description: Cada modo de exibição do xamarin. Forms tem as propriedades HorizontalOptions e propriedades VerticalOptions, do tipo LayoutOptions. Este artigo explica o efeito que cada valor LayoutOptions tem sobre o alinhamento e a expansão de um modo de exibição.
ms.prod: xamarin
ms.assetid: 7CAB5631-5153-4DEF-8AD7-C6011CE44307
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2017
ms.openlocfilehash: a78911a13ca3682a18b0911d020d98445b4f560c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61370968"
---
# <a name="layout-options-in-xamarinforms"></a>Opções de layout no xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutoptions/)

_Cada modo de exibição do xamarin. Forms tem as propriedades HorizontalOptions e propriedades VerticalOptions, do tipo LayoutOptions. Este artigo explica o efeito que cada valor LayoutOptions tem sobre o alinhamento e a expansão de um modo de exibição._

## <a name="overview"></a>Visão geral

O [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) estrutura encapsula duas as preferências de layout:

- **Alinhamento** – o modo de exibição preferencial do alinhamento, que determina sua posição e tamanho dentro do seu layout pai.
- **Expansão** – usada somente por um [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)e indica se o modo de exibição deve usar o espaço extra, se ele estiver disponível.

Essas preferências de layout podem ser aplicadas a um [ `View` ](xref:Xamarin.Forms.View), em relação a seu pai, definindo o [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) ou [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propriedade das `View` dentre os campos públicos do [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) estrutura. Os campos públicos são da seguinte maneira:

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

O `Start`, `Center`, `End`, e `Fill` campos são usados para definir o alinhamento do modo de exibição do layout do pai:

- Para alinhamento horizontal, [ `Start` ](xref:Xamarin.Forms.LayoutOptions.Start) posições a [ `View` ](xref:Xamarin.Forms.View) no lado esquerdo do layout pai e o alinhamento vertical, ela posiciona o `View` na parte superior das layout pai.
- Para o alinhamento horizontal e vertical, [ `Center` ](xref:Xamarin.Forms.LayoutOptions.Center) horizontal ou verticalmente centraliza as [ `View` ](xref:Xamarin.Forms.View).
- Para o alinhamento horizontal, [ `End` ](xref:Xamarin.Forms.LayoutOptions.End) posições a [ `View` ](xref:Xamarin.Forms.View) no lado direito do layout pai e o alinhamento vertical, ela posiciona o `View` na parte inferior do layout pai.
- Para o alinhamento horizontal, [ `Fill` ](xref:Xamarin.Forms.LayoutOptions.Fill) garante que o [ `View` ](xref:Xamarin.Forms.View) preenche a largura do layout pai e o alinhamento vertical, garante que o `View` preenche o altura do layout pai.

O `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, e `FillAndExpand` valores são usados para definir a preferência de alinhamento, e se o modo de exibição será ocupam mais espaço, se disponível dentro do pai [ `StackLayout` ](xref:Xamarin.Forms.StackLayout).

> [!NOTE]
> O valor padrão de um modo de exibição [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) e [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propriedades é [ `LayoutOptions.Fill` ](xref:Xamarin.Forms.LayoutOptions.Fill).

<a name="alignment" />

## <a name="alignment"></a>Alinhamento

Alinhamento de controles como um modo de exibição é posicionado dentro de seu layout pai quando o layout pai contém espaço não utilizado (ou seja, o layout do pai é maior do que o tamanho combinado de todos os seus filhos).

Um [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) somente respeita o `Start`, `Center`, `End`, e `Fill` [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) campos em modos de exibição filho que estão na direção oposta para o `StackLayout` orientação. Portanto, o filho exibições dentro de uma orientação vertical `StackLayout` pode definir seus [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) as propriedades para um dos `Start`, `Center`, `End`, ou `Fill` campos. Da mesma forma, o filho exibições dentro de uma orientação horizontal `StackLayout` pode definir seus [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) as propriedades para um dos `Start`, `Center`, `End`, ou `Fill` campos.

Um [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) não respeita a `Start`, `Center`, `End`, e `Fill` [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) campos em modos de exibição filho que estão na mesma direção o `StackLayout` orientação. Portanto, uma orientação vertical `StackLayout` ignora os `Start`, `Center`, `End`, ou `Fill` campos se eles são definidos na [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propriedades de exibições filho. Da mesma forma, um orientado horizontalmente `StackLayout` ignora os `Start`, `Center`, `End`, ou `Fill` campos se elas são definidas no [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) propriedades de exibições filho.

> [!NOTE]
> [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) geralmente substituições dimensionar solicitações especificadas usando o [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) e [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) propriedades.

O exemplo de código XAML a seguir demonstra uma orientação vertical [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) onde cada filho [ `Label` ](xref:Xamarin.Forms.Label) define seu [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) propriedade para um dos campos de alinhamento de quatro a [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) estrutura:

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <Label Text="Start" BackgroundColor="Gray" HorizontalOptions="Start" />
  <Label Text="Center" BackgroundColor="Gray" HorizontalOptions="Center" />
  <Label Text="End" BackgroundColor="Gray" HorizontalOptions="End" />
  <Label Text="Fill" BackgroundColor="Gray" HorizontalOptions="Fill" />
</StackLayout>
```

O código c# equivalente é mostrado abaixo:

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

O código resulta no layout mostrado nas capturas de tela seguir:

[![](layout-options-images/alignment.png "Opções de Layout de alinhamento")](layout-options-images/alignment-large.png#lightbox "opções de Layout de alinhamento")

<a name="expansion" />

## <a name="expansion"></a>Expansão

Expansão controla se um modo de exibição ocupará mais espaço, se disponível, dentro de um [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Se o `StackLayout` contém espaço não utilizado (ou seja, o `StackLayout` é maior do que o tamanho combinado de todos os seus filhos), o espaço não utilizado é compartilhado igualmente por todas as exibições filho que solicitam a expansão, definindo suas [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)ou [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propriedades para um [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) campo que usa o `AndExpand` sufixo. Observe que, quando todo o espaço no `StackLayout` é usado, as opções de expansão não têm nenhum efeito.

Um [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) só pode expandir os modos de exibição filho na direção de sua orientação. Portanto, uma orientação vertical `StackLayout` pode expandir os modos de exibição filho que definirem seus [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propriedades para um dos `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, ou `FillAndExpand` campos, se o `StackLayout` contém espaço não utilizado. Da mesma forma, um orientado horizontalmente `StackLayout` pode expandir os modos de exibição filho definirem seus [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) propriedades para um da `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, ou `FillAndExpand` campos, se o `StackLayout` contém espaço não utilizado.

Um [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) não é possível expandir os modos de exibição filho na direção oposta à sua orientação. Portanto, na orientação vertical `StackLayout`, definindo o [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) propriedade em um modo de exibição filho [ `StartAndExpand` ](xref:Xamarin.Forms.LayoutOptions.StartAndExpand) tem o mesmo efeito que definir a propriedade como [ `Start`](xref:Xamarin.Forms.LayoutOptions.Start).

> [!NOTE]
> Observe que habilitar a expansão não altera o tamanho de um modo de exibição, a menos que ele usa [ `LayoutOptions.FillAndExpand` ](xref:Xamarin.Forms.LayoutOptions.FillAndExpand).

O exemplo de código XAML a seguir demonstra uma orientação vertical [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) onde cada filho [ `Label` ](xref:Xamarin.Forms.Label) define seu [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propriedade para um dos campos de expansão de quatro a [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) estrutura:

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

O código c# equivalente é mostrado abaixo:

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

O código resulta no layout mostrado nas capturas de tela seguir:

[![](layout-options-images/expansion.png "Opções de Layout de expansão")](layout-options-images/expansion-large.png#lightbox "opções de Layout de expansão")

Cada [ `Label` ](xref:Xamarin.Forms.Label) ocupa a mesma quantidade de espaço dentro de [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). No entanto, somente o último `Label`, que define seu [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propriedade a ser [ `FillAndExpand` ](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) tem um tamanho diferente. Além disso, cada `Label` é separado por um pequeno vermelho [ `BoxView` ](xref:Xamarin.Forms.BoxView), que permite que o espaço de `Label` ocupa para ser facilmente exibidos.

## <a name="summary"></a>Resumo

Este artigo explicou o efeito que cada [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) valor estrutura tem sobre o alinhamento e a expansão de uma exibição, relativo ao seu pai. O `Start`, `Center`, `End`, e `Fill` campos são usados para definir o alinhamento do modo de exibição do layout do pai e o `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, e `FillAndExpand` campos são usados para definir a preferência de alinhamento e para determinar se o modo de exibição ocupará mais espaço, se disponível, dentro de um [ `StackLayout` ](xref:Xamarin.Forms.StackLayout).



## <a name="related-links"></a>Links relacionados

- [LayoutOptions (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutoptions/)
- [LayoutOptions](xref:Xamarin.Forms.LayoutOptions)
