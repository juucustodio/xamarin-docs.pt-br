---
title: Associação de um ControlTemplate do Xamarin.Forms
description: Associações de modelo permitem que os controles em um modelo de controle associem dados a propriedades públicas, permitindo que os valores de propriedade em controles no modelo de controle sejam alterados facilmente. Este artigo demonstra como usar associações de modelo para realizar a associação de dados de um modelo de controle.
ms.prod: xamarin
ms.assetid: 794A663C-3A8D-438A-BD02-8E97C919B55F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 41e5bbc42ccde5cdd5223a7d2cb0a77da66e10c1
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647013"
---
# <a name="binding-from-a-xamarinforms-controltemplate"></a>Associação de um ControlTemplate do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplates-simplethemewithtemplatebinding)

_Associações de modelo permitem que os controles em um modelo de controle associem dados a propriedades públicas, permitindo que os valores de propriedade em controles no modelo de controle sejam alterados facilmente. Este artigo demonstra como usar associações de modelo para realizar a associação de dados de um modelo de controle._

Um [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) é usado para associar a propriedade de um controle em um modelo de controle a uma propriedade associável no pai da exibição de *destino* é proprietária do modelo de controle. Por exemplo, em vez de definir o texto exibido por instâncias de [`Label`](xref:Xamarin.Forms.Label) dentro do [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate), você poderia usar uma associação de modelo para associar a propriedade [`Label.Text`](xref:Xamarin.Forms.Label.Text) a propriedades vinculáveis que definem o texto a ser exibido.

Um [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) é semelhante a um [`Binding`](xref:Xamarin.Forms.Binding) existente, exceto pelo fato de que a *origem* de um `TemplateBinding` sempre é definida automaticamente como pai da exibição de *destino* que é proprietária do modelo de controle. No entanto, observe que não há suporte para usar um `TemplateBinding` fora de um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate).

## <a name="creating-a-templatebinding-in-xaml"></a>Criando um TemplateBinding em XAML

Em XAML, um [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) é criado usando a extensão de marcação [`TemplateBinding`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension), conforme demonstrado no exemplo de código a seguir:

```xaml
<ControlTemplate x:Key="TealTemplate">
  <Grid>
    ...
    <Label Text="{TemplateBinding Parent.HeaderText}" ... />
    ...
    <Label Text="{TemplateBinding Parent.FooterText}" ... />
  </Grid>
</ControlTemplate>
```

Em vez de definir as propriedades de [`Label.Text`](xref:Xamarin.Forms.Label.Text) como texto estático, as propriedades podem usar associações de modelo para associar a propriedades associáveis no pai da exibição de *destino* que é proprietária do [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). No entanto, observe que as associações de modelo se associam a `Parent.HeaderText` e `Parent.FooterText`, em vez de `HeaderText` e `FooterText`. Isso ocorre porque, neste exemplo, as propriedades associáveis são definidas no avô da exibição de *destino*, em vez do pai, conforme demonstrado no exemplo de código a seguir:

```xaml
<ContentPage ...>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
          ...
    </ContentView>
</ContentPage>
```

A *origem* da associação de modelo sempre é definida automaticamente como pai da exibição de *destino* que é proprietária do modelo de controle, que aqui é a instância de [`ContentView`](xref:Xamarin.Forms.ContentView). A associação de modelo usa a propriedade [`Parent`](xref:Xamarin.Forms.Element.Parent) para retornar o elemento pai da instância de `ContentView`, que é a instância de [`ContentPage`](xref:Xamarin.Forms.ContentPage). Portanto, usar um [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) no [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) para associar a `Parent.HeaderText` e `Parent.FooterText` localiza as propriedades associáveis que estão definidas no `ContentPage`, conforme demonstrado no exemplo de código a seguir:

```csharp
public static readonly BindableProperty HeaderTextProperty =
  BindableProperty.Create ("HeaderText", typeof(string), typeof(HomePage), "Control Template Demo App");
public static readonly BindableProperty FooterTextProperty =
  BindableProperty.Create ("FooterText", typeof(string), typeof(HomePage), "(c) Xamarin 2016");

public string HeaderText {
  get { return (string)GetValue (HeaderTextProperty); }
}

public string FooterText {
  get { return (string)GetValue (FooterTextProperty); }
}
```

Isso resulta na aparência mostrada nas capturas de tela seguir:

![](template-binding-images/teal-theme.png "Modelo de controle azul usando associações de modelo")

## <a name="creating-a-templatebinding-in-c35"></a>Criando um TemplateBinding em C&#35;

Em C#, um [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) é criado usando o construtor `TemplateBinding`, conforme demonstrado no exemplo de código a seguir:

```csharp
class TealTemplate : Grid
{
  public TealTemplate ()
  {
    ...
    var topLabel = new Label { TextColor = Color.White, VerticalOptions = LayoutOptions.Center };
    topLabel.SetBinding (Label.TextProperty, new TemplateBinding ("Parent.HeaderText"));
    ...
    var bottomLabel = new Label { TextColor = Color.White, VerticalOptions = LayoutOptions.Center };
    bottomLabel.SetBinding (Label.TextProperty, new TemplateBinding ("Parent.FooterText"));
    ...
  }
}
```

Em vez de definir as propriedades de [`Label.Text`](xref:Xamarin.Forms.Label.Text) como texto estático, as propriedades podem usar associações de modelo para associar a propriedades associáveis no pai da exibição de *destino* que é proprietária do [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). A associação de modelo é criada usando o método [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)), especificando uma instância de [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) como o segundo parâmetro. Observe que as associações de modelo se associam a `Parent.HeaderText` e `Parent.FooterText`, porque as propriedades associáveis são definidas no avô da exibição de *destino*, em vez do pai, conforme demonstrado no exemplo de código a seguir:

```csharp
public class HomePageCS : ContentPage
{
  ...
  public HomePageCS ()
  {
    Content = new ContentView {
      ControlTemplate = tealTemplate,
      Content = new StackLayout {
        ...
      },
      ...
    };
    ...
  }
}
```

As propriedades associáveis são definidas no `ContentPage`, conforme descrito anteriormente.

### <a name="binding-a-bindableproperty-to-a-viewmodel-property"></a>Associando uma BindableProperty a uma propriedade de ViewModel

Conforme descrito anteriormente, um [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) associa a propriedade de um controle em um modelo de controle a uma propriedade associável no pai da exibição de *destino* é proprietária do modelo de controle. Por sua vez, essas propriedades associáveis podem ser associadas a propriedades em ViewModels.

O exemplo de código a seguir define duas propriedades em um ViewModel:

```csharp
public class HomePageViewModel
{
  public string HeaderText { get { return "Control Template Demo App"; } }
  public string FooterText { get { return "(c) Xamarin 2016"; } }
}
```

As propriedades `HeaderText` e `FooterText` de ViewModel podem receber associações, conforme mostrado no exemplo de código XAML a seguir:

```xaml
<ContentPage xmlns:local="clr-namespace:SimpleTheme;assembly=SimpleTheme"
             HeaderText="{Binding HeaderText}" FooterText="{Binding FooterText}" ...>
    <ContentPage.BindingContext>
        <local:HomePageViewModel />
    </ContentPage.BindingContext>
    <ContentView ControlTemplate="{StaticResource TealTemplate}" ...>
    ...
    </ContentView>
</ContentPage>
```

As propriedades associáveis `HeaderText` e `FooterText` são associadas às propriedades `HomePageViewModel.HeaderText` e `HomePageViewModel.FooterText`, devido à configuração de [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) como uma instância da classe `HomePageViewModel`. De modo geral, isso faz com que propriedades de controle no [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) sejam associadas às instâncias de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) no [`ContentPage`](xref:Xamarin.Forms.ContentPage), que, por sua vez, são associadas às propriedades de ViewModel.

O código C# equivalente é mostrado no exemplo de código a seguir:

```csharp
public class HomePageCS : ContentPage
{
  ...
  public HomePageCS ()
  {
    BindingContext = new HomePageViewModel ();
    this.SetBinding (HeaderTextProperty, "HeaderText");
    this.SetBinding (FooterTextProperty, "FooterText");
    ...
  }
}
```

Você também pode estabelecer a associação com as propriedades do modelo de exibição diretamente, para que não seja necessário declarar `BindableProperty`s para `HeaderText` e `FooterText` no `ContentPage`, associando o modelo de controle a Parent.BindingContext._PropertyName_, por exemplo:

```xaml
<ControlTemplate x:Key="TealTemplate">
  <Grid>
    ...
    <Label Text="{TemplateBinding Parent.BindingContext.HeaderText}" ... />
    ...
    <Label Text="{TemplateBinding Parent.BindingContext.FooterText}" ... />
  </Grid>
</ControlTemplate>
```

Para obter mais informações sobre a associação de dados para ViewModels, confira [De associações de dados a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar associações de modelo para realizar a associação de dados de um modelo de controle. Associações de modelo permitem que os controles em um modelo de controle associem dados a propriedades públicas, permitindo que os valores de propriedade em controles no modelo de controle sejam alterados facilmente.

## <a name="related-links"></a>Links relacionados

- [Conceitos básicos da associação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [De associações de dados a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
- [Tema simples com associação de modelo (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplates-simplethemewithtemplatebinding)
- [Tema simples com associação de modelo e ViewModel (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplates-simplethemewithtemplatebindingandviewmodel)
- [TemplateBinding](xref:Xamarin.Forms.TemplateBinding)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentView](xref:Xamarin.Forms.ContentView)
