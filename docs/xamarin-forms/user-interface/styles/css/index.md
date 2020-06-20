---
title: Estilizando Xamarin.Forms aplicativos usando folhas de estilos em cascata (CSS)
description: Xamarin.Formsdá suporte a elementos visuais de estilo usando folhas de estilos em cascata (CSS).
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9375c4078c75d8e4788cb31a3d6a6a3a10100f49
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84946215"
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>Estilizando Xamarin.Forms aplicativos usando folhas de estilos em cascata (CSS)

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)

_O Xamarin. Forms dá suporte a elementos visuais de estilo usando folhas de estilos em cascata (CSS)._

Xamarin.Formsos aplicativos podem ser estilizados usando o CSS. Uma folha de estilos consiste em uma lista de regras, com cada regra que consiste em um ou mais seletores e um bloco de declaração. Um bloco de declaração consiste em uma lista de declarações entre chaves, com cada declaração que consiste em uma propriedade, dois-pontos e um valor. Quando há várias declarações em um bloco, um ponto-e-vírgula é inserido como um separador. O exemplo de código a seguir mostra algum Xamarin.Forms CSS em conformidade:

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

No Xamarin.Forms , as folhas de estilo CSS são analisadas e avaliadas em tempo de execução, em vez de tempo de compilação, e as folhas de estilo são analisadas novamente em uso.

> [!NOTE]
> Atualmente, todo o estilo possível com o estilo XAML não pode ser executado com o CSS. No entanto, os estilos XAML podem ser usados para complementar o CSS para propriedades que não têm suporte no momento pelo Xamarin.Forms . Para obter mais informações sobre estilos XAML, consulte [estilizando Xamarin.Forms aplicativos usando estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

O exemplo [MonkeyAppCSS](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss) demonstra como usar o CSS para estilizar um aplicativo simples e é mostrado nas seguintes capturas de tela:

[![Página principal do MonkeyApp com estilo de CSS](css-images/MonkeyAppMainPage.png "Página principal do MonkeyApp com estilo de CSS")](css-images/MonkeyAppMainPage-Large.png#lightbox "Página principal do MonkeyApp com estilo de CSS")

[![Página de detalhes do MonkeyApp com estilo de CSS](css-images/MonkeyAppDetailPage.png "Página de detalhes do MonkeyApp com estilo de CSS")](css-images/MonkeyAppDetailPage-Large.png#lightbox "Página de detalhes do MonkeyApp com estilo de CSS")

## <a name="consuming-a-style-sheet"></a>Consumindo uma folha de estilos

O processo para adicionar uma folha de estilo a uma solução é o seguinte:

1. Adicione um arquivo CSS vazio ao seu projeto de biblioteca .NET Standard.
1. Defina a ação de compilação do arquivo CSS como **EmbeddedResource**.

### <a name="loading-a-style-sheet"></a>Carregando uma folha de estilos

Há várias abordagens que podem ser usadas para carregar uma folha de estilos.

> [!NOTE]
> No momento, não é possível alterar uma folha de estilos em tempo de execução e ter a nova folha de estilos aplicada.

### <a name="xaml"></a>XAML

Uma folha de estilos pode ser carregada e analisada com a [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) classe antes de ser adicionada a [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) :

```xaml
<Application ...>
    <Application.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </Application.Resources>
</Application>
```

A [`StyleSheet.Source`](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source) propriedade especifica a folha de estilos como um URI relativo ao local do arquivo XAML delimitador ou relativo à raiz do projeto se o URI começar com um `/` .

> [!WARNING]
> O arquivo CSS não será carregado se sua ação de Build não estiver definida como **EmbeddedResource**.

Como alternativa, uma folha de estilos pode ser carregada e analisada com a [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) classe, antes de ser adicionada a uma [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , inserindo-a em uma `CDATA` seção:

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

No C#, uma folha de estilos pode ser carregada de a `StringReader` e adicionada a um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) :

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

## <a name="selecting-elements-and-applying-properties"></a>Selecionando elementos e aplicando Propriedades

O CSS usa seletores para determinar quais elementos direcionar. Os estilos com seletores correspondentes são aplicados consecutivamente, em ordem de definição. Os estilos definidos em um item específico são sempre aplicados por último. Para obter mais informações sobre seletores com suporte, consulte [referência de seletor](#selector-reference).

O CSS usa propriedades para estilizar um elemento selecionado. Cada propriedade tem um conjunto de valores possíveis e algumas propriedades podem afetar qualquer tipo de elemento, enquanto outras se aplicam a grupos de elementos. Para obter mais informações sobre as propriedades com suporte, consulte [referência de propriedade](#property-reference).

As folhas de estilo filho sempre substituem as folhas de estilos pai se definirem as mesmas propriedades. Portanto, as seguintes regras de precedência são seguidas durante a aplicação de estilos que definem as mesmas propriedades:

- Um estilo definido nos recursos do aplicativo será substituído por um estilo definido nos recursos da página, se eles definirem as mesmas propriedades.
- Um estilo definido nos recursos da página será substituído por um estilo definido nos recursos de controle, se eles definirem as mesmas propriedades.
- Um estilo definido nos recursos do aplicativo será substituído por um estilo definido nos recursos de controle, se eles definirem as mesmas propriedades.

> [!IMPORTANT]
> Não há suporte para variáveis CSS.

### <a name="selecting-elements-by-type"></a>Selecionando elementos por tipo

Os elementos na árvore visual podem ser selecionados por tipo com o seletor de não diferenciação de maiúsculas e minúsculas `element` :

```css
stacklayout {
    margin: 20;
}
```

Esse seletor identifica quaisquer [`StackLayout`](xref:Xamarin.Forms.StackLayout) elementos em páginas que consomem a folha de estilos e define suas margens com uma espessura uniforme de 20.

> [!NOTE]
> O `element` seletor não identifica subclasses do tipo especificado.

### <a name="selecting-elements-by-base-class"></a>Selecionando elementos por classe base

Os elementos na árvore visual podem ser selecionados por classe base com o seletor de não diferenciação de maiúsculas e minúsculas `^base` :

```css
^contentpage {
    background-color: lightgray;
}
```

Esse seletor identifica todos os [`ContentPage`](xref:Xamarin.Forms.ContentPage) elementos que consomem a folha de estilos e define a cor do plano de fundo como `lightgray` .

> [!NOTE]
> O `^base` seletor é específico para Xamarin.Forms e não faz parte da especificação de CSS.

### <a name="selecting-an-element-by-name"></a>Selecionando um elemento por nome

Elementos individuais na árvore visual podem ser selecionados com o seletor de maiúsculas e minúsculas `#id` :

```css
#listView {
    background-color: lightgray;
}
```

Este seletor identifica o elemento cuja [`StyleId`](xref:Xamarin.Forms.Element.StyleId) propriedade está definida como `listView` . No entanto, se a `StyleId` propriedade não for definida, o seletor voltará a usar o `x:Name` do elemento. Portanto, no exemplo de XAML a seguir, o `#listView` seletor identificará o [`ListView`](xref:Xamarin.Forms.ListView) cujo `x:Name` atributo está definido como `listView` e definirá a cor do plano de fundo como `lightgray` .

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

### <a name="selecting-elements-with-a-specific-class-attribute"></a>Selecionando elementos com um atributo de classe específico

Elementos com um atributo de classe específico podem ser selecionados com o seletor de maiúsculas e minúsculas `.class` :

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

Uma classe CSS pode ser atribuída a um elemento XAML definindo a [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) Propriedade do elemento como o nome da classe CSS. Portanto, no exemplo de XAML a seguir, os estilos definidos pela `.detailPageTitle` classe são atribuídos ao primeiro [`Label`](xref:Xamarin.Forms.Label) , enquanto os estilos definidos pela `.detailPageSubtitle` classe são atribuídos ao segundo `Label` .

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

Elementos filho na árvore visual podem ser selecionados com o seletor de não diferenciação de maiúsculas e minúsculas `element element` :

```css
listview image {
    height: 60;
    width: 60;
}
```

Esse seletor identifica quaisquer [`Image`](xref:Xamarin.Forms.Image) elementos que sejam filhos de [`ListView`](xref:Xamarin.Forms.ListView) elementos e define sua altura e largura como 60. Portanto, no exemplo de XAML a seguir, o `listview image` seletor identificará o [`Image`](xref:Xamarin.Forms.Image) que é filho de [`ListView`](xref:Xamarin.Forms.ListView) e definirá sua altura e largura como 60.

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
> O `element element` seletor não exige que o elemento filho seja um filho _direto_ do pai – o elemento filho pode ter um pai diferente. A seleção ocorre desde que um ancestral seja o primeiro elemento especificado.

### <a name="selecting-direct-child-elements"></a>Selecionando elementos filho diretos

Elementos filho diretos na árvore visual podem ser selecionados com o seletor de não diferenciação de maiúsculas e minúsculas `element>element` :

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

Esse seletor identifica todos os [`Image`](xref:Xamarin.Forms.Image) elementos que são filhos diretos de [`StackLayout`](xref:Xamarin.Forms.StackLayout) elementos e define sua altura e largura como 200. Portanto, no exemplo de XAML a seguir, o `stacklayout>image` seletor identificará o [`Image`](xref:Xamarin.Forms.Image) que é um filho direto do [`StackLayout`](xref:Xamarin.Forms.StackLayout) e definirá sua altura e largura como 200.

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
> O `element>element` seletor requer que o elemento filho seja um filho _direto_ do pai.

## <a name="selector-reference"></a>Referência do seletor

Os seguintes seletores de CSS são compatíveis com Xamarin.Forms :

|Seletor|Exemplo|Descrição|
|---|---|---|
|`.class`|`.header`|Seleciona todos os elementos com a `StyleClass` propriedade que contém ' header '. Observe que esse seletor diferencia maiúsculas de minúsculas.|
|`#id`|`#email`|Seleciona todos os elementos com `StyleId` definido como `email` . Se `StyleId` não estiver definido, fallback para `x:Name` . Ao usar o XAML, `x:Name` é preferível `StyleId` . Observe que esse seletor diferencia maiúsculas de minúsculas.|
|`*`|`*`|Seleciona todos os elementos.|
|`element`|`label`|Seleciona todos os elementos do tipo `Label` , mas não as subclasses. Observe que esse seletor não diferencia maiúsculas de minúsculas.|
|`^base`|`^contentpage`|Seleciona todos os elementos com `ContentPage` como a classe base, incluindo a `ContentPage` si mesmo. Observe que esse seletor não diferencia maiúsculas de minúsculas e não faz parte da especificação de CSS.|
|`element,element`|`label,button`|Seleciona todos os `Button` elementos e todos os `Label` elementos. Observe que esse seletor não diferencia maiúsculas de minúsculas.|
|`element element`|`stacklayout label`|Seleciona todos os `Label` elementos dentro de um `StackLayout` . Observe que esse seletor não diferencia maiúsculas de minúsculas.|
|`element>element`|`stacklayout>label`|Seleciona todos os `Label` elementos com `StackLayout` como um pai direto. Observe que esse seletor não diferencia maiúsculas de minúsculas.|
|`element+element`|`label+entry`|Seleciona todos os `Entry` elementos diretamente após a `Label` . Observe que esse seletor não diferencia maiúsculas de minúsculas.|
|`element~element`|`label~entry`|Seleciona todos os `Entry` elementos precedidos por um `Label` . Observe que esse seletor não diferencia maiúsculas de minúsculas.|

Os estilos com seletores correspondentes são aplicados consecutivamente, em ordem de definição. Os estilos definidos em um item específico são sempre aplicados por último.

> [!TIP]
> Os seletores podem ser combinados sem limitação, como `StackLayout>ContentView>label.email` .

No momento, não há suporte para os seguintes seletores:

- `[attribute]`
- `@media` e `@supports`
- `:` e `::`

> [!NOTE]
> A especificidade e as substituições de especificidade não têm suporte.

## <a name="property-reference"></a>Referência de propriedade

As propriedades de CSS a seguir têm suporte no Xamarin.Forms (na coluna **valores** , os tipos são em _itálico_, enquanto os literais de cadeia de caracteres são `gray` ):

|Propriedade|Aplica-se a|Valores|Exemplo|
|---|---|---|---|
|`align-content`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial` |`align-content: space-between;`|
|`align-items`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial` |`align-items: flex-start;`|
|`align-self`|`VisualElement`| `auto` \| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial`|`align-self: flex-end;`|
|`background-color`|`VisualElement`|_color_ \| `initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_cadeia de caracteres_ \|`initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`, `ImageButton`|_color_ \| `initial`|`border-color: #9acd32;`|
|`border-radius`|`BoxView`, `Button`, `Frame`, `ImageButton`|_duplo_ \|`initial` |`border-radius: 10;`|
|`border-width`|`Button`, `ImageButton`|_duplo_ \|`initial` |`border-width: .5;`|
|`color`|`ActivityIndicator`, `BoxView`, `Button`, `CheckBox`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `ProgressBar`, `SearchBar`, `Switch`, `TimePicker`|_color_ \| `initial` |`color: rgba(255, 0, 0, 0.3);`|
|`column-gap`|`Grid`|_duplo_ \|`initial`|`column-gap: 9;`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`flex-direction`|`FlexLayout`| `column` \| `columnreverse` \| `row` \| `rowreverse` \| `row-reverse` \| `column-reverse` \| `initial`|`flex-direction: column-reverse;`|
|`flex-basis`|`VisualElement`|_float_ \| `auto` \| `initial`. Além disso, um percentual no intervalo de 0% a 100% pode ser especificado com o `%` sinal.|`flex-basis: 25%;`|
|`flex-grow`|`VisualElement`|_float_ \|`initial`|`flex-grow: 1.5;`|
|`flex-shrink`|`VisualElement`|_float_ \|`initial`|`flex-shrink: 1;`|
|`flex-wrap`|`VisualElement`| `nowrap` \| `wrap` \| `reverse` \| `wrap-reverse` \| `initial`|`flex-wrap: wrap-reverse;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_cadeia de caracteres_ \|`initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_duplo_ \| _namedsize_ \|  `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_duplo_ \|`initial` |`min-height: 250;`|
|`justify-content`|`FlexLayout`| `start` \| `center` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial`|`justify-content: flex-end;`|
|`letter-spacing`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `SearchHandler`, `Span`, `TimePicker`|_duplo_ \|`initial`|`letter-spacing: 2.5;`|
|`line-height`|`Label`, `Span`|_duplo_ \|`initial` |`line-height: 1.8;`|
|`margin`|`View`|_espessura_ \|`initial` |`margin: 6 12;`|
|`margin-left`|`View`|_espessura_ \|`initial` |`margin-left: 3;`|
|`margin-top`|`View`|_espessura_ \|`initial` |`margin-top: 2;`|
|`margin-right`|`View`|_espessura_ \|`initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_espessura_ \|`initial` |`margin-bottom: 6;`|
|`max-lines`|`Label`|_INT_ \| `initial`|`max-lines: 2;`|
|`min-height`|`VisualElement`|_duplo_ \|`initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_duplo_ \|`initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_duplo_ \|`initial` |`opacity: .3;`|
|`order`|`VisualElement`|_INT_ \| `initial`|`order: -1;`|
|`padding`|`Button`, `ImageButton`, `Layout`, `Page`|_espessura_ \|`initial` |`padding: 6 12 12;`|
|`padding-left`|`Button`, `ImageButton`, `Layout`, `Page`|_duplo_ \|`initial`|`padding-left: 3;`|
|`padding-top`|`Button`, `ImageButton`, `Layout`, `Page`| _duplo_ \|`initial` |`padding-top: 4;`|
|`padding-right`|`Button`, `ImageButton`, `Layout`, `Page`| _duplo_ \|`initial` |`padding-right: 2;`|
|`padding-bottom`|`Button`, `ImageButton`, `Layout`, `Page`| _duplo_ \|`initial` |`padding-bottom: 6;`|
|`position`|`FlexLayout`| `relative` \| `absolute` \| `initial`|`position: absolute;`|
|`row-gap`|`Grid`| _duplo_ \|`initial`|`row-gap: 12;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`. `left`e `right` devem ser evitados em ambientes da direita para a esquerda.| `text-align: right;`|
|`text-decoration`|`Label`, `Span`|`none` \| `underline` \| `strikethrough` \| `line-through` \| `initial`|`text-decoration: underline, line-through;`|
|`transform`|`VisualElement`| `none`, `rotate`, `rotateX`, `rotateY`, `scale`, `scaleX`, `scaleY`, `translate`, `translateX`, `translateY`, `initial` |`transform: rotate(180), scaleX(2.5);`|
|`transform-origin`|`VisualElement`| _duplo_, _duplo_ \|`initial` |`transform-origin: 7.5, 12.5;`|
|`vertical-align`|`Label`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`|`vertical-align: bottom;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial`|`visibility: hidden;`|
|`width`|`VisualElement`|_duplo_ \|`initial`|`min-width: 320;`|

> [!NOTE]
> `initial`é um valor válido para todas as propriedades. Ele limpa o valor (redefinido como padrão) que foi definido com base em outro estilo.

Atualmente, não há suporte para as seguintes propriedades:

- `all: initial`.
- Propriedades de layout (caixa ou grade).
- Propriedades abreviadas, como `font` e `border` .

Além disso, não há nenhum `inherit` valor e, portanto, não há suporte para herança. Portanto, você não pode, por exemplo, definir a `font-size` propriedade em um layout e esperar [`Label`](xref:Xamarin.Forms.Label) que todas as instâncias no layout herdem o valor. A única exceção é a `direction` propriedade, que tem um valor padrão de `inherit` .

`Span`Elementos de direcionamento têm um problema conhecido que impede que os spans sejam o destino de estilos CSS por elemento e nome (usando o `#` símbolo). O `Span` elemento deriva de `GestureElement` , que não tem a `StyleClass` propriedade, portanto, não há suporte para direcionamento de classe CSS. Para obter mais informações, consulte [não é possível aplicar estilos de CSS para controle de extensão](https://github.com/xamarin/Xamarin.Forms/issues/5979).

### <a name="xamarinforms-specific-properties"></a>Xamarin.FormsPropriedades específicas

As Xamarin.Forms Propriedades específicas de CSS a seguir também têm suporte (na coluna **valores** , os tipos são em _itálico_, enquanto os literais de cadeia de caracteres são `gray` ):

|Propriedade|Aplica-se a|Valores|Exemplo|
|---|---|---|---|
|`-xf-bar-background-color`|`NavigationPage`, `TabbedPage`|_color_ \| `initial` |`-xf-bar-background-color: teal;`|
|`-xf-bar-text-color`|`NavigationPage`, `TabbedPage`|_color_ \| `initial` |`-xf-bar-text-color: gray`|
|`-xf-horizontal-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-horizontal-scroll-bar-visibility: never;`|
|`-xf-max-length`|`Entry`, `Editor`, `SearchBar`|_INT_ \| `initial` |`-xf-max-length: 20;`|
|`-xf-max-track-color`|`Slider`|_color_ \| `initial` |`-xf-max-track-color: red;`|
|`-xf-min-track-color`|`Slider`|_color_ \| `initial` |`-xf-min-track-color: yellow;`|
|`-xf-orientation`|`ScrollView`, `StackLayout`| `horizontal` \| `vertical` \| `both` \| `initial`. `both`Só tem suporte em um `ScrollView` . |`-xf-orientation: horizontal;`|
|`-xf-placeholder`|`Entry`, `Editor`, `SearchBar`|_texto_ \| entre aspas`initial` |`-xf-placeholder: Enter name;`|
|`-xf-placeholder-color`|`Entry`, `Editor`, `SearchBar`|_color_ \| `initial` |`-xf-placeholder-color: green;`|
|`-xf-spacing`|`StackLayout`|_duplo_ \|`initial` |`-xf-spacing: 8;`|
|`-xf-thumb-color`|`Slider`, `Switch`|_color_ \| `initial` |`-xf-thumb-color: limegreen;`|
|`-xf-vertical-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-vertical-scroll-bar-visibility: always;`|
|`-xf-vertical-text-alignment`|`Label`| `start` \| `center` \| `end` \| `initial`|`-xf-vertical-text-alignment: end;`|
|`-xf-visual`|`VisualElement`|_cadeia de caracteres_ \|`initial` |`-xf-visual: material;`|

### <a name="xamarinforms-shell-specific-properties"></a>Xamarin.FormsPropriedades específicas do Shell

As Xamarin.Forms Propriedades CSS específicas do Shell a seguir também têm suporte (na coluna **valores** , os tipos são em _itálico_, enquanto os literais de cadeia de caracteres são `gray` ):

|Propriedade|Aplica-se a|Valores|Exemplo|
|---|---|---|---|
|`-xf-flyout-background`|`Shell`|_color_ \| `initial` |`-xf-flyout-background: red;`|
|`-xf-shell-background`|`Element`|_color_ \| `initial` |`-xf-shell-background: green;`|
|`-xf-shell-disabled`|`Element`|_color_ \| `initial` |`-xf-shell-disabled: blue;`|
|`-xf-shell-foreground`|`Element`|_color_ \| `initial` |`-xf-shell-foreground: yellow;`|
|`-xf-shell-tabbar-background`|`Element`|_color_ \| `initial` |`-xf-shell-tabbar-background: white;`|
|`-xf-shell-tabbar-disabled`|`Element`|_color_ \| `initial` |`-xf-shell-tabbar-disabled: black;`|
|`-xf-shell-tabbar-foreground`|`Element`|_color_ \| `initial` |`-xf-shell-tabbar-foreground: gray;`|
|`-xf-shell-tabbar-title`|`Element`|_color_ \| `initial` |`-xf-shell-tabbar-title: lightgray;`|
|`-xf-shell-tabbar-unselected`|`Element`|_color_ \| `initial` |`-xf-shell-tabbar-unselected: cyan;`|
|`-xf-shell-title`|`Element`|_color_ \| `initial` |`-xf-shell-title: teal;`|
|`-xf-shell-unselected`|`Element`|_color_ \| `initial` |`-xf-shell-unselected: limegreen;`|

### <a name="color"></a>Color

Há `color` suporte para os seguintes valores:

- `X11`[cores](https://en.wikipedia.org/wiki/X11_color_names), que correspondem a cores CSS, cores predefinidas UWP e Xamarin.Forms cores. Observe que esses valores de cor não diferenciam maiúsculas de minúsculas.
- cores hexa: `#rgb` , `#argb` , `#rrggbb` ,`#aarrggbb`
- cores RGB: `rgb(255,0,0)` , `rgb(100%,0%,0%)` . Os valores estão no intervalo de 0-255 ou 0%-100%.
- cores RGBA: `rgba(255, 0, 0, 0.8)` , `rgba(100%, 0%, 0%, 0.8)` . O valor de opacidade está no intervalo 0,0-1,0.
- cores HSL: `hsl(120, 100%, 50%)` . O valor h está no intervalo de 0-360, enquanto s e l estão no intervalo de 0%-100%.
- cores de HSLA: `hsla(120, 100%, 50%, .8)` . O valor de opacidade está no intervalo 0,0-1,0.

### <a name="thickness"></a>Espessura

Há suporte para um, dois, três ou quatro `thickness` valores, cada um separado por um espaço em branco:

- Um único valor indica A espessura uniforme.
- Dois valores indicam a espessura horizontal vertical.
- Três valores indicam superior, horizontal (esquerda e direita) e, em seguida, a espessura inferior.
- Quatro valores indicam superior, direita e inferior e a espessura esquerda.

> [!NOTE]
> `thickness`Os valores de CSS diferem dos [`Thickness`](xref:Xamarin.Forms.Thickness) valores XAML. Por exemplo, em XAML, um valor de dois valores `Thickness` indica a espessura horizontal e vertical, enquanto um valor de quatro valores `Thickness` indica esquerda, em cima, à direita e na espessura inferior. Além disso, `Thickness` os valores XAML são delimitados por vírgula.

### <a name="namedsize"></a>NamedSize

Os valores de não diferenciação de caso a seguir `namedsize` têm suporte:

- `default`
- `micro`
- `small`
- `medium`
- `large`

O significado exato de cada `namedsize` valor é dependente da plataforma e depende do modo de exibição.

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>CSS no Xamarin.Forms com Xamarin. University

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Xamarin.Formsvídeo de CSS 3,0**

## <a name="related-links"></a>Links relacionados

- [MonkeyAppCSS (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)
- [Dicionários de recurso](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilizando Xamarin.Forms aplicativos usando estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
