---
title: Layout do Shell do Xamarin.Forms
description: Depois de um submenu, o próximo nível de navegação em um aplicativo Shell é a barra de guias inferior. Como alternativa, o padrão de navegação de um aplicativo pode começar com as guias inferiores e sem o uso de um submenu. Em ambos os casos, quando uma guia inferior contiver mais de uma página, as páginas poderão ser navegadas pelas guias superiores.
ms.prod: xamarin
ms.assetid: 318D81DB-E456-4E44-B083-36A27DBD9523
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/23/2019
ms.openlocfilehash: 365e580d7d1182cca55a53a714ebb2b9be45ce35
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650738"
---
# <a name="xamarinforms-shell-tabs"></a>Guias do Shell do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)

Quando o padrão de navegação de um aplicativo inclui um submenu, o próximo nível de navegação no aplicativo é a barra de guias inferior. Além disso, quando o submenu é fechado, a barra de guias inferior pode ser considerada o nível principal da navegação.

Como alternativa, o padrão de navegação de um aplicativo pode começar com as guias inferiores e sem o uso de um submenu. Neste cenário, o filho do objeto `Shell` deve ser um objeto `TabBar`, que representa a barra de guias inferior.

> [!NOTE]
> O tipo `TabBar` desabilita o submenu.

Cada objeto `FlyoutItem` ou `TabBar` pode conter um ou mais objetos `Tab`, e cada objeto `Tab` representa uma guia na barra de guias inferior. Cada objeto `Tab` pode conter um ou mais objetos `ShellContent`, e cada objeto `ShellContent` exibirá um único objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage). Quando mais de um objeto `ShellContent` estiver presente em um objeto `Tab`, será possível navegar pelos objetos `ContentPage` por meio das guias principais.

Dentro de cada objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage), é possível navegar para objetos `ContentPage` adicionais. Saiba mais sobre a navegação na [navegação do Shell do Xamarin.Forms](navigation.md).

## <a name="single-page-application"></a>Aplicativo de página única

O aplicativo Shell mais simples é um aplicativo de página única, que pode ser criado pela adição de um único objeto `Tab` a um objeto `TabBar`. Dentro do objeto `Tab`, um objeto `ShellContent` deve ser definido como um objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab>
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

Este exemplo de código resulta no seguinte aplicativo de página única:

[![Captura de tela de um aplicativo Shell de página única no iOS e Android](tabs-images/single-page-app.png "Aplicativo Shell de página única")](tabs-images/single-page-app-large.png#lightbox "Aplicativo Shell de página única")

> [!NOTE]
> A barra de navegação pode ser ocultada, se necessário, definindo a propriedade anexada `Shell.NavBarIsVisible` como `false` no objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage).

O Shell tem operadores de conversão implícita que permitem que a hierarquia visual no Shell seja simplificada, sem a introdução de modos de exibição adicionais na árvore visual. Isso é possível porque um objeto `Shell` na subclasse só pode conter objetos `FlyoutItem` ou um objeto `TabBar`, que só podem conter objetos `Tab`, que só podem conter objetos `ShellContent`. Esses operadores de conversão implícita podem ser usados para remover os objetos de `TabBar`, `Tab` e `ShellContent` do exemplo anterior:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell"
       FlyoutBehavior="Disabled">
    <views:CatsPage />
</Shell>
```

Essa conversão implícita automaticamente encapsula o objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) em um objeto `ShellContent`, que é encapsulado em um objeto `Tab`, que é encapsulado em um objeto `FlyoutItem`. O submenu não é necessário em um aplicativo de página única e, portanto, a propriedade `Shell.FlyoutBehavior` é definida como `Disabled`.

> [!IMPORTANT]
> Em um aplicativo do Shell, cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) que é filho de um objeto `ShellContent` é criado durante a inicialização do aplicativo. Adicionar outros objetos `ShellContent` usando essa abordagem fará com que sejam criadas outras páginas durante a inicialização do aplicativo, o que pode levar a uma experiência ruim de inicialização. No entanto, o Shell também é capaz de criar páginas sob demanda, em resposta à navegação. Saiba mais em [carregamento de páginas eficiente](tabs.md#efficient-page-loading).

## <a name="bottom-tabs"></a>Guias inferiores

Os objetos `Tab` são renderizados como guias inferiores, desde que haja vários objetos `Tab` em um único objeto `TabBar`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Cats"
             Icon="cat.png">
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
        <Tab Title="Dogs"
             Icon="dog.png">
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

Os títulos e os ícones da guia são definidos em cada objeto `Tab` e exibidos nas guias inferiores:

[![Captura de tela de um aplicativo Shell de duas páginas com guias inferiores no iOS e no Android](tabs-images/two-page-app-bottom-tabs.png "Aplicativo Shell de duas páginas com guias inferiores")](tabs-images/two-page-app-bottom-tabs-large.png#lightbox "Aplicativo Shell de duas páginas com guias inferiores")

Como alternativa, os operadores de conversão implícita do Shell podem ser usados para remover os objetos `ShellContent` e `Tab` do exemplo anterior:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <views:CatsPage IconImageSource="cat.png" />
        <views:DogsPage IconImageSource="dog.png" />
    </TabBar>
</Shell>
```

Essa conversão implícita automaticamente encapsula o objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) em um objeto `ShellContent`, que são então encapsulados em um objeto `Tab`.

> [!IMPORTANT]
> Em um aplicativo do Shell, cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) que é filho de um objeto `ShellContent` é criado durante a inicialização do aplicativo. Adicionar outros objetos `ShellContent` usando essa abordagem fará com que sejam criadas outras páginas durante a inicialização do aplicativo, o que pode levar a uma experiência ruim de inicialização. No entanto, o Shell também é capaz de criar páginas sob demanda, em resposta à navegação. Saiba mais em [carregamento de páginas eficiente](tabs.md#efficient-page-loading).

### <a name="tab-class"></a>Classe Tab

A classe `Tab` inclui as seguintes propriedades que controlam a aparência e comportamento da guia:

- `CurrentItem`, do tipo `ShellContent`, o item selecionado.
- `FlyoutDisplayOptions`, do tipo `FlyoutDisplayOptions`, define como o item e seus filhos são exibidos no submenu. O valor padrão é `AsSingleItem`.
- `FlyoutIcon`, do tipo `ImageSource`, define o ícone que será exibido no submenu.
- `Icon`, do tipo `ImageSource`, define o ícone a ser exibido em partes do cromado que não são o submenu.
- `IsChecked`, do tipo `boolean`, define se o item está realçado naquele momento no submenu.
- `IsEnabled`, do tipo `boolean`, define se o item é selecionável no cromado.
- `IsTabStop`, do tipo `bool`, indica se um `Tab` está incluído na navegação por tabulação. Seu valor padrão é `true`, e quando o valor é `false`, o `Tab` é ignorado pela infraestrutura da navegação por tabulação, independentemente de `TabIndex` estar definido.
- `Items`, do tipo `IList<ShellContent>`, define todo o conteúdo dentro de um `Tab`.
- `TabIndex`, do tipo `int`, indica a ordem na qual os objetos de `Tab` recebem o foco quando o usuário navega pelos itens pressionando a tecla Tab. O valor padrão da propriedade é 0.
- `Title`, do tipo `string`, o título a ser exibido na guia na interface do usuário.

## <a name="shell-content"></a>Conteúdo do Shell

O filho de cada objeto `Tab` é um objeto `ShellContent`, cuja propriedade `Content` é definida como [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Cats"
             Icon="cat.png">
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
        <Tab Title="Dogs"
             Icon="dog.png">
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

Dentro de cada objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage), é possível navegar para objetos `ContentPage` adicionais. Saiba mais sobre a navegação na [navegação do Shell do Xamarin.Forms](navigation.md).

### <a name="shellcontent-class"></a>Classe ShellContent

A classe `ShellContent` inclui as seguintes propriedades que controlam o comportamento e a aparência da guia:

- `Content`, do tipo `object`, o conteúdo do `ShellContent`.
- `ContentTemplate`, do tipo `DataTemplate`, o modelo usado para aumentar dinamicamente o conteúdo de `ShellContent`.
- `FlyoutIcon`, do tipo `ImageSource`, define o ícone que será exibido no submenu.
- `Icon`, do tipo `ImageSource`, define o ícone a ser exibido em partes do cromado que não são o submenu.
- `IsChecked`, do tipo `boolean`, define se o item está realçado naquele momento no submenu.
- `IsEnabled`, do tipo `boolean`, define se o item é selecionável no cromado.
- `MenuItems`, do tipo `MenuItemCollection`, são os itens de menu a serem exibidos no submenu quando esse `ShellContent` for a página apresentada.
- `Title`, do tipo `string`, o título a ser exibido na interface do usuário.

Todas essas propriedades são apoiadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), o que significa que essas propriedades podem ser o destino de vinculações de dados.

## <a name="bottom-and-top-tabs"></a>Guias inferior e superior

Quando mais de um objeto `ShellContent` está presente em um objeto `Tab`, uma barra de guia superior é adicionada à guia inferior, por meio da qual é possível navegar pelos objetos [`ContentPage`](xref:Xamarin.Forms.ContentPage):

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Domestic"
             Icon="domestic.png">
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
        <Tab Title="Monkeys"
             Icon="monkey.png">
            <ShellContent>
                <views:MonkeysPage />
            </ShellContent>
        </Tab>
    </TabBar>
</Shell>
```

Isso resulta no layout mostrado nas capturas de tela seguir:

[![Captura de tela de um aplicativo Shell de duas páginas com guias superior e inferior no iOS e no Android](tabs-images/two-page-app-top-tabs.png "Aplicativo Shell de duas páginas com guias superior e inferior")](tabs-images/two-page-app-top-tabs-large.png#lightbox "Aplicativo Shell de duas páginas com guias superior e inferior")

Como alternativa, os operadores de conversão implícita do Shell podem ser usados para remover os objetos `ShellContent` e o segundo objeto `Tab` do exemplo anterior:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <TabBar>
        <Tab Title="Domestic"
             Icon="domestic.png">
            <views:CatsPage />
            <views:DogsPage />
        </Tab>
        <views:MonkeysPage IconImageSource="monkey.png" />
    </TabBar>
</Shell>
```

Essa conversão implícita automaticamente encapsula `MonkeysPage` em um objeto `ShellContent`, que é então encapsulado em um objeto `Tab`. Além disso, `CatsPage` e `DogsPage` são implicitamente encapsulados nos objetos `ShellContent`.

## <a name="efficient-page-loading"></a>Carregamento de página eficiente

Em um aplicativo Shell, cada objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) em um objeto `ShellContent` é criado durante a inicialização do aplicativo, o que pode levar a uma experiência ruim de inicialização. No entanto, o Shell também permite que sejam criadas páginas sob demanda, em resposta à navegação. Isso pode ser feito usando a extensão de marcação `DataTemplate` para converter cada `ContentPage` em um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) e, em seguida, definindo o resultado como o valor da propriedade `ShellContent.ContentTemplate`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">    
    <TabBar>
        <Tab Title="Domestic"
             Icon="paw.png">
            <ShellContent Title="Cats"
                          Icon="cat.png"
                          ContentTemplate="{DataTemplate views:CatsPage}" />
            <ShellContent Title="Dogs"
                          Icon="dog.png"
                          ContentTemplate="{DataTemplate views:DogsPage}" />
        </Tab>
        <ShellContent Title="Monkeys"
                      Icon="monkey.png"
                      ContentTemplate="{DataTemplate views:MonkeysPage}" />
    </TabBar>    
</Shell>
```

Esse XAML cria e exibe `CatsPage`, pois esse é o primeiro item do conteúdo declarado no objeto `Shell` na subclasse. `CatsPage` e `MonkeysPage` podem ser navegados por meio das guias inferiores, e essas páginas são criadas apenas quando o usuário navegar até eles. A vantagem dessa abordagem é que se evita a experiência ruim de inicialização, uma vez que as páginas são criadas sob demanda em resposta à navegação e não na inicialização do aplicativo.

## <a name="tab-appearance"></a>Aparência da guia

A classe `Shell` define as propriedades anexadas a seguir, que controlam a aparência das guias:

- `TabBarBackgroundColor`, do tipo `Color`, que define a cor da tela de fundo da barra de guias. Se a propriedade não for definida, o valor de propriedade `BackgroundColor` será usado.
- `TabBarDisabledColor`, do tipo `Color`, que define a cor desabilitada da barra de guias. Se a propriedade não for definida, o valor de propriedade `DisabledColor` será usado.
- `TabBarForegroundColor`, do tipo `Color`, que define a cor de primeiro plano da barra de guias. Se a propriedade não for definida, o valor de propriedade `ForegroundColor` será usado.
- `TabBarTitleColor`, do tipo `Color`, que define a cor do título da barra de guias. Se a propriedade não for definida, o valor de propriedade `TitleColor` será usado.
- `TabBarUnselectedColor`, do tipo `Color`, que define a cor não selecionada da barra de guias. Se a propriedade não for definida, o valor de propriedade `UnselectedColor` será usado.

Todas essas propriedades são apoiadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), o que significa que essas propriedades podem ser o destino de vinculações de dados e estilizadas.

O exemplo a seguir mostra um estilo XAML que define diferentes propriedades de cor da guia:

```xaml
<Style x:Key="BaseStyle"
       TargetType="Element">
    <Setter Property="Shell.TabBarBackgroundColor"
            Value="#3498DB" />
    <Setter Property="Shell.TabBarTitleColor"
            Value="White" />
    <Setter Property="Shell.TabBarUnselectedColor"
            Value="#B4FFFFFF" />
</Style>
```

Além disso, as guias também podem ser estilizadas usando as folhas de estilo em cascata (CSS). Saiba mais em [propriedades específicas do Shell do Xamarin.Forms](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties).

## <a name="related-links"></a>Links relacionados

- [Xaminals (exemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)
- [Navegação do Shell do Xamarin.Forms](navigation.md)
- [Propriedades específicas do CSS Shell do Xamarin.Forms](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
