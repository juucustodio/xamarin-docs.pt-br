---
title: Xamarin.Forms Configuração da página do Shell
description: A classe Shell define as propriedades anexadas que podem ser usadas para configurar a aparência de páginas em Xamarin.Forms aplicativos Shell. Isso inclui a configuração das cores da página, a desabilitação da barra de navegação, a desabilitação da barra de guias e a exibição dos modos de exibição na barra de navegação.
ms.prod: xamarin
ms.assetid: 3FC2FBD1-C30B-4408-97B2-B04E3A2E4F03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c5be4e600b370c39387302aea42645c8684501ea
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374369"
---
# <a name="no-locxamarinforms-shell-page-configuration"></a>Xamarin.Forms Configuração da página do Shell

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

A `Shell` classe define as propriedades anexadas que podem ser usadas para configurar a aparência de páginas em Xamarin.Forms aplicativos Shell. Isso inclui a definição de cores de página, a definição do modo de apresentação de página, a desabilitação da barra de navegação, a desabilitação da barra de guias e a exibição de exibições na barra de navegação.

## <a name="set-page-colors"></a>Definir as cores da página

A classe do `Shell` define as seguintes propriedades anexadas que podem ser usadas para definir as cores da página em um aplicativo do Shell:

- `BackgroundColor`, do tipo `Color`, que define a cor da tela de fundo no cromado Shell. A cor não preencherá por trás do conteúdo Shell.
- `DisabledColor`, do tipo `Color`, que define a cor da tonalidade do texto e dos ícones que estão desabilitados.
- `ForegroundColor`, do tipo `Color`, que define a cor da tonalidade do texto e dos ícones.
- `TitleColor`, do tipo `Color`, que define a cor usada no título da página atual.
- `UnselectedColor`, do tipo `Color`, que define a cor usada no texto não selecionado e nos ícones no cromado Shell.

Todas essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados e estilizadas usando estilos XAML. Além disso, as propriedades podem ser definidas usando as folhas de estilo em cascata (CSS). Para obter mais informações, consulte [ Xamarin.Forms Propriedades específicas do Shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties).

> [!NOTE]
> Também há propriedades que permitem definir as cores das guias. Para saber mais, confira [Aparência da guia](tabs.md#tab-appearance).

O XAML a seguir mostra como configurar propriedades de cor em uma classe do `Shell` na subclasse:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       x:Class="Xaminals.AppShell"
       BackgroundColor="#455A64"
       ForegroundColor="White"
       TitleColor="White"
       DisabledColor="#B4FFFFFF"
       UnselectedColor="#95FFFFFF">

</Shell>
```

Neste exemplo, os valores de cor serão aplicados a todas as páginas no aplicativo do Shell, a menos que sejam substituídos no nível da página.

Como as propriedades de cor são propriedades anexadas, elas também podem ser definidas em páginas individuais, para definir as cores nessa página:

```xaml
<ContentPage ...
             Shell.BackgroundColor="Gray"
             Shell.ForegroundColor="White"
             Shell.TitleColor="Blue"
             Shell.DisabledColor="#95FFFFFF"
             Shell.UnselectedColor="#B4FFFFFF">

</ContentPage>
```

Como alternativa, as propriedades de cor podem ser definidas com um estilo XAML:

```xaml
<Style x:Key="DomesticShell"
       TargetType="Element" >
    <Setter Property="Shell.BackgroundColor"
            Value="#039BE6" />
    <Setter Property="Shell.ForegroundColor"
            Value="White" />
    <Setter Property="Shell.TitleColor"
            Value="White" />
    <Setter Property="Shell.DisabledColor"
            Value="#B4FFFFFF" />
    <Setter Property="Shell.UnselectedColor"
            Value="#95FFFFFF" />
</Style>
```

Para obter mais informações sobre estilos XAML, consulte [estilizando Xamarin.Forms aplicativos usando estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

## <a name="set-page-presentation-mode"></a>Definir modo de apresentação de página

Por padrão, uma pequena animação de navegação ocorre quando uma página é navegada com o `GoToAsync` método. No entanto, esse comportamento pode ser alterado definindo a `Shell.PresentationMode` Propriedade anexada em um [`ContentPage`](xref:Xamarin.Forms.ContentPage) para um dos `PresentationMode` membros da enumeração:

- `NotAnimated` indica que a página será exibida sem uma animação de navegação.
- `Animated` indica que a página será exibida com uma animação de navegação. Esse é o valor padrão da `Shell.PresentationMode` Propriedade anexada.
- `Modal` indica que a página será exibida como uma página modal.
- `ModalAnimated` indica que a página será exibida como uma página modal, com uma animação de navegação.
- `ModalNotAnimated` indica que a página será exibida como uma página modal, sem uma animação de navegação.

> [!IMPORTANT]
> O `PresentationMode` tipo é uma enumeração de sinalizadores. Isso significa que uma combinação de membros de enumeração pode ser aplicada no código. No entanto, para facilitar o uso em XAML, o `ModalAnimated` membro é uma combinação `Animated` dos `Modal` Membros e, e o `ModalNotAnimated` membro é uma combinação dos `NotAnimated` `Modal` Membros e. Para obter mais informações sobre enumerações de sinalizador, consulte [tipos de enumeração como sinalizadores de bits](/dotnet/csharp/language-reference/builtin-types/enum#enumeration-types-as-bit-flags).

O exemplo XAML a seguir define a `Shell.PresentationMode` Propriedade anexada em um [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

```xaml
<ContentPage ...
             Shell.PresentationMode="Modal">
    ...             
</ContentPage>
```

Neste exemplo, o [`ContentPage`](xref:Xamarin.Forms.ContentPage) é definido para ser exibido como uma página modal, quando a página é navegada com o `GoToAsync` método.

## <a name="enable-navigation-bar-shadow"></a>Habilitar sombra da barra de navegação

A `Shell` classe define a `NavBarHasShadow` Propriedade anexada, do tipo `bool` , que controla se a barra de navegação tem uma sombra. Por padrão, o valor da propriedade é `false` no Ios e `true` no Android.

Embora essa propriedade possa ser definida em um objeto de subclasse `Shell` , ela também pode ser definida em todas as páginas que desejam habilitar a sombra da barra de navegação. Por exemplo, o XAML a seguir mostra a habilitação da sombra da barra de navegação de a [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

```xaml
<ContentPage ...
             Shell.NavBarHasShadow="true">
    ...
</ContentPage>
```

Isso resulta na habilitação da sombra da barra de navegação.

## <a name="disable-the-navigation-bar"></a>Desabilitar a barra de navegação

A classe `Shell` define a propriedade anexada `NavBarIsVisible`, do tipo `bool`, que controla se a barra de navegação deve estar visível quando uma página é apresentada. Por padrão, o valor da propriedade é `true`.

Embora essa propriedade possa ser definida em um objeto `Shell` na subclasse, ela normalmente é definida em todas as páginas que desejam tornar a barra de navegação invisível. Por exemplo, o XAML a seguir mostra a desabilitação da barra de navegação de um [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

```xaml
<ContentPage ...
             Shell.NavBarIsVisible="false">
    ...
</ContentPage>
```

Isso torna o painel de navegação invisível quando a página é apresentada:

![Captura de tela da página do shell com uma barra de navegação invisível, no iOS e no Android](configuration-images/navigationbar-invisible.png "Página do shell com barra de navegação invisível")

## <a name="disable-the-tab-bar"></a>Desabilitar a barra de guias

A classe `Shell` define a propriedade anexada `TabBarIsVisible`, do tipo `bool`, que controla se a barra de guias deve estar visível quando uma página é apresentada. Por padrão, o valor da propriedade é `true`.

Embora essa propriedade possa ser definida em um objeto `Shell` na subclasse, ela normalmente é definida em todas as páginas que desejam tornar a barra de guias invisível. Por exemplo, o XAML a seguir mostra a desabilitação da barra de guias de um [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

```xaml
<ContentPage ...
             Shell.TabBarIsVisible="false">
    ...
</ContentPage>
```

Isso torna o painel de guias invisível quando a página é apresentada:

![Captura de tela da página do shell com uma barra de guias invisível, no iOS e no Android](configuration-images/tabbar-invisible.png "Página do shell com barra de guias invisível")

## <a name="display-views-in-the-navigation-bar"></a>Exibir modos de exibição na barra de navegação

A `Shell` classe define a `TitleView` Propriedade anexada, do tipo `View` , que permite que qualquer Xamarin.Forms [`View`](xref:Xamarin.Forms.View) seja exibido na barra de navegação.

Embora essa propriedade possa ser definida em um objeto `Shell` na subclasse, ela também pode ser definida em todas as páginas que desejam exibir e visualizar a barra de navegação. Por exemplo, o XAML a seguir mostra a exibição de um [`Image`](xref:Xamarin.Forms.Image) na barra de navegação de um [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

```xaml
<ContentPage ...>
    <Shell.TitleView>
        <Image Source="xamarin_logo.png"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Shell.TitleView>
    ...
</ContentPage>
```

Isso faz com que uma imagem seja exibido na barra de navegação na página:

![Captura de tela da página do shell com uma exibição de título, no iOS e no Android](configuration-images/titleview.png "Página do shell com um modo de exibição de título")

> [!IMPORTANT]
> Se a barra de navegação for definida como invisível, com a propriedade anexada `NavBarIsVisible`, a exibição de título não será exibida.

Muitas exibições não aparecerão na barra de navegação, a menos que o tamanho da exibição seja especificado com as [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) Propriedades e, ou o local da exibição seja especificado com as [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriedades e.

Como a [`Layout`](xref:Xamarin.Forms.Layout) classe deriva da [`View`](xref:Xamarin.Forms.View) classe, a `TitleView` Propriedade anexada pode ser definida para exibir uma classe de layout que contém várias exibições. Da mesma forma, como a [`ContentView`](xref:Xamarin.Forms.ContentView) classe basicamente deriva da [`View`](xref:Xamarin.Forms.View) classe, a `TitleView` Propriedade anexada pode ser definida para exibir um `ContentView` que contém uma única exibição.

## <a name="page-visibility"></a>Visibilidade da página

O Shell respeita a visibilidade da página, definida com a [`IsVisible`](xref:Xamarin.Forms.VisualElement.IsVisible) propriedade. Portanto, quando a propriedade de uma página `IsVisible` é definida como `false` ela não estará visível no aplicativo de shell e não será possível navegar até ela.

## <a name="related-links"></a>Links relacionados

- [Xaminals (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Estilizando Xamarin.Forms aplicativos usando estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Xamarin.Forms Propriedades específicas do Shell CSS](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)