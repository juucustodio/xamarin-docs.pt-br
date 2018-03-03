---
title: LayoutOptions
description: "Cada exibição xamarin. Forms tem propriedades HorizontalOptions e VerticalOptions, do tipo LayoutOptions. Este artigo explica o efeito de cada valor de LayoutOptions no alinhamento e expansão de um modo de exibição."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7CAB5631-5153-4DEF-8AD7-C6011CE44307
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2017
ms.openlocfilehash: 978985c4e9803fad33760e4b40ab73d57f3ec420
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="layoutoptions"></a>LayoutOptions

_Cada exibição xamarin. Forms tem propriedades HorizontalOptions e VerticalOptions, do tipo LayoutOptions. Este artigo explica o efeito de cada valor de LayoutOptions no alinhamento e expansão de um modo de exibição._

## <a name="overview"></a>Visão geral

O [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) estrutura encapsula as preferências de layout dois:

- **Alinhamento** – o modo de exibição preferencial do alinhamento, que determina sua posição e o tamanho do layout de seu pai.
- **Expansão** – usada somente por um [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)e indica se o modo de exibição deve usar o espaço extra, se estiver disponível.

Essas preferências de layout podem ser aplicadas a um [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/), relativo a seu pai, definindo a [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) ou [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propriedade das `View` para um dos campos de público a [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) estrutura. Os campos públicos são da seguinte maneira:

- [`Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/)
- [`Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/)
- [`End`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/)
- [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)
- [`StartAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/)
- [`CenterAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.CenterAndExpand/)
- [`EndAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.EndAndExpand/)
- [`FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)

O `Start`, `Center`, `End`, e `Fill` campos são usados para definir o alinhamento do modo de exibição do layout do pai:

- Para alinhamento horizontal, [ `Start` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/) posições a [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) no lado esquerdo do layout do pai e para o alinhamento vertical, ele posiciona o `View` na parte superior das layout de pai.
- Para o alinhamento horizontal e vertical, [ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/) horizontal ou verticalmente centraliza o [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/).
- Para o alinhamento horizontal, [ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/) posições de [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) no lado direito do layout do pai e para o alinhamento vertical, ele posiciona o `View` na parte inferior do layout do pai.
- Para o alinhamento horizontal, [ `Fill` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/) garante que o [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) ocupa a largura do layout do pai e para o alinhamento vertical, ele garante que o `View` preenche o altura do layout do pai.

O `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, e `FillAndExpand` valores são usados para definir a preferência de alinhamento, e se o modo de exibição ocupam mais espaço se estiver disponível dentro do pai [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/).

> [!NOTE]
> O valor padrão de um modo de exibição [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) e [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propriedades é [ `LayoutOptions.Fill` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/).

<a name="alignment" />

## <a name="alignment"></a>Alinhamento

Alinhamento controla como um modo de exibição é posicionado no layout de seu pai quando o layout do pai contém espaço não utilizado (ou seja, o layout de pai é maior do que o tamanho combinado de todos os seus filhos).

Um [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) somente respeita o `Start`, `Center`, `End`, e `Fill` [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) campos em exibições filho que estão na direção oposta para o `StackLayout` orientação. Portanto, o filho exibições na orientação vertical `StackLayout` pode definir seus [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) propriedades para um do `Start`, `Center`, `End`, ou `Fill` campos. Da mesma forma, o filho exibições em uma orientação horizontal `StackLayout` pode definir seus [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propriedades para um do `Start`, `Center`, `End`, ou `Fill` campos.

Um [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) não respeita a `Start`, `Center`, `End`, e `Fill` [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) campos em exibições filho que estão na mesma direção o `StackLayout` orientação. Portanto, uma orientação vertical `StackLayout` ignora o `Start`, `Center`, `End`, ou `Fill` campos se eles estiverem definidos no [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propriedades dos modos de exibição do filho. Da mesma forma, uma orientação horizontal `StackLayout` ignora o `Start`, `Center`, `End`, ou `Fill` campos se eles estiverem definidos no [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) propriedades dos modos de exibição do filho.

> [!NOTE]
> [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/) geralmente substituições tamanho solicitações especificadas usando o [ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/) e [ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/) propriedades.

O exemplo de código XAML a seguir demonstra uma orientação vertical [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) onde cada filho [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) define seu [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) propriedade para um dos campos de alinhamento de quatro a [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) estrutura:

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

[![](layout-options-images/alignment.png "Opções de alinhamento de Layout")](layout-options-images/alignment-large.png "opções de alinhamento de Layout")

<a name="expansion" />

## <a name="expansion"></a>Expansão

Expansão controla se uma exibição ocupam mais espaço, se disponível, dentro de um [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/). Se o `StackLayout` contém espaço não utilizado (ou seja, o `StackLayout` é maior do que o tamanho combinado de todos os seus filhos), o espaço não utilizado é compartilhado igualmente por todos os modos de filho que solicitam expansão definindo suas [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)ou [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propriedades para um [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) campo que usa o `AndExpand` sufixo. Observe que, quando todo o espaço no `StackLayout` é usado, as opções de expansão não têm nenhum efeito.

Um [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) só pode expandir os modos de exibição filho na direção de sua orientação. Portanto, uma orientação vertical `StackLayout` pode expandir exibições filho que definam seus [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propriedades para um do `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, ou `FillAndExpand` campos, se o `StackLayout` contém espaço não utilizado. Da mesma forma, uma orientação horizontal `StackLayout` pode expandir exibições filho que definam seus [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) propriedades para um do `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, ou `FillAndExpand` campos, se o `StackLayout` contém espaço não utilizado.

Um [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) não é possível expandir exibições filho na direção oposta à sua orientação. Portanto, na orientação vertical `StackLayout`, a definição de [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) propriedade em uma exibição de filho para [ `StartAndExpand` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/) tem o mesmo efeito que definir a propriedade como [ `Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/).

> [!NOTE]
> Observe que habilitar expansão não altera o tamanho de uma exibição, a menos que ele usa [ `LayoutOptions.FillAndExpand` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/).

O exemplo de código XAML a seguir demonstra uma orientação vertical [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) onde cada filho [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) define seu [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propriedade para um dos campos da expansão de quatro a [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) estrutura:

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

[![](layout-options-images/expansion.png "Opções de Layout de expansão")](layout-options-images/expansion-large.png "opções de Layout de expansão")

Cada [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) ocupam a mesma quantidade de espaço dentro de [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/). No entanto, apenas o último `Label`, que define seu [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propriedade [ `FillAndExpand` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/) tem um tamanho diferente. Além disso, cada `Label` é separado por um vermelho pequeno [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/), que permite que o espaço de `Label` ocupa para ser exibidos com facilidade.

## <a name="summary"></a>Resumo

Este artigo explicou o efeito que cada [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/) tem valor de estrutura no alinhamento e expansão de uma exibição, em relação ao seu pai. O `Start`, `Center`, `End`, e `Fill` campos são usados para definir o alinhamento do modo de exibição do layout do pai e o `StartAndExpand`, `CenterAndExpand`, `EndAndExpand`, e `FillAndExpand` campos são usados para definir a preferência de alinhamento e para determinar se o modo de exibição ocupam mais espaço, se disponível, dentro de um [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/).



## <a name="related-links"></a>Links relacionados

- [LayoutOptions (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutoptions/)
- [LayoutOptions](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)
