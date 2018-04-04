---
title: Introdução ao MonoTouch.Dialog
description: O MonoTouch.Dialog (MT. D) o Kit de ferramentas é uma estrutura indispensável para rápido desenvolvimento de interface do usuário no xamarin de aplicativos. MT. D torna rápido e fácil definir o aplicativo complexo da interface do usuário usando uma abordagem declarativa, em vez do tédio de controladores de navegação, tabelas, etc. Além disso, MT. D tem um conjunto flexível de APIs que fornecer aos desenvolvedores um controle total ou entrega abordagem, bem como recursos adicionais, como o carregamento da imagem de plano de fundo de puxe para atualizar, procure o suporte e a geração dinâmica de interface do usuário por meio de dados JSON. Este guia apresenta as diferentes maneiras de trabalhar com MT. D e, em seguida, examina profundamente uso avançado.
ms.prod: xamarin
ms.assetid: 52A35B24-C23B-8461-A8FF-5928A2128FB0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: be979b35ffdd597dae74f1f661a381ae44433b10
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-monotouchdialog"></a>Introdução ao MonoTouch.Dialog

_O MonoTouch.Dialog (MT. D) o Kit de ferramentas é uma estrutura indispensável para rápido desenvolvimento de interface do usuário no xamarin de aplicativos. MT. D torna rápido e fácil definir o aplicativo complexo da interface do usuário usando uma abordagem declarativa, em vez do tédio de controladores de navegação, tabelas, etc. Além disso, MT. D tem um conjunto flexível de APIs que fornecer aos desenvolvedores um controle total ou entrega abordagem, bem como recursos adicionais, como o carregamento da imagem de plano de fundo de puxe para atualizar, procure o suporte e a geração dinâmica de interface do usuário por meio de dados JSON. Este guia apresenta as diferentes maneiras de trabalhar com MT. D e, em seguida, examina profundamente uso avançado._


MonoTouch.Dialog, conhecido como MT. D para resumir, é um kit de ferramentas de desenvolvimento da interface do usuário rápida que permite aos desenvolvedores criar telas de aplicativo e a navegação usando informações, em vez do tédio de criação de controladores de exibição, tabelas, etc. Como tal, ele fornece uma simplificação significativa de redução de desenvolvimento e o código de interface do usuário. Por exemplo, considere a captura de tela a seguir:

 [![](images/image1.png "Por exemplo, considere esta captura de tela")](images/image1.png#lightbox)

O código a seguir foi usado para definir esta tela inteira:

```csharp
public enum Category
{
    Travel,
    Lodging,
    Books
}
        
public class Expense
{
    [Section("Expense Entry")]

    [Entry("Enter expense name")]
    public string Name;
    [Section("Expense Details")]
  
    [Caption("Description")]
    [Entry]
    public string Details;
        
    [Checkbox]
    public bool IsApproved = true;
    [Caption("Category")]
    public Category ExpenseCategory;
}
```

Ao trabalhar com tabelas no iOS, geralmente há inúmeras código redundante.
Por exemplo, toda vez que uma tabela é necessária, uma fonte de dados é necessário para popular a tabela. Em um aplicativo que tem duas telas baseadas em tabelas que estão conectadas por meio de um controlador de navegação, cada tela compartilha o mesmo código muito.

MT. D que simplifica encapsulando todo esse código em uma API genérica para criação de tabela. Ele fornece uma abstração sobre essa API que permite que um objeto declarativo de sintaxe que torna mais fácil de associação. Assim, há duas APIs disponíveis no MT. UNIDADE D:

-   **API de elementos de nível baixo** – a *elementos API* baseia-se na criação de uma árvore hierárquica de elementos que representam as telas e seus componentes. A API de elementos fornece aos desenvolvedores mais flexibilidade e controle para criar interfaces do usuário. Além disso, a API de elementos tem suporte avançado para definição declarativa via JSON, que permite que tanto a declaração incrivelmente rápida, bem como geração dinâmica de interface do usuário de um servidor. 
-   **API de reflexão de alto nível** — também conhecido como o *associação**API* , em quais classes são anotadas com dicas de interface do usuário e, em seguida, MT. D automaticamente cria com base nos objetos de telas e fornece uma associação entre o que é exibida (e opcionalmente editada) na tela e o objeto base fazendo. O exemplo acima ilustra o uso da API de reflexão. Esta API não fornece um controle refinado do que os elementos de API, mas reduz ainda mais a complexidade criando automaticamente a hierarquia de elementos com base em atributos de classe. 


MT. D vem compactado com um grande conjunto de criados em elementos de interface do usuário para a criação da tela, mas também reconhece a necessidade de elementos personalizados e layouts de tela avançadas. Como tal, a extensibilidade é que uma primeira classe em destaque Pães a API. Os desenvolvedores podem estender os elementos existentes ou criar novos e, em seguida, integrar perfeitamente.

Além disso, MT. D tem um número de recursos comuns de UX iOS interna, como "puxe para atualizar" suporte a carregamento, assíncrono da imagem e suporte de pesquisa.

Este artigo examinará abrangente como trabalhar com MT. D, incluindo:

-   **MT. Componentes de D** – o foco em compreender as classes que compõem o MT. D possa obter rapidamente rapidamente. 
-   **Referência de elementos** – uma lista abrangente dos elementos internos de MT. D. 
-   **O uso avançado** – isso inclui recursos avançados, como puxe para atualizar, pesquisa, carregamento de imagem de plano de fundo, usando o LINQ para criar hierarquias de elemento e criar elementos personalizados, células, e os controladores para usam com MT. D. 

## <a name="understanding-the-pieces-of-mtd"></a>Compreendendo as partes de MT. D

Mesmo ao usar a reflexão API, MT. D cria uma hierarquia de elemento subjacente, como se foram criada por meio da API de elementos diretamente. Além disso, o suporte JSON mencionado na seção anterior cria elementos também. Por esse motivo, é importante ter uma compreensão básica das partes constituintes do MT. D.

MT. D cria telas usando as seguintes quatro partes:

-  **DialogViewController**
-  **RootElement**
-  **Section**
-  **Elemento**


### <a name="dialogviewcontroller"></a>DialogViewController

Um *DialogViewController*, ou *DVC* para resumir, herda do `UITableViewController` e, portanto, representa uma tela com uma tabela. DVCs podem ser enviadas para um controlador de navegação como um UITableViewController regular.

### <a name="rootelement"></a>RootElement

Um *RootElement* é o contêiner de nível superior para os itens que entram em um DVC. Ele contém seções, que, em seguida, podem conter elementos. RootElements não são renderizadas; em vez disso, eles são simplesmente contêineres para o que realmente é renderizado. Um RootElement é atribuído a um DVC e, em seguida, o DVC processa seus filhos.

### <a name="section"></a>Seção

Uma seção é um grupo de células em uma tabela. Como com uma seção de tabela normal, opcionalmente, ele pode ter um cabeçalho e rodapé que pode ser texto ou modos de exibição mesmo personalizados, como a captura de tela a seguir:

 [![](images/image2.png "Como com uma seção de tabela normal, opcionalmente, ele pode ter um cabeçalho e rodapé que pode ser texto ou até mesmo personalizados modos de exibição, como esta captura de tela")](images/image2.png#lightbox)

### <a name="element"></a>Elemento

Um elemento representa uma célula real na tabela. MT. D é compactado com uma ampla variedade de elementos que representam os diferentes tipos de dados ou entradas diferentes. Por exemplo, as capturas de tela a seguir ilustram alguns dos elementos disponíveis:

 [![](images/image3.png "Por exemplo, este capturas de tela ilustrar alguns dos elementos disponíveis")](images/image3.png#lightbox)

## <a name="more-on-sections-and-rootelements"></a>Mais sobre seções e RootElements

Vamos agora discutir RootElements e seções em maiores detalhes.

### <a name="rootelements"></a>RootElements

Pelo menos um RootElement é necessária para iniciar o processo de MonoTouch.Dialog.

Se um RootElement é inicializada com um valor de elemento da seção/esse valor é usado para localizar um filho do elemento que fornece um resumo da configuração, que é renderizado no lado direito da tela. Por exemplo, a captura de tela abaixo mostra uma tabela à esquerda com uma célula que contém o título da tela de detalhes à direita, "Sobremesa", junto com o valor do Deserto selecionado.

 [![](images/image4.png "Esta captura de tela mostra uma tabela à esquerda com uma célula que contém o título da tela de detalhes à direita, sobremesa, junto com o valor do Deserto selecionado") ](images/image4.png#lightbox) [ ![ ] (images/image5.png "isso captura de tela abaixo mostra uma tabela à esquerda com uma célula que contém o título da tela de detalhes à direita, sobremesa, junto com o valor do Deserto selecionado")](images/image5.png#lightbox)

Elementos raiz também podem ser usados dentro de seções para disparar a carregar uma nova página de configuração aninhados, como mostrado acima. Quando usado nesse modo a legenda fornecida é usada enquanto renderizado dentro de uma seção e também é usada como o título para a subpágina. Por exemplo:

```csharp
var root = new RootElement ("Meals") {
    new Section ("Dinner"){
            new RootElement ("Dessert", new RadioGroup ("dessert", 2)) {
                new Section () {
                    new RadioElement ("Ice Cream", "dessert"),
                    new RadioElement ("Milkshake", "dessert"),
                    new RadioElement ("Chocolate Cake", "dessert")
                }
            }
        }
    }
```

No exemplo acima, quando o usuário toca em "Sobremesa" MonoTouch.Dialog será criar uma nova página e navegue até ele com a raiz sendo "Sobremesa" e ter um grupo de rádio com três valores.

Este exemplo específico, o grupo de opção selecionará "Bolo de Chocolate" na seção "Sobremesa" porque passamos o valor "2" para o RadioGroup. Isso significa que escolher o item 3 na lista (índice de zero).

Chamar o método Add ou usando a sintaxe do inicializador de C# 4 adiciona seções.
Os métodos de inserção são fornecidos para inserir seções com uma animação.

Se você criar a RootElement com uma instância de grupo (em vez de um RadioGroup), o valor de resumo de RootElement quando exibido em uma seção será a contagem cumulativa de todas as BooleanElements e CheckboxElements que têm a mesma chave que o valor de Group.Key.

### <a name="sections"></a>Seções

As seções são usadas para agrupar elementos na tela e são os filhos diretos só é válidos da RootElement. Seções podem conter qualquer um dos elementos padrão, incluindo RootElements novo.

RootElements inseridos em uma seção são usados para navegar até um nível mais profundo de novo.

Seções podem ter cabeçalhos e rodapés como cadeias de caracteres ou como UIViews.
Normalmente você usará somente as cadeias de caracteres, mas para criar interfaces do usuário personalizadas, você pode usar qualquer UIView como o cabeçalho ou rodapé. Você pode usar uma cadeia de caracteres para criá-los como este:

```csharp
var section = new Section ("Header", "Footer")
```

Para usar os modos de exibição, passe os modos de exibição para o construtor:

```csharp
var header = new UIImageView (Image.FromFile ("sample.png"));
var section = new Section (header)
```

### <a name="getting-notified"></a>Ser notificado

#### <a name="handling-nsaction"></a>Tratamento de NSAction

MT. As superfícies de D um `NSAction` como um delegado para tratar retornos de chamada.
Por exemplo, digamos que você deseja manipular um evento de toque de uma célula de tabela criado pelo MT. D. Ao criar um elemento com MT. D, simplesmente forneça um retorno de chamada de função, conforme mostrado abaixo:

```csharp
new Section () {
        new StringElement ("Demo Callback", 
                delegate { Console.WriteLine ("Handled"); })
}
```

#### <a name="retrieving-element-value"></a>Recuperar o valor do elemento

Combinado com o `Element.Value` propriedade, o retorno de chamada pode recuperar o valor definido em outros elementos. Por exemplo, considere o seguinte código:

```csharp
var element = new EntryElement (task.Name, "Enter task description",
        task.Description);
                
var taskElement = new RootElement (task.Name){
        new Section () { element },
        new Section () { 
                new DateElement ("Due Date", task.DueDate)
        },
        new Section ("Demo Retrieving Element Value") {
                new StringElement ("Output Task Description", 
                        delegate { Console.WriteLine (element.Value); })
        }
};
```

Esse código cria uma interface do usuário, conforme mostrado abaixo. Para obter uma explicação completa deste exemplo, consulte o [passo a passo de API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) tutorial.

 [![](images/image6.png "Combinado com a propriedade Element.Value, o retorno de chamada pode recuperar o valor definido em outros elementos")](images/image6.png#lightbox)

Quando o usuário pressiona a célula inferior da tabela, o código na função anônima que é executado, gravar o valor da `element` de instância para o **saída do aplicativo** teclado no Visual Studio para Mac.

## <a name="built-in-elements"></a>Elementos internos

MT. D vem com um número de itens de célula de tabela interna, conhecido como elementos.
Esses elementos são usados para exibir uma variedade de tipos diferentes em células de tabela, como cadeias de caracteres, flutuações, datas e até mesmo imagens, para mencionar apenas alguns. Cada elemento cuida de exibir o tipo de dados adequadamente. Por exemplo, um elemento booliano exibirá um switch para alternar seu valor. Da mesma forma, um elemento de float exibirá um controle deslizante para alterar o valor de float.

Há elementos ainda mais complexos para dar suporte a tipos de dados mais avançados, como imagens e html. Por exemplo, um elemento html, o que abrirá um UIWebView para carregar uma página da web, quando selecionado, exibe uma legenda na célula da tabela.

### <a name="working-with-element-values"></a>Trabalhando com valores de elemento

Elementos que são usados para capturar a entrada do usuário expõem um público `Value` propriedade que contém o valor atual do elemento a qualquer momento. Ele é atualizado automaticamente conforme o usuário usa o aplicativo.

Esse é o comportamento para todos os elementos que fazem parte de MonoTouch.Dialog, mas ele não é necessário para elementos criados pelo usuário.

### <a name="string-element"></a>Elemento de cadeia de caracteres

A `StringElement` mostra uma legenda no lado esquerdo de uma célula de tabela e o valor de cadeia de caracteres à direita da célula.

 [![](images/image7.png "Um StringElement mostra uma legenda no lado esquerdo de uma célula de tabela e o valor de cadeia de caracteres à direita da célula")](images/image7.png#lightbox)

Para usar um `StringElement` como um botão, fornecer um representante.

```csharp
new StringElement (
        "Click me",
        () => { new UIAlertView("Tapped", "String Element Tapped"
, null, "ok", null).Show(); })
```

 [![](images/image8.png "Para usar um StringElement como um botão, fornecer um representante")](images/image8.png#lightbox)

### <a name="styled-string-element"></a>Elemento de estilo de cadeia de caracteres

Um `StyledStringElement` permite que cadeias de caracteres a ser apresentados usando qualquer um dos estilos de célula de tabela interna ou com formatação personalizada.

 [![](images/image9.png "Um StyledStringElement permite que cadeias de caracteres a ser apresentados usando qualquer um dos estilos de célula de tabela interna ou com formatação personalizada")](images/image9.png#lightbox)

O `StyledStringElement` classe derivada de `StringElement`, mas permite que os desenvolvedores personalizar algumas propriedades como a fonte, cor do texto, cor de fundo, o modo de quebra de linha, o número de linhas a serem exibidas, e se um acessório deve ser exibido.

### <a name="multiline-element"></a>Elemento de várias linhas

 [![](images/image10.png "Elemento de várias linhas")](images/image10.png#lightbox)

### <a name="entry-element"></a>Elemento de entrada

O `EntryElement`, como o nome implica, que é usado para obter a entrada do usuário. Ele dá suporte a cadeias de caracteres regulares ou senhas, onde os caracteres são ocultos.

 [![](images/image11.png "O EntryElement é usado para obter a entrada do usuário")](images/image11.png#lightbox)

Ele é inicializado com três valores:

-  A legenda para a entrada que será mostrada ao usuário.
-  Texto do espaço reservado (esse é o texto em cinza que fornece uma dica para o usuário). 
-  O valor do texto.


O espaço reservado e o valor podem ser nulos. No entanto, a legenda é necessária.

A qualquer momento acessar sua propriedade de valor pode recuperar o valor da `EntryElement`.

Além do `KeyboardType` propriedade pode ser definida no momento da criação para o estilo de tipo de teclado desejado para a entrada de dados. Isso pode ser usado para configurar o teclado usando os valores de `UIKeyboardType` como listado abaixo:

-  Numeric
-  Telefone
-  Url
-  Email


### <a name="boolean-element"></a>Elemento booleano

 [![](images/image12.png "Elemento booleano")](images/image12.png#lightbox)

### <a name="checkbox-element"></a>Elemento de caixa de seleção

 [![](images/image13.png "Elemento de caixa de seleção")](images/image13.png#lightbox)

### <a name="radio-element"></a>Elemento de opção

Um `RadioElement` requer um `RadioGroup` será necessário especificar o `RootElement`.

```csharp
mtRoot = new RootElement ("Demos", new RadioGroup("MyGroup", 0))
```

 [![](images/image14.png "Um RadioElement requer um RadioGroup será necessário especificar a RootElement")](images/image14.png#lightbox)

 `RootElements` também são usados para coordenar os elementos de opção. O `RadioElement` membros podem abranger várias seções (por exemplo implementar algo semelhante para o seletor de tom de toque e separado de toques personalizados de toques do sistema). O modo de exibição de resumo mostra o elemento de opção selecionado no momento. Para usar isso, crie o `RootElement` com o construtor de grupo, como este:

```csharp
var root = new RootElement ("Meals", new RadioGroup ("myGroup", 0))
```

O nome do grupo no `RadioGroup` é usado para mostrar o valor selecionado no conteúdo da página (se houver) e o valor, que é zero, nesse caso, é o índice do primeiro item selecionado.

### <a name="badge-element"></a>Elemento de notificação

 [![](images/image15.png "Elemento de notificação")](images/image15.png#lightbox)

### <a name="float-element"></a>Elemento de ponto flutuante

 [![](images/image16.png "Elemento de ponto flutuante")](images/image16.png#lightbox)

### <a name="activity-element"></a>Elemento da atividade

 [![](images/image17.png "Elemento da atividade")](images/image17.png#lightbox)

### <a name="date-element"></a>Elemento Data

 ![](images/image18.png "Elemento Data")

Quando a célula que corresponde a DateElement é selecionada, um seletor de data é apresentado como mostrado abaixo:

 [![](images/image19.png "Quando a célula que corresponde a DateElement é selecionada, um seletor de data é apresentado como mostrado")](images/image19.png#lightbox)

### <a name="time-element"></a>Elemento time

 [![](images/image20.png "Elemento time")](images/image20.png#lightbox)

Quando a célula que corresponde a TimeElement é selecionada, um seletor de tempo é apresentado como mostrado abaixo:

 [![](images/image21.png "Quando a célula que corresponde a TimeElement é selecionada, um seletor de tempo é apresentado como mostrado")](images/image21.png#lightbox)

### <a name="datetime-element"></a>Elemento de data e hora

 [![](images/image22.png "Elemento de data e hora")](images/image22.png#lightbox)

Quando a célula que corresponde a DateTimeElement é selecionada, um seletor de data/hora é apresentado como mostrado abaixo:

 [![](images/image23.png "Quando a célula que corresponde a DateTimeElement é selecionada, um seletor de data/hora é apresentado como mostrado")](images/image23.png#lightbox)

### <a name="html-element"></a>Elemento HTML

 [![](images/image24.png "Elemento HTML")](images/image24.png#lightbox)

O `HTMLElement` exibe o valor da sua `Caption` propriedade da célula de tabela. Quando selecionado, o `Url` atribuído ao elemento é carregado em um `UIWebView` controle conforme mostrado abaixo:

 [![](images/image25.png "Quando selecionada, a Url atribuídos ao elemento é carregado em um controle UIWebView, conforme mostrado abaixo")](images/image25.png#lightbox)

### <a name="message-element"></a>Elemento de mensagem

 [![](images/image26.png "Elemento de mensagem")](images/image26.png#lightbox)

### <a name="load-more-element"></a>Carregar mais de elemento

Use esse elemento para permitir que os usuários carregar mais itens na lista. Você pode personalizar o normal e legendas de carregamento, bem como a cor de fonte e texto.
O `UIActivity` indicador inicia a animação e a legenda de carregamento é exibida quando um usuário toca a célula e, em seguida, o `NSAction` passado para o construtor é executado. Uma vez em seu código no `NSAction` for concluído, o `UIActivity` indicador para a animação e a legenda normal é exibida novamente.

### <a name="uiview-element"></a>Elemento UIView

Além disso, qualquer personalizado `UIView` podem ser exibidos usando o `UIViewElement`.

### <a name="owner-drawn-element"></a>Elemento de desenho proprietário

Esse elemento deve ser uma subclasse porque é uma classe abstrata. Você deve substituir o `Height(RectangleF bounds)` método no qual você deve retornar a altura do elemento, bem como `Draw(RectangleF bounds, CGContext context, UIView view)` em que você deve fazer todas as desenho personalizado dentro dos limites de determinado, usando os parâmetros de contexto e o modo de exibição. Esse elemento faz o trabalho de pesado de subclassificação um `UIView`e colocá-lo na célula a ser retornado, deixando apenas a necessidade de implementar duas substituições simples. Você pode ver um exemplo da implementação melhor no aplicativo de exemplo no `DemoOwnerDrawnElement.cs` arquivo.

Aqui está um exemplo muito simples de implementar a classe:

```csharp
public class SampleOwnerDrawnElement : OwnerDrawnElement
 {
    public SampleOwnerDrawnElement (string text) : base(UITableViewCellStyle.Default, "sampleOwnerDrawnElement")
    {
        this.Text = text;
    }

    public string Text
    {
        get;set;    
    }

    public override void Draw (RectangleF bounds, CGContext context, UIView view)
    {
        UIColor.White.SetFill();
        context.FillRect(bounds);

        UIColor.Black.SetColor();   
        view.DrawString(this.Text, new RectangleF(10, 15, bounds.Width - 20, bounds.Height - 30), UIFont.BoldSystemFontOfSize(14.0f), UILineBreakMode.TailTruncation);
    }

    public override float Height (RectangleF bounds)
    {
        return 44.0f;
    }
 }
```

### <a name="json-element"></a>Elemento JSON

O `JsonElement` é uma subclasse de `RootElement` que estende um `RootElement` para ser capaz de carregar o conteúdo de um filho aninhado de uma url local ou remoto.

O `JsonElement` é um `RootElement` que pode ser instanciado em duas formas. Cria uma versão um `RootElement` que carregará o conteúdo sob demanda. Eles são criados usando o `JsonElement` construtores que usam um argumento extra no final, a url para carregar o conteúdo de:

```csharp
var je = new JsonElement ("Dynamic Data", "http://tirania.org/tmp/demo.json");
```

O outro formulário cria os dados de um arquivo local ou um existente `System.Json.JsonObject` que você já tiver analisado:

```csharp
var je = JsonElement.FromFile ("json.sample");
using (var reader = File.OpenRead ("json.sample"))
    return JsonElement.FromJson (JsonObject.Load (reader) as JsonObject, arg);
```

Para obter mais informações sobre como usar JSON com MT. D, consulte o [passo a passo do JSON elemento](http://docs.xamarin.com/guides/ios/user_interface/monotouch.dialog/json_element_walkthrough) tutorial.

## <a name="other-features"></a>Outros recursos

### <a name="pull-to-refresh-support"></a>Suporte de atualização de pull

 *Pull-para-* *atualizar* é um efeito visual originalmente encontrado no *Tweetie2* aplicativo, que se tornou um efeito comum entre muitos aplicativos.

Para adicionar suporte de pull de atualização automática para as caixas de diálogo, você só precisa fazer duas coisas: ligar um manipulador de eventos para ser notificado quando o usuário obtém os dados e notificar o `DialogViewController` quando os dados foram carregados para retornar ao estado padrão.

Conectar uma notificação é simple; conectar-se apenas para o `RefreshRequested` evento no `DialogViewController`, assim:

```csharp
dvc.RefreshRequested += OnUserRequestedRefresh;
```

Em seguida, em seu método `OnUserRequestedRefresh`, seria fila alguns carregamento de dados, alguns dados de solicitação de rede ou criar um thread para computar os dados. Depois que os dados forem carregados, você deve notificar a `DialogViewController` os novos dados no, e para restaurar o modo de exibição para seu estado padrão, você faz isso chamando `ReloadComplete`:

```csharp
dvc.ReloadComplete ();
```

### <a name="search-support"></a>Suporte para pesquisa

Para dar suporte à pesquisa, defina o `EnableSearch` propriedade em seu `DialogViewController`. Você também pode definir o `SearchPlaceholder` propriedade a ser usado como o texto de marca d'água na barra de pesquisa.

Pesquisando alterará o conteúdo da exibição dos tipos de usuário. Ele procura os campos visíveis e mostra os para o usuário. O `DialogViewController` expõe três métodos para iniciar, encerrar programaticamente ou acionar uma nova operação de filtro nos resultados. Esses métodos estão listados abaixo:

-  `StartSearch`
-  `FinishSearch`
-  `PerformFilter`


O sistema é extensível, portanto você pode alterar esse comportamento, se desejar.

### <a name="background-image-loading"></a>Carregamento de imagem de plano de fundo

MonoTouch.Dialog incorpora o [TweetStation](https://github.com/migueldeicaza/TweetStation) o carregador de imagem do aplicativo. O carregador de imagem pode ser usado para carregar imagens em segundo plano, dá suporte a cache e pode notificar o seu código quando a imagem foi carregada.

Ele também limitará o número de conexões de rede de saída.

O carregador de imagem é implementado no `ImageLoader` classe, tudo o que você precisa fazer é chamada de `DefaultRequestImage` método, você precisará fornecer o Uri para a imagem que você deseja carregar, bem como uma instância do `IImageUpdated` interface que será invocado quando a imagem ha s foi carregado.

Por exemplo de código a seguir carrega uma imagem de uma Url em um `BadgeElement`:

```csharp
string uriString = "http://some-server.com/some image url";

var rootElement = new RootElement("Image Loader") {
        new Section(){
                new BadgeElement( ImageLoader.DefaultRequestImage( new Uri(uriString), this), "Xamarin")
        }
};
```

A classe ImageLoader expõe um método de limpeza que você pode chamar quando você deseja liberar todas as imagens que estão atualmente armazenados em cache na memória. O código atual tem um cache de 50 imagens. Se você deseja usar um tamanho de cache diferente (por exemplo, se estiver esperando que as imagens sejam muito grandes que 50 imagens seria muito), basta criar instâncias de ImageLoader e passe o número de imagens a ser mantido no cache.

## <a name="using-linq-to-create-element-hierarchy"></a>Usando o LINQ para criar a hierarquia de elementos

Por meio do uso inteligente da sintaxe de inicialização do LINQ e do #, LINQ pode ser usado para criar uma hierarquia de elemento. Por exemplo, o código a seguir cria uma tela de alguns conjuntos de cadeia de caracteres e identificadores de célula seleção por meio de uma função anônima que é passada para cada `StringElement`:

```csharp
var rootElement = new RootElement ("LINQ root element") {
from x in new string [] { "one", "two", "three" }
select new Section (x) {
from y in "Hello:World".Split (':')
select (Element) new StringElement (y,
delegate { Debug.WriteLine("cell tapped"); })
}
};
```

Facilmente poderiam ser combinado com um repositório de dados XML ou dados de um banco de dados para criar aplicativos complexos quase inteiramente de dados.

## <a name="extending-mtd"></a>Estendendo MT. D

### <a name="creating-custom-elements"></a>Criando elementos personalizados

Você pode criar seu próprio elemento herdando de em um elemento existente ou derivado da classe raiz do elemento.

Para criar seu próprio elemento, você desejará substituir os métodos a seguir:

```csharp
// To release any heavy resources that you might have
    void Dispose (bool disposing);

    // To retrieve the UITableViewCell for your element
    // you would need to prepare the cell to be reused, in the
    // same way that UITableView expects reusable cells to work
    UITableViewCell GetCell (UITableView tv)

    // To retrieve a "summary" that can be used with
    // a root element to render a summary one level up.  
    string Summary ()
    // To detect when the user has tapped on the cell
    void Selected (DialogViewController dvc, UITableView tableView, NSIndexPath path)
    // If you support search, to probe if the cell matches the user input
    bool Matches (string text)
```

Se o elemento pode ter um tamanho variável, você precisa implementar o `IElementSizing` interface, que contém um método:

```csharp
// Returns the height for the cell at indexPath.Section, indexPath.Row
    float GetHeight (UITableView tableView, NSIndexPath indexPath);
```

Se você estiver planejando implementar sua `GetCell` método chamando `base.GetCell(tv)` e personalizando a célula retornada, você precisa também substituir o `CellKey` propriedade para retornar uma chave que será exclusiva para o elemento, como este:

```csharp
static NSString MyKey = new NSString ("MyKey");
    protected override NSString CellKey {
        get {
            return MyKey;
        }
    }
```

Isso funciona para a maioria dos elementos, mas não para o `StringElement` e `StyledStringElement` como aquelas usam seu próprio conjunto de chaves para vários cenários de renderização. Você precisa replicar o código nessas classes.

### <a name="dialogviewcontrollers-dvcs"></a>DialogViewControllers (DVCs)

A reflexão e a API de elementos de usam o mesmo `DialogViewController`. Às vezes, convém personalizar a aparência do modo de exibição ou talvez você queira usar alguns recursos da `UITableViewController` que vão além de criação básica de interfaces do usuário.

O `DialogViewController` é simplesmente uma subclasse do `UITableViewController` e você pode personalizá-lo da mesma maneira que você faz para personalizar um `UITableViewController`.

Por exemplo, se você quiser alterar o estilo de lista para ser `Grouped` ou `Plain`, você pode definir este valor alterando a propriedade quando você criar o controlador, como este:

```csharp
var myController = new DialogViewController (root, true){
        Style = UITableViewStyle.Grouped;
    }
```

Para obter mais avançado personalizações do `DialogViewController`, como definir o plano de fundo, você faria subclasse-lo e substituir o próprio métodos, como mostrado no exemplo a seguir:

```csharp
class SpiffyDialogViewController : DialogViewController {
    UIImage image;

    public SpiffyDialogViewController (RootElement root, bool pushing, UIImage image) 
        : base (root, pushing) 
    {
        this.image = image;
    }

    public override LoadView ()
    {
        base.LoadView ();
        var color = UIColor.FromPatternImage(image);
        TableView.BackgroundColor = UIColor.Clear;
        ParentViewController.View.BackgroundColor = color;
    }
}
```

Outro ponto de personalização é dos seguintes métodos virtuais no `DialogViewController`:

```csharp
public override Source CreateSizingSource (bool unevenRows)
```

Esse método deve retornar uma subclasse de `DialogViewController.Source` para casos em que as células são uniformemente dimensionadas ou uma subclasse de `DialogViewController.SizingSource` se as células são desiguais.

Você pode usar essa substituição é para capturar qualquer um do `UITableViewSource` métodos. Por exemplo, [TweetStation](https://github.com/migueldeicaza/TweetStation) usa isso para controlar quando o usuário tiver rolado para a parte superior e atualizar adequadamente o número de tweets não lidas.

## <a name="validation"></a>Validação

Elementos não fornecem validação si como os modelos que são adequados para páginas da web e aplicativos de área de trabalho não são mapeados diretamente para o modelo de interação do iPhone.

Se você quiser fazer a validação de dados, você deve fazer isso quando o usuário aciona uma ação com os dados inseridos. Por exemplo um <span class="ui">feito</span> ou <span class="ui">próximo</span> botão na barra de ferramentas superior ou alguns `StringElement` usado como um botão para ir para o próximo estágio.

Isso é onde você deve executar a validação de entrada básico, e talvez mais complicado validação como verificar a validade de uma combinação de senha do usuário com um servidor.

Como você pode notificar o usuário de erro é específica do aplicativo. Você poderia pop-up um `UIAlertView` ou mostrar uma dica.

## <a name="summary"></a>Resumo

Este artigo coberto muitas informações sobre MonoTouch.Dialog. Ele discutiu os fundamentos de como MT. D funciona e abordados os vários componentes que compõem MT. D. Ele também mostrou a ampla variedade de elementos e personalizações de tabela com suporte MT. D e discutido como MT. D pode ser estendido com elementos personalizados. Além dele explicado o suporte a JSON no MT. D que permite criar elementos dinamicamente do JSON.


## <a name="related-links"></a>Links relacionados

- [Screencast - Miguel de Icaza cria uma tela de logon do iOS com MonoTouch.Dialog](http://youtu.be/3butqB1EG0c)
- [Screencast - criar facilmente iOS interfaces de usuário com MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [Passo a passo: criar um aplicativo usando a API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Passo a passo: criar um aplicativo usando a API de reflexão](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Passo a passo: usar um elemento JSON para criar uma interface do usuário](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [MonoTouch.Dialog JSON Markup](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Caixa de diálogo MonoTouch no Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Referência de classe UITableViewController](http://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referência de classe UINavigationController](http://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
