---
title: Associação a partir de um ControlTemplate de xamarin. Forms
description: Associações do modelo permitem que os controles em um modelo de controle aos dados associar a propriedades públicas, permitindo que os valores de propriedade em controles no modelo de controle a ser alterado facilmente. Este artigo demonstra como usar associações de modelo para realizar a associação de dados de um modelo de controle.
ms.prod: xamarin
ms.assetid: 794A663C-3A8D-438A-BD02-8E97C919B55F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 86de2ad6009365b3debbe1a2310651002b023219
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995555"
---
# <a name="binding-from-a-xamarinforms-controltemplate"></a>Associação a partir de um ControlTemplate de xamarin. Forms

_Associações do modelo permitem que os controles em um modelo de controle aos dados associar a propriedades públicas, permitindo que os valores de propriedade em controles no modelo de controle a ser alterado facilmente. Este artigo demonstra como usar associações de modelo para realizar a associação de dados de um modelo de controle._

Um [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) é usada para associar a propriedade de um controle em um modelo de controle a uma propriedade associável no pai do *destino* modo de exibição que é proprietária do modelo de controle. Por exemplo, em vez de definir o texto exibido pelo [ `Label` ](xref:Xamarin.Forms.Label) instâncias dentro de [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate), você poderia usar uma associação de modelo para associar o [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) propriedade para propriedades vinculáveis que definem o texto a ser exibido.

Um [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) é semelhante a um existente [ `Binding` ](xref:Xamarin.Forms.Binding), exceto que o *origem* de um `TemplateBinding` automaticamente sempre é definido como o pai das *destino* modo de exibição que é proprietária do modelo de controle. No entanto, observe que o uso de um `TemplateBinding` fora de um [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) não tem suporte.

## <a name="creating-a-templatebinding-in-xaml"></a>Criando um TemplateBinding no XAML

No XAML, uma [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) é criado usando o [ `TemplateBinding` ](xref:Xamarin.Forms.Xaml.TemplateBindingExtension) extensão de marcação, conforme demonstrado no exemplo de código a seguir:

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

Em vez de definir a [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) propriedades em texto estático, as propriedades podem usar associações de modelo para associar a propriedades associáveis no pai do *destino* exibição que possui o [ `ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). No entanto, observe que as associações de modelo se associar aos `Parent.HeaderText` e `Parent.FooterText`, em vez de `HeaderText` e `FooterText`. Isso ocorre porque, neste exemplo, as propriedades vinculáveis são definidas no avô do *destino* exibir, em vez de pai, conforme demonstrado no exemplo de código a seguir:

```xaml
<ContentPage ...>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
          ...
    </ContentView>
</ContentPage>
```

O *fonte* do modelo de associação é definida automaticamente para o pai do *destino* modo de exibição que possui o modelo de controle, aqui está o [ `ContentView` ](xref:Xamarin.Forms.ContentView) instância. O modelo de ligação usa a [ `Parent` ](xref:Xamarin.Forms.Element.Parent) propriedade para retornar o elemento pai do `ContentView` instância, que é o [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) instância. Portanto, usando um [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) no [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) para associar a `Parent.HeaderText` e `Parent.FooterText` localiza as propriedades vinculáveis que estão definidas no `ContentPage`, como como demonstrado no exemplo de código a seguir:

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

No c#, uma [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) é criado usando o `TemplateBinding` construtor, conforme demonstrado no exemplo de código a seguir:

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

Em vez de definir a [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) propriedades em texto estático, as propriedades podem usar associações de modelo para associar a propriedades associáveis no pai do *destino* exibição que possui o [ `ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). A associação de modelo é criada usando o [ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) método, especificando um [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) instância como o segundo parâmetro. Observe que as associações de modelo é associar a `Parent.HeaderText` e `Parent.FooterText`, porque as propriedades vinculáveis são definidas no avô do *destino* exibir, em vez de pai, conforme demonstrado no exemplo de código a seguir:

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

As propriedades vinculáveis são definidas na `ContentPage`, conforme descrito anteriormente.

### <a name="binding-a-bindableproperty-to-a-viewmodel-property"></a>Associando um BindableProperty a uma propriedade de ViewModel

Conforme mencionado anteriormente, uma [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding) associa uma propriedade do controle em um modelo de controle a uma propriedade associável no pai do *destino* modo de exibição que é proprietária do modelo de controle. Por sua vez, essas propriedades vinculáveis poderão estar associadas a propriedades em ViewModels.

O exemplo de código a seguir define duas propriedades em um ViewModel:

```csharp
public class HomePageViewModel
{
  public string HeaderText { get { return "Control Template Demo App"; } }
  public string FooterText { get { return "(c) Xamarin 2016"; } }
}
```

O `HeaderText` e `FooterText` propriedades do ViewModel podem ser associadas a, conforme mostrado no exemplo de código XAML a seguir:

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

O `HeaderText` e `FooterText` propriedades vinculáveis são associadas para o `HomePageViewModel.HeaderText` e `HomePageViewModel.FooterText` propriedades, devido à configuração o [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) a uma instância da `HomePageViewModel` classe. Em geral, isso resulta em Propriedades de controle na [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) sendo associado ao [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instâncias o [ `ContentPage` ](xref:Xamarin.Forms.ContentPage), que por sua vez de associar a Propriedades do ViewModel.

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

Para obter mais informações sobre associação de dados para ViewModels, consulte [de associações de dados a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md).

## <a name="summary"></a>Resumo

Este artigo demonstrado usando associações de modelo para realizar a associação de dados de um modelo de controle. Associações do modelo permitem que os controles em um modelo de controle aos dados associar a propriedades públicas, permitindo que os valores de propriedade em controles no modelo de controle a ser alterado facilmente.



## <a name="related-links"></a>Links relacionados

- [Noções básicas de vinculação de dados](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [De associações de dados a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
- [Tema simples com o modelo de associação (amostra)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebinding/)
- [Tema simples com o modelo de associação e o ViewModel (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebindingandviewmodel/)
- [TemplateBinding](xref:Xamarin.Forms.TemplateBinding)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentView](xref:Xamarin.Forms.ContentView)
