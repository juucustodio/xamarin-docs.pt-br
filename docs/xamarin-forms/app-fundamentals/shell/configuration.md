---
title: Configuração da Página do Shell do Xamarin.Forms
description: A classe do Shell define as propriedades anexadas que podem ser usadas para configurar a aparência das páginas em aplicativos do Shell do Xamarin.Forms. Isso inclui a configuração das cores da página, a desabilitação da barra de navegação, a desabilitação da barra de guias e a exibição dos modos de exibição na barra de navegação.
ms.prod: xamarin
ms.assetid: 3FC2FBD1-C30B-4408-97B2-B04E3A2E4F03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.openlocfilehash: 022aa9f1aeb2961d98b52747441e875bd89a584a
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739324"
---
# <a name="xamarinforms-shell-page-configuration"></a>Configuração da Página do Shell do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

A classe do `Shell` define as propriedades anexadas que podem ser usadas para configurar a aparência das páginas em aplicativos do Shell do Xamarin.Forms. Isso inclui a configuração das cores da página, a desabilitação da barra de navegação, a desabilitação da barra de guias e a exibição dos modos de exibição na barra de navegação.

## <a name="set-page-colors"></a>Definir as cores da página

A classe do `Shell` define as seguintes propriedades anexadas que podem ser usadas para definir as cores da página em um aplicativo do Shell:

- `BackgroundColor`, do tipo `Color`, que define a cor da tela de fundo no cromado Shell. A cor não preencherá por trás do conteúdo Shell.
- `DisabledColor`, do tipo `Color`, que define a cor da tonalidade do texto e dos ícones que estão desabilitados.
- `ForegroundColor`, do tipo `Color`, que define a cor da tonalidade do texto e dos ícones.
- `TitleColor`, do tipo `Color`, que define a cor usada no título da página atual.
- `UnselectedColor`, do tipo `Color`, que define a cor usada no texto não selecionado e nos ícones no cromado Shell.

Todas essas propriedades são apoiadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), o que significa que essas propriedades podem ser o destino de vinculações de dados e estilizadas usando os estilos XAML. Além disso, as propriedades podem ser definidas usando as folhas de estilo em cascata (CSS). Saiba mais em [propriedades específicas do Shell do Xamarin.Forms](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties).

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

Confira mais informações sobre os estilos XAML em [Aplicar estilo a aplicativos do Xamarin.Forms usando os estilos do XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

## <a name="disable-the-navigation-bar"></a>Desabilitar a barra de navegação

A classe `Shell` define a propriedade anexada `NavBarIsVisible`, do tipo `bool`, que controla se a barra de navegação deve estar visível quando uma página é apresentada. Por padrão, o valor da propriedade é `true`.

Embora essa propriedade possa ser definida em um objeto `Shell` na subclasse, ela normalmente é definida em todas as páginas que desejam tornar a barra de navegação invisível. Por exemplo, o XAML a seguir mostra como desabilitar a barra de navegação de uma [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage ...
             Shell.NavBarIsVisible="false">
    ...
</ContentPage>
```

Isso torna o painel de navegação invisível quando a página é apresentada:

![Captura de tela da página do Shell com uma barra de navegação invisível, no iOS e no Android](configuration-images/navigationbar-invisible.png "Página do Shell com a barra de navegação invisível")

## <a name="disable-the-tab-bar"></a>Desabilitar a barra de guias

A classe `Shell` define a propriedade anexada `TabBarIsVisible`, do tipo `bool`, que controla se a barra de guias deve estar visível quando uma página é apresentada. Por padrão, o valor da propriedade é `true`.

Embora essa propriedade possa ser definida em um objeto `Shell` na subclasse, ela normalmente é definida em todas as páginas que desejam tornar a barra de guias invisível. Por exemplo, o XAML a seguir mostra como desabilitar a barra de guias de uma [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage ...
             Shell.TabBarIsVisible="false">
    ...
</ContentPage>
```

Isso torna o painel de guias invisível quando a página é apresentada:

![Captura de tela da página do Shell com uma barra de guias invisível, no iOS e no Android](configuration-images/tabbar-invisible.png "Página do Shell com a barra de guias invisível")

## <a name="display-views-in-the-navigation-bar"></a>Exibir modos de exibição na barra de navegação

A classe `Shell` define a propriedade anexada `TitleView`, do tipo `View`, que permite que qualquer [`View`](xref:Xamarin.Forms.View) do Xamarin.Forms seja exibido na barra de navegação.

Embora essa propriedade possa ser definida em um objeto `Shell` na subclasse, ela também pode ser definida em todas as páginas que desejam exibir e visualizar a barra de navegação. Por exemplo, o XAML a seguir mostra como exibir uma [`Image`](xref:Xamarin.Forms.Image) na barra de navegação de uma [`ContentPage`](xref:Xamarin.Forms.ContentPage):

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

![Captura de tela da página do Shell com uma visualização de título, no iOS e no Android](configuration-images/titleview.png "Página do Shell com uma exibição de título")

> [!IMPORTANT]
> Se a barra de navegação for definida como invisível, com a propriedade anexada `NavBarIsVisible`, a exibição de título não será exibida.

Muitos modos de exibição não aparecerão na barra de navegação a menos que o tamanho do modo de exibição seja especificado com as propriedades [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest), ou que o local da visualização seja especificado com as propriedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions).

Como a classe [`Layout`](xref:Xamarin.Forms.Layout) é derivada da classe [`View`](xref:Xamarin.Forms.View), a propriedade anexada `TitleView` pode ser definida para exibir uma classe de layout que contém vários modos de exibição. Da mesma forma, como a classe [`ContentView`](xref:Xamarin.Forms.ContentView) é derivada da classe [`View`](xref:Xamarin.Forms.View), a propriedade anexada `TitleView` pode ser definida para exibir uma `ContentView` que contém um único modos de exibição.

## <a name="related-links"></a>Links relacionados

- [Xaminals (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Aplicar estilo a aplicativos do Xamarin.Forms usando os estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
- [Propriedades específicas do CSS Shell do Xamarin.Forms](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
