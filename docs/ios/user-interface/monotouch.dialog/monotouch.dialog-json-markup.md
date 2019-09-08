---
title: Markup Json MonoTouch.Dialog
description: Este documento descreve a sintaxe JSON que pode ser usada para criar uma interface do usuário do Xamarin. iOS usando MonoTouch. Dialog.
ms.prod: xamarin
ms.assetid: 59F3E18C-3A73-69B8-DA5E-21B19B9DFB98
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: conceptdev
ms.author: crdun
ms.openlocfilehash: d9174e9b2d6c056c94b405033a25eeb787c92f9f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768762"
---
# <a name="monotouchdialog-json-markup"></a>Markup Json MonoTouch.Dialog

Esta página descreve a marcação JSON aceita por o [jsonelement](xref:MonoTouch.Dialog.JsonElement) da caixa de diálogo.

Vamos começar com um exemplo. Este é um arquivo JSON completo que pode ser passado para o Jsonelement.

```json
{     
    "title": "Json Sample",
    "sections": [ 
        {
            "header": "Booleans",
            "footer": "Slider or image-based",
            "id": "first-section",
            "elements": [
                { 
                    "type": "boolean",
                    "caption": "Demo of a Boolean",
                    "value": true
                }, {
                    "type": "boolean",
                    "caption": "Boolean using images",
                    "value": false,
                    "on": "favorite.png",
                    "off": "~/favorited.png"
                }, {
                    "type": "root",
                    "title": "Tap for nested controller",
                    "sections": [
                        {
                            "header": "Nested view!",
                            "elements": [
                                {
                                    "type": "boolean",
                                    "caption": "Just a boolean",
                                    "id": "the-boolean",
                                    "value": false
                                }, {
                                    "type": "string",
                                    "caption": "Welcome to the nested controller"
                                }
                            ]
                        }
                    ]
                }
            ]
        }, {
            "header": "Entries",
            "elements" : [
                {
                    "type": "entry",
                    "caption": "Username",
                    "value": "",
                    "placeholder": "Your account username"
                }
            ]
        }
    ]
}
```

A marcação acima produz a seguinte interface do usuário:

 [![](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png "A interface do usuário criada pela marcação fornecida")](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png#lightbox)

Cada elemento na árvore pode conter a propriedade `"id"`. É possível que o tempo de execução referencie seções ou elementos individuais usando o indexador Jsonelement. Assim:

```csharp
var jsonElement = JsonElement.FromFile ("demo.json");

var firstSection = jsonElement ["first-section"] as Section;

var theBoolean = jsonElement ["the-boolean"] as BooleanElement;
```

 <a name="Root_Element_Syntax" />

## <a name="root-element-syntax"></a>Sintaxe do elemento raiz

O elemento raiz contém os seguintes valores:

- `title`
- `sections` (opcional)

O elemento raiz pode aparecer dentro de uma seção como um elemento para criar um controlador aninhado. Nesse caso, a propriedade `"type"` extra deve ser definida como`"root"`

 <a name="url" />

### <a name="url"></a>url

Se a `"url"` propriedade for definida, se o usuário tocar nesse RootElement, o código solicitará um arquivo da URL especificada e fará com que o conteúdo das novas informações sejam exibidas. Você pode usar isso para criar estender a interface do usuário do servidor com base no que o usuário toca.

 <a name="group" />

### <a name="group"></a>grupo

Se definido, define o GroupName para o elemento raiz. Os nomes de grupo são usados para escolher um resumo que é exibido como o valor do elemento raiz de um dos elementos aninhados no elemento. Esse é o valor de uma caixa de seleção ou o valor de um botão de opção.

 <a name="radioselected" />

### <a name="radioselected"></a>radioselected

Identifica o item de opção selecionado em elementos aninhados

 <a name="title" />

### <a name="title"></a>título

Se presente, será o título usado para o RootElement

 <a name="type" />

### <a name="type"></a>tipo

Deve ser definido como `"root"` quando isso aparece em uma seção (usada para aninhar controladores).

 <a name="sections" />

### <a name="sections"></a>sections

Esta é uma matriz JSON com seções individuais

 <a name="Section_Syntax" />

## <a name="section-syntax"></a>Sintaxe da seção

A seção contém:

- `header` (opcional)
- `footer` (opcional)
- Matriz `elements`

 <a name="header" />

### <a name="header"></a>cabeçalho

Se estiver presente, o texto do cabeçalho será exibido como uma legenda da seção.

 <a name="footer" />

### <a name="footer"></a>cabeçalho

Se estiver presente, o rodapé será exibido na parte inferior da seção.

 <a name="elements" />

### <a name="elements"></a>elementos

Esta é uma matriz de elementos. Cada elemento deve conter pelo menos uma chave, a `"type"` chave usada para identificar o tipo de elemento a ser criado.
Alguns dos elementos compartilham algumas propriedades comuns como `"caption"` e. `"value"` Estes são a lista de elementos com suporte:

- `string`elementos (com e sem estilo)
- `entry`linhas (regular ou senha)
- `boolean`valores (usando opções ou imagens)

Elementos de cadeia de caracteres podem ser usados como botões fornecendo um método para invocar quando o usuário toca na célula ou no acessório,

 <a name="Rendering_Elements" />

## <a name="rendering-elements"></a>Renderizando elementos

Os elementos de renderização são baseados em C# stringelement e StyledStringElement e podem renderizar informações de várias maneiras e é possível renderizá-los de várias maneiras. Os elementos mais simples podem ser criados da seguinte maneira:

```json
{
    "type": "string",
    "caption": "Json Serializer"
}
```

Isso mostrará uma cadeia de caracteres simples com todos os padrões: fonte, plano de fundo, cor do texto e decorações. É possível vincular ações a esses elementos e fazê-los se comportarem como botões definindo a `"ontap"` propriedade ou as `"onaccessorytap"` Propriedades:

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos"
}
```

O acima invocará o método "Configurations" na classe "Acme. Photo Library". O `"onaccessorytap"` é semelhante, mas só será invocado se o usuário tocar no acessório em vez de tocar na célula. Para habilitar isso, você também deve definir o acessório:

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos",
    "accessory": "detail-disclosure",
    "onaccessorytap": "Acme.PhotoLibrary.ShowStats"
}
```

A renderização de elementos pode exibir duas cadeias de caracteres ao mesmo tempo, uma é a legenda e outra é o valor. A forma como essas cadeias de caracteres são renderizadas depende do estilo, você `"style"` pode definir isso usando a propriedade. O padrão mostrará a legenda à esquerda e o valor à direita. Consulte a seção sobre estilo para obter mais detalhes. As cores são codificadas usando o símbolo ' # ' seguido por números hexadecimais que representam os valores dos valores vermelho, verde, azul e talvez alfa. O conteúdo pode ser codificado em forma abreviada (3 ou 4 dígitos hexadecimais), que representa valores RGB ou RGBA. Ou a forma longa (6 ou 8 dígitos) que representa os valores RGB ou RGBA. A versão curta é uma abreviação para gravar o mesmo dígito hexadecimal duas vezes. Portanto, a constante "#1bc" é interpretado como vermelho = 0x11, verde = 0xBB e azul = 0xCC. Se o valor alfa não estiver presente, a cor será opaca. Alguns exemplos:

```json
"background": "#f00"
"background": "#fa08f880"
```

 <a name="accessory" />

### <a name="accessory"></a>embuti

Determina o tipo de acessório a ser mostrado no elemento de renderização, os valores possíveis são:

- `checkmark`
- `detail-disclosure`
- `disclosure-indicator`

Se o valor não estiver presente, nenhum acessório será mostrado

 <a name="background" />

### <a name="background"></a>tela de fundo

A propriedade Background define a cor do plano de fundo da célula. O valor é uma URL para uma imagem (nesse caso, o download de imagem assíncrona será invocado e o plano de fundo será atualizado quando a imagem for baixada) ou pode ser uma cor especificada usando a sintaxe de cor.

 <a name="caption" />

### <a name="caption"></a>Legenda

A cadeia de caracteres principal a ser mostrada no elemento de renderização. A fonte e a cor podem ser personalizadas definindo as `"textcolor"` propriedades `"font"` e. O estilo de renderização é determinado pela `"style"` propriedade.

 <a name="color_and_detailcolor" />

### <a name="color-and-detailcolor"></a>cor e detailcolor

A cor a ser usada para o texto principal ou o texto detalhado.

 <a name="detailfont_and_font" />

### <a name="detailfont-and-font"></a>detailfont e fonte

A fonte a ser usada para a legenda ou o texto detalhado. O formato de uma especificação de fonte é o nome da fonte seguido opcionalmente por um traço e o tamanho do ponto.
Veja a seguir as especificações de fonte válidas:

- Helvetica
- "Helvetica-14"

 <a name="linebreak" />

### <a name="linebreak"></a>LineBreak

Determina como as linhas são divididas. Os valores possíveis são:

- `character-wrap`
- `clip`
- `head-truncation`
- `middle-truncation`
- `tail-truncation`
- `word-wrap`

Ambos `character-wrap` `"lines"` e `word-wrap` podem ser usados junto com a propriedade definida como zero para transformar o elemento de renderização em um elemento de várias linhas.

 <a name="ontap_and_onaccessorytap" />

### <a name="ontap-and-onaccessorytap"></a>ONTAP e onaccessorytap

Essas propriedades devem apontar para um nome de método estático em seu aplicativo que usa um objeto como um parâmetro. Ao criar sua hierarquia usando os métodos JsonDialog. FromFile ou JsonDialog. FromJson, você pode passar um valor de objeto opcional. Esse valor de objeto é passado para seus métodos. Você pode usar isso para passar algum contexto para seu método estático. Por exemplo:

```csharp
class Foo {
    Foo ()
    {
        root = JsonDialog.FromJson (myJson, this);
    }

    static void Callback (object obj)
    {
        Foo myFoo = (Foo) obj;
        obj.Callback ();
    }
}
```

 <a name="lines" />

### <a name="lines"></a>linhas

Se isso for definido como zero, ele fará com que o tamanho automático do elemento, dependendo do conteúdo das cadeias de caracteres contidas. Para que isso funcione, você também deve definir a `"linebreak"` Propriedade como `"character-wrap"` ou `"word-wrap"`.

 <a name="style" />

### <a name="style"></a>Estilo

O estilo determina o tipo de estilo de célula que será usado para renderizar o conteúdo e eles correspondem aos valores de enumeração UITableViewCellStyle.
Os valores possíveis são:

- `"default"`
- `"value1"`
- `"value2"`
- `"subtitle"`: texto com um subtítulo.

 <a name="subtitle" />

### <a name="subtitle"></a>subtítulo

O valor a ser usado para o subtítulo. Este é um atalho para definir o estilo como `"subtitle"` e definir a `"value"` Propriedade como uma cadeia de caracteres.
Isso faz tanto com uma única entrada.

 <a name="textcolor" />

### <a name="textcolor"></a>TextColor

A cor a ser usada para o texto.

 <a name="value" />

### <a name="value"></a>value

O valor secundário a ser mostrado no elemento de renderização. O layout disso é afetado pela `"style"` configuração. A fonte e a cor podem ser personalizadas definindo o `"detailfont"` e `"detailcolor"`o.

 <a name="Boolean_Elements" />

## <a name="boolean-elements"></a>Elementos boolianos

Elementos boolianos devem definir o tipo `"bool"`como, pode conter `"caption"` um para exibir e `"value"` o é definido como true ou false. Se as `"on"` propriedades `"off"` e estiverem definidas, elas serão consideradas imagens. As imagens são resolvidas em relação ao diretório de trabalho atual no aplicativo. Se você quiser referenciar arquivos relativos a pacote, poderá usar o `"~"` como um atalho para representar o diretório do pacote de aplicativos. Por exemplo `"~/favorite.png"` , será o favorito. png contido no arquivo de pacote. Por exemplo:

```json
{ 
    "type": "boolean",
    "caption": "Demo of a Boolean",
    "value": true
},

{
    "type": "boolean",
    "caption": "Boolean using images",
    "value": false,
    "on": "favorite.png",
    "off": "~/favorited.png"
}
```

 <a name="type" />

### <a name="type"></a>tipo

O `"boolean"` tipo pode ser definido como ou `"checkbox"`. Se definido como booliano, ele usará uma UISlider ou imagens ( `"on"` se `"off"` ambas e estiverem definidas). Se for definido como CheckBox, ele usará uma caixa de seleção. A `"group"` propriedade pode ser usada para marcar um elemento booliano como pertencente a um grupo específico. Isso será útil se a raiz que a contém também `"group"` tiver uma propriedade, pois a raiz resumirá os resultados com uma contagem de todos os Boolianos (ou caixas de seleção) que pertencem ao mesmo grupo.

 <a name="Entry_Elements" />

## <a name="entry-elements"></a>Elementos de entrada

Você usa elementos de entrada para permitir que o usuário insira dados. O tipo de elementos de entrada é `"entry"` ou `"password"`. A `"caption"` propriedade é definida como o texto a ser mostrado à direita e o `"value"` é definido como o valor inicial para definir a entrada como. O `"placeholder"` é usado para mostrar uma dica ao usuário para entradas vazias (ele é mostrado esmaecido). Estes são alguns exemplos:

```json
{
    "type": "entry",
    "caption": "Username",
    "value": "",
    "placeholder": "Your account username"
}, {
    "type": "password",
    "caption": "Password",
    "value": "",
    "placeholder": "You password"
}, {
    "type": "entry",
    "caption": "Zip Code",
    "value": "01010",
    "placeholder": "your zip code",
    "keyboard": "numbers"
}, {
    "type": "entry",
    "return-key": "route",
    "caption": "Entry with 'route'",
    "placeholder": "captialization all + no corrections",
    "capitalization": "all",
    "autocorrect": "no"
}
```

 <a name="autocorrect" />

### <a name="autocorrect"></a>matemática

Determina o estilo de correção automática a ser usado para a entrada. Os valores possíveis são true ou false (ou as cadeias `"no"`de caracteres `"yes"` e).

 <a name="capitalization" />

### <a name="capitalization"></a>/

O estilo de capitalização a ser usado para a entrada. Os valores possíveis são:

- `all`
- `none`
- `sentences`
- `words`

 <a name="caption" />

### <a name="caption"></a>Legenda

A legenda a ser usada para a entrada

 <a name="keyboard" />

### <a name="keyboard"></a>teclado

O tipo de teclado a ser usado para entrada de dados. Os valores possíveis são:

- `ascii`
- `decimal`
- `default`
- `email`
- `name`
- `numbers`
- `numbers-and-punctuation`
- `twitter`
- `url`

 <a name="placeholder" />

### <a name="placeholder"></a>reservado

O texto de dica que é mostrado quando a entrada tem um valor vazio.

 <a name="return-key" />

### <a name="return-key"></a>chave de retorno

O rótulo usado para a chave de retorno. Os valores possíveis são:

- `default`
- `done`
- `emergencycall`
- `go`
- `google`
- `join`
- `next`
- `route`
- `search`
- `send`
- `yahoo`

 <a name="value" />

### <a name="value"></a>value

O valor inicial para a entrada

 <a name="Radio_Elements" />

## <a name="radio-elements"></a>Elementos de rádio

Elementos de rádio têm `"radio"`tipo. O item selecionado é escolhido pela propriedade no elemento `radioselected` raiz que o contém.
Além disso, se um valor for definido para `"group"` a propriedade, esse botão de opção pertencerá a esse grupo.

 <a name="Date_and_Time_Elements" />

## <a name="date-and-time-elements"></a>Elementos de data e hora

Os tipos `"datetime"` `"time"` de elemento `"date"` e são usados para renderizar datas com horas, datas ou horas. Esses elementos assumem como parâmetros uma legenda e um valor. O valor pode ser escrito em qualquer formato com suporte da função DateTime. Parse do .NET. Exemplo:

```json
"header": "Dates and Times",
"elements": [
    {
        "type": "datetime",
        "caption": "Date and Time",
        "value": "Sat, 01 Nov 2008 19:35:00 GMT"
    }, {
        "type": "date",
        "caption": "Date",
        "value": "10/10"
    }, {
        "type": "time",
        "caption": "Time",
        "value": "11:23"
    }                       
]
```

 <a name="Html/Web_Element" />

## <a name="htmlweb-element"></a>Elemento HTML/Web

Você pode criar uma célula que, quando tocou, inserirá um UIWebView que renderiza o conteúdo de uma URL especificada, seja local ou remoto usando `"html"` o tipo. As duas únicas propriedades desse elemento são `"caption"` e: `"url"`

```json
{
    "type": "html",
    "caption": "Miguel's blog",
    "url": "https://tirania.org/blog" 
}
```
