---
title: Aplicar estilo a aplicativos do Xamarin.Forms usando as CSS (folhas de estilos em cascata)
description: Xamarin. Forms dá suporte a elementos visuais de estilo usando folhas de estilo em cascata (CSS).
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2019
ms.openlocfilehash: 726ebd55b38460ee966113e4ee487327cd42b03d
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79303928"
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>Aplicar estilo a aplicativos xamarin. Forms usando folhas de estilo em cascata (CSS)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)

_O Xamarin. Forms dá suporte a elementos visuais de estilo usando folhas de estilos em cascata (CSS)._

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
> Atualmente, todos os estilos que são possíveis com estilo de XAML não podem ser executados com CSS. No entanto, os estilos XAML podem ser usados para complementar o CSS para propriedades que não são atualmente suportadas pelo xamarin. Forms. Confira mais informações sobre os estilos XAML em [Aplicar estilo a aplicativos do Xamarin.Forms usando os estilos do XAML](~/xamarin-forms/user-interface/styles/xaml/index.md).

O exemplo [MonkeyAppCSS](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss) demonstra como usar o CSS para estilizar um aplicativo simples e é mostrado nas seguintes capturas de tela:

[![Página principal do MonkeyApp com estilo de CSS](css-images/MonkeyAppMainPage.png "Página principal do MonkeyApp com estilo de CSS")](css-images/MonkeyAppMainPage-Large.png#lightbox "Página principal do MonkeyApp com estilo de CSS")

[![Página de detalhes do MonkeyApp com estilo de CSS](css-images/MonkeyAppDetailPage.png "Página de detalhes do MonkeyApp com estilo de CSS")](css-images/MonkeyAppDetailPage-Large.png#lightbox "Página de detalhes do MonkeyApp com estilo de CSS")

## <a name="consuming-a-style-sheet"></a>Consumindo uma folha de estilos

O processo para adicionar uma folha de estilos a uma solução é da seguinte maneira:

1. Adicione um arquivo CSS vazio ao seu projeto de biblioteca .NET Standard.
1. Defina a ação de compilação do arquivo CSS como **EmbeddedResource**.

### <a name="loading-a-style-sheet"></a>Carregando uma folha de estilos

Há várias abordagens que podem ser usados para carregar uma folha de estilos.

### <a name="xaml"></a>XAML

Uma folha de estilos pode ser carregada e analisada com a classe [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) antes de ser adicionada a uma [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary):

```xaml
<Application ...>
    <Application.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </Application.Resources>
</Application>
```

A propriedade [`StyleSheet.Source`](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source) especifica a folha de estilos como um URI relativo ao local do arquivo XAML delimitador ou relativo à raiz do projeto se o URI começar com um `/`.

> [!WARNING]
> O arquivo CSS não será carregado se sua ação de Build não estiver definida como **EmbeddedResource**.

Como alternativa, uma folha de estilos pode ser carregada e analisada com a classe [`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet) , antes de ser adicionada a uma [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), ao inseri-la em uma `CDATA` seção:

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

No C#, uma folha de estilos pode ser carregada de um `StringReader` e adicionada a uma [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary):

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

O argumento para o método `StyleSheet.FromReader` é o `TextReader` que leu a folha de estilos.

## <a name="selecting-elements-and-applying-properties"></a>Selecionando elementos e aplicação de propriedades

O CSS usa seletores para determinar quais elementos de destino. Estilos com correspondência seletores são aplicados consecutivamente, na ordem de definição. Os estilos definidos em um item específico são sempre aplicados pela última vez. Para obter mais informações sobre seletores com suporte, consulte [referência de seletor](#selector-reference).

O CSS usa propriedades para definir o estilo de um elemento selecionado. Cada propriedade tem um conjunto de valores possíveis, e algumas propriedades podem afetar qualquer tipo de elemento, enquanto outras se aplicam a grupos de elementos. Para obter mais informações sobre as propriedades com suporte, consulte [referência de propriedade](#property-reference).

### <a name="selecting-elements-by-type"></a>Seleção de elementos por tipo

Os elementos na árvore visual podem ser selecionados por tipo com o seletor de `element` de não diferenciação de maiúsculas e minúsculas:

```css
stacklayout {
    margin: 20;
}
```

Este seletor identifica quaisquer elementos [`StackLayout`](xref:Xamarin.Forms.StackLayout) em páginas que consomem a folha de estilos e define suas margens com uma espessura uniforme de 20.

> [!NOTE]
> O seletor de `element` não identifica subclasses do tipo especificado.

### <a name="selecting-elements-by-base-class"></a>Selecionando elementos pela classe base

Os elementos na árvore visual podem ser selecionados por classe base com o seletor de `^base` não diferenciação de maiúsculas e minúsculas:

```css
^contentpage {
    background-color: lightgray;
}
```

Esse seletor identifica todos os elementos [`ContentPage`](xref:Xamarin.Forms.ContentPage) que consomem a folha de estilos e define sua cor de plano de fundo como `lightgray`.

> [!NOTE]
> O seletor de `^base` é específico para Xamarin. Forms e não faz parte da especificação de CSS.

### <a name="selecting-an-element-by-name"></a>Seleção de um elemento por nome

Elementos individuais na árvore visual podem ser selecionados com o seletor de `#id` diferenciado de maiúsculas e minúsculas:

```css
#listView {
    background-color: lightgray;
}
```

Este seletor identifica o elemento cujo [`StyleId`](xref:Xamarin.Forms.Element.StyleId) Propriedade está definida como `listView`. No entanto, se a propriedade `StyleId` não for definida, o seletor voltará a usar o `x:Name` do elemento. Portanto, no exemplo de XAML a seguir, o seletor de `#listView` identificará o [`ListView`](xref:Xamarin.Forms.ListView) cujo atributo `x:Name` está definido como `listView`e definirá a cor do plano de fundo como `lightgray`.

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

Elementos com um atributo de classe específico podem ser selecionados com o seletor de `.class` diferenciado de maiúsculas e minúsculas:

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

Uma classe CSS pode ser atribuída a um elemento XAML definindo a propriedade [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) do elemento como o nome da classe CSS. Portanto, no exemplo de XAML a seguir, os estilos definidos pela classe `.detailPageTitle` são atribuídos à primeira [`Label`](xref:Xamarin.Forms.Label), enquanto os estilos definidos pela classe `.detailPageSubtitle` são atribuídos à segunda `Label`.

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

Elementos filho na árvore visual podem ser selecionados com o seletor de `element element` não diferenciação de maiúsculas e minúsculas:

```css
listview image {
    height: 60;
    width: 60;
}
```

Esse seletor identifica quaisquer elementos [`Image`](xref:Xamarin.Forms.Image) que sejam filhos de elementos [`ListView`](xref:Xamarin.Forms.ListView) e define sua altura e largura como 60. Portanto, no exemplo de XAML a seguir, o seletor de `listview image` identificará o [`Image`](xref:Xamarin.Forms.Image) filho do [`ListView`](xref:Xamarin.Forms.ListView)e definirá sua altura e largura como 60.

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
> O seletor de `element element` não exige que o elemento filho seja um filho _direto_ do pai – o elemento filho pode ter um pai diferente. Seleção ocorre desde que um ancestral seja o primeiro elemento especificado.

### <a name="selecting-direct-child-elements"></a>Selecionar os elementos filho direto

Elementos filho diretos na árvore visual podem ser selecionados com o seletor de `element>element` não diferenciação de maiúsculas e minúsculas:

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

Esse seletor identifica todos os elementos [`Image`](xref:Xamarin.Forms.Image) que são filhos diretos de elementos [`StackLayout`](xref:Xamarin.Forms.StackLayout) e define sua altura e largura como 200. Portanto, no exemplo de XAML a seguir, o seletor de `stacklayout>image` identificará o [`Image`](xref:Xamarin.Forms.Image) que é um filho direto do [`StackLayout`](xref:Xamarin.Forms.StackLayout)e definirá sua altura e largura como 200.

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
> O seletor de `element>element` requer que o elemento filho seja um filho _direto_ do pai.

## <a name="selector-reference"></a>Referência do seletor

Os seguintes seletores CSS são suportados pelo xamarin. Forms:

|Seletor|{1&gt;Exemplo&lt;1}|Descrição|
|---|---|---|
|`.class`|`.header`|Seleciona todos os elementos com a propriedade `StyleClass` contendo ' header '. Observe que este seletor é diferencia maiusculas de minúsculas.|
|`#id`|`#email`|Seleciona todos os elementos com `StyleId` definido como `email`. Se `StyleId` não estiver definido, fallback para `x:Name`. Ao usar o XAML, `x:Name` é preferível sobre `StyleId`. Observe que este seletor é diferencia maiusculas de minúsculas.|
|`*`|`*`|Seleciona todos os elementos.|
|`element`|`label`|Seleciona todos os elementos do tipo `Label`, mas não as subclasses. Observe que este seletor é diferencia maiusculas de minúsculas.|
|`^base`|`^contentpage`|Seleciona todos os elementos com `ContentPage` como a classe base, incluindo `ContentPage` si mesmo. Observe que este seletor diferencia maiusculas de minúsculas e não faz parte da especificação do CSS.|
|`element,element`|`label,button`|Seleciona todos os elementos de `Button` e todos os elementos de `Label`. Observe que este seletor é diferencia maiusculas de minúsculas.|
|`element element`|`stacklayout label`|Seleciona todos os elementos de `Label` dentro de um `StackLayout`. Observe que este seletor é diferencia maiusculas de minúsculas.|
|`element>element`|`stacklayout>label`|Seleciona todos os elementos de `Label` com `StackLayout` como um pai direto. Observe que este seletor é diferencia maiusculas de minúsculas.|
|`element+element`|`label+entry`|Seleciona todos os elementos de `Entry` diretamente após uma `Label`. Observe que este seletor é diferencia maiusculas de minúsculas.|
|`element~element`|`label~entry`|Seleciona todos os elementos de `Entry` precedidos por um `Label`. Observe que este seletor é diferencia maiusculas de minúsculas.|

Estilos com correspondência seletores são aplicados consecutivamente, na ordem de definição. Os estilos definidos em um item específico são sempre aplicados pela última vez.

> [!TIP]
> Os seletores podem ser combinados sem limitação, como `StackLayout>ContentView>label.email`.

Os seguintes seletores são atualmente suportados:

- `[attribute]`
- `@media` e `@supports`
- `:` e `::`

> [!NOTE]
> Não há suporte a especificidade e substituições de especificidade.

## <a name="property-reference"></a>Referência de propriedade

As propriedades CSS a seguir têm suporte do Xamarin. Forms (na coluna **valores, os** tipos são em _itálico_, enquanto os literais de cadeia de caracteres são `gray`):

|Propriedade|Aplica-se a|Valores|{1&gt;Exemplo&lt;1}|
|---|---|---|---|
|`align-content`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial` |`align-content: space-between;`|
|`align-items`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial` |`align-items: flex-start;`|
|`align-self`|`VisualElement`| `auto` \| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial`|`align-self: flex-end;`|
|`background-color`|`VisualElement`|\| de _cores_ `initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_cadeia de caracteres_ \| `initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`, `ImageButton`|\| de _cores_ `initial`|`border-color: #9acd32;`|
|`border-radius`|`BoxView`, `Button`, `Frame`, `ImageButton`|\| _duplo_ `initial` |`border-radius: 10;`|
|`border-width`|`Button`, `ImageButton`|\| _duplo_ `initial` |`border-width: .5;`|
|`color`|`ActivityIndicator`, `BoxView`, `Button`, `CheckBox`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `ProgressBar`, `SearchBar`, `Switch`, `TimePicker`|\| de _cores_ `initial` |`color: rgba(255, 0, 0, 0.3);`|
|`column-gap`|`Grid`|\| _duplo_ `initial`|`column-gap: 9;`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`flex-direction`|`FlexLayout`| `column` \| `columnreverse` \| `row` \| `rowreverse` \| `row-reverse` \| `column-reverse` \| `initial`|`flex-direction: column-reverse;`|
|`flex-basis`|`VisualElement`|\| _float_ `auto` \| `initial`. Além disso, um percentual no intervalo de 0% a 100% pode ser especificado com o sinal de `%`.|`flex-basis: 25%;`|
|`flex-grow`|`VisualElement`|\| _flutuante_ `initial`|`flex-grow: 1.5;`|
|`flex-shrink`|`VisualElement`|\| _flutuante_ `initial`|`flex-shrink: 1;`|
|`flex-wrap`|`VisualElement`| `nowrap` \| `wrap` \| `reverse` \| `wrap-reverse` \| `initial`|`flex-wrap: wrap-reverse;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_cadeia de caracteres_ \| `initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_double_\| _namedsize_ \| `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|\| _duplo_ `initial` |`min-height: 250;`|
|`justify-content`|`FlexLayout`| `start` \| `center` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial`|`justify-content: flex-end;`|
|`letter-spacing`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `SearchHandler`, `Span`, `TimePicker`|\| _duplo_ `initial`|`letter-spacing: 2.5;`|
|`line-height`|`Label`, `Span`|\| _duplo_ `initial` |`line-height: 1.8;`|
|`margin`|`View`|\| de _espessura_ `initial` |`margin: 6 12;`|
|`margin-left`|`View`|\| de _espessura_ `initial` |`margin-left: 3;`|
|`margin-top`|`View`|\| de _espessura_ `initial` |`margin-top: 2;`|
|`margin-right`|`View`|\| de _espessura_ `initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|\| de _espessura_ `initial` |`margin-bottom: 6;`|
|`max-lines`|`Label`|\| _int_ `initial`|`max-lines: 2;`|
|`min-height`|`VisualElement`|\| _duplo_ `initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|\| _duplo_ `initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|\| _duplo_ `initial` |`opacity: .3;`|
|`order`|`VisualElement`|\| _int_ `initial`|`order: -1;`|
|`padding`|`Button`, `ImageButton`, `Layout`, `Page`|\| de _espessura_ `initial` |`padding: 6 12 12;`|
|`padding-left`|`Button`, `ImageButton`, `Layout`, `Page`|\| _duplo_ `initial`|`padding-left: 3;`|
|`padding-top`|`Button`, `ImageButton`, `Layout`, `Page`| \| _duplo_ `initial` |`padding-top: 4;`|
|`padding-right`|`Button`, `ImageButton`, `Layout`, `Page`| \| _duplo_ `initial` |`padding-right: 2;`|
|`padding-bottom`|`Button`, `ImageButton`, `Layout`, `Page`| \| _duplo_ `initial` |`padding-bottom: 6;`|
|`position`|`FlexLayout`| `relative` \| `absolute` \| `initial`|`position: absolute;`|
|`row-gap`|`Grid`| \| _duplo_ `initial`|`row-gap: 12;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`. `left` e `right` devem ser evitados em ambientes da direita para a esquerda.| `text-align: right;`|
|`text-decoration`|`Label`, `Span`|`none` \| `underline` \| `strikethrough` \| `line-through` \| `initial`|`text-decoration: underline, line-through;`|
|`transform`|`VisualElement`| `none`, `rotate`, `rotateX`, `rotateY`, `scale`, `scaleX`, `scaleY`, `translate`, `translateX`, `translateY`, `initial` |`transform: rotate(180), scaleX(2.5);`|
|`transform-origin`|`VisualElement`| _Double_, \| _duplo_ `initial` |`transform-origin: 7.5, 12.5;`|
|`vertical-align`|`Label`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`|`vertical-align: bottom;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial`|`visibility: hidden;`|
|`width`|`VisualElement`|\| _duplo_ `initial`|`min-width: 320;`|

> [!NOTE]
> `initial` é um valor válido para todas as propriedades. Limpa o valor (redefine como padrão) que foi definido de outro estilo.

As propriedades a seguir têm suportadas no momento:

- `all: initial`
- Propriedades de layout (caixa ou grade).
- Propriedades abreviadas, como `font`e `border`.

Além disso, não há nenhum valor `inherit` e, portanto, não há suporte para herança. Portanto, você não pode, por exemplo, definir a propriedade `font-size` em um layout e esperar que todas as instâncias de [`Label`](xref:Xamarin.Forms.Label) no layout herdem o valor. A única exceção é a propriedade `direction`, que tem um valor padrão de `inherit`.

O direcionamento de elementos de `Span` tem um problema conhecido que impede que os spans sejam o destino de estilos CSS por elemento e nome (usando o símbolo de `#`). O elemento `Span` deriva de `GestureElement`, que não tem a propriedade `StyleClass`, portanto, não há suporte para direcionamento de classe CSS. Para obter mais informações, consulte [não é possível aplicar estilos de CSS para controle de extensão](https://github.com/xamarin/Xamarin.Forms/issues/5979).

### <a name="xamarinforms-specific-properties"></a>Propriedades específicas do Xamarin. Forms

As seguintes propriedades CSS específicas do Xamarin. Forms também têm suporte (na coluna **valores** , os tipos são em _itálico_, enquanto os literais de cadeia de caracteres são `gray`):

|Propriedade|Aplica-se a|Valores|{1&gt;Exemplo&lt;1}|
|---|---|---|---|
|`-xf-bar-background-color`|`NavigationPage`, `TabbedPage`|\| de _cores_ `initial` |`-xf-bar-background-color: teal;`|
|`-xf-bar-text-color`|`NavigationPage`, `TabbedPage`|\| de _cores_ `initial` |`-xf-bar-text-color: gray`|
|`-xf-horizontal-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-horizontal-scroll-bar-visibility: never;`|
|`-xf-max-length`|`Entry`, `Editor`|\| _int_ `initial` |`-xf-max-length: 20;`|
|`-xf-max-track-color`|`Slider`|\| de _cores_ `initial` |`-xf-max-track-color: red;`|
|`-xf-min-track-color`|`Slider`|\| de _cores_ `initial` |`-xf-min-track-color: yellow;`|
|`-xf-orientation`|`ScrollView`, `StackLayout`| `both` \| `vertical` \| \| `initial``horizontal`. Só há suporte para `both` em um `ScrollView`. |`-xf-orientation: horizontal;`|
|`-xf-placeholder`|`Entry`, `Editor`, `SearchBar`|\| de _texto entre aspas_ `initial` |`-xf-placeholder: Enter name;`|
|`-xf-placeholder-color`|`Entry`, `Editor`, `SearchBar`|\| de _cores_ `initial` |`-xf-placeholder-color: green;`|
|`-xf-spacing`|`StackLayout`|\| _duplo_ `initial` |`-xf-spacing: 8;`|
|`-xf-thumb-color`|`Slider`, `Switch`|\| de _cores_ `initial` |`-xf-thumb-color: limegreen;`|
|`-xf-vertical-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-vertical-scroll-bar-visibility: always;`|
|`-xf-vertical-text-alignment`|`Label`| `start` \| `center` \| `end` \| `initial`|`-xf-vertical-text-alignment: end;`|
|`-xf-visual`|`VisualElement`|_cadeia de caracteres_ \| `initial` |`-xf-visual: material;`|

### <a name="xamarinforms-shell-specific-properties"></a>Propriedades específicas do shell do Xamarin. Forms

As seguintes propriedades de CSS específicas do shell do Xamarin. Forms também têm suporte (na coluna **valores, os** tipos são em _itálico_, enquanto os literais de cadeia de caracteres são `gray`):

|Propriedade|Aplica-se a|Valores|{1&gt;Exemplo&lt;1}|
|---|---|---|---|
|`-xf-flyout-background`|`Shell`|\| de _cores_ `initial` |`-xf-flyout-background: red;`|
|`-xf-shell-background`|`Element`|\| de _cores_ `initial` |`-xf-shell-background: green;`|
|`-xf-shell-disabled`|`Element`|\| de _cores_ `initial` |`-xf-shell-disabled: blue;`|
|`-xf-shell-foreground`|`Element`|\| de _cores_ `initial` |`-xf-shell-foreground: yellow;`|
|`-xf-shell-tabbar-background`|`Element`|\| de _cores_ `initial` |`-xf-shell-tabbar-background: white;`|
|`-xf-shell-tabbar-disabled`|`Element`|\| de _cores_ `initial` |`-xf-shell-tabbar-disabled: black;`|
|`-xf-shell-tabbar-foreground`|`Element`|\| de _cores_ `initial` |`-xf-shell-tabbar-foreground: gray;`|
|`-xf-shell-tabbar-title`|`Element`|\| de _cores_ `initial` |`-xf-shell-tabbar-title: lightgray;`|
|`-xf-shell-tabbar-unselected`|`Element`|\| de _cores_ `initial` |`-xf-shell-tabbar-unselected: cyan;`|
|`-xf-shell-title`|`Element`|\| de _cores_ `initial` |`-xf-shell-title: teal;`|
|`-xf-shell-unselected`|`Element`|\| de _cores_ `initial` |`-xf-shell-unselected: limegreen;`|

### <a name="color"></a>Cor

Há suporte para os seguintes valores de `color`:

- `X11` [cores](https://en.wikipedia.org/wiki/X11_color_names), que correspondem a cores CSS, cores predefinidas UWP e cores Xamarin. Forms. Observe que esses valores de cor diferenciam maiusculas de minúsculas.
- cores hexa: `#rgb`, `#argb`, `#rrggbb``#aarrggbb`
- cores RGB: `rgb(255,0,0)`, `rgb(100%,0%,0%)`. Valores estão no intervalo 0 a 255, ou 0 a 100%.
- cores RGBA: `rgba(255, 0, 0, 0.8)`, `rgba(100%, 0%, 0%, 0.8)`. O valor de opacidade está no intervalo 0,0 1,0.
- cores HSL: `hsl(120, 100%, 50%)`. O valor h está no intervalo de 0 a 360, embora a s e l sejam no intervalo de 0 a 100%.
- cores HSLA: `hsla(120, 100%, 50%, .8)`. O valor de opacidade está no intervalo 0,0 1,0.

### <a name="thickness"></a>Espessura

Um, dois, três ou quatro `thickness` valores têm suporte, cada um separado por um espaço em branco:

- Um único valor indica a espessura uniforme.
- Dois valores indicam a espessura vertical e horizontal.
- Três valores indicam a parte superior, em seguida, horizontal (left e right) e espessura da parte inferior.
- Quatro valores indicam superior, seguido de à direita, em seguida, inferior e espessura à esquerda.

> [!NOTE]
> Os valores de `thickness` de CSS diferem dos valores de [`Thickness`](xref:Xamarin.Forms.Thickness) XAML. Por exemplo, em XAML, um `Thickness` de dois valores indica a espessura horizontal e vertical, enquanto uma `Thickness` de quatro valores indica a esquerda, a parte superior, a direita e a espessura inferior. Além disso, os valores de `Thickness` XAML são delimitados por vírgula.

### <a name="namedsize"></a>NamedSize

Os seguintes valores de `namedsize` não diferenciam de maiúsculas e minúsculas são suportados:

- `default`
- `micro`
- `small`
- `medium`
- `large`

O significado exato de cada valor de `namedsize` é dependente da plataforma e depende do modo de exibição.

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>CSS no xamarin. Forms com Xamarin.University

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Vídeo de CSS do Xamarin. Forms 3,0**

## <a name="related-links"></a>Links Relacionados

- [MonkeyAppCSS (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)
- [Dicionários de recurso](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Aplicar estilo a aplicativos do Xamarin.Forms usando os estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
