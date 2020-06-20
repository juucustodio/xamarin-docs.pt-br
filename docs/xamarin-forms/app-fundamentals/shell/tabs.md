---
title: Xamarin.FormsLayout do Shell
description: Depois de um submenu, o próximo nível de navegação em um aplicativo Shell é a barra de guias inferior. Como alternativa, o padrão de navegação de um aplicativo pode começar com as guias inferiores e sem o uso de um submenu. Em ambos os casos, quando uma guia inferior contiver mais de uma página, as páginas poderão ser navegadas pelas guias superiores.
ms.prod: xamarin
ms.assetid: 318D81DB-E456-4E44-B083-36A27DBD9523
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/06/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fa49cdb7944e3ab942ccda611af906e2822b072a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139952"
---
# <a name="xamarinforms-shell-tabs"></a>Xamarin.FormsGuias do Shell

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Quando o padrão de navegação de um aplicativo inclui um submenu, o próximo nível de navegação no aplicativo é a barra de guias inferior. Além disso, quando o submenu é fechado, a barra de guias inferior pode ser considerada o nível principal da navegação.

Como alternativa, o padrão de navegação de um aplicativo pode começar com as guias inferiores e sem o uso de um submenu. Neste cenário, o filho do objeto `Shell` deve ser um objeto `TabBar`, que representa a barra de guias inferior.

> [!NOTE]
> O tipo `TabBar` desabilita o submenu.

Cada objeto `FlyoutItem` ou `TabBar` pode conter um ou mais objetos `Tab`, e cada objeto `Tab` representa uma guia na barra de guias inferior. Cada `Tab` objeto pode conter um ou mais `ShellContent` objetos, e cada `ShellContent` objeto exibirá um único [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto. Quando mais de um objeto `ShellContent` estiver presente em um objeto `Tab`, será possível navegar pelos objetos `ContentPage` por meio das guias principais.

Dentro de cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto, `ContentPage` é possível navegar por objetos adicionais. Para obter mais informações sobre navegação, consulte [ Xamarin.Forms navegação do Shell](navigation.md).

## <a name="single-page-application"></a>Aplicativo de página única

O aplicativo Shell mais simples é um aplicativo de página única, que pode ser criado pela adição de um único objeto `Tab` a um objeto `TabBar`. Dentro do `Tab` objeto, um `ShellContent` objeto deve ser definido como um [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto:

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

[![Captura de tela de um aplicativo de página única do Shell, no iOS e no Android](tabs-images/single-page-app.png "Aplicativo de página única do Shell")](tabs-images/single-page-app-large.png#lightbox "Aplicativo de página única do Shell")

> [!NOTE]
> A barra de navegação pode ser ocultada, se necessário, definindo a `Shell.NavBarIsVisible` Propriedade anexada como `false` no [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto.

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

Essa conversão implícita encapsula automaticamente o [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto em um `ShellContent` objeto, que é encapsulado em um `Tab` objeto, que é encapsulado em um `FlyoutItem` objeto. O submenu não é necessário em um aplicativo de página única e, portanto, a propriedade `Shell.FlyoutBehavior` é definida como `Disabled`.

> [!IMPORTANT]
> Em um aplicativo de Shell, cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) um filho de um `ShellContent` objeto é criado durante a inicialização do aplicativo. Adicionar outros objetos `ShellContent` usando essa abordagem fará com que sejam criadas outras páginas durante a inicialização do aplicativo, o que pode levar a uma experiência ruim de inicialização. No entanto, o Shell também é capaz de criar páginas sob demanda, em resposta à navegação. Saiba mais em [carregamento de páginas eficiente](tabs.md#efficient-page-loading).

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

[![Captura de tela de um aplicativo de duas páginas do shell com guias inferiores, no iOS e no Android](tabs-images/two-page-app-bottom-tabs.png "Aplicativo de duas páginas do shell com guias inferiores")](tabs-images/two-page-app-bottom-tabs-large.png#lightbox "Aplicativo de duas páginas do shell com guias inferiores")

Quando houver mais de cinco guias, será exibida uma guia **mais** , que pode ser usada para acessar as guias adicionais:

[![Captura de tela de um aplicativo de shell com uma guia mais, no iOS e no Android](tabs-images/more-tabs.png "Aplicativo shell com mais guias")](tabs-images/more-tabs-large.png#lightbox "Shellapp com mais guias")

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

Essa conversão implícita encapsula automaticamente cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto em um `ShellContent` objeto, que são então ambos encapsulados em um `Tab` objeto.

> [!IMPORTANT]
> Em um aplicativo de Shell, cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) um filho de um `ShellContent` objeto é criado durante a inicialização do aplicativo. Adicionar outros objetos `ShellContent` usando essa abordagem fará com que sejam criadas outras páginas durante a inicialização do aplicativo, o que pode levar a uma experiência ruim de inicialização. No entanto, o Shell também é capaz de criar páginas sob demanda, em resposta à navegação. Saiba mais em [carregamento de páginas eficiente](tabs.md#efficient-page-loading).

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

O filho de cada `Tab` objeto é um `ShellContent` objeto, cuja `Content` propriedade é definida como a [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

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

Dentro de cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto, `ContentPage` é possível navegar por objetos adicionais. Para obter mais informações sobre navegação, consulte [ Xamarin.Forms navegação do Shell](navigation.md).

> [!NOTE]
> O [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de cada `ShellContent` objeto é herdado do `Tab` objeto pai.

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

Todas essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

## <a name="bottom-and-top-tabs"></a>Guias inferior e superior

Quando há mais de um `ShellContent` objeto presente em um `Tab` objeto, uma barra de guias superior é adicionada à guia inferior, por meio da qual os [`ContentPage`](xref:Xamarin.Forms.ContentPage) objetos são navegáveis:

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

[![Captura de tela de um aplicativo de duas páginas do shell com as guias superior e inferior, no iOS e no Android](tabs-images/two-page-app-top-tabs.png "Aplicativo de duas páginas do shell com guias superior e inferior")](tabs-images/two-page-app-top-tabs-large.png#lightbox "Aplicativo de duas páginas do shell com guias superior e inferior")

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

Em um aplicativo de Shell, cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto em um `ShellContent` objeto é criado durante a inicialização do aplicativo, o que pode levar a uma experiência de inicialização ruim. No entanto, o Shell também permite que sejam criadas páginas sob demanda, em resposta à navegação. Isso pode ser feito usando a `DataTemplate` extensão de marcação para converter cada `ContentPage` em um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) e, em seguida, definindo o resultado como o `ShellContent.ContentTemplate` valor da propriedade:

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

Todas essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados e com estilo.

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

Além disso, as guias também podem ser estilizadas usando as folhas de estilo em cascata (CSS). Para obter mais informações, consulte [ Xamarin.Forms Propriedades específicas do Shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties).

## <a name="related-links"></a>Links relacionados

- [Xaminals (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.FormsNavegação do Shell](navigation.md)
- [Xamarin.FormsPropriedades específicas do Shell CSS](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties)
