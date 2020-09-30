---
title: Xamarin.Forms Submenu do Shell
description: O submenu é o menu raiz de um aplicativo Shell e é acessível por meio de um ícone ou passando o dedo na lateral da tela. O submenu consiste em um cabeçalho opcional, itens de submenu e itens de menu opcionais.
ms.prod: xamarin
ms.assetid: FEDE51EB-577E-4B3E-9890-B7C1A5E52516
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/30/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a0ec978c1947cde4bd6f7fd619ad8cd2f0dbdbce
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563036"
---
# <a name="no-locxamarinforms-shell-flyout"></a>Xamarin.Forms Submenu do Shell

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

O submenu é o menu raiz de um aplicativo Shell e é acessível por meio de um ícone ou passando o dedo na lateral da tela. O submenu consiste em um cabeçalho opcional, itens de submenu e itens de menu opcionais:

![Captura de tela de um submenu anotado do Shell](flyout-images/flyout-annotated.png "Submenu anotado")

Se necessário, a cor do plano de fundo do submenu pode ser definida como a [`Color`](xref:Xamarin.Forms.Color) por meio da `Shell.FlyoutBackgroundColor` propriedade vinculável. Essa propriedade também pode ser definida em uma folha de estilos em cascata (CSS). Para obter mais informações, consulte [ Xamarin.Forms Propriedades específicas do Shell](~/xamarin-forms/user-interface/styles/css/index.md#xamarinforms-shell-specific-properties).

## <a name="flyout-icon"></a>Ícone de submenu

Por padrão, os aplicativos Shell têm um ícone de hambúrguer que, quando pressionado, abre o submenu suspenso. Esse ícone pode ser alterado definindo a `Shell.FlyoutIcon` propriedade vinculável, do tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) , para um ícone apropriado:

```xaml
<Shell ...
       FlyoutIcon="flyouticon.png">
    ...       
</Shell>
```

## <a name="flyout-behavior"></a>Comportamento do submenu

O submenu por ser acessado pelo ícone de hambúrguer ou passando o dedo na lateral da tela. No entanto, esse comportamento pode ser alterado definindo a propriedade anexada `Shell.FlyoutBehavior` como um dos membros de enumeração `FlyoutBehavior`:

- `Disabled` – indica que o submenu não pode ser aberto pelo usuário.
- `Flyout` – indica que o submenu pode ser aberto e fechado pelo usuário. Este é o valor padrão para a propriedade de `FlyoutBehavior` .
- `Locked` – indica que o submenu não pode ser fechado pelo usuário, e o submenu não se sobrepõe ao conteúdo.

O exemplo a seguir mostra como desabilitar o submenu:

```xaml
<Shell ...
       FlyoutBehavior="Disabled">
    ...
</Shell>
```

> [!NOTE]
> A propriedade anexada `FlyoutBehavior` pode ser definida em `Shell`, `FlyoutItem`, `ShellContent` e em objetos da página para substituir o comportamento padrão do submenu.

Além disso, o submenu pode ser aberto de forma programática e fechado com a definição da propriedade associável `Shell.FlyoutIsPresented` como um valor `boolean` que indica se o submenu está visível ou não:

```csharp
Shell.Current.FlyoutIsPresented = false;
```

## <a name="flyout-header"></a>Cabeçalho do submenu

O cabeçalho do submenu é o conteúdo exibido opcionalmente na parte superior do submenu, e sua aparência é definida por um `object` que pode ser configurado por meio do valor da propriedade `Shell.FlyoutHeader`:

```xaml
<Shell.FlyoutHeader>
    <controls:FlyoutHeader />
</Shell.FlyoutHeader>
```

O tipo `FlyoutHeader` é mostrado no exemplo a seguir:

```xaml
<ContentView xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Xaminals.Controls.FlyoutHeader"
             HeightRequest="200">
    <Grid BackgroundColor="Black">
        <Image Aspect="AspectFill"
               Source="xamarinstore.jpg"
               Opacity="0.6" />
        <Label Text="Animals"
               TextColor="White"
               FontAttributes="Bold"
               HorizontalTextAlignment="Center"
               VerticalTextAlignment="Center" />
    </Grid>
</ContentView>
```

Como resultado, teremos o cabeçalho do submenu a seguir:

![Captura de tela do cabeçalho do submenu](flyout-images/flyout-header.png "Cabeçalho do submenu")

Como alternativa, a aparência do cabeçalho do submenu pode ser definida definindo a `Shell.FlyoutHeaderTemplate` propriedade como a [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<Shell.FlyoutHeaderTemplate>
    <DataTemplate>
        <Grid BackgroundColor="Black"
              HeightRequest="200">
            <Image Aspect="AspectFill"
                   Source="xamarinstore.jpg"
                   Opacity="0.6" />
            <Label Text="Animals"
                   TextColor="White"
                   FontAttributes="Bold"
                   HorizontalTextAlignment="Center"
                   VerticalTextAlignment="Center" />
        </Grid>            
    </DataTemplate>
</Shell.FlyoutHeaderTemplate>
```

Por padrão, o cabeçalho do submenu ficará fixo no submenu enquanto o conteúdo abaixo poderá ser rolado se houver itens suficientes. No entanto, esse comportamento pode ser alterado definindo-se a propriedade associável `Shell.FlyoutHeaderBehavior` como um dos membros da enumeração `FlyoutHeaderBehavior`:

- `Default` – indica que o comportamento padrão da plataforma será usado. Este é o valor padrão da propriedade `FlyoutHeaderBehavior`.
- `Fixed` – indica que o cabeçalho do submenu permanece sempre inalterado e visível.
- `Scroll` – indica que o cabeçalho do submenu rola para fora da exibição conforme o usuário rola os itens.
- `CollapseOnScroll` – indica que o cabeçalho do submenu é recolhido, tornando-se apenas um título à medida que o usuário rola os itens.

O exemplo a seguir mostra como recolher o cabeçalho do submenu à medida que o usuário rola a tela:

```xaml
<Shell ...
       FlyoutHeaderBehavior="CollapseOnScroll">
    ...
</Shell>
```

## <a name="flyout-background-image"></a>Imagem da tela de fundo do submenu

O submenu pode ter uma imagem de tela de fundo opcional, que aparece sob o cabeçalho do submenu e atrás de quaisquer itens do submenu e do menu. A imagem de plano de fundo pode ser especificada definindo a `FlyoutBackgroundImage` propriedade vinculável, do tipo [`ImageSource`](xref:Xamarin.Forms.ImageSource) , em um arquivo, recurso inserido, URI ou fluxo.

A taxa de proporção da imagem de plano de fundo pode ser configurada definindo a `FlyoutBackgroundImageAspect` propriedade vinculável, do tipo [`Aspect`](xref:Xamarin.Forms.Aspect) , para um dos `Aspect` membros da enumeração:

- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -Corta a imagem para que ela preencha a área de exibição enquanto preserva a taxa de proporção.
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -letterboxes a imagem, se necessário, para que a imagem caiba na área de exibição, com espaço em branco adicionado à parte superior/inferior ou aos lados, dependendo se a imagem for larga ou comprida.
- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) – Alonga a imagem para preencher completamente e exatamente a área de exibição. Isso pode resultar em distorção de imagem.

A propriedade de `FlyoutBackgroundImageAspect` é definida por padrão como `AspectFit`.

O exemplo a seguir mostra a definição dessas propriedades:

```xaml
<Shell ...
       FlyoutBackgroundImage="photo.jpg"
       FlyoutBackgroundImageAspect="AspectFill">
    ...
</Shell>
```

Isso resulta em uma imagem de tela de fundo que aparece no submenu:

![Captura de tela de uma imagem de fundo de submenu](flyout-images/flyout-backgroundimage.png "Imagem da tela de fundo do submenu")

## <a name="flyout-items"></a>Itens de submenu

Quando o padrão de navegação para um aplicativo inclui um submenu, o objeto `Shell` na subclasse deve conter um ou mais objetos `FlyoutItem`, sendo que cada objeto `FlyoutItem` representa um item no submenu. Cada objeto `FlyoutItem` deve ser um filho do objeto `Shell`.

O exemplo a seguir cria um submenu que contém um cabeçalho de submenu e dois itens de submenu:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>
    <FlyoutItem Title="Cats"
                Icon="cat.png">
        <Tab>
            <ShellContent>
                <views:CatsPage />
            </ShellContent>
        </Tab>
    </FlyoutItem>
    <FlyoutItem Title="Dogs"
                Icon="dog.png">
        <Tab>
            <ShellContent>
                <views:DogsPage />
            </ShellContent>
        </Tab>
    </FlyoutItem>
</Shell>
```

Neste exemplo, cada um [`ContentPage`](xref:Xamarin.Forms.ContentPage) só pode ser acessado por meio de itens de submenu:

[![Captura de tela de um aplicativo de duas páginas do shell com itens de submenu, no iOS e no Android](flyout-images/two-page-app-flyout.png "Aplicativo de duas páginas do shell com itens de submenu")](flyout-images/two-page-app-flyout-large.png#lightbox "Aplicativo de duas páginas do shell com itens de submenu")

> [!NOTE]
> Quando o cabeçalho de submenu não estiver presente, os itens do submenu serão exibidos na parte superior dele. Caso contrário, eles aparecerão abaixo do cabeçalho do submenu.

O Shell tem operadores de conversão implícita que permitem que a hierarquia visual no Shell seja simplificada, sem a introdução de modos de exibição adicionais na árvore visual. Isso é possível porque um objeto `Shell` na subclasse só pode conter objetos `FlyoutItem` ou um objeto `TabBar`, que só podem conter objetos `Tab`, que só podem conter objetos `ShellContent`. Esses operadores de conversão implícita podem ser usados para remover os objetos de `FlyoutItem`, `Tab` e `ShellContent` do exemplo anterior:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>
    <views:CatsPage IconImageSource="cat.png" />
    <views:DogsPage IconImageSource="dog.png" />
</Shell>
```

Essa conversão implícita encapsula automaticamente cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto em `ShellContent` objetos, que são encapsulados em objetos `Tab` , que são encapsulados em `FlyoutItem` objetos.

> [!IMPORTANT]
> Em um aplicativo de Shell, cada [`ContentPage`](xref:Xamarin.Forms.ContentPage) um filho de um `ShellContent` objeto é criado durante a inicialização do aplicativo. Adicionar outros objetos `ShellContent` usando essa abordagem fará com que sejam criadas outras páginas durante a inicialização do aplicativo, o que pode levar a uma experiência ruim de inicialização. No entanto, o Shell também é capaz de criar páginas sob demanda, em resposta à navegação. Para obter mais informações, consulte [carregamento de página eficiente](tabs.md#efficient-page-loading) no guia de [ Xamarin.Forms guias do Shell](tabs.md) .

### <a name="flyoutitem-class"></a>Classe FlyoutItem

A classe `FlyoutItem` inclui as seguintes propriedades que controlam o comportamento e a aparência do item do submenu:

- `FlyoutDisplayOptions`, do tipo `FlyoutDisplayOptions`, define como o item e seus filhos são exibidos no submenu. O valor padrão é `AsSingleItem`.
- `CurrentItem`, do tipo `Tab`, o item selecionado.
- `Items`, do tipo `IList<Tab>`, define todas as guias dentro de um `FlyoutItem`.
- `FlyoutIcon`, do tipo `ImageSource`, o ícone a ser usado para o item. Se essa propriedade não for definida, ele fará o fallback para usar o valor da propriedade `Icon`.
- `Icon`, do tipo `ImageSource`, define o ícone a ser exibido em partes do cromado que não são o submenu.
- `IsChecked`, do tipo `boolean`, define se o item está realçado naquele momento no submenu.
- `IsEnabled`, do tipo `boolean`, define se o item é selecionável no cromado.
- `IsTabStop`, do tipo `bool`, indica se um `FlyoutItem` está incluído na navegação por tabulação. Seu valor padrão é `true`, e quando o valor é `false`, o `FlyoutItem` é ignorado pela infraestrutura da navegação por tabulação, independentemente de `TabIndex` estar definido.
- `IsVisible`, do tipo `bool` , indica se o `FlyoutItem` está oculto no menu do submenu. Seu valor padrão é `true`.
- `TabIndex`, do tipo `int`, indica a ordem na qual os objetos de `FlyoutItem` recebem o foco quando o usuário navega pelos itens pressionando a tecla Tab. O valor padrão da propriedade é 0.
- `Title`, do tipo `string`, o título a ser exibido na interface do usuário.
- `Route`, do tipo `string`, a cadeia de caracteres usada para resolver o item.

Todas essas propriedades, exceto a `Route` propriedade, são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

> [!NOTE]
> Todos os objetos de `FlyoutItem` em um objeto Shell na subclasse são adicionados à coleção `Shell.Items`, que define a lista de itens que será exibida no submenu.

Além disso, a classe `FlyoutItem` expõe os seguintes métodos substituíveis:

- `OnTabIndexPropertyChanged`, chamado sempre que a propriedade `TabIndex` muda.
- `OnTabStopPropertyChanged`, chamado sempre que a propriedade `IsTabStop` muda.
- `TabIndexDefaultValueCreator`, retorna um `int` e é chamado para definir o valor padrão da propriedade `TabIndex`.
- `TabStopDefaultValueCreator`, retorna um `bool` e é chamado para definir o valor padrão da propriedade `TabStop`.

## <a name="flyout-backdrop"></a>Pano de fundo do submenu

O pano de fundo do submenu, que é a aparência da sobreposição do submenu, pode ser especificado definindo a `Shell.FlyoutBackdrop` Propriedade anexada como um `Brush` :

```xaml
<Shell ...
       FlyoutBackdrop="Silver">
    ...
</Shell>
```

Neste exemplo, o pano de fundo do submenu é pintado com um prata `SolidColorBrush` .

> [!IMPORTANT]
> A `FlyoutBackdrop` Propriedade anexada pode ser definida em qualquer elemento Shell, mas só será aplicada quando for definida em `Shell` objetos, `FlyoutItem` ou `TabBar` .

O exemplo a seguir mostra a configuração da fundo do submenu em um `FlyoutItem` para um `LinearGradientBrush` :

```xaml
<Shell ...>
    <FlyoutItem ...>
      <Shell.FlyoutBackdrop>
          <LinearGradientBrush StartPoint="0,0"
                               EndPoint="1,1">
              <GradientStop Color="#8A2387"
                            Offset="0.1" />
              <GradientStop Color="#E94057"
                            Offset="0.6" />
              <GradientStop Color="#F27121"
                            Offset="1.0" />
          </LinearGradientBrush>
      </Shell.FlyoutBackdrop>
      ...
    </FlyoutItem>
    ...
</Shell>
```

Para obter mais informações sobre pincéis, consulte [ Xamarin.Forms pincéis](~/xamarin-forms/user-interface/brushes/index.md).

## <a name="flyout-vertical-scroll"></a>Rolagem vertical do submenu

Por padrão, um submenu pode ser rolado verticalmente quando os itens do submenu não se ajustam ao submenu. Esse comportamento pode ser alterado definindo a `Shell.FlyoutVerticalScrollMode` propriedade vinculável como um dos `ScrollMode` membros da enumeração:

- `Disabled` – indica que a rolagem vertical será desabilitada.
- `Enabled` – indica que a rolagem vertical será habilitada.
- `Auto` – indica que a rolagem vertical será habilitada se os itens do submenu não couberem no submenu. Este é o valor padrão da propriedade `Shell.FlyoutVerticalScrollMode`.

O exemplo a seguir mostra como desabilitar a rolagem vertical:

```xaml
<Shell ...
       FlyoutVerticalScrollMode="Disabled"
    ...
</Shell>
```

## <a name="flyout-display-options"></a>Opções de exibição do submenu

A enumeração `FlyoutDisplayOptions` define os seguintes membros:

- `AsSingleItem`, indica que o item ficará visível como um item único.
- `AsMultipleItems`, indica que o item e seus filhos ficarão visíveis no submenu como um grupo de itens.

Ao definir a propriedade `FlyoutItem.FlyoutDisplayOptions` como `AsMultipleItems`, será criado um item do submenu para cada `Tab` dentro de um `FlyoutItem`:

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:controls="clr-namespace:Xaminals.Controls"
       xmlns:views="clr-namespace:Xaminals.Views"
       FlyoutHeaderBehavior="CollapseOnScroll"
       x:Class="Xaminals.AppShell">

    <Shell.FlyoutHeader>
        <controls:FlyoutHeader />
    </Shell.FlyoutHeader>

    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
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
        <ShellContent Title="Elephants"
                      Icon="elephant.png"
                      ContentTemplate="{DataTemplate views:ElephantsPage}" />  
        <ShellContent Title="Bears"
                      Icon="bear.png"
                      ContentTemplate="{DataTemplate views:BearsPage}" />
    </FlyoutItem>

    <ShellContent Title="About"
                  Icon="info.png"
                  ContentTemplate="{DataTemplate views:AboutPage}" />    
</Shell>
```

Neste exemplo, os itens do submenu foram criados para o objeto `Tab` que é um filho do objeto `FlyoutItem` e os objetos de `ShellContent` que são filhos do objeto `FlyoutItem`. Isso ocorre porque cada objeto `ShellContent` que é um filho do objeto `FlyoutItem` é automaticamente encapsulado em um objeto `Tab`. Além disso, um item do submenu é criado para o último objeto `ShellContent`, que é encapsulado em um objeto `Tab` e, em seguida, em um objeto `FlyoutItem`.

Isso resulta nos seguintes itens de submenu:

[![Captura de tela do submenu contendo objetos FlyoutItem, no iOS e no Android](flyout-images/flyout-reduced.png "Submenu do Shell contendo objetos FlyoutItem")](flyout-images/flyout-reduced-large.png#lightbox "Submenu do Shell contendo objetos FlyoutItem")

## <a name="define-flyoutitem-appearance"></a>Definir a aparência de FlyoutItem

A aparência de cada um `FlyoutItem` pode ser personalizada definindo a `Shell.ItemTemplate` Propriedade anexada como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<Shell ...>
    ...
    <Shell.ItemTemplate>
        <DataTemplate>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.8*" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding FlyoutIcon}"
                       Margin="5"
                       HeightRequest="45" />
                <Label Grid.Column="1"
                       Text="{Binding Title}"
                       FontAttributes="Italic"
                       VerticalTextAlignment="Center" />
            </Grid>
        </DataTemplate>
    </Shell.ItemTemplate>
</Shell>
```

Este exemplo exibe o título de cada objeto `FlyoutItem` em itálico:

[![Captura de tela dos objetos modelados FlyoutItem, no iOS e no Android](flyout-images/flyoutitem-templated.png "Objetos FlyoutItem modelo de Shell")](flyout-images/flyoutitem-templated-large.png#lightbox "Objetos FlyoutItem modelo de Shell")

Como `Shell.ItemTemplate` é uma propriedade anexada, modelos diferentes podem ser anexados a `FlyoutItem` objetos específicos.

> [!NOTE]
> O Shell fornece `Title` as `FlyoutIcon` Propriedades e para o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do `ItemTemplate` .

Além disso, o Shell inclui três classes de estilo, que são automaticamente aplicadas a `FlyoutItem` objetos. Para obter mais informações, consulte [classes de estilo FlyoutItem e MenuItem](#flyoutitem-and-menuitem-style-classes).

### <a name="default-template-for-flyoutitems"></a>Modelo padrão para FlyoutItems

O padrão [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) usado para cada um `FlyoutItem` é mostrado abaixo:

```xaml
<DataTemplate x:Key="FlyoutTemplate">
    <Grid x:Name="FlyoutItemLayout"
          HeightRequest="{x:OnPlatform Android=50}"
          ColumnSpacing="{x:OnPlatform UWP=0}"
          RowSpacing="{x:OnPlatform UWP=0}">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroupList>
                <VisualStateGroup x:Name="CommonStates">
                    <VisualState x:Name="Normal" />
                    <VisualState x:Name="Selected">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor"
                                    Value="{x:OnPlatform Android=#F2F2F2, iOS=#F2F2F2}" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateGroupList>
        </VisualStateManager.VisualStateGroups>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="{x:OnPlatform Android=54, iOS=50, UWP=Auto}" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        <Image x:Name="FlyoutItemImage"
               Source="{Binding FlyoutIcon}"
               VerticalOptions="Center"
               HorizontalOptions="{x:OnPlatform Default=Center, UWP=Start}"
               HeightRequest="{x:OnPlatform Android=24, iOS=22, UWP=16}"
               WidthRequest="{x:OnPlatform Android=24, iOS=22, UWP=16}">
            <Image.Margin>
                <OnPlatform x:TypeArguments="Thickness">
                    <OnPlatform.Platforms>
                        <On Platform="UWP"
                            Value="12,0,12,0" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Image.Margin>
        </Image>
        <Label x:Name="FlyoutItemLabel"
               Grid.Column="1"
               Text="{Binding Title}"
               FontSize="{x:OnPlatform Android=14, iOS=Small}"
               HorizontalOptions="{x:OnPlatform UWP=Start}"
               HorizontalTextAlignment="{x:OnPlatform UWP=Start}"
               FontAttributes="{x:OnPlatform iOS=Bold}"
               VerticalTextAlignment="Center">
            <Label.TextColor>
                <OnPlatform x:TypeArguments="Color">
                    <OnPlatform.Platforms>
                        <On Platform="Android"
                            Value="#D2000000" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.TextColor>
            <Label.Margin>
                <OnPlatform x:TypeArguments="Thickness">
                    <OnPlatform.Platforms>
                        <On Platform="Android"
                            Value="20, 0, 0, 0" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.Margin>
            <Label.FontFamily>
                <OnPlatform x:TypeArguments="x:String">
                    <OnPlatform.Platforms>
                        <On Platform="Android"
                            Value="sans-serif-medium" />
                    </OnPlatform.Platforms>
                </OnPlatform>
            </Label.FontFamily>
        </Label>
    </Grid>
</DataTemplate>
```

Esse modelo pode ser usado como base para fazer alterações no layout de submenu existente e também mostra os Estados visuais que são implementados para itens de submenu.

Além disso, os [`Grid`](xref:Xamarin.Forms.Grid) [`Image`](xref:Xamarin.Forms.Image) elementos, e [`Label`](xref:Xamarin.Forms.Label) têm `x:Name` valores e, portanto, podem ser direcionados com o Gerenciador de estado visual. Para obter mais informações, consulte [definir estado em vários elementos](~/xamarin-forms/user-interface/visual-state-manager.md#set-state-on-multiple-elements).

> [!NOTE]
> O mesmo modelo também pode ser usado para `MenuItem` objetos.

## <a name="flyoutitem-tab-order"></a>Ordem de tabulação do FlyoutItem

Por padrão, a ordem de tabulação dos objetos `FlyoutItem` é a mesma ordem em que eles são listados no XAML ou em que são adicionados de maneira programática a uma coleção filha. Essa é a ordem em que ocorrerá a navegação dos objetos `FlyoutItem` por meio do teclado e, geralmente, essa ordem padrão é a melhor ordem.

A ordem de tabulação padrão pode ser alterada definindo a propriedade `FlyoutItem.TabIndex`, que indica a ordem em que os objetos `FlyoutItem` recebem o foco quando o usuário navega pelos itens ao pressionar a tecla Tab. O valor padrão da propriedade é 0 e ela pode ser definida como qualquer valor de `int`.

As seguintes regras se aplicam ao usar a ordem de tabulação padrão ou ao definir a propriedade `TabIndex`:

- Os objetos `FlyoutItem` com um `TabIndex` igual a 0 são adicionados à ordem de tabulação com base em sua ordem de declaração no XAML ou nas coleções filhas.
- Os objetos `FlyoutItem` com um `TabIndex` maior que 0 são adicionados à ordem de tabulação com base em seu valor do `TabIndex`.
- Os objetos `FlyoutItem` com um `TabIndex` menor que 0 são adicionados à ordem de tabulação e aparecem antes de qualquer valor zero.
- Conflitos em um `TabIndex` são resolvidos pela ordem de declaração.

Após definir uma ordem de tabulação, ao pressionar a tecla Tab, o foco passará pelos objetos `FlyoutItem` na ordem ascendente do `TabIndex` e voltam ao início quando o último objeto é atingido.

Além de definir a ordem de tabulação dos objetos `FlyoutItem`, pode ser necessário excluir alguns objetos da ordem de tabulação. Isso pode ser feito usando a propriedade `FlyoutItem.IsTabStop`, que indica se um `FlyoutItem` está incluído na navegação por tabulação. Seu valor padrão é `true`, e quando o valor é `false`, o `FlyoutItem` é ignorado pela infraestrutura da navegação por tabulação, independentemente de `TabIndex` estar definido.

## <a name="set-the-current-flyoutitem"></a>Configurar o FlyoutItem atual

A classe `Shell` tem uma propriedade associável denominada `CurrentItem`, do tipo `FlyoutItem`, que representa o `FlyoutItem` selecionado naquele momento. Quando o aplicativo Shell é executado pela primeira vez, essa propriedade será definida como o primeiro `FlyoutItem` no objeto `Shell` na subclasse. No entanto, a propriedade pode ser definida para outro `FlyoutItem`, conforme mostrado no exemplo a seguir:

```xaml
<Shell ...
       CurrentItem="{x:Reference aboutItem}">
    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
        ...
    </FlyoutItem>
    <ShellContent x:Name="aboutItem"
                  Title="About"
                  Icon="info.png"
                  ContentTemplate="{DataTemplate views:AboutPage}" />
</Shell>
```

Esse código define o objeto `ShellContent` denominado `aboutItem` como a propriedade `CurrentItem`, o que faz com que ele seja exibido. Nesse exemplo, uma conversão implícita é usada para encapsular o objeto `ShellContent` em um objeto `Tab`, que é encapsulado em um objeto `FlyoutItem`.

O código C# equivalente, dado um `ShellContent` objeto chamado `aboutItem` , é:

```csharp
CurrentItem = aboutItem;
```

Neste exemplo, a `CurrentItem` propriedade é definida na classe de subclasse `Shell` . Como alternativa, a `CurrentItem` propriedade pode ser definida em qualquer classe por meio da `Shell.Current` propriedade estática:

```csharp
Shell.Current.CurrentItem = aboutItem;
```

## <a name="menu-items"></a>Itens de menu

Os itens de menu podem ser adicionados opcionalmente ao submenu e cada item de menu é representado por um [`MenuItem`](xref:Xamarin.Forms.MenuItem) objeto. A posição dos objetos `MenuItem` no submento depende da ordem de declaração deles na hierarquia visual do Shell. Portanto, qualquer objeto `MenuItem` declarado antes do objeto `FlyoutItem` aparecerá na parte superior do submenu, e qualquer objeto `MenuItem` declarado após `FlyoutItem` aparecerá na parte inferior do submenu.

> [!NOTE]
> A `MenuItem` classe tem um [`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked) evento e uma [`Command`](xref:Xamarin.Forms.MenuItem.Command) propriedade. Portanto, os objetos `MenuItem` permitem cenários que executam uma ação em resposta ao `MenuItem` que está sendo tocado. Esses cenários incluem realizar a navegação e abrir um navegador em uma página da Web específica.

[`MenuItem`](xref:Xamarin.Forms.MenuItem) os objetos podem ser adicionados ao submenu, conforme mostrado no exemplo a seguir:

```xaml
<Shell ...>
    ...            
    <MenuItem Text="Random"
              IconImageSource="random.png"
              Command="{Binding RandomPageCommand}" />
    <MenuItem Text="Help"
              IconImageSource="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell" />    
</Shell>
```

Esse código adiciona dois [`MenuItem`](xref:Xamarin.Forms.MenuItem) objetos ao submenu, abaixo de todos os itens de submenu:

[![Captura de tela do submenu contendo objetos MenuItem, no iOS e no Android](flyout-images/flyout.png "Submenu do Shell contendo objetos MenuItem")](flyout-images/flyout-large.png#lightbox "Submenu do Shell contendo objetos MenuItem")

O primeiro [`MenuItem`](xref:Xamarin.Forms.MenuItem) objeto executa um `ICommand` chamado `RandomPageCommand` , que navega para uma página aleatória no aplicativo. O segundo objeto `MenuItem` executa um `ICommand` denominado `HelpCommand`, que abre a URL especificada pela propriedade `CommandParameter` em um navegador da Web.

> [!NOTE]
> O [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de cada `MenuItem` é herdado do objeto de subclasse `Shell` .

## <a name="define-menuitem-appearance"></a>Definir a aparência do MenuItem

A aparência de cada um `MenuItem` pode ser personalizada definindo a `Shell.MenuItemTemplate` Propriedade anexada como um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<Shell ...>
    <Shell.MenuItemTemplate>
        <DataTemplate>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.8*" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding Icon}"
                       Margin="5"
                       HeightRequest="45" />
                <Label Grid.Column="1"
                       Text="{Binding Text}"
                       FontAttributes="Italic"
                       VerticalTextAlignment="Center" />
            </Grid>
        </DataTemplate>
    </Shell.MenuItemTemplate>
    ...
    <MenuItem Text="Random"
              IconImageSource="random.png"
              Command="{Binding RandomPageCommand}" />
    <MenuItem Text="Help"
              IconImageSource="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell" />  
</Shell>
```

Este exemplo anexa o `MenuItemTemplate` no nível do Shell a cada objeto `MenuItem`, exibindo o título de cada objeto `MenuItem` em itálico:

[![Captura de tela de objetos MenuItem com modelo, em iOS e Android](flyout-images/menuitem-templated.png "Objetos MenuItem modelo do Shell")](flyout-images/menuitem-templated-large.png#lightbox "Objetos MenuItem modelo do Shell")

> [!NOTE]
> O Shell fornece [`Text`](xref:Xamarin.Forms.MenuItem.Text) as [`IconImageSource`](xref:Xamarin.Forms.MenuItem.IconImageSource) Propriedades e para o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) do `MenuItemTemplate` . Você também pode usar `Title` o no lugar de `Text` e, `Icon` no lugar dele `IconImageSource` , permitirá que você reutilize o mesmo modelo para itens de menu e itens de submenu

Como `Shell.MenuItemTemplate` é uma propriedade anexada, diferentes modelos podem ser anexados a objetos `MenuItem` específicos:

```xaml
<Shell ...>
    <Shell.MenuItemTemplate>
        <DataTemplate>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.8*" />
                </Grid.ColumnDefinitions>
                <Image Source="{Binding Icon}"
                       Margin="5"
                       HeightRequest="45" />
                <Label Grid.Column="1"
                       Text="{Binding Text}"
                       FontAttributes="Italic"
                       VerticalTextAlignment="Center" />
            </Grid>
        </DataTemplate>
    </Shell.MenuItemTemplate>
    ...
    <MenuItem Text="Random"
              IconImageSource="random.png"
              Command="{Binding RandomPageCommand}" />
    <MenuItem Text="Help"
              Icon="help.png"
              Command="{Binding HelpCommand}"
              CommandParameter="https://docs.microsoft.com/xamarin/xamarin-forms/app-fundamentals/shell">
        <Shell.MenuItemTemplate>
            <DataTemplate>
                ...
            </DataTemplate>
        </Shell.MenuItemTemplate>
    </MenuItem>
</Shell>
```

Este exemplo anexa o `MenuItemTemplate` no nível do Shell ao primeiro objeto `MenuItem` e anexa o `MenuItemTemplate` em linha ao segundo `MenuItem`.

> [!NOTE]
> O modelo padrão para `FlyoutItem` objetos também pode ser usado para `MenuItem` objetos. Para obter mais informações, consulte [modelo padrão para FlyoutItems](#default-template-for-flyoutitems).

## <a name="flyoutitem-and-menuitem-style-classes"></a>Classes de estilo FlyoutItem e MenuItem

O Shell inclui três classes de estilo, que são automaticamente aplicadas aos `FlyoutItem` `MenuItem` objetos e. Os nomes de classe de estilo são:

- `FlyoutItemLabelStyle`
- `FlyoutItemImageStyle`
- `FlyoutItemLayoutStyle`

O XAML a seguir mostra um exemplo de definição de estilos para essas classes de estilo:

```xaml
<Style TargetType="Label"
       Class="FlyoutItemLabelStyle">
    <Setter Property="TextColor"
            Value="Black" />
    <Setter Property="HeightRequest"
            Value="100" />
</Style>

<Style TargetType="Image"
       Class="FlyoutItemImageStyle">
    <Setter Property="Aspect"
            Value="Fill" />
</Style>

<Style TargetType="Layout"
       Class="FlyoutItemLayoutStyle"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Teal" />
</Style>
```

Esses estilos serão automaticamente aplicados aos `FlyoutItem` objetos e `MenuItem` , sem a necessidade de definir suas [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) Propriedades para os nomes de classe de estilo.

Além disso, as classes de estilo personalizado podem ser definidas e aplicadas a `FlyoutItem` `MenuItem` objetos e. Para obter mais informações sobre classes de estilo, consulte [ Xamarin.Forms classes de estilo](~/xamarin-forms/user-interface/styles/xaml/style-class.md).

## <a name="related-links"></a>Links relacionados

- [Xaminals (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.Forms Classes de estilo](~/xamarin-forms/user-interface/styles/xaml/style-class.md)
- [Xamarin.Forms Gerenciador de estado visual](~/xamarin-forms/user-interface/visual-state-manager.md)
- [Xamarin.Forms Pincéis](~/xamarin-forms/user-interface/brushes/index.md)