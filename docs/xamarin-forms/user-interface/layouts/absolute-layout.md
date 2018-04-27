---
title: AbsoluteLayout
description: Use AbsoluteLayout para criar interfaces do usuário perfeita de pixel.
ms.prod: xamarin
ms.assetid: 01A5CCE0-AD45-4806-84FD-72C007005B38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: 110c608558059ba0f207b4cff343b428125e1784
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2018
---
# <a name="absolutelayout"></a>AbsoluteLayout

[`AbsoluteLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) posiciona e tamanhos de elementos filho proporcionais ao seu próprio tamanho e posição ou por valores absolutos. Modos de exibição filho podem ser posicionados e dimensionados usando proporcionais valores ou valores estáticos e proporcional e valores estáticos podem ser combinados.

[![](absolute-layout-images/layouts-sml.png "Layouts de xamarin. Forms")](absolute-layout-images/layouts.png#lightbox "xamarin. Forms Layouts")

Este artigo aborda:

- **[Finalidade](#Purpose)**  &ndash; usos comuns `AbsoluteLayout`.
- **[Uso](#Usage)**  &ndash; como usar `AbsoluteLayout` para obter o design desejado.
  - **[Layouts proporcionais](#Proportional_Layouts)**  &ndash; entender valores proporcionais como trabalho em um `AbsoluteLayout`.
  - **[Especificando valores](#Specifying_Values)**  &ndash; entender como proporcional e valores absolutos são especificados.
  - **[Valores proporcionais](#Proportional_Values)**  &ndash; entender valores como proporcionais de trabalho.
    - **[Valores absolutos](#Absolute_Values)**  &ndash; entender como funcionam os valores absolutos.

<a name="Purpose" />

## <a name="purpose"></a>Finalidade

Por causa do modelo de posicionamento de `AbsoluteLayout`, o layout torna relativamente simples posicionar elementos para que eles sejam alinhada com qualquer lado do layout ou centralizado. Com tamanhos proporcionais e posições, elementos de um `AbsoluteLayout` pode dimensionar automaticamente a qualquer tamanho de exibição. Para itens em que apenas a posição, mas não o tamanho deve ser dimensionado, valores absolutos e proporcionais podem ser mesclados.

`AbsoluteLayout` pode ser usado em qualquer local precisa ser posicionado em uma exibição de elementos e é especialmente útil ao alinhar elementos para bordas.

<a name="Usage" />

## <a name="usage"></a>Uso

<a name="Proportional_Layouts" />

### <a name="proportional-layouts"></a>Layouts proporcionais

`AbsoluteLayout` tem um modelo de âncora exclusivo pelo qual a âncora do elemento é posicionada em relação ao seu elemento como o elemento é posicionado em relação ao layout quando posicionamento proporcional é usado. Quando o posicionamento absoluto é usado, a âncora é em (0,0) no modo de exibição. Isso tem duas consequências importantes:

- Elementos não podem ser posicionados fora da tela usando valores proporcionais.
- Elementos podem ser posicionados ao longo de qualquer lado do layout ou no centro, independentemente do tamanho do layout ou dispositivo confiável.

`AbsoluteLayout`, como `RelativeLayout`, pode posicionar elementos para que eles se sobrepõem.

Observe na captura de tela abaixo, a âncora da caixa é um ponto branco. Observe a relação entre a âncora e a caixa enquanto se movimentam pelo layout:

![](absolute-layout-images/anchor-start.png "Âncora no início")
![](absolute-layout-images/anchor-center.png "âncora no Centro de")
![](absolute-layout-images/anchor-end.png "âncora final")

<a name="Specifying_Values" />

### <a name="specifying-values"></a>Especificando valores

Exibições de dentro de um `AbsoluteLayout` são posicionados usando quatro valores:

- **X** &ndash; a posição de x (horizontal) da âncora do modo de exibição
- **Y** &ndash; a posição y (vertical) da âncora do modo de exibição
- **Largura** &ndash; a largura do modo de exibição
- **Altura** &ndash; a altura da exibição

Cada um desses valores pode ser definida como um [proporcional](#Proportional_Values) valor ou uma [absoluto](#Absolute_Values) valor.

Os valores são especificados como uma combinação de limites e um sinalizador. `LayoutBounds` é um [ `Rectangle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Rectangle/) consiste em quatro valores: `x`, `y`, `width`, `height`.

### <a name="absolutelayoutflags"></a>AbsoluteLayoutFlags

[`AbsoluteLayoutFlags`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayoutFlags/) Especifica como os valores serão interpretados e tem as seguintes opções predefinidas:

- **Nenhum** &ndash; interpreta todos os valores como absoluto. Este é o valor padrão se nenhum sinalizador de layout forem especificados.
- **Todos os** &ndash; interpreta todos os valores como proporcional.
- **WidthProportional** &ndash; interpreta o `Width` valor proporcional e todos os outros valores como absoluto.
- **HeightProportional** &ndash; interpreta apenas o valor da altura como proporcional com todos os outros valores absolutos.
- **XProportional** &ndash; interpreta o `X` valor proporcional, ao tratar todos os outros valores como absoluto.
- **YProportional** &ndash; interpreta o `Y` valor proporcional, ao tratar todos os outros valores como absoluto.
- **PositionProportional** &ndash; interpreta o `X` e `Y` valores como proporcional, enquanto os valores de tamanho são interpretados como absoluto.
- **SizeProportional** &ndash; interpreta o `Width` e `Height` valores como proporcional, enquanto os valores de posição são absolutos.

Em XAML, limites e os sinalizadores são definidos como parte da definição de exibições no layout, usando o `AbsoluteLayout.LayoutBounds` propriedade. Limites são definidos como uma lista separada por vírgulas de valores, `X`, `Y`, `Width`, e `Height`, nessa ordem. Sinalizadores também são especificados na declaração de modos de exibição de layout usando o `AbsoluteLayout.LayoutFlags` propriedade. Observe que os sinalizadores podem ser combinados em XAML usando uma lista separada por vírgulas. Considere o exemplo a seguir:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.AbsoluteLayoutExploration"
Title="Absolute Layout Exploration">
    <ContentPage.Content>
        <AbsoluteLayout>
            <Label Text="I'm centered on iPhone 4 but no other device"
                AbsoluteLayout.LayoutBounds="115,150,100,100" LineBreakMode="WordWrap"  />
            <Label Text="I'm bottom center on every device."
                AbsoluteLayout.LayoutBounds=".5,1,.5,.1" AbsoluteLayout.LayoutFlags="All"
                LineBreakMode="WordWrap"  />
            <BoxView Color="Olive"  AbsoluteLayout.LayoutBounds="1,.5, 25, 100"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
            <BoxView Color="Red" AbsoluteLayout.LayoutBounds="0,.5,25,100"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
            <BoxView Color="Blue" AbsoluteLayout.LayoutBounds=".5,0,100,25"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

![](absolute-layout-images/exploration.png "Exemplos de AbsoluteLayout")

Observe o seguinte:

- O rótulo no centro é posicionado usando valores de tamanho e posição absolutos. Por isso, ele aparece em 4S iPhone e inferior, mas não centralizado em dispositivos maior.
- O texto na parte inferior do layout é posicionado usando valores de tamanho e posição proporcionais. Ele sempre será exibido na parte inferior central do layout, mas seu tamanho aumentará com os maiores tamanhos de layout.
- Três cores `BoxView`s são posicionados nas bordas superiores, esquerdas e direito da tela usando proporcional posição e tamanho absoluto.

O exemplo a seguir obtém o mesmo layout em c#:

```csharp
public class AbsoluteLayoutExplorationCode : ContentPage
{
    public AbsoluteLayoutExplorationCode ()
    {
        Title = "Absolute Layout Exploration - Code";
        var layout = new AbsoluteLayout();

        var centerLabel = new Label {
        Text = "I'm centered on iPhone 4 but no other device.",
        LineBreakMode = LineBreakMode.WordWrap};

        AbsoluteLayout.SetLayoutBounds (centerLabel, new Rectangle (115, 159, 100, 100));
        // No need to set layout flags, absolute positioning is the default

        var bottomLabel = new Label { Text = "I'm bottom center on every device.", LineBreakMode = LineBreakMode.WordWrap };
        AbsoluteLayout.SetLayoutBounds (bottomLabel, new Rectangle (.5, 1, .5, .1));
        AbsoluteLayout.SetLayoutFlags (bottomLabel, AbsoluteLayoutFlags.All);

        var rightBox = new BoxView{ Color = Color.Olive };
        AbsoluteLayout.SetLayoutBounds (rightBox, new Rectangle (1, .5, 25, 100));
        AbsoluteLayout.SetLayoutFlags (rightBox, AbsoluteLayoutFlags.PositionProportional);

        var leftBox = new BoxView{ Color = Color.Red };
        AbsoluteLayout.SetLayoutBounds (leftBox, new Rectangle (0, .5, 25, 100));
        AbsoluteLayout.SetLayoutFlags (leftBox, AbsoluteLayoutFlags.PositionProportional);

        var topBox = new BoxView{ Color = Color.Blue };
        AbsoluteLayout.SetLayoutBounds (topBox, new Rectangle (.5, 0, 100, 25));
        AbsoluteLayout.SetLayoutFlags (topBox, AbsoluteLayoutFlags.PositionProportional);

        layout.Children.Add (bottomLabel);
        layout.Children.Add (centerLabel);
        layout.Children.Add (rightBox);
        layout.Children.Add (leftBox);
        layout.Children.Add (topBox);

        Content = layout;
    }
}
```
<a name="Proportional_Values" />

### <a name="proportional-values"></a>Valores proporcionais

Valores proporcionais definem uma relação entre um layout e um modo de exibição. Essa relação define a posição de um modo de exibição de filho ou valor de escala como uma proporção do valor correspondente do layout do pai. Esses valores são expressos como `double`s com valores entre 0 e 1.

Valores proporcionais são usados para exibições de tamanho no layout e posição. Assim, quando a largura da exibição é definida como uma proporção, o valor de largura resultante é a proporção multiplicada pelo `AbsoluteLayout`da largura. Por exemplo, com um `AbsoluteLayout` da largura `500` e uma exibição definida para ter uma largura proporcional de.5, a largura processada do modo de exibição será 250 (500 x.5).

Para usar valores proporcionais, defina `LayoutBounds` usando (x, y) proporções e proporcionais tamanhos, em seguida, defina `LayoutFlags` para `All`.

Em XAML:

```xaml
<Label Text="I'm bottom center on every device."
  AbsoluteLayout.LayoutBounds=".5,1,.5,.1" AbsoluteLayout.LayoutFlags="All"  />
```

No C#:

```csharp
var label = new Label {Text = "I'm bottom center on every device."};
AbsoluteLayout.SetLayoutBounds(label, new Rectangle(.5,1,.5,.1));
AbsoluteLayout.SetLayoutFlags(label, AbsoluteLayoutFlags.All);
```

<a name="Absolute_Values" />

### <a name="absolute-values"></a>Valores absolutos

Valores absolutos definir explicitamente onde os modos de exibição devem ser posicionados dentro do layout. Ao contrário de valores proporcionais, valores absolutos são capazes de posicionamento e dimensionamento de um modo de exibição que não cabe dentro dos limites do layout.

Usando valores absolutos para posicionamento pode ser perigoso quando o tamanho do layout não é conhecido. Ao usar posições absolutas, um elemento no centro da tela de um tamanho foi deslocamento em qualquer outro tamanho. É importante testar seu aplicativo em vários tamanhos de tela de seus dispositivos com suporte.

Para usar valores de layout absoluto, defina `LayoutBounds` usando (x, y) coordenadas e tamanhos explícitos, em seguida, defina `LayoutFlags` para `None`.

Em XAML:

```xaml
<Label Text="I'm centered on iPhone 4 but no other device."
  AbsoluteLayout.LayoutBounds="115,150,100,100"  />
```

No C#:

```csharp
var label = new Label {Text = "I'm centered on iPhone 4 but no other device."};
AbsoluteLayout.SetLayoutBounds(label, new Rectangle(115,150,100,100));
```

## <a name="exploring-a-complex-layout"></a>Explorar um Layout complexo

Cada um dos layouts têm vantagens e desvantagens para a criação de layouts específicos. Em toda esta série de artigos de layout, um aplicativo de exemplo foi criado com o mesmo layout de página implementado usando três layouts diferentes.

Considere o XAML a seguir:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.AbsoluteLayoutPage"
Title="AbsoluteLayout">
    <ContentPage.ToolbarItems>
        <ToolbarItem Text="Save" />
    </ContentPage.ToolbarItems>
    <ContentPage.Content>
        <ScrollView>
            <AbsoluteLayout BackgroundColor="Maroon">
                <BoxView BackgroundColor="Gray" AbsoluteLayout.LayoutBounds="0
                    0,1,100" AbsoluteLayout.LayoutFlags="XProportional,YProportional,WidthProportional" />
                <Button BackgroundColor="Maroon"
                    AbsoluteLayout.LayoutBounds=".5,55,70,70" AbsoluteLayout.LayoutFlags="XProportional" BorderRadius="35" />
                <Button BackgroundColor="Red" AbsoluteLayout.LayoutBounds=".5
                    60,60,60" AbsoluteLayout.LayoutFlags="XProportional" BorderRadius="30" />
                <Label Text="User Name" FontAttributes="Bold" FontSize="26"
                    TextColor="Black" HorizontalTextAlignment="Center" AbsoluteLayout.LayoutBounds=".5,140,1,40" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                <Entry Text="Bio + Hashtags" TextColor="White"
                    BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds=".5,180,1,40" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                <AbsoluteLayout BackgroundColor="White"
                        AbsoluteLayout.LayoutBounds="0, 220, 1, 50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional">
                    <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,0,.5,1" AbsoluteLayout.LayoutFlags="WidthProportional,HeightProportional">
                        <Button BackgroundColor="Black" BorderRadius="20"
                            AbsoluteLayout.LayoutBounds="5,.5,40,40" AbsoluteLayout.LayoutFlags="YProportional" />
                        <Label Text="Accent Color" TextColor="Black"
                            AbsoluteLayout.LayoutBounds="50,.55,1,25" AbsoluteLayout.LayoutFlags="YProportional,WidthProportional" />
                    </AbsoluteLayout>
                    <AbsoluteLayout AbsoluteLayout.LayoutBounds="1,0,.5,1" AbsoluteLayout.LayoutFlags="WidthProportional,HeightProportional,XProportional">
                        <Button BackgroundColor="Maroon" BorderRadius="20"
                            AbsoluteLayout.LayoutBounds="5,.5,40,40" AbsoluteLayout.LayoutFlags="YProportional" />
                        <Label Text="Primary Color" TextColor="Black"
                            AbsoluteLayout.LayoutBounds="50,.55,1,25" AbsoluteLayout.LayoutFlags="YProportional,WidthProportional" />
                    </AbsoluteLayout>
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,270,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Age:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="35" TextColor="White" BackgroundColor="Maroon"
                        AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,320,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Interests:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="Xamarin.Forms" TextColor="White"
                        BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,370,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Ask me about:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="Xamarin, C#, .NET, Mono" TextColor="White"
                        BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
            </AbsoluteLayout>
        </ScrollView>
    </ContentPage.Content>
</ContentPage>
```

O código acima resulta no layout a seguir:

![](absolute-layout-images/abs.png "AbsoluteLayout complexo")

Observe que `AbsoluteLayout`s estiverem aninhados, como em alguns casos aninhamento layouts pode ser mais fácil do que apresentar todos os elementos do layout do mesmo.

## <a name="related-links"></a>Links relacionados

- [Criando aplicativos móveis com o xamarin. Forms, capítulo 14](https://developer.xamarin.com/r/xamarin-forms/book/chapter14.pdf)
- [AbsoluteLayout](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/)
- [Layout (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Exemplo de BusinessTumble (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
