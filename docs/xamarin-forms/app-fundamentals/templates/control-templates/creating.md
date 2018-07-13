---
title: Criando um ControlTemplate
description: Modelos de controle podem ser definidos no nível do aplicativo ou no nível da página. Este artigo demonstra como criar e consumir modelos de controle.
ms.prod: xamarin
ms.assetid: A9AEB052-FBF5-4589-9BD4-6D6F62BED7F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: b83668f6836b1d5d98f67592bf3e2b01e7319edc
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998173"
---
# <a name="creating-a-controltemplate"></a>Criando um ControlTemplate

_Modelos de controle podem ser definidos no nível do aplicativo ou no nível da página. Este artigo demonstra como criar e consumir modelos de controle._

## <a name="creating-a-controltemplate-in-xaml"></a>Criando um ControlTemplate no XAML

Para definir um [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) no nível do aplicativo, uma [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) deve ser adicionado para o `App` classe. Por padrão, todos os aplicativos xamarin. Forms criados de um modelo usam a **App** classe para implementar o [ `Application` ](xref:Xamarin.Forms.Application) subclasse. Para declarar uma `ControlTemplate` no nível do aplicativo, na caixa de diálogo `ResourceDictionary` usando XAML, o padrão **App** classe deve ser substituído por um XAML **aplicativo** classe e code-behind associado, como como mostrado no exemplo de código a seguir:

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

Cada [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) instância é criada como um objeto reutilizável em uma [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary).  Isso é feito dando cada declaração de uma única `x:Key` atributo, que fornece a ele uma chave descritiva no `ResourceDictionary`.

O exemplo de código a seguir mostra associado `App` de lógica:

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

Bem como a configuração de [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) propriedade, o code-behind também deve chamar o `InitializeComponent` método para carregar e analisar o XAML associado.

O seguinte exemplo de código mostra uma [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) aplicando o `TealTemplate` para o [ `ContentView` ](xref:Xamarin.Forms.ContentView):

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

O `TealTemplate` é atribuído para o [ `ContentView.ControlTemplate` ](xref:Xamarin.Forms.TemplatedView.ControlTemplate) propriedade usando o `StaticResource` extensão de marcação. O [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) estiver definida como um [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) que define o conteúdo a ser exibido no [ `ContentPage` ](xref:Xamarin.Forms.ContentPage). Esse conteúdo será exibido quando o [ `ContentPresenter` ](xref:Xamarin.Forms.ContentPresenter) contidos no `TealTemplate`. Isso resulta na exibição mostrada nas capturas de tela seguir:

![](creating-images/teal-theme.png "Modelo de controle azul-petróleo")

### <a name="re-theming-an-application-at-runtime"></a>Um aplicativo em tempo de execução de re-temas

Clicar a **alterar o tema** botão executa o `OnButtonClicked` método, que é mostrado no exemplo de código a seguir:

```csharp
void OnButtonClicked (object sender, EventArgs e)
{
  originalTemplate = !originalTemplate;
  contentView.ControlTemplate = (originalTemplate) ? tealTemplate : aquaTemplate;
}
```

Esse método substitui o Active Directory [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) instância com a alternativa `ControlTemplate` instância, resultando na seguinte captura de tela:

![](creating-images/aqua-theme.png "Modelo de controle Aqua")

> [!NOTE]
> Em um `ContentPage`, o `Content` propriedade pode ser atribuída e o `ControlTemplate` propriedade também pode ser definida. Quando isso ocorre, se o `ControlTemplate` contém uma `ContentPresenter` da instância, o conteúdo atribuído ao `Content` propriedade será apresentada pela `ContentPresenter` dentro a `ControlTemplate`.

### <a name="setting-a-controltemplate-with-a-style"></a>Definindo um ControlTemplate com um estilo

Um [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) também podem ser aplicadas por meio de uma [ `Style` ](xref:Xamarin.Forms.Style) expandir ainda mais a capacidade de tema. Isso pode ser obtido com a criação de um *implícita* ou *explícita* estilo para a exibição de destino em um [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)e configuração o `ControlTemplate` propriedade de destino Exibir na [ `Style` ](xref:Xamarin.Forms.Style) instância. O seguinte exemplo de código mostra uma *implícita* estilo que foi adicionado para o nível de aplicativo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

```xaml
<Style TargetType="ContentView">
    <Setter Property="ControlTemplate" Value="{StaticResource TealTemplate}" />
</Style>
```

Porque o [ `Style` ](xref:Xamarin.Forms.Style) instância está *implícita*, ela será aplicada a todos os `ContentView` instâncias no aplicativo. Portanto, não é necessário definir as [ `ContentView.ControlTemplate` ](xref:Xamarin.Forms.TemplatedView.ControlTemplate) propriedade, conforme demonstrado no exemplo de código a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0">
      ...
    </ContentView>
</ContentPage>
```

Para obter mais informações sobre estilos, consulte [estilos](~/xamarin-forms/user-interface/styles/index.md).

### <a name="creating-a-controltemplate-at-page-level"></a>Criando um ControlTemplate no nível de página

Além de criar [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) instâncias no nível do aplicativo, eles também podem ser criados no nível da página, conforme mostrado no exemplo de código a seguir:

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

Ao adicionar um [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) no nível da página, uma [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) é adicionado ao [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)e, em seguida, o `ControlTemplate` instâncias estão incluídas no `ResourceDictionary`.

## <a name="creating-a-controltemplate-in-c35"></a>Criando um ControlTemplate em C&#35;

Para definir um [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) no nível do aplicativo, uma `class` deve ser criado que representa o `ControlTemplate`. A classe deve derivar de [layout](~/xamarin-forms/user-interface/layouts/index.md) que está sendo usado para o modelo, conforme mostrado no exemplo de código a seguir:

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

O `AquaTemplate` classe é idêntico de `TealTemplate` classe, exceto que as diferentes cores são usadas para o [ `BoxView.Color` ](xref:Xamarin.Forms.BoxView.Color) e [ `Label.TextColor` ](xref:Xamarin.Forms.Label.TextColor) propriedades.

O seguinte exemplo de código mostra uma [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) aplicando o `TealTemplate` para o [ `ContentView` ](xref:Xamarin.Forms.ContentView):

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

O [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) instâncias são criadas, especificando o tipo das classes que definem os modelos de controle, o `ControlTemplate` construtor.

O [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) estiver definida como um [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) que define o conteúdo a ser exibido no [ `ContentPage` ](xref:Xamarin.Forms.ContentPage). Esse conteúdo será exibido quando o [ `ContentPresenter` ](xref:Xamarin.Forms.ContentPresenter) contidos no `TealTemplate`. O mesmo mecanismo descrito anteriormente é usado para alterar o tema em tempo de execução para o `AquaTheme`.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar e consumir modelos de controle. Modelos de controle podem ser definidos no nível do aplicativo ou no nível da página.


## <a name="related-links"></a>Links relacionados

- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [Tema simples (amostra)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simpletheme/)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
- [ContentView](xref:Xamarin.Forms.ContentView)
- [Dicionário de recurso](xref:Xamarin.Forms.ResourceDictionary)
