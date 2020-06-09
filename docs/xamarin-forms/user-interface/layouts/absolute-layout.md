---
Título: " Xamarin.Forms AbsoluteLayout" Descrição: "Este artigo explica como usar a Xamarin.Forms classe AbsoluteLayout para criar interfaces do uso de pixel perfeito. Essa classe posiciona e dimensiona elementos filho proporcionais ao seu próprio tamanho e posição ou por valores absolutos. "
MS. Prod: xamarin MS. AssetID: 01A5CCE0-AD45-4806-84FD-72C007005B38 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 11/25/2015 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-absolutelayout"></a>Xamarin.FormsAbsoluteLayout

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)Posiciona e dimensiona elementos filho proporcionais ao seu próprio tamanho e posição ou por valores absolutos. Os modos de exibição filho podem ser posicionados e dimensionados usando valores proporcionais ou valores estáticos, e os valores proporcionais e estáticos podem ser misturados.

[![](absolute-layout-images/layouts-sml.png "Xamarin.Forms Layouts")](absolute-layout-images/layouts.png#lightbox "Xamarin.Forms Layouts")

Este artigo abordará:

- **[Finalidade](#purpose)** &ndash; usos comuns do `AbsoluteLayout` .
- **[Uso](#usage)** &ndash; do como usar `AbsoluteLayout` o para obter o design desejado.
  - **[Layouts](#proportional-layouts)** &ndash; proporcionais Entenda como os valores proporcionais funcionam em um `AbsoluteLayout` .
  - **[Especificando valores](#specifying-values)** &ndash; Entenda como os valores proporcionais e absolutos são especificados.
  - **[Valores](#proportional-values)** &ndash; proporcionais Entenda como funcionam os valores proporcionais.
    - **[Valores](#absolute-values)** &ndash; absolutos Entenda como os valores absolutos funcionam.

## <a name="purpose"></a>Finalidade

Devido ao modelo de posicionamento do `AbsoluteLayout` , o layout torna relativamente simples posicionar elementos para que eles sejam liberados com qualquer lado do layout ou centralizado. Com tamanhos e posições proporcionais, os elementos em um `AbsoluteLayout` podem ser dimensionados automaticamente para qualquer tamanho de exibição. Para itens em que apenas a posição, mas não o tamanho, devem ser dimensionados, os valores absolutos e proporcionais podem ser misturados.

`AbsoluteLayout`pode ser usado em qualquer lugar que os elementos precisem ser posicionados em uma exibição e seja especialmente útil ao alinhar elementos a bordas.

## <a name="usage"></a>Uso

### <a name="proportional-layouts"></a>Layouts proporcionais

`AbsoluteLayout`tem um modelo de âncora exclusivo no qual a âncora do elemento é posicionada em relação ao seu elemento, pois o elemento é posicionado em relação ao layout quando o posicionamento proporcional é usado. Quando o posicionamento absoluto é usado, a âncora é em (0, 0) na exibição. Isso tem duas consequências importantes:

- Os elementos não podem ser posicionados fora da tela usando valores proporcionais.
- Os elementos podem ser posicionados de forma confiável junto com qualquer lado do layout ou no centro, independentemente do tamanho do layout ou do dispositivo.

`AbsoluteLayout`, como `RelativeLayout` , é capaz de posicionar elementos para que eles se sobreponham.

Observe que, na captura de tela a seguir, a âncora da caixa é um ponto branco. Observe a relação entre a âncora e a caixa à medida que percorre o layout:

![](absolute-layout-images/anchor-start.png "Anchor at Start")
![](absolute-layout-images/anchor-center.png "Anchor at Center")
![](absolute-layout-images/anchor-end.png "Anchor at End")

### <a name="specifying-values"></a>Especificando valores

Exibições em um `AbsoluteLayout` são posicionadas usando quatro valores:

- **X** &ndash; a posição x (horizontal) da âncora da exibição
- **Y** &ndash; a posição y (vertical) da âncora da exibição
- **Largura** &ndash; a largura da exibição
- **Altura** &ndash; a altura da exibição

Cada um desses valores pode ser definido como um valor [proporcional](#proportional-values) ou um valor [absoluto](#absolute-values) .

Os valores são especificados como uma combinação de limites e um sinalizador. `LayoutBounds`é [`Rectangle`](xref:Xamarin.Forms.Rectangle) composto por quatro valores:,, `x` `y` `width` , `height` .

### <a name="absolutelayoutflags"></a>AbsoluteLayoutFlags

[`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags)Especifica como os valores serão interpretados e tem as seguintes opções predefinidas:

- **Nenhum** &ndash; interpreta todos os valores como absolutos. Esse será o valor padrão se nenhum sinalizador de layout for especificado.
- **Todos** &ndash; interpreta todos os valores como proporcionais.
- **WidthProportional** &ndash; interpreta o `Width` valor como proporcional e todos os outros valores como absolutos.
- **HeightProportional** &ndash; interpreta apenas o valor de altura como proporcional a todos os outros valores absolutos.
- **XProportional** &ndash; interpreta o `X` valor como proporcional, ao mesmo tempo que trata todos os outros valores como absolutos.
- **YProportional** &ndash; interpreta o `Y` valor como proporcional, ao mesmo tempo que trata todos os outros valores como absolutos.
- **PositionProportional** &ndash; interpreta os `X` valores e `Y` como proporcionais, enquanto os valores de tamanho são interpretados como absolutos.
- **SizeProportional** &ndash; interpreta os `Width` valores e `Height` como proporcionais, enquanto os valores de posição são absolutos.

No XAML, os limites e os sinalizadores são definidos como parte da definição de exibições no layout, usando a `AbsoluteLayout.LayoutBounds` propriedade. Os limites são definidos como uma lista de valores separados por vírgulas, `X` ,, `Y` `Width` e `Height` , nessa ordem. Os sinalizadores também são especificados na declaração de exibições no layout usando a `AbsoluteLayout.LayoutFlags` propriedade. Observe que os sinalizadores podem ser combinados em XAML usando uma lista separada por vírgulas. Considere o exemplo a seguir:

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

![](absolute-layout-images/exploration.png "AbsoluteLayout Examples")

Observe o seguinte:

- O rótulo no centro é posicionado usando valores de tamanho absoluto e posição. Por isso, ele aparece centralizado no iPhone 4S e inferior, mas não é centralizado em dispositivos maiores.
- O texto na parte inferior do layout é posicionado usando valores de tamanho proporcional e posição. Ele sempre aparecerá na parte inferior central do layout, mas seu tamanho aumentará com tamanhos de layout maiores.
- Três `BoxView` s coloridas são posicionadas nas bordas superior, esquerda e direita da tela usando a posição proporcional e o tamanho absoluto.

O seguinte Obtém o mesmo layout em C#:

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

### <a name="proportional-values"></a>Valores proporcionais

Os valores proporcionais definem uma relação entre um layout e uma exibição. Essa relação define a posição de uma exibição filho ou o valor de escala como uma proporção do valor correspondente do layout pai. Esses valores são expressos como `double` s com valores entre 0 e 1.

Os valores proporcionais são usados para posicionar e dimensionar exibições dentro do layout. Assim, quando a largura de uma exibição é definida como uma proporção, o valor de largura resultante é a proporção multiplicada pela `AbsoluteLayout` largura do. Por exemplo, com uma `AbsoluteLayout` largura `500` e uma exibição definidas para ter uma largura proporcional de 0,5, a largura renderizada da exibição será 250 (500 x. 5).

Para usar valores proporcionais, defina `LayoutBounds` usando as proporções (x, y) e os tamanhos proporcionais e, em seguida, defina `LayoutFlags` como `All` .

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

### <a name="absolute-values"></a>Valores absolutos

Os valores absolutos definem explicitamente onde as exibições devem ser posicionadas dentro do layout. Ao contrário dos valores proporcionais, os valores absolutos são capazes de posicionar e dimensionar uma exibição que não caiba nos limites do layout.

O uso de valores absolutos para posicionamento pode ser perigoso quando o tamanho do layout não for conhecido. Ao usar posições absolutas, um elemento no centro da tela em um tamanho pode ser deslocado em qualquer outro tamanho. É importante testar seu aplicativo em vários tamanhos de tela dos seus dispositivos com suporte.

Para usar valores de layout absolutos, defina `LayoutBounds` usando coordenadas (x, y) e tamanhos explícitos e, em seguida, defina `LayoutFlags` como `None` .

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

## <a name="exploring-a-complex-layout"></a>Explorando um layout complexo

Cada um dos layouts tem pontos fortes e fracos para a criação de layouts específicos. Ao longo desta série de artigos de layout, um aplicativo de exemplo foi criado com o mesmo layout de página implementado usando três layouts diferentes.

Considere o seguinte XAML:

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

O código acima resulta no seguinte layout:

![](absolute-layout-images/abs.png "Complex AbsoluteLayout")

Observe que `AbsoluteLayout` s estão aninhados, pois, em alguns casos, o aninhamento de layouts pode ser mais fácil do que apresentar todos os elementos dentro do mesmo layout.

## <a name="related-links"></a>Links relacionados

- [Criando aplicativos móveis com o Xamarin.Forms , capítulo 14](https://developer.xamarin.com/r/xamarin-forms/book/chapter14.pdf)
- [AbsoluteLayout](xref:Xamarin.Forms.AbsoluteLayout)
- [Layout (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Exemplo de BusinessTumble (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
