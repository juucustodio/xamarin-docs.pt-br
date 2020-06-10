---
Título: "exibições nativas em C#" Descrição: "exibições nativas do iOS, Android e UWP podem ser referenciadas diretamente de Xamarin.Forms páginas criadas usando C#. Este artigo demonstra como adicionar exibições nativas a um Xamarin.Forms layout criado usando C# e como substituir o layout de modos de exibição personalizados para corrigir o uso da API de medição. "
MS. Prod: xamarin MS. AssetID: 230F937C-F914-4B21-8EA1-1A2A9E644769 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 04/27/2016 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="native-views-in-c"></a>Exibições nativas em C\#

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeembedding)

_Exibições nativas do iOS, Android e UWP podem ser referenciadas diretamente de Xamarin.Forms páginas criadas usando C#. Este artigo demonstra como adicionar exibições nativas a um Xamarin.Forms layout criado usando C# e como substituir o layout de modos de exibição personalizados para corrigir o uso da API de medição._

## <a name="overview"></a>Visão geral

Qualquer Xamarin.Forms controle que permita `Content` ser definido, ou que tenha uma `Children` coleção, pode adicionar exibições específicas da plataforma. Por exemplo, um iOS `UILabel` pode ser adicionado diretamente à [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) propriedade ou à [`StackLayout.Children`](xref:Xamarin.Forms.Layout`1.Children) coleção. No entanto, observe que essa funcionalidade requer o uso de `#if` define em Xamarin.Forms soluções de projeto compartilhadas e não está disponível em Xamarin.Forms .net Standard soluções de biblioteca.

As capturas de tela a seguir demonstram as exibições específicas da plataforma que foram adicionadas a um Xamarin.Forms [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

[![](code-images/screenshots-sml.png "StackLayout Containing Platform-Specific Views")](code-images/screenshots.png#lightbox "StackLayout Containing Platform-Specific Views")

A capacidade de adicionar exibições específicas de plataforma a um Xamarin.Forms layout é habilitada por dois métodos de extensão em cada plataforma:

- `Add`– Adiciona uma exibição específica da plataforma à [`Children`](xref:Xamarin.Forms.Layout`1.Children) coleção de um layout.
- `ToView`– usa uma exibição específica da plataforma e a encapsula como uma Xamarin.Forms [`View`](xref:Xamarin.Forms.View) que pode ser definida como a `Content` propriedade de um controle.

O uso desses métodos em um Xamarin.Forms projeto compartilhado requer a importação do namespace específico da plataforma apropriado Xamarin.Forms :

- **Ios** – Xamarin.Forms . Platform. iOS
- **Android** – Xamarin.Forms . Platform. Android
- **Plataforma universal do Windows (UWP)** – Xamarin.Forms . Platform. UWP

## <a name="adding-platform-specific-views-on-each-platform"></a>Adicionando exibições específicas da plataforma em cada plataforma

As seções a seguir demonstram como adicionar exibições específicas da plataforma a um Xamarin.Forms layout em cada plataforma.

### <a name="ios"></a>iOS

O exemplo de código a seguir demonstra como adicionar um `UILabel` a um [`StackLayout`](xref:Xamarin.Forms.StackLayout) e um [`ContentView`](xref:Xamarin.Forms.ContentView) :

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

O exemplo supõe que as `stackLayout` `contentView` instâncias e tenham sido criadas anteriormente em XAML ou C#.

### <a name="android"></a>Android

O exemplo de código a seguir demonstra como adicionar um `TextView` a um [`StackLayout`](xref:Xamarin.Forms.StackLayout) e um [`ContentView`](xref:Xamarin.Forms.ContentView) :

```csharp
var textView = new TextView (MainActivity.Instance) { Text = originalText, TextSize = 14 };
stackLayout.Children.Add (textView);
contentView.Content = textView.ToView();
```

O exemplo supõe que as `stackLayout` `contentView` instâncias e tenham sido criadas anteriormente em XAML ou C#.

### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

O exemplo de código a seguir demonstra como adicionar um `TextBlock` a um [`StackLayout`](xref:Xamarin.Forms.StackLayout) e um [`ContentView`](xref:Xamarin.Forms.ContentView) :

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

O exemplo supõe que as `stackLayout` `contentView` instâncias e tenham sido criadas anteriormente em XAML ou C#.

## <a name="overriding-platform-measurements-for-custom-views"></a>Substituindo medidas de plataforma para exibições personalizadas

As exibições personalizadas em cada plataforma geralmente implementam a medida corretamente apenas para o cenário de layout para o qual foram projetadas. Por exemplo, um modo de exibição personalizado pode ter sido projetado para ocupar apenas metade da largura disponível do dispositivo. No entanto, depois de ser compartilhado com outros usuários, a exibição personalizada pode ser necessária para ocupar a largura total disponível do dispositivo. Portanto, pode ser necessário substituir uma implementação de medida de exibições personalizadas quando ela é reutilizada em um Xamarin.Forms layout. Por esse motivo, os `Add` `ToView` métodos de extensão e fornecem substituições que permitem que os delegados de medição sejam especificados, o que pode substituir o layout de exibição personalizado quando ele é adicionado a um Xamarin.Forms layout.

As seções a seguir demonstram como substituir o layout de modos de exibição personalizados para corrigir o uso da API de medição.

### <a name="ios"></a>iOS

O exemplo de código a seguir mostra a `CustomControl` classe, que herda de `UILabel` :

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

Uma instância dessa exibição é adicionada a um [`StackLayout`](xref:Xamarin.Forms.StackLayout) , conforme demonstrado no exemplo de código a seguir:

```csharp
var customControl = new CustomControl {
  MinimumFontSize = 14,
  Lines = 0,
  LineBreakMode = UILineBreakMode.WordWrap,
  Text = "This control has incorrect sizing - there's empty space above and below it."
};
stackLayout.Children.Add (customControl);
```

No entanto, como a `CustomControl.SizeThatFits` substituição sempre retorna uma altura de 150, a exibição será exibida com espaço vazio acima e abaixo dela, conforme mostrado na seguinte captura de tela:

![](code-images/ios-bad-measurement.png "iOS CustomControl with Bad SizeThatFits Implementation")

Uma solução para esse problema é fornecer uma `GetDesiredSizeDelegate` implementação, conforme demonstrado no exemplo de código a seguir:

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

Esse método usa a largura fornecida pelo `CustomControl.SizeThatFits` método, mas substitui a altura de 150 por uma altura de 70. Quando a `CustomControl` instância é adicionada ao [`StackLayout`](xref:Xamarin.Forms.StackLayout) , o `FixSize` método pode ser especificado como o `GetDesiredSizeDelegate` para corrigir a medida inadequada fornecida pela `CustomControl` classe:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

Isso faz com que a exibição personalizada seja exibida corretamente, sem espaço vazio acima e abaixo dela, conforme mostrado na seguinte captura de tela:

![](code-images/ios-good-measurement.png "iOS CustomControl with GetDesiredSize Override")

### <a name="android"></a>Android

O exemplo de código a seguir mostra a `CustomControl` classe, que herda de `TextView` :

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

Uma instância dessa exibição é adicionada a um [`StackLayout`](xref:Xamarin.Forms.StackLayout) , conforme demonstrado no exemplo de código a seguir:

```csharp
var customControl = new CustomControl (MainActivity.Instance) {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device.",
  TextSize = 14
};
stackLayout.Children.Add (customControl);
```

No entanto, como a `CustomControl.OnMeasure` substituição sempre retorna metade da largura solicitada, a exibição será exibida ocupando apenas metade da largura disponível do dispositivo, conforme mostrado na seguinte captura de tela:

![](code-images/android-bad-measurement.png "Android CustomControl with Bad OnMeasure Implementation")

Uma solução para esse problema é fornecer uma `GetDesiredSizeDelegate` implementação, conforme demonstrado no exemplo de código a seguir:

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

Esse método usa a largura fornecida pelo `CustomControl.OnMeasure` método, mas a multiplica por dois. Quando a `CustomControl` instância é adicionada ao [`StackLayout`](xref:Xamarin.Forms.StackLayout) , o `FixSize` método pode ser especificado como o `GetDesiredSizeDelegate` para corrigir a medida inadequada fornecida pela `CustomControl` classe:

```csharp
stackLayout.Children.Add (customControl, FixSize);
```

Isso faz com que a exibição personalizada seja exibida corretamente, ocupando a largura do dispositivo, conforme mostrado na seguinte captura de tela:

![](code-images/android-good-measurement.png "Android CustomControl with Custom GetDesiredSize Delegate")

### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

O exemplo de código a seguir mostra a `CustomControl` classe, que herda de `Panel` :

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

Uma instância dessa exibição é adicionada a um [`StackLayout`](xref:Xamarin.Forms.StackLayout) , conforme demonstrado no exemplo de código a seguir:

```csharp
var brokenControl = new CustomControl {
  Text = "This control has incorrect sizing - it doesn't occupy the available width of the device."
};
stackLayout.Children.Add(brokenControl);
```

No entanto, como a `CustomControl.ArrangeOverride` substituição sempre retorna metade da largura solicitada, a exibição será recortada para metade da largura disponível do dispositivo, conforme mostrado na seguinte captura de tela:

![](code-images/winrt-bad-measurement.png "UWP CustomControl with Bad ArrangeOverride Implementation")

Uma solução para esse problema é fornecer uma `ArrangeOverrideDelegate` implementação, ao adicionar a exibição ao [`StackLayout`](xref:Xamarin.Forms.StackLayout) , conforme demonstrado no exemplo de código a seguir:

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

Esse método usa a largura fornecida pelo `CustomControl.ArrangeOverride` método, mas a multiplica por dois. Isso faz com que a exibição personalizada seja exibida corretamente, ocupando a largura do dispositivo, conforme mostrado na seguinte captura de tela:

![](code-images/winrt-good-measurement.png "UWP CustomControl with ArrangeOverride Delegate")

## <a name="summary"></a>Resumo

Este artigo explicou como adicionar exibições nativas a um Xamarin.Forms layout criado usando C# e como substituir o layout de modos de exibição personalizados para corrigir o uso da API de medição.

## <a name="related-links"></a>Links relacionados

- [NativeEmbedding (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-nativeembedding)
- [Formulários nativos](~/xamarin-forms/platform/native-forms.md)
