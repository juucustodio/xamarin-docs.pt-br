---
title: Aplicativos do estilo xamarin. Forms usando folhas de estilo em cascata (CSS)
description: Xamarin. Forms oferece suporte a elementos visuais de estilo usando folhas de estilo em cascata (CSS).
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 76ca67f7ac8a8e27e5f502455d48874c775fc172
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794079"
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>Aplicativos do estilo xamarin. Forms usando folhas de estilo em cascata (CSS)

_Xamarin. Forms oferece suporte a elementos visuais de estilo usando folhas de estilo em cascata (CSS)._

Xamarin. Forms 3.0 apresenta a capacidade de estilizar um aplicativo de uso de CSS. Uma folha de estilo consiste em uma lista de regras, com cada regra consiste em um bloco de declaração e um ou mais dos seletores. Um bloco de declaração consiste em uma lista de declarações entre chaves, com cada declaração consiste em uma propriedade, um vírgula e um valor. Quando há várias declarações em um bloco, um ponto e vírgula é inserido como um separador. O exemplo de código a seguir mostra o CSS xamarin. Forms compatíveis:

```css
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

Xamarin. Forms, folhas de estilo CSS são analisadas e avaliadas em tempo de execução, em vez de tempo de compilação e folhas de estilo são analisadas novamente em uso.

> [!NOTE]
> No momento, todos os estilos que são possíveis com estilo de XAML não podem ser executados com o CSS. No entanto, os estilos XAML podem ser usados para complementar CSS para propriedades que não são atualmente suportadas pelo xamarin. Forms. Para obter mais informações sobre estilos XAML, consulte [aplicativos de estilo xamarin. Forms usando XAML estilos](~/xamarin-forms/user-interface/styles/xaml/index.md).

O [MonkeyAppCSS](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/MonkeyAppCSS/) exemplo demonstra o uso de CSS para definir o estilo de um aplicativo simples e é mostrado nas capturas de tela seguir:

[![Página principal MonkeyApp de estilo CSS](css-images/MonkeyAppMainPage.png "MonkeyApp principal página com estilo de CSS")](css-images/MonkeyAppMainPage-Large.png#lightbox "MonkeyApp principal página com estilo de CSS")

[![Página de detalhes de MonkeyApp com estilo de CSS](css-images/MonkeyAppDetailPage.png "MonkeyApp página de detalhes com estilo de CSS")](css-images/MonkeyAppDetailPage-Large.png#lightbox "MonkeyApp página de detalhes com estilo de CSS")

> [!NOTE]
> Não é possível no momento definir o estilo de cor de fundo de um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) usando uma folha de estilos. Portanto, no aplicativo de amostra a [ `NavigationPage.BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) propriedade é definida no código.

## <a name="consuming-a-style-sheet"></a>Consumindo uma folha de estilos

O processo para adicionar uma folha de estilos a uma solução é o seguinte:

1. Adicione um arquivo CSS vazio ao seu projeto de biblioteca .NET padrão.
1. Definir a ação de compilação do arquivo CSS para **EmbeddedResource**.

### <a name="loading-a-style-sheet"></a>Carregar uma folha de estilos

Há várias abordagens que podem ser usados para carregar uma folha de estilos.

### <a name="xaml"></a>XAML

Uma folha de estilo pode ser carregada e analisada com o [ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet) classe antes de serem adicionados para o [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) para a página:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    ...
</ContentPage>
```

O [ `StyleSheet.Source` ](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source) propriedade especifica a folha de estilos como um URI relativo ao local do arquivo XAML delimitador ou relativo à raiz do projeto se o URI começa com um `/`.

> [!WARNING]
> O arquivo CSS não carregarão caso se trate de ação de compilação não está definida como **EmbeddedResource**.

Como alternativa, a folha de estilo pode ser carregada e analisada com o [ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet) classe por inlining-lo em um `CDATA` seção:

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

### <a name="c"></a>C#

No c#, uma folha de estilos pode ser carregada como um recurso incorporado e adicionada para o [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) para a página:

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

O primeiro argumento para o `StyleSheet.FromAssemblyResource` método é o assembly que contém a folha de estilos, enquanto o segundo argumento é uma `string` que representa o identificador de recurso. O identificador de recurso pode ser obtido o **propriedades** janela quando o arquivo CSS está selecionado.

Como alternativa, a folha de estilos pode ser carregada de um `StringReader` e adicionado para o [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) para a página:

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

## <a name="selecting-elements-and-applying-properties"></a>Selecionar elementos e aplicar propriedades

CSS usa seletores para determinar quais elementos para o destino. Estilos com correspondência seletores são aplicados consecutivamente, em ordem de definição. Os estilos definidos em um item específico sempre são aplicados. Para obter mais informações sobre os seletores com suporte, consulte [referência seletor](#selector-reference).

CSS usa propriedades para definir o estilo de um elemento selecionado. Cada propriedade tem um conjunto de valores possíveis, e algumas propriedades podem afetar qualquer tipo de elemento, enquanto outras se aplicam a grupos de elementos. Para obter mais informações sobre propriedades com suporte, consulte [referência da propriedade](#property-reference).

### <a name="selecting-elements-by-type"></a>Selecionando elementos por tipo

Elementos na árvore visual podem ser selecionados por tipo com as maiusculas e minúsculas `element` seletor:

```css
stacklayout {
    margin: 20;
}
```

Este seletor identifica qualquer [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) elementos nas páginas que consomem a folha de estilo e define suas margens para uma espessura de uniforme de 20.

> [!NOTE]
> O `element` seletor não identificar sub classes do tipo especificado.

### <a name="selecting-elements-by-base-class"></a>Selecionando elementos pela classe base

Elementos na árvore visual podem ser selecionados pela classe base com as maiusculas e minúsculas `^base` seletor:

```css
^contentpage {
    background-color: lightgray;
}
```

Este seletor identifica qualquer [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) elementos que consomem a folha de estilo e define o plano de fundo de cor para `lightgray`.

> [!NOTE]
> O `^base` seletor é específico para xamarin. Forms e não faz parte da especificação de CSS.

### <a name="selecting-an-element-by-name"></a>Seleção de um elemento por nome

Elementos individuais na árvore visual podem ser selecionados com as maiusculas e minúsculas `#id` seletor:

```css
#listView {
    background-color: lightgray;
}
```

Este seletor identifica o elemento cujo [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) está definida como `listView`. No entanto, se o `StyleId` propriedade não for definida, o seletor volte a usar o `x:Name` do elemento. Portanto, no exemplo XAML a seguir, o `#listView` seletor identificará o [ `ListView` ](xref:Xamarin.Forms.ListView) cujo `x:Name` atributo é definido como `listView`e definirá a cor do plano de fundo `lightgray`.

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

### <a name="selecting-elements-with-a-specific-class-attribute"></a>Selecionar os elementos com um atributo de classe específica

Elementos com um atributo de classe específica podem ser selecionados com as maiusculas e minúsculas `.class` seletor:

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

Uma classe CSS pode ser atribuída a um elemento XAML, definindo o [ `StyleClass` ](xref:Xamarin.Forms.VisualElement.StyleClass) propriedade do elemento a ser o nome da classe CSS. Portanto, no exemplo XAML a seguir, os estilos definidos pelo `.detailPageTitle` classe são atribuídos ao primeiro [ `Label` ](xref:Xamarin.Forms.Label), enquanto os estilos definidos pelo `.detailPageSubtitle` classe são atribuídos à segunda `Label`.

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

Elementos filho na árvore visual podem ser selecionados com as maiusculas e minúsculas `element element` seletor:

```css
listview image {
    height: 60;
    width: 60;
}
```

Este seletor identifica qualquer [ `Image` ](xref:Xamarin.Forms.Image) elementos que são filhos do [ `ListView` ](xref:Xamarin.Forms.ListView) elementos e define sua altura e largura para 60. Portanto, no exemplo XAML a seguir, o `listview image` seletor identificará o [ `Image` ](xref:Xamarin.Forms.Image) que é um filho do [ `ListView` ](xref:Xamarin.Forms.ListView)e define sua altura e largura para 60.

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
> O `element element` seletor não exigir que o elemento filho para ser um _direto_ filho do pai – o elemento filho pode ter um pai diferente. A seleção ocorre desde que um ancestral é o primeiro elemento especificado.

### <a name="selecting-direct-child-elements"></a>Selecionar os elementos filho direto

Direcionar elementos filho na árvore visual podem ser selecionados com as maiusculas e minúsculas `element>element` seletor:

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

Este seletor identifica qualquer [ `Image` ](xref:Xamarin.Forms.Image) elementos que são filhos direcionam de [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) elementos e define sua altura e largura para 200. Portanto, no exemplo XAML a seguir, o `stacklayout>image` seletor identificará o [ `Image` ](xref:Xamarin.Forms.Image) que é um filho direto do [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)e define sua altura e largura para 200.

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

Os seletores CSS a seguir são suportados pelo xamarin. Forms:

|Seletor|Exemplo|Descrição|
|---|---|---|
|`.class`|`.header`|Seleciona todos os elementos com o `StyleClass` propriedade contendo 'Cabeçalho'. Observe que este seletor diferencia maiusculas de minúsculas.|
|`#id`|`#email`|Seleciona todos os elementos com `StyleId` definido como `email`. Se `StyleId` não estiver definida, fallback para `x:Name`. Ao usar XAML, `x:Name` é preferencial em `StyleId`. Observe que este seletor diferencia maiusculas de minúsculas.|
|`*`|`*`|Seleciona todos os elementos.|
|`element`|`label`|Seleciona todos os elementos do tipo `Label`, mas não abaixo de classes. Observe que este seletor diferencia maiusculas de minúsculas.|
|`^base`|`^contentpage`|Seleciona todos os elementos com `ContentPage` como a classe base, incluindo `ContentPage` em si. Observe que este seletor diferencia maiusculas de minúsculas e não faz parte da especificação de CSS.|
|`element,element`|`label,button`|Seleciona todos os `Button` elementos e todos os `Label` elementos. Observe que este seletor diferencia maiusculas de minúsculas.|
|`element element`|`stacklayout label`|Seleciona todos os `Label` elementos dentro de um `StackLayout`. Observe que este seletor diferencia maiusculas de minúsculas.|
|`element>element`|`stacklayout>label`|Seleciona todos os `Label` elementos com `StackLayout` como um pai direto. Observe que este seletor diferencia maiusculas de minúsculas.|
|`element+element`|`label+entry`|Seleciona todos os `Entry` elementos diretamente após um `Label`. Observe que este seletor diferencia maiusculas de minúsculas.|
|`element~element`|`label~entry`|Seleciona todos os `Entry` elementos precedido por um `Label`. Observe que este seletor diferencia maiusculas de minúsculas.|

Estilos com correspondência seletores são aplicados consecutivamente, em ordem de definição. Os estilos definidos em um item específico sempre são aplicados.

> [!TIP]
> Seletores podem ser combinados, sem limitação, como `StackLayout>ContentView>label.email`.

Os seletores a seguir não têm suportados no momento:

- `[attribute]`
- `@media` e `@supports`
- `:` e `::`

> [!NOTE]
> Especificidade e substituições de especificidade não têm suporte.

## <a name="property-reference"></a>Referência de propriedade

As propriedades CSS a seguir são suportadas pelo xamarin. Forms (no **valores** são de tipos de coluna, _itálico_, enquanto os literais de cadeia de caracteres são `gray`):

|Propriedade|Aplica-se a|Valores|Exemplo|
|---|---|---|---|
|`background-color`|`VisualElement`|_Cor_ \| `initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_Cadeia de caracteres_ \| `initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`|_Cor_ \| `initial`|`border-color: #9acd32;`|
|`border-width`|`Button`|_Duplo_ \| `initial` |`border-width: .5;`|
|`color`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`|_Cor_ \| `initial` |`color: rgba(255, 0, 0, 0.3);`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_Cadeia de caracteres_ \| `initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_duplo_ \| _namedsize_ \| `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold`\|`italic`\|`initial` |`font-style: bold;`|
|`height`|`VisualElement`|_Duplo_ \| `initial` |`min-height: 250;`|
|`margin`|`View`|_Espessura_ \| `initial` |`margin: 6 12;`|
|`margin-left`|`View`|_Espessura_ \| `initial` |`margin-left: 3;`|
|`margin-top`|`View`|_Espessura_ \| `initial` |`margin-top: 2;`|
|`margin-right`|`View`|_Espessura_ \| `initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_Espessura_ \| `initial` |`margin-bottom: 6;`|
|`min-height`|`VisualElement`|_Duplo_ \| `initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_Duplo_ \| `initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_Duplo_ \| `initial` |`opacity: .3;`|
|`padding`|`Layout`, `Page`|_Espessura_ \| `initial` |`padding: 6 12 12;`|
|`padding-left`|`Layout`, `Page`|_Duplo_ \| `initial`|`padding-left: 3;`|
|`padding-top`|`Layout`, `Page`| _Duplo_ \| `initial` |`padding-top: 4;`|
|`padding-right`|`Layout`, `Page`| _Duplo_ \| `initial` |`padding-right: 2;`|
|`padding-bottom`|`Layout`, `Page`| _Duplo_ \| `initial` |`padding-bottom: 6;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `right` \| `center` \| `start` \| `end` \| `initial`. `left` e `right` devem ser evitados em ambientes da direita para esquerda.| `text-align: right;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial `|`visibility: hidden;`|
|`width`|`VisualElement`|_Duplo_ \| `initial`|`min-width: 320;`|

> [!NOTE]
> `initial` é um valor válido para todas as propriedades. Limpa o valor (redefine como a padrão) que foi definido de outro estilo.

As propriedades a seguir não têm suportadas no momento:

- `all: initial`.
- Propriedades de layout (caixa ou grade).
- Propriedades de forma abreviada, como `font`, e `border`.

Além disso, não há nenhum `inherit` herança de valor e, portanto não é suportada. Portanto você não pode, por exemplo, definir o `font-size` propriedade em um layout e espera que todos os o [ `Label` ](xref:Xamarin.Forms.Label) instâncias no layout herde o valor. A única exceção é o `direction` propriedade, que tem um valor padrão de `inherit`.

### <a name="color"></a>Cor

O seguinte `color` valores têm suporte:

- `X11` [cores](https://en.wikipedia.org/wiki/X11_color_names/), que corresponde a cores CSS, cores predefinidas UWP e xamarin. Forms cores. Observe que esses valores de cor diferenciam maiusculas de minúsculas.
- Hex cores: `#rgb`, `#argb`, `#rrggbb`, `#aarrggbb`
- as cores RGB: `rgb(255,0,0)`, `rgb(100%,0%,0%)`. Os valores estão no intervalo 0-255 ou 0 a 100%.
- cores de RGBA: `rgba(255, 0, 0, 0.8)`, `rgba(100%, 0%, 0%, 0.8)`. O valor de opacidade está no intervalo 0,0 1.0.
- cores HSL: `hsl(120, 100%, 50%)`. O valor de h é no intervalo 0-360, enquanto a s e l são na intervalo 0 a 100%.
- cores de hsla: `hsla(120, 100%, 50%, .8)`. O valor de opacidade está no intervalo 0,0 1.0.

### <a name="thickness"></a>Espessura

Uma, duas, três ou quatro `thickness` há suporte para valores, cada um separado por espaços em branco:

- Um único valor indica a espessura uniforme.
- Dois valores indicam a espessura vertical e horizontal.
- Três valores indicam a parte superior, em seguida, horizontal (esquerda e direita) e espessura inferior.
- Quatro valores indicam superior, em seguida, direita, seguido de inferior e espessura esquerda.

> [!NOTE]
> CSS `thickness` valores diferentes do XAML [ `Thickness` ](/api/type/Xamarin.Forms.Thickness/) valores. Por exemplo, em um valor de dois de XAML `Thickness` indica a espessura horizontal e vertical, enquanto um valor de quatro `Thickness` indica à esquerda, em seguida, superior e direita, inferior, em seguida, espessura. Além disso, o XAML `Thickness` valores são delimitados por vírgulas.

### <a name="namedsize"></a>NamedSize

O seguinte não diferencia maiusculas de minúsculas `namedsize` valores têm suporte:

- `default`
- `micro`
- `small`
- `medium`
- `large`

O significado exato de cada `namedsize` valor é dependente de plataforma e dependente do modo de exibição.

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>CSS no xamarin. Forms com Xamarin.University

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Xamarin. Forms 3.0 CSS, pelo [University Xamarin](https://university.xamarin.com/)**

## <a name="related-links"></a>Links relacionados

- [MonkeyAppCSS (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/MonkeyAppCSS/)
- [Aplicar estilo a aplicativos do Xamarin.Forms usando os estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)
