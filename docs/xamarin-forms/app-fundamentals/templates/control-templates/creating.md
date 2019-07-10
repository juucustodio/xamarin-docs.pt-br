---
title: Criar um ControlTemplate
description: Modelos de controle podem ser definidos no nível do aplicativo ou no nível da página. Este artigo demonstra como criar e consumir modelos de controle.
ms.prod: xamarin
ms.assetid: A9AEB052-FBF5-4589-9BD4-6D6F62BED7F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/14/2019
ms.openlocfilehash: 0642f304589d30284bc8d3577c0383099e349033
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67513039"
---
# <a name="create-a-controltemplate"></a>Criar um ControlTemplate

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/Templates/ControlTemplates/SimpleTheme/)

_Modelos de controle podem ser definidos no nível do aplicativo ou no nível da página. Este artigo demonstra como criar e consumir modelos de controle._

## <a name="create-a-controltemplate-in-xaml"></a>Criar um ControlTemplate em XAML

Para definir um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) no nível do aplicativo, um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) deve ser adicionado à classe `App`. Por padrão, todos os aplicativos do Xamarin.Forms criados de um modelo usam a classe **App** para implementar a subclasse [`Application`](xref:Xamarin.Forms.Application). Para declarar um `ControlTemplate` no nível do aplicativo, no `ResourceDictionary` do aplicativo usando XAML, a classe **App** padrão deve ser substituída por uma classe **App** XAML e pelo code-behind associado, conforme mostrado no exemplo de código a seguir:

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

Cada instância de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) é criada como um objeto reutilizável em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary).  Isso é feito dando a cada declaração um atributo `x:Key` exclusivo, que fornece a ela uma chave descritiva no `ResourceDictionary`.

O exemplo de código a seguir mostra o code-behind do `App` associado:

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

Além de configurar a propriedade [`MainPage`](xref:Xamarin.Forms.Application.MainPage), o code-behind também deve chamar o método `InitializeComponent` para carregar e analisar o XAML associado.

O exemplo de código a seguir mostra um [`ContentPage`](xref:Xamarin.Forms.ContentPage) aplicando um `TealTemplate` ao [`ContentView`](xref:Xamarin.Forms.ContentView):

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

O `TealTemplate` é atribuído à propriedade [`ContentView.ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) usando a extensão de marcação `StaticResource`. A propriedade [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) é definida como um [`StackLayout`](xref:Xamarin.Forms.StackLayout) que define o conteúdo a ser exibido no [`ContentPage`](xref:Xamarin.Forms.ContentPage). Esse conteúdo será exibido pelo [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) contido no `TealTemplate`. Isso resulta na aparência mostrada nas capturas de tela seguir:

![](creating-images/teal-theme.png "Modelo de controle azul")

### <a name="re-theming-an-application-at-runtime"></a>Redefinindo o tema de um aplicativo em tempo de execução

Clicar no botão **Alterar Tema** executa o método `OnButtonClicked`, que é mostrado no exemplo de código a seguir:

```csharp
void OnButtonClicked (object sender, EventArgs e)
{
  originalTemplate = !originalTemplate;
  contentView.ControlTemplate = (originalTemplate) ? tealTemplate : aquaTemplate;
}
```

Esse método substitui a instância ativa de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) pela instância alternativa de `ControlTemplate`, resultando na seguinte captura de tela:

![](creating-images/aqua-theme.png "Modelo de controle azul claro")

> [!NOTE]
> Em um `ContentPage`, a propriedade `Content` pode ser atribuída e a propriedade `ControlTemplate` também pode ser definida. Quando isso ocorre, se o `ControlTemplate` contiver uma instância de `ContentPresenter`, o conteúdo atribuído à propriedade `Content` será apresentado pelo `ContentPresenter` dentro do `ControlTemplate`.

### <a name="set-a-controltemplate-with-a-style"></a>Definir um ControlTemplate com um estilo

Um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) também podem ser aplicado por meio de um [`Style`](xref:Xamarin.Forms.Style) para expandir ainda mais a funcionalidade de tema. Isso pode ser feito criando um estilo *implícito* ou *explícito* para a exibição de destino em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) e definindo a propriedade `ControlTemplate` da exibição de destino na instância de [`Style`](xref:Xamarin.Forms.Style). O seguinte exemplo de código mostra um estilo *implícito* que foi adicionado ao nível de aplicativo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary):

```xaml
<Style TargetType="ContentView">
    <Setter Property="ControlTemplate" Value="{StaticResource TealTemplate}" />
</Style>
```

Como a instância de [`Style`](xref:Xamarin.Forms.Style) é *implícita*, ela será aplicada a todas as instâncias de `ContentView` no aplicativo. Portanto, não é mais necessário definir a propriedade [`ContentView.ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate), conforme demonstrado no exemplo de código a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0">
      ...
    </ContentView>
</ContentPage>
```

Para mais informações sobre estilos, confira [Estilos](~/xamarin-forms/user-interface/styles/index.md).

### <a name="create-a-controltemplate-at-page-level"></a>Criar um ControlTemplate no nível da página

Além de criar instâncias de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) no nível do aplicativo, elas também podem ser criadas no nível da página, conforme mostrado no exemplo de código a seguir:

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

Ao adicionar um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) no nível da página, um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) é adicionado ao [`ContentPage`](xref:Xamarin.Forms.ContentPage) e, em seguida, as instâncias de `ControlTemplate` são incluídas no `ResourceDictionary`.

## <a name="create-a-controltemplate-in-c35"></a>Criar um ControlTemplate em C&#35;

Para definir um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) no nível do aplicativo, deve ser criado um `class` que representa o `ControlTemplate`. A classe deve derivar do [layout](~/xamarin-forms/user-interface/layouts/index.md) que está sendo usado para o modelo, conforme mostrado no exemplo de código a seguir:

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

A classe `AquaTemplate` é idêntica à classe `TealTemplate`, exceto pelo fato de que cores diferentes são usadas para as propriedades [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) e [`Label.TextColor`](xref:Xamarin.Forms.Label.TextColor).

O exemplo de código a seguir mostra um [`ContentPage`](xref:Xamarin.Forms.ContentPage) aplicando um `TealTemplate` ao [`ContentView`](xref:Xamarin.Forms.ContentView):

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

As instâncias de [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) são criadas especificando o tipo das classes que definem os modelos de controle, no construtor `ControlTemplate`.

A propriedade [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) é definida como um [`StackLayout`](xref:Xamarin.Forms.StackLayout) que define o conteúdo a ser exibido no [`ContentPage`](xref:Xamarin.Forms.ContentPage). Esse conteúdo será exibido pelo [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) contido no `TealTemplate`. O mesmo mecanismo descrito anteriormente é usado para alterar o tema em tempo de execução para o `AquaTheme`.

## <a name="get-a-named-element-from-a-template"></a>Obter um elemento nomeado de um modelo

Os elementos nomeados dentro de um modelo de controle podem ser recuperados depois que o modelo foi instanciado. Isso pode ser obtido com o método `GetTemplateChild`, que retorna o elemento nomeado na árvore visual [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) instanciada.

Depois que um modelo de controle foi instanciado, o método `OnApplyTemplate` do modelo é chamado. Portanto, o método `GetTemplateChild` deve ser chamado da substituição `OnApplyTemplate` em uma página derivada [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage), como um modo de exibição derivado de [`ContentPage`](xref:Xamarin.Forms.ContentPage) ou [`TemplatedView`](xref:Xamarin.Forms.TemplatedView), como um [`ContentView`](xref:Xamarin.Forms.ContentView).

> [!IMPORTANT]
> O método `GetTemplateChild` só deve ser chamado após o método `OnApplyTemplate` ser chamado.

O exemplo a seguir mostra o modelo de controle para um controle personalizado:

```xaml
<controls:MyCustomControl ...>
    <controls:MyCustomControl.ControlTemplate>
         <ControlTemplate>
              <Label x:Name="myLabel" />
         </ControlTemplate>
    <controls:MyCustomControl.ControlTemplate>
</controls:MyCustomControl>
```

O elemento [`Label`](xref:Xamarin.Forms.Label) é chamado e pode ser recuperado no code-behind para o controle personalizado. Isso é feito chamando o método `GetTemplateChild` a partir da substituição `OnApplyTemplate` para o controle personalizado:

```csharp
class MyCustomControl : ContentView
{
    Label myLabel;

    protected override OnApplyTemplate()
    {  
        myLabel = GetTemplateChild("myLabel");
    }
    //...
}
```

Neste exemplo, o objeto [`Label`](xref:Xamarin.Forms.Label) chamado `myLabel` é recuperado. Então `myLabel` pode ser acessado e manipulado pela classe `MyCustomControl`.

## <a name="related-links"></a>Links relacionados

- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [Tema simples (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Templates/ControlTemplates/SimpleTheme/)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
- [ContentView](xref:Xamarin.Forms.ContentView)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
