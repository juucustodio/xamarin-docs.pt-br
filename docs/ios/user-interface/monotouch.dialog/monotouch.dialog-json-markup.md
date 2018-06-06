---
title: MonoTouch.Dialog Json Markup
description: Este documento descreve a sintaxe JSON que pode ser usada para criar uma interface de usuário do xamarin usando MonoTouch.Dialog.
ms.prod: xamarin
ms.assetid: 59F3E18C-3A73-69B8-DA5E-21B19B9DFB98
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: dc3f4ea87bbd381a4a1767fb9179fb1bcf0c56d8
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790751"
---
# <a name="monotouchdialog-json-markup"></a>MonoTouch.Dialog Json Markup

Esta página descreve a marcação de Json aceita do MonoTouch.Dialog [JsonElement](https://developer.xamarin.com/api/type/MonoTouch.Dialog.JsonElement/)

Vamos começar com um exemplo. Este é um arquivo Json completo que pode ser passado para JsonElement.

```csharp
{     
  "title": "Json Sample",
  "sections": [ 
      {
          "header": "Booleans",
          "footer": "Slider or image-based",
          "id": "first-section",
          "elements": [
              { 
                  "type" : "boolean",
                  "caption" : "Demo of a Boolean",
                  "value"   : true
              }, {
                  "type": "boolean",
                  "caption" : "Boolean using images",
                  "value"   : false,
                  "on"      : "favorite.png",
                  "off"     : "~/favorited.png"
              }, {
                      "type": "root",
                      "title": "Tap for nested controller",
                      "sections": [ {
                         "header": "Nested view!",
                         "elements": [
                           {
                             "type": "boolean",
                             "caption": "Just a boolean",
                             "id": "the-boolean",
                             "value": false
                           },
                           {
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

A marcação acima produz a interface do usuário do seguinte:

 [![](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png "A interface do usuário criado pela marcação determinada")](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png#lightbox)

Cada elemento na árvore pode conter a propriedade `"id"`. É possível em tempo de execução para referenciar as seções individuais ou elementos usando o indexador JsonElement. Assim:

```csharp
var jsonElement = JsonElement.FromFile ("demo.json");

var firstSection = jsonElement ["first-section"] as Section;

var theBoolean = jsonElement ["the-boolean"] as BooleanElement
```

 <a name="Root_Element_Syntax" />


## <a name="root-element-syntax"></a>Sintaxe do elemento raiz

O elemento raiz contém os seguintes valores:

-  `title`
-  `sections` (opcional)


O elemento raiz pode aparecer dentro de uma seção como um elemento para criar um controlador aninhado. Nesse caso, a propriedade extra `"type"` deve ser definido como `"root"`

 <a name="url" />


### <a name="url"></a>url

Se o `"url"` propriedade for definida, se o usuário toca neste RootElement, o código irá solicitar um arquivo da url especificada e fará com que o conteúdo da novo informações exibidas. Você pode usar isso para criar estender a interface do usuário do servidor com base no qual o usuário toca.

 <a name="group" />


### <a name="group"></a>grupo

Se definido, define o nome do grupo para o elemento raiz. Nomes de grupo são usados para obter um resumo que é exibido como o valor do elemento raiz de um dos elementos aninhados no elemento. Esse é o valor de uma caixa de seleção ou o valor de um botão de opção.

 <a name="radioselected" />


### <a name="radioselected"></a>radioselected

Identifica o item de opção selecionado em elementos aninhados

 <a name="title" />


### <a name="title"></a>título

Se estiver presente, ele será o título usado para a RootElement

 <a name="type" />


### <a name="type"></a>tipo

Deve ser definido como `"root"` quando isso for exibido em uma seção (Isso é usado para aninhar controladores).

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

Se estiver presente, o rodapé de página é exibida na parte inferior da seção.

 <a name="elements" />


### <a name="elements"></a>elementos

Esta é uma matriz de elementos. Cada elemento deve conter pelo menos uma chave, o `"type"` chave que é usado para identificar o tipo de elemento para criar.
Alguns dos elementos compartilham algumas propriedades em comum como `"caption"` e `"value"`. Estes são a lista de elementos com suporte:

-  `string` elementos (ambos com e sem estilo)
-  `entry` linhas (normais ou senha)
-  `boolean` valores (usando switches ou imagens)


Elementos de cadeia de caracteres podem ser usados como botões ao fornecer um método a ser invocado quando o usuário toca na célula ou acessório,

 <a name="Rendering_Elements" />


## <a name="rendering-elements"></a>Elementos de renderização

Os elementos de renderização baseiam-se no c# StringElement e StyledStringElement e que possam processar informações de várias maneiras, e é possível renderizá-los de várias maneiras. Os elementos mais simples podem ser criados como este:

```csharp
{
        "type": "string",
        "caption": "Json Serializer",
}
```

Isso mostrará uma cadeia de caracteres simple com todos os padrões: fontes, plano de fundo, cor do texto e decorações. É possível conectar-se com as ações a esses elementos e torná-los se comportam como botões definindo o `"ontap"` propriedade ou o `"onaccessorytap"` propriedades:

```csharp
{
    "type":    "string",
        "caption": "View Photos",
        "ontap:    "Acme.PhotoLibrary.ShowPhotos"
}
```

As opções acima chamará o método "ShowPhotos" na classe "Acme.PhotoLibrary". O `"onaccessorytap"` é semelhante, mas ele só será chamado se o usuário toca em Acessórios, em vez de tocar na célula. Para habilitar isso, você também deve definir o acessório:

```csharp
{
    "type":     "string",
        "caption":  "View Photos",
        "ontap:     "Acme.PhotoLibrary.ShowPhotos",
        "accessory: "detail-disclosure",
        "onaccessorytap": "Acme.PhotoLibrary.ShowStats"
}
```

Elementos de renderização pode duas cadeias de caracteres serão exibidos ao mesmo tempo, uma é a legenda e outro é o valor. Como essas cadeias de caracteres são renderizadas dependem de estilo, você pode definir isso usando o `"style"` propriedade. O padrão mostrará a legenda à esquerda, e o valor à direita. Consulte a seção em style para obter mais detalhes. As cores são codificadas usando o símbolo '#' seguido por números hexadecimais que representam os valores para os valores de vermelhos, verdes, azuis e talvez alfa. O conteúdo pode ser codificado em forma abreviada (3 ou 4 dígitos hexadecimais) que representa os valores RGB ou RGBA. Ou o formato longo (6 ou 8 dígitos) que representam valores RGB ou RGBA. A versão curta é uma abreviação para gravar o mesmo dígito hexadecimal duas vezes. Portanto, a constante "#1bc" é interpretadas como vermelho = 0x11, verde = 0xbb e azul = 0xcc. Se o valor alfa não estiver presente, a cor é opaca. Alguns exemplos:

```csharp
"background": "#f00"
"background": "#fa08f880"
```

 <a name="accessory" />


### <a name="accessory"></a>acessório

Determina o tipo de acessório a ser mostrado no elemento seu processamento, os valores possíveis são:

-  `checkmark`
-  `detail-disclosure`
-  `disclosure-indicator`


Se o valor não estiver presente, nenhum acessório é mostrado

 <a name="background" />


### <a name="background"></a>tela de fundo

A propriedade de plano de fundo define a cor de plano de fundo da célula. O valor é a uma URL para uma imagem (nesse caso, o downloader de imagem async será chamado e o plano de fundo será atualizado depois que a imagem é baixada) ou pode ser uma cor especificada usando a sintaxe de cor.

 <a name="caption" />


### <a name="caption"></a>Legenda

A cadeia de caracteres principal a ser mostrado no elemento de renderização. A fonte e cor podem ser personalizados, definindo o `"textcolor"` e `"font"` propriedades. O estilo de renderização é determinado pelo `"style"` propriedade.

 <a name="color_and_detailcolor" />


### <a name="color-and-detailcolor"></a>cor e detailcolor

A cor a ser usada para o texto principal ou o texto detalhado.

 <a name="detailfont_and_font" />


### <a name="detailfont-and-font"></a>DetailFont e fonte

A fonte a ser usado para a legenda ou o texto detalhado. O formato de uma especificação de fonte é o nome da fonte, opcionalmente seguido por um traço e o tamanho do ponto.
Estas são as especificações de fonte válido:

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


Ambos `character-wrap` e `word-wrap` pode ser usado junto com o `"lines"` propriedade definida como zero para ativar o elemento de renderização em um elemento de várias linha.

 <a name="ontap_and_onaccessorytap" />


### <a name="ontap-and-onaccessorytap"></a>ONTAP e onaccessorytap

Essas propriedades devem apontar para um nome de método estático em seu aplicativo que utiliza um objeto como um parâmetro. Quando você cria sua hierarquia usando os métodos JsonDialog.FromFile ou JsonDialog.FromJson, você pode passar um valor de objeto opcional. Esse valor de objeto, em seguida, é passado para seus métodos. Você pode usar isso para passar o contexto para o método estático. Por exemplo:

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

Se isso for definido como zero, fazer o dimensionamento automático elemento dependendo do conteúdo das cadeias de caracteres contida. Para que isso funcione, você também deve definir o `"linebreak"` propriedade `"character-wrap"` ou `"word-wrap"`.

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

O valor a ser usado para o subtítulo. Este é um atalho para definir o estilo como `"subtitle"` e defina o `"value"` propriedade como uma cadeia de caracteres.
Isso faz com que uma única entrada.

 <a name="textcolor" />


### <a name="textcolor"></a>textcolor

A cor a ser usado para o texto.

 <a name="value" />


### <a name="value"></a>Valor 

O valor secundário a ser mostrado no elemento de renderização. O layout deste é afetado pelo `"style"` configuração. A fonte e cor podem ser personalizados, definindo o `"detailfont"` e `"detailcolor"`.

 <a name="Boolean_Elements" />


## <a name="boolean-elements"></a>Elementos boolianos

Elementos boolianos devem definir o tipo como `"bool"`, pode conter um `"caption"` para exibir e `"value"` é definido como true ou false. Se o `"on"` e `"off"` propriedades são definidas, elas são consideradas imagens. As imagens são resolvidas em relação ao diretório de trabalho atual do aplicativo. Se você quiser fazer referência a arquivos de pacote relativa, você pode usar o `"~"` como um atalho para representar o diretório do pacote de aplicativo. Por exemplo `"~/favorite.png"` será o favorite.png que está contido no arquivo de pacote. Por exemplo:

```csharp
{ 
    "type" : "boolean",
    "caption" : "Demo of a Boolean",
    "value"   : true
},

{
    "type": "boolean",
    "caption" : "Boolean using images",
    "value"   : false,
    "on"      : "favorite.png",
    "off"     : "~/favorited.png"
}
```

 <a name="type" />


### <a name="type"></a>tipo

Tipo pode ser definido como `"boolean"` ou `"checkbox"`. Se definido como booliano ele usará um UISlider ou imagens (se ambos os `"on"` e `"off"` são definidos). Se definido como caixa de seleção, ele usará a caixa de seleção. O `"group"` propriedade pode ser usada para marcar um elemento booleano como pertencente a um grupo específico. Isso será útil se a raiz contém também tem um `"group"` propriedade como a raiz resumirá os resultados com uma contagem de todos os valores booleanos (ou caixas de seleção) que pertencem ao mesmo grupo.

 <a name="Entry_Elements" />


## <a name="entry-elements"></a>Elementos de entrada

Você pode usar elementos de entrada para permitir que o usuário insira dados. O tipo de elementos de entrada é `"entry"` ou `"password"`. O `"caption"` propriedade é definida como o texto para mostrar à direita e o `"value"` é definido como o valor inicial para definir a entrada para. O `"placeholder"` é usado para mostrar uma dica para o usuário para entradas vazias (é mostrado esmaecidos). Estes são alguns exemplos:

```csharp
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


### <a name="capitalization"></a>O uso de maiusculas

O estilo de capitalização para usar para a entrada. Os valores possíveis são:

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

O texto de dica que é exibido quando a entrada tem um valor vazio.

 <a name="return-key" />


### <a name="return-key"></a>chave de retorno

O rótulo usado para a chave de retorno. Os valores possíveis são:

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


## <a name="radio-elements"></a>Elementos de opção

Elementos de opção têm tipo `"radio"`. O item selecionado é escolhido pelo `radioselected` propriedade em seu elemento raiz contém.
Além disso, se um valor é definido para o `"group"` propriedade, esse botão de opção pertence a esse grupo.

 <a name="Date_and_Time_Elements" />


## <a name="date-and-time-elements"></a>Data e hora elementos

Os tipos de elemento `"datetime"`, `"date"` e `"time"` são usados para renderizar datas com horas, datas ou horas. Esses elementos levar como parâmetros de uma legenda e um valor. O valor pode ser gravado em qualquer formato aceito pela função Parse .NET. Exemplo:

```csharp
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


## <a name="htmlweb-element"></a>Elemento HTML/da Web

Você pode criar uma célula que quando tocada incorporará um UIWebView que renderiza o conteúdo de uma URL especificada, local ou remoto usando o `"html"` tipo. Apenas duas propriedades para esse elemento são `"caption"` e `"url"`:

```csharp
{
        "type": "html",
        "caption": "Miguel's blog",
        "url": "http://tirania.org/blog" 
}
```
