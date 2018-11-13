---
title: Especificidades da plataforma do iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo demonstra como utilizar as iOS-especificidades da plataforma que são criadas no xamarin. Forms.
ms.prod: xamarin
ms.assetid: C0837996-A1E8-47F9-B3A8-98EE43B4A675
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
ms.openlocfilehash: afecf5c173e919bd20015aadd8a874f492dc4e34
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527073"
---
# <a name="ios-platform-specifics"></a>Especificidades da plataforma do iOS

_Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo demonstra como utilizar as iOS-especificidades da plataforma que são criadas no xamarin. Forms._

## <a name="visualelements"></a>VisualElements

No iOS, a seguinte funcionalidade específica da plataforma é fornecida para modos de exibição, páginas e layouts do xamarin. Forms:

- Suporte para qualquer um de desfoque [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte [Aplicar desfoque](#blur).
- Desabilitar o modo de cor herdados em um com suporte [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte [Desabilitar modo de cor herdado](#legacy-color-mode).
- Habilitando uma sombra em um [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte [habilitando uma sombra](#drop-shadow).

<a name="blur" />

### <a name="applying-blur"></a>Aplicação de desfoque

Este específicos da plataforma é usado para desfocar o conteúdo em camadas abaixo dela e é consumido em XAML, definindo o [ `VisualElement.BlurEffect` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty) propriedade anexada a um valor da [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
  ...
  <AbsoluteLayout HorizontalOptions="Center">
      <Image Source="monkeyface.png" />
      <BoxView x:Name="boxView" ios:VisualElement.BlurEffect="ExtraLight" HeightRequest="300" WidthRequest="300" />
  </AbsoluteLayout>
  ...
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

boxView.On<iOS>().UseBlurEffect(BlurEffectStyle.ExtraLight);
```

O `BoxView.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `VisualElement.UseBlurEffect` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.UseBlurEffect(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle)) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para aplicar o efeito de desfoque, com o [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) enumeração fornecendo quatro valores: [ `None` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None), [ `ExtraLight` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight), [ `Light` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light), e [ `Dark` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark).

O resultado é que o especificado [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) é aplicado ao [ `BoxView` ](xref:Xamarin.Forms.BoxView) da instância, que Desfoca a [ `Image` ](xref:Xamarin.Forms.Image) em camadas abaixo dela:

![](ios-images/blur-effect.png "Específico da plataforma efeito de desfoque")

> [!NOTE]
> Ao adicionar um efeito de desfoque a uma [ `VisualElement` ](xref:Xamarin.Forms.VisualElement), eventos de toque ainda serão recebidos pelo `VisualElement`.

<a name="legacy-color-mode" />

### <a name="disabling-legacy-color-mode"></a>Desabilitar o modo de cor herdado

Algumas das exibições de xamarin. Forms apresentam um modo de cor herdado. Nesse modo, quando o [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propriedade do modo de exibição é definida como `false`, o modo de exibição substituirá as cores definidas pelo usuário com os nativo as cores padrão para o estado desabilitado. Para versões anteriores compatibilidade, esse modo herdado cor permanece o comportamento padrão para modos de exibição com suporte.

Este específicos da plataforma desabilita nesse modo herdado de cor, para que permaneçam de cores definido em uma exibição pelo usuário, mesmo quando o modo de exibição está desabilitado. Ele é consumido em XAML, definindo o [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsLegacyColorModeEnabledProperty) anexado à propriedade `false`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                ios:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

_legacyColorModeDisabledButton.On<iOS>().SetIsLegacyColorModeEnabled(false);
```

O `VisualElement.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usada para controlar se o modo de cor herdados é desabilitado. Além disso, o [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})) método pode ser usado para retornar se o modo de cor herdado está desabilitado.

O resultado é que o modo herdado de cor pode ser desabilitado, para que as cores definidas em uma exibição pelo usuário permaneçam até mesmo quando o modo de exibição está desabilitado:

![](ios-images/legacy-color-mode-disabled.png "Modo herdado de cor desabilitado")

> [!NOTE]
> Ao definir uma [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) em uma exibição, o modo herdado de cor é totalmente ignorado. Para obter mais informações sobre estados visuais, consulte [o Gerenciador de estado Visual xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

<a name="drop-shadow" />

### <a name="enabling-a-drop-shadow"></a>Habilitando uma sombra

Este específicos da plataforma é usado para habilitar uma sombra em um [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Ele é consumido em XAML, definindo o [ `VisualElement.IsShadowEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsShadowEnabledProperty) anexado à propriedade `true`, juntamente com um número de adicionais opcional propriedades que controlam a sombra anexadas:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <BoxView ...
                 ios:VisualElement.IsShadowEnabled="true"
                 ios:VisualElement.ShadowColor="Purple"
                 ios:VisualElement.ShadowOpacity="0.7"
                 ios:VisualElement.ShadowRadius="12">
            <ios:VisualElement.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </ios:VisualElement.ShadowOffset>
         </BoxView>
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var boxView = new BoxView { Color = Color.Aqua, WidthRequest = 100, HeightRequest = 100 };
boxView.On<iOS>()
       .SetIsShadowEnabled(true)
       .SetShadowColor(Color.Purple)
       .SetShadowOffset(new Size(10,10))
       .SetShadowOpacity(0.7)
       .SetShadowRadius(12);
```

O `VisualElement.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `VisualElement.SetIsShadowEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetIsShadowEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usada para controlar se uma sombra está habilitada no `VisualElement`. Além disso, os métodos a seguir podem ser invocados para controlar a sombra:

- [`SetShadowColor`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.Color)) – Define a cor da sombra. A cor padrão é [ `Color.Default` ](xref:Xamarin.Forms.Color.Default*).
- [`SetShadowOffset`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},Xamarin.Forms.Size)) – Define o deslocamento da sombra. O deslocamento altera a direção da sombra é convertida e é especificada como uma [ `Size` ](xref:Xamarin.Forms.Size) valor. O `Size` valores de estrutura são expressos em unidades independentes de dispositivo, com o primeiro valor que está sendo a distância para a esquerda (valor negativo) ou a direita (valor positivo) e o segundo valor sendo a distância acima (valor negativo) ou abaixo (valor positivo) . O valor padrão dessa propriedade é (0,0, 0,0), que resulta na sombra que está sendo convertido em torno de cada lado do `VisualElement`.
- [`SetShadowOpacity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowOpacity(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Double)) – Define a opacidade da sombra, com o valor sendo no intervalo de 0,0 (transparente) para 1,0 (opaco). O valor de opacidade padrão é 0,5.
- [`SetShadowRadius`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetShadowRadius(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Double)) – Define o raio de desfoque usado para renderizar a sombra projetada. O valor de raio padrão é 10.0.

> [!NOTE]
> O estado de uma sombra pode ser consultado por meio da chamada a [ `GetIsShadowEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetIsShadowEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})), [ `GetShadowColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})), [ `GetShadowOffset` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})), [ `GetShadowOpacity` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowOpacity(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})), e [ `GetShadowRadius` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetShadowRadius(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})) métodos.

O resultado é que uma sombra pode ser habilitada em um [ `VisualElement` ](xref:Xamarin.Forms.VisualElement):

![](ios-images/drop-shadow.png "Sombra habilitada")

## <a name="views"></a>Exibições

No iOS, a seguinte funcionalidade específica da plataforma é fornecida para modos de exibição do xamarin. Forms:

- Garantir que inseridos texto se adapta a um [ `Entry` ](xref:Xamarin.Forms.Entry) ajustando o tamanho da fonte. Para obter mais informações, consulte [ajustando o tamanho da fonte de uma entrada](#adjust_font_size).
- Configurando a cor do cursor uma [ `Entry` ](xref:Xamarin.Forms.Entry). Para obter mais informações, consulte [Configurando a cor do Cursor de entrada](#entry-cursorcolor).
- Definindo o estilo do separador em um [ `ListView` ](xref:Xamarin.Forms.ListView). Para obter mais informações, consulte [definindo o estilo do separador em um ListView](#listview-separatorstyle).
- Controlando quando a seleção de item ocorre em um [ `Picker` ](xref:Xamarin.Forms.Picker). Para obter mais informações, consulte [seleção de Item de seletor controlando](#picker_update_mode).
- Habilitando a [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propriedade a ser definido tocando em uma posição no [ `Slider` ](xref:Xamarin.Forms.Slider) barra, em vez de precisar arrastar o `Slider` thumb. Para obter mais informações, consulte [habilitando um elevador de controle deslizante para mover com um toque de](#slider-updateontap).

<a name="adjust_font_size" />

### <a name="adjusting-the-font-size-of-an-entry"></a>Ajustar o tamanho da fonte de uma entrada

Este específicos da plataforma é usado para dimensionar o tamanho da fonte de um [ `Entry` ](xref:Xamarin.Forms.Entry) para garantir que o texto de valores se encaixa no controle. Ele é consumido em XAML, definindo o [ `Entry.AdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty) anexado à propriedade um `boolean` valor:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    <StackLayout Margin="20">
        <Entry x:Name="entry"
               Placeholder="Enter text here to see the font size change"
               FontSize="22"
               ios:Entry.AdjustsFontSizeToFitWidth="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

entry.On<iOS>().EnableAdjustsFontSizeToFitWidth();
```

O `Entry.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `Entry.EnableAdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.EnableAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para dimensionar o tamanho da fonte do texto para garantir que ela se encaixa valores de [ `Entry` ](xref:Xamarin.Forms.Entry). Além disso, o [ `Entry` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry) classe o `Xamarin.Forms.PlatformConfiguration.iOSSpecific` namespace também tem um [ `DisableAdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.DisableAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) método que desabilita essa específicos da plataforma, e um [ `SetAdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry},System.Boolean)) método que pode ser usado para ativar/desativar o tamanho da fonte dimensionamento chamando o [ `AdjustsFontSizeToFitWidth` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) método:

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

O resultado é que o tamanho da fonte de [ `Entry` ](xref:Xamarin.Forms.Entry) é dimensionado para garantir que o texto de valores se encaixa no controle:

![](ios-images/entry-font-size.png "Ajustar específicos de plataforma de tamanho de fonte de entrada")

<a name="entry-cursorcolor" />

### <a name="setting-the-entry-cursor-color"></a>Configurando a cor do Cursor de entrada

Essa plataforma específica define a cor do cursor em uma [ `Entry` ](xref:Xamarin.Forms.Entry) como uma cor especificada. Ele é consumido em XAML, definindo o [ `Entry.CursorColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.CursorColorProperty) propriedade associável a uma [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Entry ... ios:Entry.CursorColor="LimeGreen" />
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var entry = new Xamarin.Forms.Entry();
entry.On<iOS>().SetCursorColor(Color.LimeGreen);
```

O `Entry.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `Entry.SetCursorColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetCursorColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry},Xamarin.Forms.Color)) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, define a cor do cursor para um especificados [ `Color` ](xref:Xamarin.Forms.Color). Além disso, o [ `Entry.GetCursorColor` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.GetCursorColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) método pode ser usado para recuperar a cor atual do cursor.

O resultado é que a cor do cursor em uma [ `Entry` ](xref:Xamarin.Forms.Entry) pode ser definido para um determinado [ `Color` ](xref:Xamarin.Forms.Color):

![](ios-images/entry-cursorcolor.png "Cor do Cursor de entrada")

<a name="listview-separatorstyle" />

### <a name="setting-the-separator-style-on-a-listview"></a>Definindo o estilo do separador em um ListView

Este específicos da plataforma controla se o separador entre células em uma [ `ListView` ](xref:Xamarin.Forms.ListView) usa a largura total do `ListView`. Ele é consumido em XAML, definindo o [ `ListView.SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SeparatorStyleProperty) propriedade anexada a um valor da [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.SeparatorStyle="FullWidth">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetSeparatorStyle(SeparatorStyle.FullWidth);
```

O `ListView.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `ListView.SetSeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SetSeparatorStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usada para controlar se o separador entre células no [ `ListView` ](xref:Xamarin.Forms.ListView) usa completo largura do `ListView`, com o [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) enumeração fornecendo dois valores possíveis:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.Default) – indica o comportamento do separador de iOS padrão. Esse é o comportamento padrão no xamarin. Forms.
- [`FullWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.FullWidth) – indica que os separadores serão desenhados de uma borda da `ListView` para outro.

O resultado é que o especificado [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) valor é aplicado a [ `ListView` ](xref:Xamarin.Forms.ListView), que controla a largura do separador entre células:

![](ios-images/listview-separatorstyle.png "ListView SeparatorStyle específicos da plataforma")

> [!NOTE]
> Depois que o estilo do separador foi definido como `FullWidth`, ele não pode ser alterado para `Default` em tempo de execução.

<a name="picker_update_mode" />

### <a name="controlling-picker-item-selection"></a>Controlando a seleção de Item do seletor

Este específicos da plataforma controla quando a seleção de item ocorre em um [ `Picker` ](xref:Xamarin.Forms.Picker), permitindo que o usuário especifique que a seleção de item ocorre ao navegar por itens no controle, ou apenas uma vez a **feito** botão é pressionado. Ele é consumido em XAML, definindo o `Picker.UpdateMode` propriedade anexada a um valor da `UpdateMode` enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <Picker ... Title="Select a monkey" ios:Picker.UpdateMode="WhenFinished">
          ...
        </Picker>
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

O `Picker.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `Picker.SetUpdateMode` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar quando a seleção de item ocorre, com o `UpdateMode` enumeração fornecendo dois valores possíveis:

- `Immediately` – seleção de item ocorre conforme o usuário navega por itens na [ `Picker` ](xref:Xamarin.Forms.Picker). Esse é o comportamento padrão no xamarin. Forms.
- `WhenFinished` – seleção de item ocorrerá somente depois que o usuário pressiona o **feito** botão na [ `Picker` ](xref:Xamarin.Forms.Picker).

Além disso, o `SetUpdateMode` método pode ser usado para alternar os valores de enumeração por meio da chamada a `UpdateMode` método, que retorna atual `UpdateMode`:

```csharp
switch (picker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        picker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

O resultado é que a especificada `UpdateMode` é aplicada para o [ `Picker` ](xref:Xamarin.Forms.Picker), que controla quando ocorre a seleção de item:

[![](ios-images/picker-updatemode.png "Específico da plataforma seletor UpdateMode")](ios-images/picker-updatemode-large.png#lightbox "seletor UpdateMode específicos da plataforma")

<a name="slider-updateontap" />

### <a name="enabling-a-slider-thumb-to-move-on-tap"></a>Habilitando um elevador de controle deslizante para mover com um toque de

Este específicos da plataforma permite que o [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propriedade a ser definido tocando em uma posição no [ `Slider` ](xref:Xamarin.Forms.Slider) barra, em vez de precisar arrastar o `Slider` thumb. Ele é consumido em XAML, definindo o [ `Slider.UpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.UpdateOnTapProperty) para a propriedade associável `true`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Slider ... ios:Slider.UpdateOnTap="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var slider = new Xamarin.Forms.Slider();
slider.On<iOS>().SetUpdateOnTap(true);
```

O `Slider.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `Slider.SetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.SetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usada para controlar se um toque do `Slider` barra definirá o [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propriedade. Além disso, o [ `Slider.GetUpdateOnTap` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Slider.GetUpdateOnTap(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Slider})) método pode ser usado para retornar se um toque dos `Slider` barra definirá o `Slider.Value` propriedade.

O resultado é que um toque do [ `Slider` ](xref:Xamarin.Forms.Slider) barra pode mover o `Slider` thumb e defina o [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propriedade:

![](ios-images/slider-updateontap.png "Atualização do controle deslizante em toque habilitado")

## <a name="pages"></a>Pages (Páginas)

No iOS, a seguinte funcionalidade específica da plataforma é fornecida para páginas do xamarin. Forms:

- Ocultando o separador de barra de navegação em um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Para obter mais informações, consulte [ocultando o separador de barra de navegação em uma NavigationPage](#navigationpage-hideseparatorbar).
- Controlar se a barra de navegação é translúcida. Para obter mais informações, consulte [fazendo a barra de navegação translúcido](#translucent_navigation_bar).
- Controlar se o texto da barra de status de cor em um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) é ajustado para coincidir com a luminosidade da barra de navegação. Para obter mais informações, consulte [ajustando o modo de cor do texto de barra de Status](#status_bar_color_mode).
- Controlar se o título da página é exibido como um título grande na barra de navegação de página. Para obter mais informações, consulte [exibir títulos grandes](#large_title).
- Definindo a visibilidade da barra de status em uma [ `Page` ](xref:Xamarin.Forms.Page). Para obter mais informações, consulte [definindo a visibilidade da barra de Status em uma página](#set_status_bar_visibility).
- Garantir que esse conteúdo da página está posicionado em uma área da tela que é segura para todos os dispositivos iOS. Para obter mais informações, consulte [habilitando o guia do Layout da área segura](#safe_area_layout).
- Definindo o estilo de apresentação de páginas modais em um iPad. Para obter mais informações, consulte [definindo o estilo de apresentação de página Modal em um iPad](#modal-page-presentation-style).

<a name="navigationpage-hideseparatorbar" />

### <a name="hiding-the-navigation-bar-separator-on-a-navigationpage"></a>Ocultando a barra de navegação separador em uma NavigationPage

Este específicos da plataforma oculta a linha separadora e sombra que está na parte inferior da barra de navegação em um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Ele é consumido em XAML, definindo o [ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) para a propriedade associável `false`:

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ios:NavigationPage.HideNavigationBarSeparator="true">

</NavigationPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;

public class iOSTitleViewNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public iOSTitleViewNavigationPageCS()
    {
        On<iOS>().SetHideNavigationBarSeparator(true);
    }
}
```

O `NavigationPage.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `NavigationPage.SetHideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetHideNavigationBarSeparator(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usada para controlar se o separador de barra de navegação está oculto. Além disso, o [ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparator(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) método pode ser usado para retornar se o separador de barra de navegação está oculto.

O resultado é que o separador de barra de navegação em um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) podem estar ocultos:

![](ios-images/navigationpage-hideseparatorbar.png "Barra de navegação NavigationPage ocultada")

<a name="translucent_navigation_bar" />

### <a name="making-the-navigation-bar-translucent"></a>Tornando a barra de navegação translúcido

Este específicos da plataforma é usado para alterar a transparência da barra de navegação e consumido em XAML, definindo o [ `NavigationPage.IsNavigationBarTranslucent` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty) anexado à propriedade um `boolean` valor:

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                BackgroundColor="Blue"
                ios:NavigationPage.IsNavigationBarTranslucent="true">
  ...
</NavigationPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

(App.Current.MainPage as Xamarin.Forms.NavigationPage).BackgroundColor = Color.Blue;
(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().EnableTranslucentNavigationBar();
```

O `NavigationPage.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `NavigationPage.EnableTranslucentNavigationBar` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.EnableTranslucentNavigationBar(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para fazer a barra de navegação translúcido. Além disso, o [ `NavigationPage` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage) classe o `Xamarin.Forms.PlatformConfiguration.iOSSpecific` namespace também tem um [ `DisableTranslucentNavigationBar` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.DisableTranslucentNavigationBar(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) método que restaura a barra de navegação para seu estado padrão, e um [ `SetIsNavigationBarTranslucent` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetIsNavigationBarTranslucent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},System.Boolean)) método que pode ser usado para alternar a transparência da barra de navegação, chamando o [ `IsNavigationBarTranslucent` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) método:

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

O resultado é que a transparência da barra de navegação pode ser alterada:

![](ios-images/translucent-navigation-bar.png "Barra de navegação translúcido específicos da plataforma")

<a name="status_bar_color_mode" />

### <a name="adjusting-the-status-bar-text-color-mode"></a>Ajustando o modo de cor do texto da barra de Status

Este controles específicos da plataforma se o texto da barra de status de cor em um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) é ajustado para coincidir com a luminosidade da barra de navegação. Ele é consumido em XAML, definindo o [ `NavigationPage.StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty) propriedade anexada a um valor da [ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) enumeração:

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    x:Class="PlatformSpecifics.iOSStatusBarTextColorModePage">
    <MasterDetailPage.Master>
        <ContentPage Title="Master Page Title" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage BarBackgroundColor="Blue" BarTextColor="White"
                        ios:NavigationPage.StatusBarTextColorMode="MatchNavigationBarTextLuminosity">
            <x:Arguments>
                <ContentPage>
                    <Label Text="Slide the master page to see the status bar text color mode change." />
                </ContentPage>
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>

```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

IsPresentedChanged += (sender, e) =>
{
    var mdp = sender as MasterDetailPage;
    if (mdp.IsPresented)
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.DoNotAdjust);
    else
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.MatchNavigationBarTextLuminosity);
};
```

O `NavigationPage.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `NavigationPage.SetStatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetStatusBarTextColorMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode)) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, controles se o texto da barra de status de cor no [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) é ajustado para coincidir com o luminosidade da barra de navegação, com o [ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) enumeração fornecendo dois valores possíveis:

- [`DoNotAdjust`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust) – indica que a barra de cor do texto de status não devem ser ajustado.
- [`MatchNavigationBarTextLuminosity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity) – indica que a barra de cor do texto de status deve corresponder à luminosidade da barra de navegação.

Além disso, o [ `GetStatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.GetStatusBarTextColorMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage})) método pode ser usado para recuperar o valor atual da [ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) enumeração é aplicada para o [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage).

O resultado é que o status da barra de cor do texto em uma [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) pode ser ajustado de acordo com a luminosidade da barra de navegação. Neste exemplo, a barra de alterações de cor do texto de status que o usuário alterna entre o [ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master) e [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) páginas de um [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

![](ios-images/status-bar-text-color-mode.png "Barra de status texto cor modo específico da plataforma")

<a name="large_title" />

### <a name="displaying-large-titles"></a>Exibir títulos grandes

Este específicos da plataforma é usado para exibir o título da página como um título grande na barra de navegação, para dispositivos que usam iOS 11 ou superior. Um título grande é alinhado à esquerda e usa uma fonte maior e faz a transição para um título padrão conforme o usuário começa a rolagem de conteúdo, para que o espaço na tela é usado com eficiência. No entanto, em orientação paisagem, o título retornará para o centro da barra de navegação para otimizar o layout do conteúdo. Ele é consumido em XAML, definindo o `NavigationPage.PrefersLargeTitles` anexado à propriedade um `boolean` valor:

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

O `NavigationPage.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `NavigationPage.SetPrefersLargeTitle` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, que controla se os títulos grandes estão habilitados.

Desde que os títulos grandes estão habilitados na [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage), todas as páginas na pilha de navegação serão exibir títulos grandes. Esse comportamento pode ser substituído nas páginas, definindo o `Page.LargeTitleDisplay` propriedade anexada a um valor da `LargeTitleDisplayMode` enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

Como alternativa, o comportamento de página pode ser substituído no c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSLargeTitlePageCS : ContentPage
{
    public iOSLargeTitlePageCS(ICommand restore)
    {
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        ...
    }
    ...
}
```

O `Page.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `Page.SetLargeTitleDisplay` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, controla o comportamento de título grande no [ `Page` ](xref:Xamarin.Forms.Page), com o `LargeTitleDisplayMode` fornecendo três possíveis de enumeração valores:

- `Always` – forçar a barra de navegação e a fonte tamanho para usar o formato grande.
- `Automatic` – use o mesmo estilo (pequeno ou grande) como o item anterior na pilha de navegação.
- `Never` – forçar o uso da barra de navegação de formato pequeno regular.

Além disso, o `SetLargeTitleDisplay` método pode ser usado para alternar os valores de enumeração por meio da chamada a `LargeTitleDisplay` método, que retorna atual `LargeTitleDisplayMode`:

```csharp
switch (On<iOS>().LargeTitleDisplay())
{
    case LargeTitleDisplayMode.Always:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Automatic);
        break;
    case LargeTitleDisplayMode.Automatic:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        break;
    case LargeTitleDisplayMode.Never:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Always);
        break;
}
```

O resultado é que o especificado `LargeTitleDisplayMode` é aplicada para o [ `Page` ](xref:Xamarin.Forms.Page), que controla o comportamento de título grande:

![](ios-images/large-title.png "Específico da plataforma efeito de desfoque")

<a name="set_status_bar_visibility" />

### <a name="setting-the-status-bar-visibility-on-a-page"></a>Definir a barra de Status visibilidade em uma página

Este específicos da plataforma é usado para definir a visibilidade da barra de status em uma [ `Page` ](xref:Xamarin.Forms.Page), e inclui a capacidade de controlar como a barra de status entra ou sai de `Page`. Ele é consumido em XAML, definindo o `Page.PrefersStatusBarHidden` propriedade anexada a um valor da `StatusBarHiddenMode` enumeração e, opcionalmente, o `Page.PreferredStatusBarUpdateAnimation` propriedade anexada a um valor da `UIStatusBarAnimation` enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

O `Page.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `Page.SetPrefersStatusBarHidden` método, no `Xamarin.Forms.PlatformConfiguration.iOSSpecific` namespace, é usado para definir a visibilidade da barra de status em um [ `Page` ](xref:Xamarin.Forms.Page) especificando um dos `StatusBarHiddenMode` valores de enumeração: `Default`, `True` , ou `False`. O `StatusBarHiddenMode.True` e `StatusBarHiddenMode.False` valores definidos a visibilidade da barra de status, independentemente da orientação do dispositivo e o `StatusBarHiddenMode.Default` valor oculta a barra de status em um ambiente compact verticalmente.

O resultado é que a visibilidade da barra de status em uma [ `Page` ](xref:Xamarin.Forms.Page) podem ser definidos:

![](ios-images/hide-status-bar.png "Específicos de plataforma de visibilidade de barra de status")

> [!NOTE]
> Em um [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), especificado `StatusBarHiddenMode` valor de enumeração também atualizará a barra de status em todas as páginas filho. Em todos os outros [ `Page` ](xref:Xamarin.Forms.Page)-tipos derivados, especificados `StatusBarHiddenMode` valor de enumeração só atualizará a barra de status na página atual.

O `Page.SetPreferredStatusBarUpdateAnimation` método é usado para definir como a barra de status entra ou sai do [ `Page` ](xref:Xamarin.Forms.Page) especificando um da `UIStatusBarAnimation` valores de enumeração: `None`, `Fade`, ou `Slide`. Se o `Fade` ou `Slide` o valor de enumeração for especificado, uma segunda 0,25 animação é executada como a barra de status entra ou sai do `Page`.

<a name="safe_area_layout" />

### <a name="enabling-the-safe-area-layout-guide"></a>Habilitar o guia de Layout de área de segurança

Este específicos da plataforma é usado para garantir que o conteúdo da página é posicionado em uma área da tela que é segura para todos os dispositivos que usam iOS 11 e superior. Especificamente, ele ajudará a garantir que o conteúdo não é recortado pelo cantos arredondados de dispositivo, o indicador de base ou a estrutura do sensor em um iPhone X. Ele é consumido em XAML, definindo o `Page.UseSafeArea` anexado à propriedade um `boolean` valor:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Safe Area"
             ios:Page.UseSafeArea="true">
    <StackLayout>
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

O `Page.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `Page.SetUseSafeArea` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, que controla se o guia do layout da área de segurança está habilitado.

O resultado é que o conteúdo da página pode ser posicionado em uma área da tela que é segura para todos os iPhones:

[![](ios-images/safe-area-layout.png "Guia do Layout da área de segurança")](ios-images/safe-area-layout-large.png#lightbox "guia do Layout da área de segurança")

> [!NOTE]
> Área de segurança definida pela Apple é usada no xamarin. Forms para definir a [ `Page.Padding` ](xref:Xamarin.Forms.Page.Padding) propriedade e substituirá qualquer anteriores valores dessa propriedade que foram definidos.

Área de segurança pode ser personalizada, recuperando seu [ `Thickness` ](xref:Xamarin.Forms.Thickness) de valor com o `Page.SafeAreaInsets` método da [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace. Em seguida, pode ser modificado como necessário e reatribuído à `Padding` propriedade no construtor da página ou [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) substituir:

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

<a name="modal-page-presentation-style" />

### <a name="setting-the-modal-page-presentation-style-on-an-ipad"></a>Definindo o estilo de apresentação de página Modal em um iPad

Essa plataforma específica é usada para definir o estilo de apresentação de uma página modal em um iPad. Ele é consumido em XAML, definindo o `Page.ModalPresentationStyle` propriedade associável a uma `UIModalPresentationStyle` valor de enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="FormSheet">
    ...
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSModalFormSheetPageCS : ContentPage
{
    public iOSModalFormSheetPageCS()
    {
        On<iOS>().SetModalPresentationStyle(UIModalPresentationStyle.FormSheet);
        ...
    }
}
```

O `Page.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `Page.SetModalPresentationStyle` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para definir o estilo de apresentação modal em um [ `Page` ](xref:Xamarin.Forms.Page) especificando um dos seguintes `UIModalPresentationStyle` enumeração valores:

- `FullScreen`, que define o estilo de apresentação modal para abranger a tela inteira. Por padrão, as páginas modais são exibidas usando esse estilo de apresentação.
- `FormSheet`, que define o estilo de apresentação modal seja menor do que a tela e centralizada no.

Além disso, o `GetModalPresentationStyle` método pode ser usado para recuperar o valor atual do `UIModalPresentationStyle` enumeração é aplicada para o [ `Page` ](xref:Xamarin.Forms.Page).

O resultado é que o estilo de apresentação modal em uma [ `Page` ](xref:Xamarin.Forms.Page) podem ser definidos:

[![](ios-images/modal-presentation-style-small.png "Estilos de apresentação modal em um iPad")](ios-images/modal-presentation-style-large.png#lightbox "Modal estilos de apresentação em um iPad")

> [!NOTE]
> As páginas que usam esse específicos da plataforma para definir o estilo de apresentação modal devem usar a navegação modal. Para obter mais informações, consulte [páginas modais do xamarin. Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="layouts"></a>Layouts

No iOS, a seguinte funcionalidade específica da plataforma é fornecida para layouts do xamarin. Forms:

- Controlar se uma [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) manipula um gesto de toque ou passá-lo ao seu conteúdo. Para obter mais informações, consulte [atrasando toques conteúdo em um ScrollView](#delay_content_touches).

<a name="delay_content_touches" />

### <a name="delaying-content-touches-in-a-scrollview"></a>Atrasando toques de conteúdo em um ScrollView

Um temporizador implícito é disparado quando um gesto de toque começa em um [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) no iOS e o `ScrollView` decide, com base na ação de usuário na extensão de timer, se ele deve tratar o gesto ou passá-lo ao seu conteúdo. Por padrão, o iOS `ScrollView` toques de conteúdo de atrasos, mas isso pode causar problemas em algumas circunstâncias com o `ScrollView` conteúdo não ganhando o gesto quando deveria. Portanto, esse controles específicos da plataforma se um `ScrollView` manipula um gesto de toque ou passá-lo ao seu conteúdo. Ele é consumido em XAML, definindo o `ScrollView.ShouldDelayContentTouches` anexado à propriedade um `boolean` valor:

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <MasterDetailPage.Master>
        <ContentPage Title="Menu" BackgroundColor="Blue" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <ContentPage>
            <ScrollView x:Name="scrollView" ios:ScrollView.ShouldDelayContentTouches="false">
                <StackLayout Margin="0,20">
                    <Slider />
                    <Button Text="Toggle ScrollView DelayContentTouches" Clicked="OnButtonClicked" />
                </StackLayout>
            </ScrollView>
        </ContentPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

O `ScrollView.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `ScrollView.SetShouldDelayContentTouches` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usada para controlar se uma [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) manipula um gesto de toque ou passá-lo ao seu conteúdo. Além disso, o `SetShouldDelayContentTouches` método pode ser usado para ativar/desativar atrasando o conteúdo de toques, chamando o `ShouldDelayContentTouches` método para retornar se o conteúdo de toques estão atrasadas:

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

O resultado é que um [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) pode desabilitar atrasando o recebimento de toques de conteúdo, portanto, que neste cenário o [ `Slider` ](xref:Xamarin.Forms.Slider) recebe o gesto em vez de [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) página do [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

[![](ios-images/scrollview-delay-content-touches.png "Conteúdo de atraso de ScrollView toca específicos da plataforma")](ios-images/scrollview-delay-content-touches-large.png#lightbox "conteúdo de atraso de ScrollView toca específicos da plataforma")

## <a name="application"></a>Aplicativo

No iOS, a seguinte funcionalidade específica da plataforma é fornecida para o xamarin. Forms [ `Application` ](xref:Xamarin.Forms.Application) classe:

- Habilitando um [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) em uma exibição de rolagem para capturar e compartilhar o gesto de panorâmica com o modo de exibição de rolagem. Para obter mais informações, consulte [habilitando reconhecimento de gesto de panorâmica simultâneas](#simultaneous-pan-gesture).

<a name="simultaneous-pan-gesture" />

### <a name="enabling-simultaneous-pan-gesture-recognition"></a>Habilitando o reconhecimento de gesto de panorâmica simultâneas

Quando um [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) é anexado a um modo de exibição dentro de uma exibição de rolagem, todos a panorâmica de gestos são capturados pelo `PanGestureRecognizer` e não serão passados para o modo de exibição de rolagem. Portanto, o modo de exibição de rolagem não mais será rolada.

Este específicos da plataforma permite que um `PanGestureRecognizer` em uma exibição de rolagem para capturar e compartilhar o gesto de panorâmica com o modo de exibição de rolagem. Ele é consumido em XAML, definindo o [ `Application.PanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty) anexado à propriedade `true`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.PanGestureRecognizerShouldRecognizeSimultaneously="true">
    ...
</Application>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetPanGestureRecognizerShouldRecognizeSimultaneously(true);
```

O `Application.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `Application.SetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.SetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usada para controlar se um reconhecedor de gestos em uma exibição da rolagem panorâmica capturar o gesto de panorâmica, ou capturar e compartilhar a panorâmica com o modo de exibição de rolagem do gesto. Além disso, o [ `Application.GetPanGestureRecognizerShouldRecognizeSimultaneously` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.GetPanGestureRecognizerShouldRecognizeSimultaneously(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Application})) método pode ser usado para retornar se o gesto de Panorâmica é compartilhado com o modo de exibição de rolagem que contém o [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer).

Portanto, com essa específicos da plataforma habilitado, quando um [ `ListView` ](xref:Xamarin.Forms.ListView) contém um [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer), ambos o `ListView` e o `PanGestureRecognizer` receberá o gesto de Panorâmica e processá-lo. No entanto, com essa específicos da plataforma desabilitado, quando um `ListView` contém uma `PanGestureRecognizer`, o `PanGestureRecognizer` capturará o gesto de Panorâmica e processá-lo e o `ListView` não receberá o gesto de panorâmica.

## <a name="summary"></a>Resumo

Este artigo demonstrou como consumir as iOS-especificidades da plataforma que são criadas no xamarin. Forms. Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos.

## <a name="related-links"></a>Links relacionados

- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
