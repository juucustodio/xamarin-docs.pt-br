---
title: Modos de exibição nativos em c#
description: Exibições nativo do iOS, Android e UWP podem ser referenciadas diretamente do xamarin. Forms páginas criadas usando o c#. Este artigo demonstra como adicionar exibições nativo para um layout de xamarin. Forms criado usando c# e como substituir o layout dos modos de exibição personalizados para corrigir seu uso da API de medição.
ms.prod: xamarin
ms.assetid: 230F937C-F914-4B21-8EA1-1A2A9E644769
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 7979e43cdde60b1b0248118e591c096295847acb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="native-views-in-c"></a>Modos de exibição nativos em c#

_Exibições nativo do iOS, Android e UWP podem ser referenciadas diretamente do xamarin. Forms páginas criadas usando o c#. Este artigo demonstra como adicionar exibições nativo para um layout de xamarin. Forms criado usando c# e como substituir o layout dos modos de exibição personalizados para corrigir seu uso da API de medição._

## <a name="overview"></a>Visão geral

Qualquer controle de xamarin. Forms permite `Content` para ser definido, ou que tenha um `Children` coleção, pode adicionar modos de exibição específicos da plataforma. Por exemplo, um iOS `UILabel` podem ser adicionados diretamente para o [ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) propriedade, ou o [ `StackLayout.Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/) coleção. No entanto, observe que essa funcionalidade requer o uso de `#if` define em soluções de projeto compartilhado do xamarin. Forms e não está disponível de soluções de biblioteca de classe portátil xamarin. Forms (PCL).

As capturas de tela a seguir demonstram específico da plataforma exibições com foi adicionado a um xamarin. Forms [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/):

[![](code-images/screenshots-sml.png "StackLayout que contém exibições específicas de plataforma")](code-images/screenshots.png#lightbox "StackLayout que contém exibições específicas de plataforma")

A capacidade de adicionar modos de exibição específicos da plataforma para um layout de xamarin. Forms é habilitada por dois métodos de extensão em cada plataforma:

- `Add` – Adiciona uma exibição específica de plataforma para o [ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/) coleção de um layout.
- `ToView` – usa uma exibição específica de plataforma e organize-os como um xamarin. Forms [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) que pode ser definido como o `Content` propriedade de um controle.

Usar esses métodos em um projeto compartilhado xamarin. Forms requer importar o namespace apropriado do xamarin. Forms específico da plataforma:

- **iOS** – Xamarin.Forms.Platform.iOS
- **Android** – Xamarin.Forms.Platform.Android
- **Tempo de execução do Windows** – Xamarin.Forms.Platform.WinRT
- **Plataforma universal do Windows (UWP)** – Xamarin.Forms.Platform.UWP

## <a name="adding-platform-specific-views-on-each-platform"></a>Adicionando modos de exibição específicos de plataforma em cada plataforma

As seções a seguir demonstram como adicionar modos de exibição específicos da plataforma para um layout de xamarin. Forms em cada plataforma.

### <a name="ios"></a>iOS

O exemplo de código a seguir demonstra como adicionar um `UILabel` para um [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) e um [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

```csharp
var uiLabel = new UILabel {
  MinimumFontSize = 14f,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = originalText,
};
stackLayout.Children.Add (uiLabel);
contentView.Content = uiLabel.ToView();
```

O exemplo supõe que o `stackLayout` e `contentView` instâncias criadas anteriormente em XAML ou c#.

### <a name="android"></a>Android

O exemplo de código a seguir demonstra como adicionar um `TextView` para um [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) e um [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

```csharp
var textView = new TextView (MainActivity.Instance) { Text = originalText, TextSize = 14 };
stackLayout.Children.Add (textView);
contentView.Content = textView.ToView();
```

O exemplo supõe que o `stackLayout` e `contentView` instâncias criadas anteriormente em XAML ou c#.

### <a name="windows-runtime-and-universal-windows-platform"></a>Tempo de execução do Windows e a plataforma Universal do Windows

O exemplo de código a seguir demonstra como adicionar um `TextBlock` para um [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) e um [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

```csharp
var textBlock = new TextBlock
{
    Text = originalText,
    FontSize = 14,
    FontFamily = new FontFamily("HelveticaNeue"),
    TextWrapping = TextWrapping.Wrap
};
stackLayout.Children.Add(textBlock);
contentView.Content = textBlock.ToView();
```

O exemplo supõe que o `stackLayout` e `contentView` instâncias criadas anteriormente em XAML ou c#.

## <a name="overriding-platform-measurements-for-custom-views"></a>Substituindo as medidas de plataforma para modos de exibição personalizados

Modos de exibição personalizados em cada plataforma implementam geralmente só corretamente a medida para o cenário de layout para a qual eles foram criados. Por exemplo, uma exibição personalizada pode ter sido projetada para ocupar apenas metade da largura disponível do dispositivo. No entanto, depois que está sendo compartilhado com outros usuários, o modo de exibição personalizado pode ser necessário para ocupar a largura total disponível do dispositivo. Portanto, pode ser necessário substituir uma implementação de medição de modos de exibição personalizados quando está sendo reutilizado em um layout de xamarin. Forms. Por esse motivo, o `Add` e `ToView` métodos de extensão fornecem substituições que permitem que os representantes de medida seja especificada, que podem substituir o layout do modo de exibição personalizado quando ele é adicionado a um layout de xamarin. Forms.

As seções a seguir demonstram como substituir o layout dos modos de exibição personalizados, corrija a uso da API de medida.

### <a name="ios"></a>iOS

O seguinte exemplo de código mostra o `CustomControl` classe que herda de `UILabel`:

```csharp
public class CustomControl : UILabel
{
  public override string Text {
    get { return base.Text; }
    set { base.Text = value.ToUpper (); }
  }

  public override CGSize SizeThatFits (CGSize size)
  {
    return new CGSize (size.Width, 150);
  }
}
```

Uma instância deste modo de exibição é adicionada a um [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), conforme demonstrado no exemplo de código a seguir:

```csharp
var customControl = new CustomControl {
  MinimumFontSize = 14,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = "This control has incorrect sizing - there's empty space above and below it."
};
stackLayout.Children.Add (customControl);
```

No entanto, como o `CustomControl.SizeThatFits` substituição sempre retorna a altura de 150, a exibição será exibida com um espaço vazio acima e abaixo dela, conforme mostrado na seguinte captura de tela:

![](code-images/ios-bad-measurement.png "iOS CustomControl com implementação incorreta de SizeThatFits")

Uma solução para esse problema é fornecer um `GetDesiredSizeDelegate` implementação, conforme demonstrado no exemplo de código a seguir:

```csharp
SizeRequest? FixSize (NativeViewWrapperRenderer renderer, double width, double height)
{
  var uiView = renderer.Control;

  if (uiView == null) {
    return null;
  }

  var constraint = new CGSize (width, height);

  // Let the CustomControl determine its size (which will be wrong)
  var badRect = uiView.SizeThatFits (constraint);

  // Use the width and substitute the height
  return new SizeRequest (new Size (badRect.Width, 70));
}
```

Esse método usa a largura fornecida pelo `CustomControl.SizeThatFits` método, mas substitui a altura de 150 para a altura de 70. Quando o `CustomControl` instância é adicionada para o [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), o `FixSize` método pode ser especificado como o `GetDesiredSizeDelegate` para corrigir a medida incorreta fornecida pelo `CustomControl` classe:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

Isso resulta na exibição personalizada que está sendo exibida corretamente, sem espaço vazio acima e abaixo dela, conforme mostrado na seguinte captura de tela:

![](code-images/ios-good-measurement.png "iOS CustomControl com substituição GetDesiredSize")

### <a name="android"></a>Android

O seguinte exemplo de código mostra o `CustomControl` classe que herda de `TextView`:

```csharp
public class CustomControl : TextView
{
  public CustomControl (Context context) : base (context)
  {
  }

  protected override void OnMeasure (int widthMeasureSpec, int heightMeasureSpec)
  {
    int width = MeasureSpec.GetSize (widthMeasureSpec);

    // Force the width to half of what's been requested.
    // This is deliberately wrong to demonstrate providing an override to fix it with.
    int widthSpec = MeasureSpec.MakeMeasureSpec (width / 2, MeasureSpec.GetMode (widthMeasureSpec));

    base.OnMeasure (widthSpec, heightMeasureSpec);
  }
}
```

Uma instância deste modo de exibição é adicionada a um [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), conforme demonstrado no exemplo de código a seguir:

```csharp
var customControl = new CustomControl (MainActivity.Instance) {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device.",
  TextSize = 14
};
stackLayout.Children.Add (customControl);
```

No entanto, como o `CustomControl.OnMeasure` substituição sempre retorna metade da largura do solicitada, a exibição será exibida que ocupam somente metade da largura disponível do dispositivo, conforme mostrado na seguinte captura de tela:

![](code-images/android-bad-measurement.png "Android CustomControl com a implementação de OnMeasure incorreta")

Uma solução para esse problema é fornecer um `GetDesiredSizeDelegate` implementação, conforme demonstrado no exemplo de código a seguir:

```csharp
SizeRequest? FixSize (NativeViewWrapperRenderer renderer, int widthConstraint, int heightConstraint)
{
  var nativeView = renderer.Control;

  if ((widthConstraint == 0 && heightConstraint == 0) || nativeView == null) {
    return null;
  }

  int width = Android.Views.View.MeasureSpec.GetSize (widthConstraint);
  int widthSpec = Android.Views.View.MeasureSpec.MakeMeasureSpec (
    width * 2, Android.Views.View.MeasureSpec.GetMode (widthConstraint));
  nativeView.Measure (widthSpec, heightConstraint);
  return new SizeRequest (new Size (nativeView.MeasuredWidth, nativeView.MeasuredHeight));
}
```

Esse método usa a largura fornecida pelo `CustomControl.OnMeasure` método, mas o multiplica por dois. Quando o `CustomControl` instância é adicionada para o [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), o `FixSize` método pode ser especificado como o `GetDesiredSizeDelegate` para corrigir a medida incorreta fornecida pelo `CustomControl` classe:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

Isso resulta no modo de exibição personalizado que está sendo exibido corretamente, que ocupa a largura do dispositivo, conforme mostrado na seguinte captura de tela:

![](code-images/android-good-measurement.png "CustomControl Android com o delegado GetDesiredSize personalizado")

### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

O seguinte exemplo de código mostra o `CustomControl` classe que herda de `Panel`:

```csharp
public class CustomControl : Panel
{
  public static readonly DependencyProperty TextProperty =
    DependencyProperty.Register(
      "Text", typeof(string), typeof(CustomControl), new PropertyMetadata(default(string), OnTextPropertyChanged));

  public string Text
  {
    get { return (string)GetValue(TextProperty); }
    set { SetValue(TextProperty, value.ToUpper()); }
  }

  readonly TextBlock textBlock;

  public CustomControl()
  {
    textBlock = new TextBlock
    {
      MinHeight = 0,
      MaxHeight = double.PositiveInfinity,
      MinWidth = 0,
      MaxWidth = double.PositiveInfinity,
      FontSize = 14,
      TextWrapping = TextWrapping.Wrap,
      VerticalAlignment = VerticalAlignment.Center
    };

    Children.Add(textBlock);
  }

  static void OnTextPropertyChanged(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs args)
  {
    ((CustomControl)dependencyObject).textBlock.Text = (string)args.NewValue;
  }

  protected override Size ArrangeOverride(Size finalSize)
  {
      // This is deliberately wrong to demonstrate providing an override to fix it with.
      textBlock.Arrange(new Rect(0, 0, finalSize.Width/2, finalSize.Height));
      return finalSize;
  }

  protected override Size MeasureOverride(Size availableSize)
  {
      textBlock.Measure(availableSize);
      return new Size(textBlock.DesiredSize.Width, textBlock.DesiredSize.Height);
  }
}
```

Uma instância deste modo de exibição é adicionada a um [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), conforme demonstrado no exemplo de código a seguir:

```csharp
var brokenControl = new CustomControl {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device."
};
stackLayout.Children.Add(brokenControl);
```

No entanto, como o `CustomControl.ArrangeOverride` substituição sempre retorna metade da largura do solicitada, a exibição será anexada à metade da largura disponível do dispositivo, conforme mostrado na seguinte captura de tela:

![](code-images/winrt-bad-measurement.png "UWP CustomControl com implementação ArrangeOverride incorreta")

Uma solução para esse problema é fornecer um `ArrangeOverrideDelegate` implementação, ao adicionar o modo de exibição de [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/), conforme demonstrado no exemplo de código a seguir:

```csharp
stackLayout.Children.Add(fixedControl, arrangeOverrideDelegate: (renderer, finalSize) =>
{
    if (finalSize.Width <= 0 || double.IsInfinity(finalSize.Width))
    {
        return null;
    }
    var frameworkElement = renderer.Control;
    frameworkElement.Arrange(new Rect(0, 0, finalSize.Width * 2, finalSize.Height));
    return finalSize;
});
```

Esse método usa a largura fornecida pelo `CustomControl.ArrangeOverride` método, mas o multiplica por dois. Isso resulta no modo de exibição personalizado que está sendo exibido corretamente, que ocupa a largura do dispositivo, conforme mostrado na seguinte captura de tela:

![](code-images/winrt-good-measurement.png "UWP CustomControl com ArrangeOverride delegado")

## <a name="summary"></a>Resumo

Este artigo explicou como adicionar exibições nativo para um layout de xamarin. Forms criado usando c# e como substituir o layout dos modos de exibição personalizados para corrigir seu uso da API de medição.


## <a name="related-links"></a>Links relacionados

- [NativeEmbedding (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeEmbedding/)
- [Formulários nativos](~/xamarin-forms/platform/native-forms.md)
