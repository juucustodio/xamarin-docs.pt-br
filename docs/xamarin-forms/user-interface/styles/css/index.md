---
title: Aplicar estilo a aplicativos xamarin. Forms usando folhas de estilo em cascata (CSS)
description: Xamarin. Forms dá suporte a elementos visuais de estilo usando folhas de estilo em cascata (CSS).
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 06/11/2019
ms.openlocfilehash: ed9a376da0fcfebffd707e2e93919237adfef87b
ms.sourcegitcommit: 0df727caf941f1fa0aca680ec871bfe7a9089e7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69620832"
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>Aplicar estilo a aplicativos xamarin. Forms usando folhas de estilo em cascata (CSS)

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)

_Xamarin. Forms dá suporte a elementos visuais de estilo usando folhas de estilo em cascata (CSS)._

Aplicativos xamarin. Forms podem ser estilizados usando CSS. Uma folha de estilos consiste em uma lista de regras, com cada regra consiste em um bloco de declaração e um ou mais dos seletores. Um bloco de declaração consiste em uma lista de declarações entre chaves, com cada declaração consiste em uma propriedade, dois-pontos e um valor. Quando há várias declarações em um bloco, uma ponto e vírgula é inserida como um separador. O exemplo de código a seguir mostra o CSS em conformidade com xamarin. Forms:

```css
navigationpage {
    -xf-bar-background-color: lightgray;
}

^contentpage {
    background-color: lightgray;
}

#listView {
    background-color: lightgray;
}

stacklayout {
    margin: 20;
}

.mainPageTitle {
    font-style: bold;
    font-size: medium;
}

.mainPageSubtitle {
    margin-top: 15;
}

.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}

listview image {
    height: 60;
    width: 60;
}

stacklayout>image {
    height: 200;
    width: 200;
}
```

No xamarin. Forms, folhas de estilo CSS são analisadas e avaliadas em tempo de execução, em vez de tempo de compilação e folhas de estilo são analisadas novamente em uso.

> [!NOTE]
> Atualmente, todos os estilos que são possíveis com estilo de XAML não podem ser executados com CSS. No entanto, os estilos XAML podem ser usados para complementar o CSS para propriedades que não são atualmente suportadas pelo xamarin. Forms. Para obter mais informações sobre estilos XAML, consulte [estilos aplicativos xamarin. Forms usando os estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

O [MonkeyAppCSS](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss) exemplo demonstra como usar CSS para definir o estilo de um aplicativo simples e é mostrado nas capturas de tela seguir:

[![Página principal de MonkeyApp com estilo de CSS](css-images/MonkeyAppMainPage.png "MonkeyApp Main Page com estilo de CSS")](css-images/MonkeyAppMainPage-Large.png#lightbox "MonkeyApp Main Page com estilo de CSS")

[![Página de detalhes de MonkeyApp com estilo de CSS](css-images/MonkeyAppDetailPage.png "MonkeyApp página de detalhes com estilo de CSS")](css-images/MonkeyAppDetailPage-Large.png#lightbox "MonkeyApp página de detalhes com estilo de CSS")

## <a name="consuming-a-style-sheet"></a>Consumindo uma folha de estilos

O processo para adicionar uma folha de estilos a uma solução é da seguinte maneira:

1. Adicione um arquivo CSS vazio ao seu projeto de biblioteca .NET Standard.
1. Defina a ação de compilação do arquivo CSS para **EmbeddedResource**.

### <a name="loading-a-style-sheet"></a>Carregando uma folha de estilos

Há várias abordagens que podem ser usados para carregar uma folha de estilos.

### <a name="xaml"></a>XAML

Uma folha de estilos pode ser carregada e analisada com o [ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet) classe antes de serem adicionados a um [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

```xaml
<Application ...>
    <Application.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </Application.Resources>
</Application>
```

O [ `StyleSheet.Source` ](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source) propriedade especifica a folha de estilos como um URI relativo ao local do arquivo XAML delimitador ou relativa à raiz do projeto se o URI começa com um `/`.

> [!WARNING]
> O arquivo CSS não carregará se sua ação de compilação não está definida como **EmbeddedResource**.

Como alternativa, uma folha de estilos pode ser carregada e analisada com o [ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet) classe antes de serem adicionados a um [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), por inlining-lo em um `CDATA` seção:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet>
            <![CDATA[
            ^contentpage {
                background-color: lightgray;
            }
            ]]>
        </StyleSheet>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Para obter mais informações sobre dicionários de recursos, consulte [dicionários de recursos](~/xamarin-forms/xaml/resource-dictionaries.md).

### <a name="c"></a>C\#

No C#, uma folha de estilos pode ser carregada como um recurso inserido e adicionada a um [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

```csharp
public partial class MyPage : ContentPage
{
    public MyPage()
    {
        InitializeComponent();

        this.Resources.Add(StyleSheet.FromAssemblyResource(
            IntrospectionExtensions.GetTypeInfo(typeof(MyPage)).Assembly,
            "MyProject.Assets.styles.css"));
    }
}
```

O primeiro argumento para o `StyleSheet.FromAssemblyResource` método é o assembly que contém a folha de estilos, enquanto o segundo argumento é um `string` que representa o identificador de recurso. O identificador de recurso pode ser obtido de **propriedades** janela quando o arquivo CSS está selecionado.

Como alternativa, uma folha de estilos pode ser carregada de um `StringReader` e adicionado a um [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

```csharp
public partial class MyPage : ContentPage
{
    public MyPage()
    {
        InitializeComponent();

        using (var reader = new StringReader("^contentpage { background-color: lightgray; }"))
        {
            this.Resources.Add(StyleSheet.FromReader(reader));
        }
    }
}
```

O argumento para o `StyleSheet.FromReader` método é o `TextReader` que leu a folha de estilos.

## <a name="selecting-elements-and-applying-properties"></a>Selecionando elementos e aplicação de propriedades

O CSS usa seletores para determinar quais elementos de destino. Estilos com correspondência seletores são aplicados consecutivamente, na ordem de definição. Os estilos definidos em um item específico são sempre aplicados pela última vez. Para obter mais informações sobre os seletores com suporte, consulte [seletor de referência](#selector-reference).

O CSS usa propriedades para definir o estilo de um elemento selecionado. Cada propriedade tem um conjunto de valores possíveis, e algumas propriedades podem afetar qualquer tipo de elemento, enquanto outras se aplicam a grupos de elementos. Para obter mais informações sobre as propriedades com suporte, consulte [referência de propriedade](#property-reference).

### <a name="selecting-elements-by-type"></a>Seleção de elementos por tipo

Elementos na árvore visual podem ser selecionados por tipo com maiusculas e minúsculas `element` seletor:

```css
stacklayout {
    margin: 20;
}
```

Este seletor identifica qualquer [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) elementos nas páginas que consomem a folha de estilos e define suas margens para uma espessura uniforme de 20.

> [!NOTE]
> O `element` seletor não identifica subclasses do tipo especificado.

### <a name="selecting-elements-by-base-class"></a>Selecionando elementos pela classe base

Elementos na árvore visual podem ser selecionados pela classe base com maiusculas e minúsculas `^base` seletor:

```css
^contentpage {
    background-color: lightgray;
}
```

Este seletor identifica qualquer [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) elementos que consomem a folha de estilos e define seu plano de fundo de cor para `lightgray`.

> [!NOTE]
> O `^base` seletor é específico ao xamarin. Forms e não faz parte da especificação do CSS.

### <a name="selecting-an-element-by-name"></a>Seleção de um elemento por nome

Elementos individuais da árvore visual podem ser selecionados com o diferencia maiusculas de minúsculas `#id` seletor:

```css
#listView {
    background-color: lightgray;
}
```

Este seletor identifica o elemento cujos [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) estiver definida como `listView`. No entanto, se o `StyleId` não está definida, o seletor de fará o fallback para usar o `x:Name` do elemento. Portanto, no exemplo XAML a seguir, o `#listView` seletor identificará a [ `ListView` ](xref:Xamarin.Forms.ListView) cujo `x:Name` atributo é definido como `listView`e definirá a cor do plano de fundo como `lightgray`.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView x:Name="listView" ...>
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

### <a name="selecting-elements-with-a-specific-class-attribute"></a>Selecionar elementos com um atributo de classe específica

Elementos com um atributo de classe específica podem ser selecionados com o diferencia maiusculas de minúsculas `.class` seletor:

```css
.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}
```

Uma classe CSS pode ser atribuída a um elemento XAML, definindo o [ `StyleClass` ](xref:Xamarin.Forms.NavigableElement.StyleClass) propriedade do elemento a ser o nome da classe CSS. Portanto, no exemplo XAML a seguir, os estilos definidos pelo `.detailPageTitle` classe são atribuídos à primeira [ `Label` ](xref:Xamarin.Forms.Label), enquanto os estilos definidos pelo `.detailPageSubtitle` classe são atribuídos ao segundo `Label`.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            <Label ... StyleClass="detailPageTitle" />
            <Label ... StyleClass="detailPageSubtitle"/>
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

### <a name="selecting-child-elements"></a>Selecionando elementos filho

Elementos filho na árvore visual podem ser selecionados com maiusculas e minúsculas `element element` seletor:

```css
listview image {
    height: 60;
    width: 60;
}
```

Este seletor identifica qualquer [ `Image` ](xref:Xamarin.Forms.Image) elementos que são filhos do [ `ListView` ](xref:Xamarin.Forms.ListView) elementos e define sua altura e largura para 60. Portanto, no exemplo XAML a seguir, o `listview image` seletor identificará as [ `Image` ](xref:Xamarin.Forms.Image) que é um filho do [ `ListView` ](xref:Xamarin.Forms.ListView)e define sua altura e largura para 60.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView ...>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid>
                            ...
                            <Image ... />
                            ...
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> O `element element` seletor não exige o elemento filho a ser uma _direto_ filho do pai – o elemento filho pode ter um pai diferente. Seleção ocorre desde que um ancestral seja o primeiro elemento especificado.

### <a name="selecting-direct-child-elements"></a>Selecionar os elementos filho direto

Direcionar a elementos filho na árvore visual podem ser selecionados com maiusculas e minúsculas `element>element` seletor:

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

Este seletor identifica qualquer [ `Image` ](xref:Xamarin.Forms.Image) direcionam de elementos que são filhos do [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) elementos e define sua altura e largura para 200. Portanto, no exemplo XAML a seguir, o `stacklayout>image` seletor identificará as [ `Image` ](xref:Xamarin.Forms.Image) que é um filho direto do [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)e define sua altura e largura para 200.

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            ...
            <Image ... />
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

> [!NOTE]
> O `element>element` seletor requer que o elemento filho é um _direto_ filho do pai.

## <a name="selector-reference"></a>Referência do seletor

Os seguintes seletores CSS são suportados pelo xamarin. Forms:

|Seletor|Exemplo|Descrição|
|---|---|---|
|`.class`|`.header`|Seleciona todos os elementos com o `StyleClass` propriedade contendo 'header'. Observe que este seletor é diferencia maiusculas de minúsculas.|
|`#id`|`#email`|Seleciona todos os elementos com `StyleId` definido como `email`. Se `StyleId` não for definido, o retorno ao `x:Name`. Ao usar XAML, `x:Name` é preferível `StyleId`. Observe que este seletor é diferencia maiusculas de minúsculas.|
|`*`|`*`|Seleciona todos os elementos.|
|`element`|`label`|Seleciona todos os elementos do `Label`tipo, mas não as subclasses. Observe que este seletor é diferencia maiusculas de minúsculas.|
|`^base`|`^contentpage`|Seleciona todos os elementos com `ContentPage` como a classe base, incluindo `ContentPage` em si. Observe que este seletor diferencia maiusculas de minúsculas e não faz parte da especificação do CSS.|
|`element,element`|`label,button`|Seleciona todos os `Button` elementos e todos os `Label` elementos. Observe que este seletor é diferencia maiusculas de minúsculas.|
|`element element`|`stacklayout label`|Seleciona todos os `Label` elementos dentro de um `StackLayout`. Observe que este seletor é diferencia maiusculas de minúsculas.|
|`element>element`|`stacklayout>label`|Seleciona todos os `Label` elementos com `StackLayout` como um pai direto. Observe que este seletor é diferencia maiusculas de minúsculas.|
|`element+element`|`label+entry`|Seleciona todos os `Entry` elementos diretamente após um `Label`. Observe que este seletor é diferencia maiusculas de minúsculas.|
|`element~element`|`label~entry`|Seleciona todos os `Entry` elementos precedido por um `Label`. Observe que este seletor é diferencia maiusculas de minúsculas.|

Estilos com correspondência seletores são aplicados consecutivamente, na ordem de definição. Os estilos definidos em um item específico são sempre aplicados pela última vez.

> [!TIP]
> Seletores podem ser combinadas sem limitação, tais como `StackLayout>ContentView>label.email`.

Os seguintes seletores são atualmente suportados:

- `[attribute]`
- `@media` e `@supports`
- `:` e `::`

> [!NOTE]
> Não há suporte a especificidade e substituições de especificidade.

## <a name="property-reference"></a>Referência de propriedade

As propriedades CSS a seguir têm suporte pelo xamarin. Forms (na **valores** tipos de coluna, são _itálico_, enquanto os literais de cadeia de caracteres são `gray`):

|Propriedade|Aplica-se a|Valores|Exemplo|
|---|---|---|---|
|`align-content`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial` |`align-content: space-between;`|
|`align-items`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial` |`align-items: flex-start;`|
|`align-self`|`VisualElement`| `auto` \| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial`|`align-self: flex-end;`|
|`background-color`|`VisualElement`|_Cor_ \| `initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_cadeia de caracteres_ \| `initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`, `ImageButton`|_Cor_ \| `initial`|`border-color: #9acd32;`|
|`border-radius`|`BoxView`, `Button`, `Frame`, `ImageButton`|_Double_ \| `initial` |`border-radius: 10;`|
|`border-width`|`Button`, `ImageButton`|_Double_ \| `initial` |`border-width: .5;`|
|`color`|`ActivityIndicator`, `BoxView`, `Button`, `CheckBox`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `ProgressBar`, `SearchBar`, `Switch`, `TimePicker`|_Cor_ \| `initial` |`color: rgba(255, 0, 0, 0.3);`|
|`column-gap`|`Grid`|_Double_ \| `initial`|`column-gap: 9;`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`flex-direction`|`FlexLayout`| `column` \| `columnreverse` \| `row` \| `rowreverse` \| `row-reverse` \| `column-reverse` \| `initial`|`flex-direction: column-reverse;`|
|`flex-basis`|`VisualElement`|_float_ \| `auto` \| `initial`. Além disso, uma porcentagem no intervalo de 0 a 100% pode ser especificada com o `%` sinal.|`flex-basis: 25%;`|
|`flex-grow`|`VisualElement`|_float_ \| `initial`|`flex-grow: 1.5;`|
|`flex-shrink`|`VisualElement`|_float_ \| `initial`|`flex-shrink: 1;`|
|`flex-wrap`|`VisualElement`| `nowrap` \| `wrap` \| `reverse` \| `wrap-reverse` \| `initial`|`flex-wrap: wrap-reverse;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_cadeia de caracteres_ \| `initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_duplo_ \| _namedsize_ \| `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold`\|`italic`\|`initial` |`font-style: bold;`|
|`height`|`VisualElement`|_Double_ \| `initial` |`min-height: 250;`|
|`justify-content`|`FlexLayout`| `start` \| `center` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial`|`justify-content: flex-end;`|
|`line-height`|`Label`, `Span`|_Double_ \| `initial` |`line-height: 1.8;`|
|`margin`|`View`|_Espessura_ \| `initial` |`margin: 6 12;`|
|`margin-left`|`View`|_Espessura_ \| `initial` |`margin-left: 3;`|
|`margin-top`|`View`|_Espessura_ \| `initial` |`margin-top: 2;`|
|`margin-right`|`View`|_Espessura_ \| `initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_Espessura_ \| `initial` |`margin-bottom: 6;`|
|`max-lines`|`Label`|_int_ \| `initial`|`max-lines: 2;`|
|`min-height`|`VisualElement`|_Double_ \| `initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_Double_ \| `initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_Double_ \| `initial` |`opacity: .3;`|
|`order`|`VisualElement`|_int_ \| `initial`|`order: -1;`|
|`padding`|`Button`, `ImageButton`, `Layout`, `Page`|_Espessura_ \| `initial` |`padding: 6 12 12;`|
|`padding-left`|`Button`, `ImageButton`, `Layout`, `Page`|_Double_ \| `initial`|`padding-left: 3;`|
|`padding-top`|`Button`, `ImageButton`, `Layout`, `Page`| _Double_ \| `initial` |`padding-top: 4;`|
|`padding-right`|`Button`, `ImageButton`, `Layout`, `Page`| _Double_ \| `initial` |`padding-right: 2;`|
|`padding-bottom`|`Button`, `ImageButton`, `Layout`, `Page`| _Double_ \| `initial` |`padding-bottom: 6;`|
|`position`|`FlexLayout`| `relative`\|`absolute`\|`initial`|`position: absolute;`|
|`row-gap`|`Grid`| _Double_ \| `initial`|`row-gap: 12;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`. `left` e `right` devem ser evitadas em ambientes da direita para esquerda.| `text-align: right;`|
|`text-decoration`|`Label`, `Span`|`none` \| `underline` \| `strikethrough` \| `line-through` \| `initial`|`text-decoration: underline, line-through;`|
|`transform`|`VisualElement`| `none`, `rotate`, `rotateX`, `rotateY`, `scale`, `scaleX`, `scaleY`, `translate`, `translateX`, `translateY`, `initial` |`transform: rotate(180), scaleX(2.5);`|
|`transform-origin`|`VisualElement`| _duplo_, _duplo_ \| `initial` |`transform-origin: 7.5, 12.5;`|
|`vertical-align`|`Label`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`|`vertical-align: bottom;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial`|`visibility: hidden;`|
|`width`|`VisualElement`|_Double_ \| `initial`|`min-width: 320;`|

> [!NOTE]
> `initial` é um valor válido para todas as propriedades. Limpa o valor (redefine como padrão) que foi definido de outro estilo.

As propriedades a seguir têm suportadas no momento:

- `all: initial`.
- Propriedades de layout (caixa ou grade).
- Propriedades de forma abreviada, como `font`, e `border`.

Além disso, não há nenhum `inherit` herança de valor e, portanto, não é suportada. Portanto, você não pode, por exemplo, defina a `font-size` propriedade em um layout e esperar que todos os [ `Label` ](xref:Xamarin.Forms.Label) instâncias no layout para herdar o valor. A única exceção é o `direction` propriedade, que tem um valor padrão de `inherit`.

### <a name="xamarinforms-specific-properties"></a>Propriedades específicas do Xamarin. Forms

Também há suporte para as seguintes propriedades CSS específicas do xamarin. Forms (na **valores** tipos de coluna, são _itálico_, enquanto os literais de cadeia de caracteres são `gray`):

|Propriedade|Aplica-se a|Valores|Exemplo|
|---|---|---|---|
|`-xf-placeholder`|`Entry`, `Editor`, `SearchBar`|_texto entre aspas_ \| `initial` |`-xf-placeholder: Enter name;`|
|`-xf-placeholder-color`|`Entry`, `Editor`, `SearchBar`|_Cor_ \| `initial` |`-xf-placeholder-color: green;`|
|`-xf-max-length`|`Entry`, `Editor`|_int_ \| `initial` |`-xf-max-length: 20;`|
|`-xf-bar-background-color`|`NavigationPage`, `TabbedPage`|_Cor_ \| `initial` |`-xf-bar-background-color: teal;`|
|`-xf-bar-text-color`|`NavigationPage`, `TabbedPage`|_Cor_ \| `initial` |`-xf-bar-text-color: gray`|
|`-xf-orientation`|`ScrollView`, `StackLayout`| `horizontal` \| `vertical` \| `both` \| `initial`. `both` só é compatível com um `ScrollView`. |`-xf-orientation: horizontal;`|
|`-xf-horizontal-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-horizontal-scroll-bar-visibility: never;`|
|`-xf-vertical-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-vertical-scroll-bar-visibility: always;`|
|`-xf-min-track-color`|`Slider`|_Cor_ \| `initial` |`-xf-min-track-color: yellow;`|
|`-xf-max-track-color`|`Slider`|_Cor_ \| `initial` |`-xf-max-track-color: red;`|
|`-xf-thumb-color`|`Slider`|_Cor_ \| `initial` |`-xf-thumb-color: limegreen;`|
|`-xf-spacing`|`StackLayout`|_Double_ \| `initial` |`-xf-spacing: 8;`|

### <a name="xamarinforms-shell-specific-properties"></a>Propriedades específicas do shell do Xamarin. Forms

As seguintes propriedades de CSS específicas do shell do Xamarin. Forms também são suportadas (na coluna **valores, os** tipos são em _itálico_, enquanto os literais de cadeia de caracteres são `gray`):

|Propriedade|Aplica-se a|Valores|Exemplo|
|---|---|---|---|
|`-xf-flyout-background`|`Shell`|_Cor_ \| `initial` |`-xf-flyout-background: red;`|
|`-xf-shell-background`|`Element`|_Cor_ \| `initial` |`-xf-shell-background: green;`|
|`-xf-shell-disabled`|`Element`|_Cor_ \| `initial` |`-xf-shell-disabled: blue;`|
|`-xf-shell-foreground`|`Element`|_Cor_ \| `initial` |`-xf-shell-foreground: yellow;`|
|`-xf-shell-tabbar-background`|`Element`|_Cor_ \| `initial` |`-xf-shell-tabbar-background: white;`|
|`-xf-shell-tabbar-disabled`|`Element`|_Cor_ \| `initial` |`-xf-shell-tabbar-disabled: black;`|
|`-xf-shell-tabbar-foreground`|`Element`|_Cor_ \| `initial` |`-xf-shell-tabbar-foreground: gray;`|
|`-xf-shell-tabbar-title`|`Element`|_Cor_ \| `initial` |`-xf-shell-tabbar-title: lightgray;`|
|`-xf-shell-tabbar-unselected`|`Element`|_Cor_ \| `initial` |`-xf-shell-tabbar-unselected: cyan;`|
|`-xf-shell-title`|`Element`|_Cor_ \| `initial` |`-xf-shell-title: teal;`|
|`-xf-shell-unselected`|`Element`|_Cor_ \| `initial` |`-xf-shell-unselected: limegreen;`|

### <a name="color"></a>Cor

O seguinte `color` valores têm suporte:

- `X11` [cores](https://en.wikipedia.org/wiki/X11_color_names/), que corresponde ao cores CSS, cores predefinidas do UWP e xamarin. Forms cores. Observe que esses valores de cor diferenciam maiusculas de minúsculas.
- Hex cores: `#rgb`, `#argb`, `#rrggbb`, `#aarrggbb`
- as cores RGB: `rgb(255,0,0)`, `rgb(100%,0%,0%)`. Valores estão no intervalo 0 a 255, ou 0 a 100%.
- cores RGBA: `rgba(255, 0, 0, 0.8)`, `rgba(100%, 0%, 0%, 0.8)`. O valor de opacidade está no intervalo 0,0 1,0.
- cores HSL: `hsl(120, 100%, 50%)`. O valor h está no intervalo de 0 a 360, embora a s e l sejam no intervalo de 0 a 100%.
- cores de hsla: `hsla(120, 100%, 50%, .8)`. O valor de opacidade está no intervalo 0,0 1,0.

### <a name="thickness"></a>Espessura

Um, dois, três ou quatro `thickness` há suporte para valores, cada um separado por espaço em branco:

- Um único valor indica a espessura uniforme.
- Dois valores indicam a espessura vertical e horizontal.
- Três valores indicam a parte superior, em seguida, horizontal (left e right) e espessura da parte inferior.
- Quatro valores indicam superior, seguido de à direita, em seguida, inferior e espessura à esquerda.

> [!NOTE]
> CSS `thickness` valores diferentes de XAML [ `Thickness` ](xref:Xamarin.Forms.Thickness) valores. Por exemplo, em XAML, um valor de dois `Thickness` indica a espessura horizontal e vertical, enquanto um valor de quatro `Thickness` indica à esquerda, em seguida, superior e direita, inferior, em seguida, espessura. Além disso, o XAML `Thickness` valores são delimitados por vírgula.

### <a name="namedsize"></a>NamedSize

O seguinte diferencia maiusculas de minúsculas `namedsize` valores têm suporte:

- `default`
- `micro`
- `small`
- `medium`
- `large`

O significado exato de cada `namedsize` valor é dependente de plataforma e dependente do modo de exibição.

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>CSS no xamarin. Forms com Xamarin.University

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Vídeo de CSS do Xamarin. Forms 3,0**

## <a name="related-links"></a>Links relacionados

- [MonkeyAppCSS (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)
- [Dicionários de recurso](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Aplicar estilo a aplicativos do Xamarin.Forms usando os estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
