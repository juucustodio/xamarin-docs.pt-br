---
title: Criando um ControlTemplate
description: "Modelos de controle podem ser definidos no nível do aplicativo ou nível de página. Este artigo demonstra como criar e consumir os modelos de controle."
ms.topic: article
ms.prod: xamarin
ms.assetid: A9AEB052-FBF5-4589-9BD4-6D6F62BED7F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: e09ac502298296277d9264bcd18f1ce1cbbf0c55
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="creating-a-controltemplate"></a>Criando um ControlTemplate

_Modelos de controle podem ser definidos no nível do aplicativo ou nível de página. Este artigo demonstra como criar e consumir os modelos de controle._

## <a name="creating-a-controltemplate-in-xaml"></a>Criando um ControlTemplate em XAML

Para definir um [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) no nível do aplicativo, um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) devem ser adicionados para o `App` classe. Por padrão, todos os aplicativos xamarin. Forms criados a partir de um modelo usam a **aplicativo** classe para implementar o [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/) subclasse. Para declarar um `ControlTemplate` no nível do aplicativo, o aplicativo `ResourceDictionary` usando XAML, o padrão **aplicativo** classe deve ser substituída por uma XAML **aplicativo** classe e o code-behind associado, como como mostrado no exemplo de código a seguir:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.App">
    <Application.Resources>
        <ResourceDictionary>
            <ControlTemplate x:Key="TealTemplate">
                <Grid>
                    ...
                    <BoxView ... />
                    <Label Text="Control Template Demo App"
                           TextColor="White"
                           VerticalOptions="Center" ... />
                    <ContentPresenter ... />
                    <BoxView Color="Teal" ... />
                    <Label Text="(c) Xamarin 2016"
                           TextColor="White"
                           VerticalOptions="Center" ... />
                </Grid>
            </ControlTemplate>
            <ControlTemplate x:Key="AquaTemplate">
                ...
            </ControlTemplate>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Cada [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) instância é criada como um objeto reutilizável em um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/).  Isso é feito fornecendo uma única declaração de cada `x:Key` atributo, que fornece uma chave descritiva a `ResourceDictionary`.

O exemplo de código a seguir mostra o associado `App` por trás do código:

```csharp
public partial class App : Application
{
  public App ()
  {
    InitializeComponent ();
    MainPage = new HomePage ();
  }
}
```

Bem como a configuração de [ `MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) propriedade, o code-behind também deve chamar o `InitializeComponent` método para carregar e analisar o XAML associado.

O seguinte exemplo de código mostra uma [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) aplicando o `TealTemplate` para o [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0"
                 ControlTemplate="{StaticResource TealTemplate}">
        <StackLayout VerticalOptions="CenterAndExpand">
            <Label Text="Welcome to the app!" HorizontalOptions="Center" />
            <Button Text="Change Theme" Clicked="OnButtonClicked" />
        </StackLayout>
    </ContentView>
</ContentPage>
```

O `TealTemplate` é atribuído para o [ `ContentView.ControlTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TemplatedView.ControlTemplate/) propriedade usando o `StaticResource` extensão de marcação. O [ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) está definida como um [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) que define o conteúdo a ser exibido no [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/). Esse conteúdo será exibido quando o [ `ContentPresenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/) contidos no `TealTemplate`. Isso resulta na exibição mostrada nas capturas de tela seguir:

![](creating-images/teal-theme.png "Modelo de controle azul-petróleo")

### <a name="re-theming-an-application-at-runtime"></a>Um aplicativo em tempo de execução de re-temas

Clicando o **alterar o tema** botão executa o `OnButtonClicked` método, que é mostrado no exemplo de código a seguir:

```csharp
void OnButtonClicked (object sender, EventArgs e)
{
  originalTemplate = !originalTemplate;
  contentView.ControlTemplate = (originalTemplate) ? tealTemplate : aquaTemplate;
}
```

Esse método substitui ativo [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) instância com a alternativa `ControlTemplate` instância, resultando na seguinte captura de tela:

![](creating-images/aqua-theme.png "Modelo de controle de água")

> [!NOTE]
> **Observação**: em um `ContentPage`, o `Content` propriedade pode ser atribuída e o `ControlTemplate` propriedade também pode ser definida. Quando isso ocorre, se o `ControlTemplate` contém um `ContentPresenter` instância, o conteúdo atribuído ao `Content` propriedade será apresentada pelo `ContentPresenter` dentro de `ControlTemplate`.

### <a name="setting-a-controltemplate-with-a-style"></a>Definindo um ControlTemplate com um estilo

Um [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) também podem ser aplicadas por meio de um [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) para expandir a capacidade de tema. Isso pode ser feito com a criação de um *implícita* ou *explícita* estilo para a exibição de destino em um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)e configuração o `ControlTemplate` propriedade de destino Exibir no [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instância. O seguinte exemplo de código mostra uma *implícita* estilo que foi adicionado ao nível do aplicativo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/):

```xaml
<Style TargetType="ContentView">
    <Setter Property="ControlTemplate" Value="{StaticResource TealTemplate}" />
</Style>
```

Porque o [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instância é *implícita*, ela será aplicada a todos os `ContentView` instâncias do aplicativo. Portanto, não é necessário definir o [ `ContentView.ControlTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TemplatedView.ControlTemplate/) propriedade, conforme demonstrado no exemplo de código a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0">
      ...
    </ContentView>
</ContentPage>
```

Para obter mais informações sobre estilos, consulte [estilos](~/xamarin-forms/user-interface/styles/index.md).

### <a name="creating-a-controltemplate-at-page-level"></a>Criando um ControlTemplate no nível de página

Além de criar [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) instâncias no nível do aplicativo, também podem ser criadas no nível da página, conforme mostrado no exemplo de código a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentPage.Resources>
        <ResourceDictionary>
            <ControlTemplate x:Key="TealTemplate">
                ...
            </ControlTemplate>
            <ControlTemplate x:Key="AquaTemplate">
                ...
            </ControlTemplate>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
        ...
    </ContentView>
</ContentPage>
```

Ao adicionar um [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) no nível da página, um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) é adicionado ao [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)e, em seguida, o `ControlTemplate` instâncias são incluídas no `ResourceDictionary`.

## <a name="creating-a-controltemplate-in-c35"></a>Criando um ControlTemplate C &#35;

Para definir um [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) no nível do aplicativo, um `class` devem ser criados que representa o `ControlTemplate`. A classe deve derivar do [layout](~/xamarin-forms/user-interface/layouts/index.md) que está sendo usado para o modelo, conforme mostrado no exemplo de código a seguir:

```csharp
class TealTemplate : Grid
{
  public TealTemplate ()
  {
    ...
    var contentPresenter = new ContentPresenter ();
    Children.Add (contentPresenter, 0, 1);
    Grid.SetColumnSpan (contentPresenter, 2);
    ...
  }
}

class AquaTemplate : Grid
{
  ...
}
```

O `AquaTemplate` classe é idêntica de `TealTemplate` classe, exceto que as cores diferentes são usadas para o [ `BoxView.Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/) e [ `Label.TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/) propriedades.

O seguinte exemplo de código mostra uma [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) aplicando o `TealTemplate` para o [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

```csharp
public class HomePageCS : ContentPage
{
  ...
  ControlTemplate tealTemplate = new ControlTemplate (typeof(TealTemplate));
  ControlTemplate aquaTemplate = new ControlTemplate (typeof(AquaTemplate));

  public HomePageCS ()
  {
    var button = new Button { Text = "Change Theme" };
    var contentView = new ContentView {
      Padding = new Thickness (0, 20, 0, 0),
      Content = new StackLayout {
        VerticalOptions = LayoutOptions.CenterAndExpand,
        Children = {
          new Label { Text = "Welcome to the app!", HorizontalOptions = LayoutOptions.Center },
          button
        }
      },
      ControlTemplate = tealTemplate
    };
    ...
    Content = contentView;
  }
}
```

O [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) instâncias são criadas, especificando o tipo das classes que definem os modelos de controle, o `ControlTemplate` construtor.

O [ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) está definida como um [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) que define o conteúdo a ser exibido no [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/). Esse conteúdo será exibido quando o [ `ContentPresenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/) contidos no `TealTemplate`. O mesmo mecanismo descrito anteriormente é usado para alterar o tema em tempo de execução para o `AquaTheme`.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar e consumir os modelos de controle. Modelos de controle podem ser definidos no nível do aplicativo ou nível de página.


## <a name="related-links"></a>Links relacionados

- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [Tema simples (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simpletheme/)
- [ControlTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)
- [ContentPresenter](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)
- [ContentView](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
