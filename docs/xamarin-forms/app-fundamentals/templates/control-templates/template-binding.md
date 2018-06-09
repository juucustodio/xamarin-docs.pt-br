---
title: Associação de um ControlTemplate xamarin. Forms
description: Associações de modelo permitem associar controles em um modelo de controle de dados para as propriedades públicas, permitindo que os valores de propriedade em controles no modelo de controle a ser alterado facilmente. Este artigo demonstra como usar associações de modelo para executar associação de dados a partir de um modelo de controle.
ms.prod: xamarin
ms.assetid: 794A663C-3A8D-438A-BD02-8E97C919B55F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 99d798ce2c74da0cf7fa0d497128db628a12ead5
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241575"
---
# <a name="binding-from-a-xamarinforms-controltemplate"></a>Associação de um ControlTemplate xamarin. Forms

_Associações de modelo permitem associar controles em um modelo de controle de dados para as propriedades públicas, permitindo que os valores de propriedade em controles no modelo de controle a ser alterado facilmente. Este artigo demonstra como usar associações de modelo para executar associação de dados a partir de um modelo de controle._

Um [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) é usado para associar a propriedade do controle em um modelo de controle a uma propriedade ligável do pai do *destino* modo de exibição que é proprietária do modelo de controle. Por exemplo, em vez de definir o texto exibido por [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instâncias dentro de [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/), você pode usar uma associação de modelo para associar o [ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propriedade associável propriedades que definem o texto a ser exibido.

Um [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) é semelhante a um existente [ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/), exceto que o *fonte* de um `TemplateBinding` é definido automaticamente como o pai do *destino* modo de exibição que é proprietária do modelo de controle. No entanto, observe que usando um `TemplateBinding` fora de um [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) não tem suporte.

## <a name="creating-a-templatebinding-in-xaml"></a>Criando um TemplateBinding em XAML

Em XAML, uma [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) é criado usando o [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TemplateBindingExtension/) extensão de marcação, como demonstrado no exemplo de código a seguir:

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

Em vez de definir o [ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propriedades para texto estático, as propriedades podem usar associações de modelo para associar a propriedades vinculáveis do pai do *destino* exibição que possui o [ `ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/). No entanto, observe que as associações de modelo associar a `Parent.HeaderText` e `Parent.FooterText`, em vez de `HeaderText` e `FooterText`. Isso ocorre porque neste exemplo, as propriedades vinculáveis são definidas em avô do *destino* exibir, em vez de pai, conforme demonstrado no exemplo de código a seguir:

```xaml
<ContentPage ...>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
          ...
    </ContentView>
</ContentPage>
```

O *fonte* do modelo de associação é definida automaticamente para o pai do *destino* exibição que possui o modelo de controle, aqui está o [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/) instância. O modelo de associação usa o [ `Parent` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Parent/) propriedade para retornar o elemento pai do `ContentView` instância, que é o [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) instância. Portanto, usando um [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) no [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) para associar a `Parent.HeaderText` e `Parent.FooterText` localiza as propriedades vinculáveis aos que estão definidas no `ContentPage`, como como demonstrado no exemplo de código a seguir:

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

Isso resulta na exibição mostrada nas capturas de tela seguir:

![](template-binding-images/teal-theme.png "Modelo de controle azul-petróleo usando associações de modelo")

## <a name="creating-a-templatebinding-in-c35"></a>Criando um TemplateBinding em C&#35;

No c#, um [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) é criado usando o `TemplateBinding` construtor, conforme demonstrado no exemplo de código a seguir:

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

Em vez de definir o [ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propriedades para texto estático, as propriedades podem usar associações de modelo para associar a propriedades vinculáveis do pai do *destino* exibição que possui o [ `ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/). A associação de modelo é criada usando o [ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/) método, especificando um [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) instância como o segundo parâmetro. Observe que as associações de modelo associar a `Parent.HeaderText` e `Parent.FooterText`, pois as propriedades vinculáveis são definidas em avô do *destino* exibir, em vez de pai, conforme demonstrado no exemplo de código a seguir:

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

As propriedades vinculáveis são definidas no `ContentPage`, conforme descrito anteriormente.

### <a name="binding-a-bindableproperty-to-a-viewmodel-property"></a>Associando um BindableProperty a uma propriedade de ViewModel

Mencionado anteriormente, um [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/) associa a propriedade do controle em um modelo de controle a uma propriedade ligável do pai do *destino* modo de exibição que é proprietária do modelo de controle. Por sua vez, essas propriedades vinculáveis podem ser associadas às propriedades em ViewModels.

O exemplo de código a seguir define duas propriedades em um ViewModel:

```csharp
public class HomePageViewModel
{
  public string HeaderText { get { return "Control Template Demo App"; } }
  public string FooterText { get { return "(c) Xamarin 2016"; } }
}
```

O `HeaderText` e `FooterText` ViewModel propriedades podem ser vinculadas a, conforme mostrado no exemplo de código XAML a seguir:

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

O `HeaderText` e `FooterText` propriedades vinculáveis estão associadas ao `HomePageViewModel.HeaderText` e `HomePageViewModel.FooterText` propriedades, devido à configuração de [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) a uma instância do `HomePageViewModel` classe. Em geral, isso resulta em Propriedades de controle no [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) associado [ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/) instâncias no [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/), que, por sua vez, associar a Propriedades de ViewModel.

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

Para obter mais informações sobre associação de dados para ViewModels, consulte [de associações de dados para o modelo MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Resumo

Este artigo demonstrado usando associações de modelo para executar associação de dados a partir de um modelo de controle. Associações de modelo permitem associar controles em um modelo de controle de dados para as propriedades públicas, permitindo que os valores de propriedade em controles no modelo de controle a ser alterado facilmente.



## <a name="related-links"></a>Links relacionados

- [Noções básicas de associação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [De associações de dados para o modelo MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
- [Tema simples com o modelo de associação (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebinding/)
- [Tema simples com o modelo de associação e ViewModel (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebindingandviewmodel/)
- [TemplateBinding](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/)
- [ControlTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)
- [ContentView](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)
