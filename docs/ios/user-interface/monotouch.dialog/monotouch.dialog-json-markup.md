---
title: Markup Json MonoTouch.Dialog
description: Este documento descreve a sintaxe JSON que pode ser usada para criar uma interface de usuário do xamarin. IOS usando MonoTouch.
ms.prod: xamarin
ms.assetid: 59F3E18C-3A73-69B8-DA5E-21B19B9DFB98
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: bc6842871a2f59c9851e90adbc6609707a7ecd1f
ms.sourcegitcommit: 85c45dc28ab3625321c271804768d8e4fce62faf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67039650"
---
# <a name="monotouchdialog-json-markup"></a>Markup Json MonoTouch.Dialog

Esta página descreve a marcação do Json aceita do MonoTouch [JsonElement](xref:MonoTouch.Dialog.JsonElement)

Vamos começar com um exemplo. O exemplo a seguir é um arquivo Json completo que pode ser passado para JsonElement.

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

 [![](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png "A interface do usuário criado pela marcação determinada")](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png#lightbox)

Cada elemento na árvore pode conter a propriedade `"id"`. É possível em tempo de execução fazer referência a elementos usando o indexador JsonElement ou seções individuais. Assim:

```csharp
var jsonElement = JsonElement.FromFile ("demo.json");

var firstSection = jsonElement ["first-section"] as Section;

var theBoolean = jsonElement ["the-boolean"] as BooleanElement;
```

 <a name="Root_Element_Syntax" />


## <a name="root-element-syntax"></a>Sintaxe de elemento de raiz

O elemento raiz contém os seguintes valores:

-  `title`
-  `sections` (opcional)


O elemento raiz pode aparecer dentro de uma seção como um elemento para criar um controlador aninhado. Nesse caso, a propriedade extra `"type"` deve ser definida como `"root"`

 <a name="url" />


### <a name="url"></a>url

Se o `"url"` propriedade for definida, se o usuário toca esse RootElement, o código será um arquivo de solicitação da url especificada e fará com que o conteúdo as novas informações exibidas. Você pode usar isso para criar estender a interface do usuário do servidor com base no qual o usuário toca.

 <a name="group" />


### <a name="group"></a>grupo

Se definido, define o nome de grupo para o elemento raiz. Nomes de grupo são usados para selecionar um resumo do que é exibido como o valor do elemento raiz de um dos elementos aninhados no elemento. Isso é o valor de uma caixa de seleção ou o valor de um botão de opção.

 <a name="radioselected" />


### <a name="radioselected"></a>radioselected

Identifica o item de rádio selecionado em elementos aninhados

 <a name="title" />


### <a name="title"></a>título

Se estiver presente, ele será o título usado para o RootElement

 <a name="type" />


### <a name="type"></a>tipo

Deve ser definida como `"root"` quando isso for exibido em uma seção (Isso é usado para aninhar controladores).

 <a name="sections" />


### <a name="sections"></a>sections

Isso é uma matriz Json com as seções individuais

 <a name="Section_Syntax" />


## <a name="section-syntax"></a>Sintaxe de seção

A seção contém:

-  `header` (opcional)
-  `footer` (opcional)
-  Matriz `elements`


 <a name="header" />


### <a name="header"></a>cabeçalho

Se estiver presente, o texto do cabeçalho é exibido como uma legenda da seção.

 <a name="footer" />


### <a name="footer"></a>Rodapé

Se estiver presente, o rodapé é exibido na parte inferior da seção.

 <a name="elements" />


### <a name="elements"></a>elementos

Essa é uma matriz de elementos. Cada elemento deve conter pelo menos uma chave, o `"type"` chave que é usado para identificar o tipo de elemento a ser criado.
Alguns dos elementos compartilham algumas propriedades comuns, como `"caption"` e `"value"`. Esses são a lista de elementos com suporte:

-  `string` elementos (ambos com e sem estilo)
-  `entry` linhas (regular ou senha)
-  `boolean` valores (utilizando comutadores ou imagens)


Elementos de cadeia de caracteres podem ser usados como botões, fornecendo um método a ser invocado quando o usuário toca na célula ou o acessório

 <a name="Rendering_Elements" />


## <a name="rendering-elements"></a>Renderização de elementos

Os elementos de renderização são baseados no C# StringElement e StyledStringElement e eles podem processar informações de várias maneiras e é possível renderizá-los de várias maneiras. Os elementos mais simples podem ser criados como este:

```json
{
    "type": "string",
    "caption": "Json Serializer"
}
```

Isso mostrará uma simple cadeia de caracteres com todos os padrões: fonte, plano de fundo, cor do texto e decorações. É possível vincular ações a esses elementos e torná-los a se comportar como botões, definindo o `"ontap"` propriedade ou o `"onaccessorytap"` propriedades:

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos"
}
```

As opções acima invocará o método "ShowPhotos" na classe "Acme.PhotoLibrary". O `"onaccessorytap"` é semelhante, mas ele será invocado apenas se o usuário toca o acessório em vez de tocar na célula. Para habilitar isso, você também deve definir o acessório:

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos",
    "accessory": "detail-disclosure",
    "onaccessorytap": "Acme.PhotoLibrary.ShowStats"
}
```

Renderização de elementos ao mesmo tempo é possível exibir a duas cadeias de caracteres, um é a legenda e outro é o valor. Como essas cadeias de caracteres são renderizadas depende do estilo, você pode definir isso usando o `"style"` propriedade. O padrão mostrará a legenda à esquerda, e o valor à direita. Consulte a seção sobre o estilo para obter mais detalhes. As cores são codificadas usando o símbolo '#' seguido de números hexadecimais que representam os valores para os valores de vermelhos, verdes, azuis e alfa talvez. O conteúdo pode ser codificado em forma abreviada (3 ou 4 dígitos hexadecimais) que representa valores RGB ou RGBA. Ou o formato longo (6 ou 8 dígitos) que representam valores RGB ou RGBA. A versão curta é um tipo de taquigrafia para escrever o mesmo dígito hexadecimal duas vezes. Portanto, a constante "#1bc" é interpretado como vermelho = 0x11, verde = 0xbb e azul = 0xcc. Se o valor alfa não estiver presente, a cor é opaca. Alguns exemplos:

```json
"background": "#f00"
"background": "#fa08f880"
```

 <a name="accessory" />


### <a name="accessory"></a>Acessório

Determina o tipo de acessório sejam mostrados no seu elemento de renderização, os valores possíveis são:

-  `checkmark`
-  `detail-disclosure`
-  `disclosure-indicator`


Se o valor não estiver presente, nenhum acessório é mostrado

 <a name="background" />


### <a name="background"></a>tela de fundo

A propriedade background define a cor de fundo da célula. O valor é a uma URL para uma imagem (nesse caso, o downloader de imagem async será invocado e o plano de fundo será atualizado depois que a imagem é baixada) ou pode ser uma cor especificada usando a sintaxe de cor.

 <a name="caption" />


### <a name="caption"></a>Legenda

A cadeia de caracteres principal a ser mostrado no elemento de renderização. A fonte e cor podem ser personalizados definindo a `"textcolor"` e `"font"` propriedades. O estilo de renderização é determinado pelo `"style"` propriedade.

 <a name="color_and_detailcolor" />


### <a name="color-and-detailcolor"></a>cor e detailcolor

A cor a ser usado para o texto principal ou o texto detalhado.

 <a name="detailfont_and_font" />


### <a name="detailfont-and-font"></a>fonte e DetailFont

A fonte a ser usado para a legenda ou o texto de detalhes. O formato de uma especificação de fonte é o nome da fonte, opcionalmente, seguido por um traço e o tamanho do ponto.
Estas são as especificações de uma fonte válida:

-  "Helvetica"
-  "Helvetica-14"


 <a name="linebreak" />


### <a name="linebreak"></a>LineBreak

Determina como linhas são divididas. Os valores possíveis são:

-  `character-wrap`
-  `clip`
-  `head-truncation`
-  `middle-truncation`
-  `tail-truncation`
-  `word-wrap`


Ambos `character-wrap` e `word-wrap` pode ser usada junto com o `"lines"` propriedade definida como zero para transformar o elemento de renderização em um elemento de várias linha.

 <a name="ontap_and_onaccessorytap" />


### <a name="ontap-and-onaccessorytap"></a>ONTAP e onaccessorytap

Essas propriedades devem apontar para um nome de método estático em seu aplicativo que usa um objeto como um parâmetro. Quando você cria sua hierarquia usando os métodos JsonDialog.FromFile ou JsonDialog.FromJson, você pode passar um valor de objeto opcional. Esse valor de objeto, em seguida, é passado para seus métodos. Você pode usar isso para passar algum contexto para o método estático. Por exemplo:

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

Se isso for definido como zero, ele se tornará o autotamanho do elemento dependendo do conteúdo das cadeias de caracteres contida. Para isso funcionar, você também deve definir a `"linebreak"` propriedade para `"character-wrap"` ou `"word-wrap"`.

 <a name="style" />


### <a name="style"></a>Estilo

O estilo determina o tipo de estilo de célula que será usado para renderizar o conteúdo e correspondem aos valores de enumeração UITableViewCellStyle.
Os valores possíveis são:

-  `"default"`
-  `"value1"`
-  `"value2"`
-  `"subtitle"` : texto com um subtítulo.


 <a name="subtitle" />


### <a name="subtitle"></a>Subtítulo

O valor a ser usado para o subtítulo. Esse é um atalho para definir o estilo `"subtitle"` e defina o `"value"` propriedade como uma cadeia de caracteres.
Isso faz isso com uma única entrada.

 <a name="textcolor" />


### <a name="textcolor"></a>textcolor

A cor a ser usada para o texto.

 <a name="value" />


### <a name="value"></a>Valor

O valor secundário a ser mostrado no elemento de renderização. O layout dessa é afetado pelo `"style"` configuração. A fonte e cor podem ser personalizados definindo a `"detailfont"` e `"detailcolor"`.

 <a name="Boolean_Elements" />


## <a name="boolean-elements"></a>Elementos boolianos

Elementos boolianos devem definir o tipo como `"bool"`, pode conter um `"caption"` para exibir e o `"value"` é definido como true ou false. Se o `"on"` e `"off"` são definidas, elas são consideradas imagens. As imagens são resolvidas em relação ao diretório de trabalho atual no aplicativo. Se você quiser fazer referência a arquivos de pacote relativo, você pode usar o `"~"` como um atalho para representar o diretório de pacote do aplicativo. Por exemplo `"~/favorite.png"` será o favorite.png que está contido no arquivo de pacote. Por exemplo:

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

Tipo pode ser definido como `"boolean"` ou `"checkbox"`. Se definido como booliano ele usará um UISlider ou imagens (se ambos `"on"` e `"off"` são definidos). Se definido como a caixa de seleção, ele usará uma caixa de seleção. O `"group"` propriedade pode ser usada para marcar um elemento booliano como pertencente a um grupo específico. Isso será útil se a raiz do recipiente também tem um `"group"` propriedade como a raiz resumirá os resultados com uma contagem de todos os boolianos (ou caixas de seleção) que pertencem ao mesmo grupo.

 <a name="Entry_Elements" />


## <a name="entry-elements"></a>Elementos de entrada

Você pode usar elementos de entrada para permitir que o usuário insira dados. O tipo de elementos de entrada é `"entry"` ou `"password"`. O `"caption"` propriedade é definida para o texto para mostrar à direita e o `"value"` é definido como o valor inicial para definir a entrada para. O `"placeholder"` é usado para mostrar uma dica para o usuário para entradas vazias (ele é mostrado esmaecido). Estes são alguns exemplos:

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


### <a name="autocorrect"></a>correção automática

Determina o estilo de correção automática a ser usado para a entrada. Os valores possíveis são true ou false (ou as cadeias de caracteres `"yes"` e `"no"`).

 <a name="capitalization" />


### <a name="capitalization"></a>uso de maiusculas

O estilo de capitalização a ser usado para a entrada. Os valores possíveis são:

-  `all`
-  `none`
-  `sentences`
-  `words`


 <a name="caption" />


### <a name="caption"></a>Legenda

A legenda a ser usado para a entrada

 <a name="keyboard" />


### <a name="keyboard"></a>teclado

O tipo de teclado a ser usado para entrada de dados. Os valores possíveis são:

-  `ascii`
-  `decimal`
-  `default`
-  `email`
-  `name`
-  `numbers`
-  `numbers-and-punctuation`
-  `twitter`
-  `url`


 <a name="placeholder" />


### <a name="placeholder"></a>Espaço reservado

O texto de dica que é mostrado quando a entrada tem um valor vazio.

 <a name="return-key" />


### <a name="return-key"></a>return-key

O rótulo usado para a tecla return. Os valores possíveis são:

-  `default`
-  `done`
-  `emergencycall`
-  `go`
-  `google`
-  `join`
-  `next`
-  `route`
-  `search`
-  `send`
-  `yahoo`


 <a name="value" />


### <a name="value"></a>Valor

O valor inicial para a entrada

 <a name="Radio_Elements" />


## <a name="radio-elements"></a>Elementos de rádio

Elementos de rádio têm tipo `"radio"`. O item selecionado é escolhido pelo `radioselected` propriedade no elemento raiz que contém.
Além disso, se um valor é definido para o `"group"` propriedade, esse botão de opção pertence a esse grupo.

 <a name="Date_and_Time_Elements" />


## <a name="date-and-time-elements"></a>Data e hora elementos

Os tipos de elementos `"datetime"`, `"date"` e `"time"` são usados para renderizar as datas com horas, datas ou horas. Esses elementos usam como parâmetros de uma legenda e um valor. O valor pode ser escrito em qualquer formato aceito pela função DateTime. Parse do .NET. Exemplo:

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


## <a name="htmlweb-element"></a>Elemento de HTML/Web

Você pode criar uma célula que quando tocada incorporará um UIWebView que renderiza o conteúdo de uma URL especificada, local ou remoto usando o `"html"` tipo. São apenas duas propriedades para este elemento `"caption"` e `"url"`:

```json
{
    "type": "html",
    "caption": "Miguel's blog",
    "url": "https://tirania.org/blog" 
}
```
