---
title: Layout do Shell do Xamarin.Forms
description: Depois de um submenu, o próximo nível de navegação em um aplicativo Shell é a barra de guias inferior. Como alternativa, o padrão de navegação de um aplicativo pode começar com as guias inferiores e sem o uso de um submenu. Em ambos os casos, quando uma guia inferior contiver mais de uma página, as páginas poderão ser navegadas pelas guias superiores.
ms.prod: xamarin
ms.assetid: 318D81DB-E456-4E44-B083-36A27DBD9523
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/06/2019
ms.openlocfilehash: 0ffcbe99ef9696c5fde501809bea5ddcc7a793a7
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305139"
---
# <a name="xamarinforms-shell-tabs"></a>Guias do Shell do Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Quando o padrão de navegação de um aplicativo inclui um submenu, o próximo nível de navegação no aplicativo é a barra de guias inferior. Além disso, quando o submenu é fechado, a barra de guias inferior pode ser considerada o nível principal da navegação.

Como alternativa, o padrão de navegação de um aplicativo pode começar com as guias inferiores e sem o uso de um submenu. Neste cenário, o filho do objeto `Shell` deve ser um objeto `TabBar`, que representa a barra de guias inferior.

> [!NOTE]
> O tipo `TabBar` desabilita o submenu.

Cada objeto `FlyoutItem` ou `TabBar` pode conter um ou mais objetos `Tab`, e cada objeto `Tab` representa uma guia na barra de guias inferior. Cada `Tab` objeto pode conter `ShellContent` um ou `ShellContent` mais objetos, e cada objeto exibirá um único [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto. Quando mais de um objeto `ShellContent` estiver presente em um objeto `Tab`, será possível navegar pelos objetos `ContentPage` por meio das guias principais.

Dentro [`ContentPage`](xref:Xamarin.Forms.ContentPage) de cada `ContentPage` objeto, objetos adicionais podem ser navegados para. Saiba mais sobre a navegação na [navegação do Shell do Xamarin.Forms](navigation.md).

## <a name="single-page-application"></a>Aplicativo de página única

O aplicativo Shell mais simples é um aplicativo de página única, que pode ser criado pela adição de um único objeto `Tab` a um objeto `TabBar`. Dentro `Tab` do objeto, um `ShellContent` objeto [`ContentPage`](xref:Xamarin.Forms.ContentPage) deve ser definido como um objeto:

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

[![Captura de tela de um aplicativo de página única da Shell, no iOS e Android](tabs-images/single-page-app.png "Aplicativo de página única shell")](tabs-images/single-page-app-large.png#lightbox "Aplicativo de página única shell")

> [!NOTE]
> A barra de navegação pode ser ocultada, se necessário, definindo a `Shell.NavBarIsVisible` propriedade anexada `false` no [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto.

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

Esta conversão implícita envolve [`ContentPage`](xref:Xamarin.Forms.ContentPage) automaticamente o `ShellContent` objeto em um `Tab` objeto, que está `FlyoutItem` envolto em um objeto, que está envolto em um objeto. O submenu não é necessário em um aplicativo de página única e, portanto, a propriedade `Shell.FlyoutBehavior` é definida como `Disabled`.

> [!IMPORTANT]
> Em um aplicativo [`ContentPage`](xref:Xamarin.Forms.ContentPage) Shell, cada um `ShellContent` que é filho de um objeto é criado durante a inicialização do aplicativo. Adicionar outros objetos `ShellContent` usando essa abordagem fará com que sejam criadas outras páginas durante a inicialização do aplicativo, o que pode levar a uma experiência ruim de inicialização. No entanto, o Shell também é capaz de criar páginas sob demanda, em resposta à navegação. Saiba mais em [carregamento de páginas eficiente](tabs.md#efficient-page-loading).

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

[![Captura de tela de um aplicativo shell de duas páginas com abas inferiores, no iOS e Android](tabs-images/two-page-app-bottom-tabs.png "Shell aplicativo de duas páginas com abas inferiores")](tabs-images/two-page-app-bottom-tabs-large.png#lightbox "Shell aplicativo de duas páginas com abas inferiores")

Quando houver mais de cinco guias, aparecerá uma guia **Mais,** que pode ser usada para acessar as guias adicionais:

[![Captura de tela de um aplicativo Shell com uma guia Mais, no iOS e Android](tabs-images/more-tabs.png "Aplicativo Shell com mais guia")](tabs-images/more-tabs-large.png#lightbox "Shellapp com mais guias")

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

Esta conversão implícita envolve [`ContentPage`](xref:Xamarin.Forms.ContentPage) automaticamente cada `ShellContent` objeto em um objeto, `Tab` que são então ambos envoltos em um objeto.

> [!IMPORTANT]
> Em um aplicativo [`ContentPage`](xref:Xamarin.Forms.ContentPage) Shell, cada um `ShellContent` que é filho de um objeto é criado durante a inicialização do aplicativo. Adicionar outros objetos `ShellContent` usando essa abordagem fará com que sejam criadas outras páginas durante a inicialização do aplicativo, o que pode levar a uma experiência ruim de inicialização. No entanto, o Shell também é capaz de criar páginas sob demanda, em resposta à navegação. Saiba mais em [carregamento de páginas eficiente](tabs.md#efficient-page-loading).

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

O filho `Tab` de cada `ShellContent` objeto `Content` é um objeto, cuja propriedade é definida como: [`ContentPage`](xref:Xamarin.Forms.ContentPage)

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

Dentro [`ContentPage`](xref:Xamarin.Forms.ContentPage) de cada `ContentPage` objeto, objetos adicionais podem ser navegados para. Saiba mais sobre a navegação na [navegação do Shell do Xamarin.Forms](navigation.md).

> [!NOTE]
> O [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de `ShellContent` cada objeto é `Tab` herdado do objeto pai.

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

Todas essas propriedades são [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) apoiadas por objetos, o que significa que as propriedades podem ser alvos de vinculações de dados.

## <a name="bottom-and-top-tabs"></a>Guias inferior e superior

Quando mais `ShellContent` de um objeto `Tab` está presente em um objeto, uma barra [`ContentPage`](xref:Xamarin.Forms.ContentPage) de guia superior é adicionada à guia inferior, através da qual os objetos são navegáveis:

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

[![Captura de tela de um aplicativo shell de duas páginas com guias superior e inferior, no iOS e Android](tabs-images/two-page-app-top-tabs.png "Shell aplicativo de duas páginas com guias superior e inferior")](tabs-images/two-page-app-top-tabs-large.png#lightbox "Shell aplicativo de duas páginas com guias superior e inferior")

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

Em um aplicativo [`ContentPage`](xref:Xamarin.Forms.ContentPage) Shell, `ShellContent` cada objeto em um objeto é criado durante a inicialização do aplicativo, o que pode levar a uma experiência de inicialização ruim. No entanto, o Shell também permite que sejam criadas páginas sob demanda, em resposta à navegação. Isso pode ser feito `DataTemplate` usando a extensão [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)de marcação para converter `ShellContent.ContentTemplate` cada um `ContentPage` em um e, em seguida, definir o resultado como o valor da propriedade:

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

Todas essas propriedades são [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) apoiadas por objetos, o que significa que as propriedades podem ser alvos de vinculações de dados e estilizadas.

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

- [Xaminals (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Navegação do Shell do Xamarin.Forms](navigation.md)
- [Propriedades específicas do CSS Shell do Xamarin.Forms](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
