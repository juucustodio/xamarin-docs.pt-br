---
title: Introdução à MonoTouch. caixa de diálogo para Xamarin. iOS
description: Este documento descreve o MonoTouch. Dialog (MT. D), uma estrutura para o desenvolvimento rápido e declarativo da interface do usuário com o Xamarin. iOS. Ele aborda como usar as APIs MonoTouch. Dialog para criar uma interface em código ou JSON e usar recursos como pull-to-Refresh, pesquisa, carregamento de imagem em segundo plano e muito mais.
ms.prod: xamarin
ms.assetid: 52A35B24-C23B-8461-A8FF-5928A2128FB0
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: davidortinau
ms.author: daortin
ms.openlocfilehash: c57a6d02488272934ea77714b07c0328ac501d26
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431217"
---
# <a name="introduction-to-monotouchdialog-for-xamarinios"></a>Introdução à MonoTouch. caixa de diálogo para Xamarin. iOS

MonoTouch. dialog, conhecido como MT. D para abreviar, é um kit de ferramentas de desenvolvimento de interface do usuário rápido que permite aos desenvolvedores criar telas e navegação de aplicativos usando informações, em vez do tédio de criar controladores de exibição, tabelas, etc. Assim, ele fornece uma simplificação significativa do desenvolvimento da interface do usuário e da redução de código. Por exemplo, considere a seguinte captura de tela:

 [![Por exemplo, considere esta captura de tela](images/image1.png)](images/image1.png#lightbox)

O código a seguir foi usado para definir essa tela inteira:

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

Ao trabalhar com tabelas no iOS, geralmente há uma tonelada de código redundante.
Por exemplo, toda vez que uma tabela é necessária, uma fonte de dados é necessária para popular essa tabela. Em um aplicativo que tem duas telas baseadas em tabela conectadas por meio de um controlador de navegação, cada tela compartilha muito o mesmo código.

MT. A D simplifica isso encapsulando todo o código em uma API genérica para a criação de tabelas. Em seguida, ele fornece uma abstração sobre essa API que permite uma sintaxe de vinculação de objeto declarativo que a torna ainda mais fácil. Assim, há duas APIs disponíveis em MT. 3D

- **API de elementos de nível baixo** – a  *API de elementos* é baseada na criação de uma árvore hierárquica de elementos que representam telas e seus componentes. A API dos elementos oferece aos desenvolvedores mais flexibilidade e controle na criação de UIs. Além disso, a API dos elementos tem suporte avançado para definição declarativa via JSON, que permite uma declaração incrivelmente rápida, bem como a geração de interface do usuário dinâmica de um servidor. 
- **API de reflexão de alto nível** – também conhecida como *Binding**API* de associação, na qual as classes são anotadas com dicas de interface do usuário e, em seguida, Mt. D cria automaticamente telas com base nos objetos e fornece uma associação entre o que é exibido (e, opcionalmente, editado) na tela e o backup do objeto subjacente.   O exemplo acima ilustrou o uso da API de reflexão. Essa API não fornece o controle refinado que a API dos elementos faz, mas reduz ainda mais a complexidade ao criar automaticamente a hierarquia de elementos com base nos atributos de classe. 

MT. O D vem embalado de um grande conjunto de elementos de interface do usuário internos para a criação de tela, mas também reconhece a necessidade de elementos personalizados e layouts de tela avançados. Dessa forma, a extensibilidade é uma inclusas em destaque de primeira classe na API. Os desenvolvedores podem estender os elementos existentes ou criar novos e, em seguida, integrar-se perfeitamente.

Além disso, o MT. A D tem vários recursos comuns do iOS UX incorporados, como suporte de "pull para atualização", carregamento de imagem assíncrona e suporte de pesquisa.

Este artigo conseguirá uma visão abrangente do trabalho com o MT. D, incluindo:

- **Mt. Componentes D** – isso se concentrará na compreensão das classes que compõem o Mt. D para permitir a rápida rapidez. 
- **Referência de elementos** – uma lista abrangente dos elementos internos de Mt. D. 
- **Uso avançado** – isso abrange recursos avançados, como pull-to-Refresh, pesquisa, carregamento de imagem em segundo plano, uso do LINQ para criar hierarquias de elemento e criar elementos personalizados, células e controladores para uso com MT. D. 

## <a name="setting-up-mtd"></a>Configurando o MT. 3D

MT. A D é distribuída com o Xamarin. iOS. Para usá-lo, clique com o botão direito do mouse no nó **referências** de um projeto Xamarin. Ios no Visual Studio 2017 ou Visual Studio para Mac e adicione uma referência ao assembly **MonoTouch. caixa de diálogo-1** . Em seguida, adicione `using MonoTouch.Dialog` instruções em seu código-fonte, conforme necessário.

## <a name="understanding-the-pieces-of-mtd"></a>Entendendo as partes do MT. 3D

Mesmo ao usar a API de reflexão, MT. D cria uma hierarquia de elementos nos bastidores, assim como se ela fosse criada por meio da API Elements diretamente. Além disso, o suporte a JSON mencionado na seção anterior também cria elementos. Por esse motivo, é importante ter uma compreensão básica dos componentes constituintes de MT. D.

MT. O D compila as telas usando as quatro partes a seguir:

- **DialogViewController**
- **RootElement**
- **Seção**
- **Element**

### <a name="dialogviewcontroller"></a>DialogViewController

Um *DialogViewController*, ou *DVC* de curto, herda de `UITableViewController` e, portanto, representa uma tela com uma tabela. DVCs pode ser enviado por push para um controlador de navegação, assim como um UITableViewController regular.

### <a name="rootelement"></a>RootElement

Um *RootElement* é o contêiner de nível superior para os itens que entram em um DVC. Ele contém seções que podem, então, conter elementos. RootElements não são renderizados; em vez disso, eles são simplesmente contêineres para o que realmente é renderizado. Um RootElement é atribuído a um DVC e, em seguida, o DVC renderiza seus filhos.

### <a name="section"></a>Seção

Uma seção é um grupo de células em uma tabela. Como com uma seção de tabela normal, opcionalmente, ele pode ter um cabeçalho e um rodapé que podem ser texto ou até mesmo exibições personalizadas, como na seguinte captura de tela:

 [![Como com uma seção de tabela normal, opcionalmente, ele pode ter um cabeçalho e um rodapé que podem ser texto ou até mesmo exibições personalizadas, como nesta captura de tela](images/image2.png)](images/image2.png#lightbox)

### <a name="element"></a>Elemento

Um elemento representa uma célula real na tabela. MT. A D vem empacotada com uma ampla variedade de elementos que representam diferentes tipos de dados ou entradas diferentes. Por exemplo, as capturas de tela a seguir ilustram alguns dos elementos disponíveis:

 [![Por exemplo, essas capturas de tela ilustram alguns dos elementos disponíveis](images/image3.png)](images/image3.png#lightbox)

## <a name="more-on-sections-and-rootelements"></a>Mais em seções e RootElements

Agora, vamos discutir RootElements e seções mais detalhadamente.

### <a name="rootelements"></a>RootElements

Pelo menos um RootElement é necessário para iniciar o processo de MonoTouch. Dialog.

Se um RootElement for inicializado com um valor de seção/elemento, esse valor será usado para localizar um elemento filho que fornecerá um resumo da configuração, que é renderizado no lado direito da exibição. Por exemplo, a captura de tela abaixo mostra uma tabela à esquerda com uma célula que contém o título do painel de detalhes à direita, "sobremesa", junto com o valor do deserto selecionado.

 [ ![ Esta captura de tela mostra uma tabela à esquerda com uma célula que contém o título do painel de detalhes à direita, sobrecopiando, juntamente com o valor do deserto selecionado](images/image4.png)](images/image4.png#lightbox) , [ ![ esta captura abaixo mostra uma tabela à esquerda com uma célula que contém o título da tela de detalhes à direita, sobremesando, junto com o valor do deserto selecionado](images/image5.png)](images/image5.png#lightbox)

Elementos raiz também podem ser usados dentro de seções para disparar o carregamento de uma nova página de configuração aninhada, como mostrado acima. Quando usado nesse modo, a legenda fornecida é usada enquanto renderizado dentro de uma seção e também é usada como o título da subpágina. Por exemplo:

```csharp
var root = new RootElement ("Meals") {
    new Section ("Dinner") {
        new RootElement ("Dessert", new RadioGroup ("dessert", 2)) {
            new Section () {
                new RadioElement ("Ice Cream", "dessert"),
                new RadioElement ("Milkshake", "dessert"),
                new RadioElement ("Chocolate Cake", "dessert")
            }
        }
    }
};
```

No exemplo acima, quando o usuário toca em "sobremesar", o MonoTouch. Dialog criará uma nova página e navegará até ela com a raiz sendo "sobremesada" e tendo um grupo de rádio com três valores.

Nesse exemplo específico, o grupo de rádio selecionará "bolo de chocolate" na seção "sobremesa", pois passamos o valor "2" para o grupo de rádios. Isso significa selecionar o terceiro item na lista (zero-index).

Chamar o método Add ou usar a sintaxe do inicializador C# 4 adiciona seções.
Os métodos INSERT são fornecidos para inserir seções com uma animação.

Se você criar o RootElement com uma instância de grupo (em vez de um Radio), o valor de resumo do RootElement quando exibido em uma seção será a contagem cumulativa de todos os BooleanElements e CheckboxElements que têm a mesma chave que o grupo. valor de chave.

### <a name="sections"></a>Seções

As seções são usadas para agrupar elementos na tela e são os únicos filhos diretos válidos do RootElement. As seções podem conter qualquer um dos elementos padrão, incluindo New RootElements.

O RootElements inserido em uma seção é usado para navegar para um novo nível mais profundo.

As seções podem ter cabeçalhos e rodapés como cadeias de caracteres ou como UIViews.
Normalmente, você usará apenas as cadeias de caracteres, mas para criar UIs personalizadas, será possível usar qualquer UIView como o cabeçalho ou o rodapé. Você pode usar uma cadeia de caracteres para criá-las da seguinte maneira:

```csharp
var section = new Section ("Header", "Footer");
```

Para usar exibições, basta passar as exibições para o construtor:

```csharp
var header = new UIImageView (Image.FromFile ("sample.png"));
var section = new Section (header);
```

### <a name="getting-notified"></a>Sendo notificado

#### <a name="handling-nsaction"></a>Manipulando NSAction

MT. D mostra um `NSAction` como um delegado para lidar com retornos de chamada.
Por exemplo, digamos que você deseja manipular um evento de toque para uma célula de tabela criada por MT. D. Ao criar um elemento com MT. D, basta fornecer uma função de retorno de chamada, conforme mostrado abaixo:

```csharp
new Section () {
    new StringElement ("Demo Callback", delegate { Console.WriteLine ("Handled"); })
}
```

#### <a name="retrieving-element-value"></a>Recuperando valor do elemento

Combinado com a `Element.Value` propriedade, o retorno de chamada pode recuperar o valor definido em outros elementos. Por exemplo, considere o seguinte código:

```csharp
var element = new EntryElement (task.Name, "Enter task description", task.Description);
                
var taskElement = new RootElement (task.Name) {
    new Section () { element },
    new Section () { new DateElement ("Due Date", task.DueDate) },
    new Section ("Demo Retrieving Element Value") {
        new StringElement ("Output Task Description", delegate { Console.WriteLine (element.Value); })
    }
};
```

Esse código cria uma interface do usuário, conforme mostrado abaixo. Para obter uma explicação completa deste exemplo, consulte o tutorial sobre a [API dos elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md) .

 [![Combinado com a propriedade Element. Value, o retorno de chamada pode recuperar o valor definido em outros elementos](images/image6.png)](images/image6.png#lightbox)

Quando o usuário pressiona a célula de tabela inferior, o código na função anônima é executado, gravando o valor da `element` instância para o painel de **saída do aplicativo** em Visual Studio para Mac.

## <a name="built-in-elements"></a>Elementos internos

MT. O D vem com uma série de itens de célula de tabela internos conhecidos como elementos.
Esses elementos são usados para exibir uma variedade de tipos diferentes em células de tabela, como cadeias de caracteres, floats, datas e até mesmo imagens, para citar apenas alguns. Cada elemento cuida da exibição do tipo de dados adequadamente. Por exemplo, um elemento booliano exibirá uma opção para alternar seu valor. Da mesma forma, um elemento float exibirá um controle deslizante para alterar o valor float.

Há ainda elementos mais complexos para dar suporte a tipos de dados mais avançados, como imagens e HTML. Por exemplo, um elemento HTML, que abrirá um UIWebView para carregar uma página da Web quando selecionado, exibirá uma legenda na célula da tabela.

### <a name="working-with-element-values"></a>Trabalhando com valores de elemento

Os elementos usados para capturar a entrada do usuário expõem uma `Value` propriedade pública que contém o valor atual do elemento a qualquer momento. Ele é atualizado automaticamente à medida que o usuário usa o aplicativo.

Esse é o comportamento para todos os elementos que fazem parte da caixa de diálogo MonoTouch., mas não é necessário para elementos criados pelo usuário.

### <a name="string-element"></a>Elemento String

Um `StringElement` mostra uma legenda no lado esquerdo de uma célula de tabela e o valor da cadeia de caracteres no lado direito da célula.

 [![Uma Stringelement mostra uma legenda no lado esquerdo de uma célula de tabela e o valor da cadeia de caracteres no lado direito da célula](images/image7.png)](images/image7.png#lightbox)

Para usar um `StringElement` como botão, forneça um delegado.

```csharp
new StringElement ("Click me", () => { 
    new UIAlertView("Tapped", "String Element Tapped", null, "ok", null).Show();
});
```

 [![Para usar uma Stringelement como um botão, forneça um delegado](images/image8.png)](images/image8.png#lightbox)

### <a name="styled-string-element"></a>Elemento de cadeia de caracteres com estilo

Um `StyledStringElement` permite que as cadeias de caracteres sejam apresentadas usando estilos de célula de tabela internos ou com formatação personalizada.

 [![Um StyledStringElement permite que as cadeias de caracteres sejam apresentadas usando estilos de célula de tabela internos ou com formatação personalizada](images/image9.png)](images/image9.png#lightbox)

A `StyledStringElement` classe deriva de `StringElement` , mas permite que os desenvolvedores personalizem algumas propriedades como a fonte, a cor do texto, a cor da célula do plano de fundo, o modo de quebra de linha, o número de linhas a serem exibidas e se um acessório deve ser exibido.

### <a name="multiline-element"></a>Elemento Multiline

 [![Elemento Multiline](images/image10.png)](images/image10.png#lightbox)

### <a name="entry-element"></a>Elemento entry

O `EntryElement` , como o nome indica, é usado para obter a entrada do usuário. Ele dá suporte a cadeias de caracteres ou senhas regulares, em que os personagens ficam ocultos.

 [![O Entryelement é usado para obter a entrada do usuário](images/image11.png)](images/image11.png#lightbox)

Ele é inicializado com três valores:

- A legenda da entrada que será mostrada ao usuário.
- Texto do espaço reservado (esse é o texto esmaecido que fornece uma dica para o usuário). 
- O valor do texto.

O espaço reservado e o valor podem ser nulos. No entanto, a legenda é necessária.

A qualquer momento, acessar sua propriedade Value pode recuperar o valor de `EntryElement` .

Além disso `KeyboardType` , a propriedade pode ser definida no momento da criação para o estilo de tipo de teclado desejado para a entrada de dados. Isso pode ser usado para configurar o teclado usando os valores de `UIKeyboardType` conforme listado abaixo:

- Numérico
- Telefone
- Url
- Email

### <a name="boolean-element"></a>Elemento booliano

 [![Elemento booliano](images/image12.png)](images/image12.png#lightbox)

### <a name="checkbox-element"></a>Elemento CheckBox

 [![Elemento CheckBox](images/image13.png)](images/image13.png#lightbox)

### <a name="radio-element"></a>Elemento Radio

Um `RadioElement` requer que um seja `RadioGroup` especificado no `RootElement` .

```csharp
mtRoot = new RootElement ("Demos", new RadioGroup("MyGroup", 0));
```

 [![Um Radioelement requer que um Radiogroup seja especificado no RootElement](images/image14.png)](images/image14.png#lightbox)

 `RootElements` também são usados para coordenar elementos de rádio. Os `RadioElement` Membros podem abranger várias seções (por exemplo, para implementar algo semelhante ao seletor de Tom de toque e separar tons de toque personalizados de toques do sistema). A exibição de resumo mostrará o elemento Radio selecionado no momento. Para usar isso, crie o `RootElement` com o construtor do grupo, da seguinte maneira:

```csharp
var root = new RootElement ("Meals", new RadioGroup ("myGroup", 0));
```

O nome do grupo no `RadioGroup` é usado para mostrar o valor selecionado na página recipiente (se houver) e o valor, que é zero nesse caso, é o índice do primeiro item selecionado.

### <a name="badge-element"></a>Elemento de notificação

 [![Elemento de notificação](images/image15.png)](images/image15.png#lightbox)

### <a name="float-element"></a>Elemento float

 [![Elemento float](images/image16.png)](images/image16.png#lightbox)

### <a name="activity-element"></a>Elemento Activity

 [![Elemento Activity](images/image17.png)](images/image17.png#lightbox)

### <a name="date-element"></a>Elemento Date

 ![Elemento Date](images/image18.png)

Quando a célula correspondente ao Dateelement é selecionada, um seletor de data é apresentado como mostrado abaixo:

 [![Quando a célula correspondente ao Dateelement é selecionada, um seletor de data é apresentado como mostrado](images/image19.png)](images/image19.png#lightbox)

### <a name="time-element"></a>Elemento Time

 [![Elemento time](images/image20.png)](images/image20.png#lightbox)

Quando a célula correspondente ao Timeelement é selecionada, um seletor de hora é apresentado como mostrado abaixo:

 [![Quando a célula correspondente ao Timeelement é selecionada, um seletor de tempo é apresentado como mostrado](images/image21.png)](images/image21.png#lightbox)

### <a name="datetime-element"></a>Elemento DateTime

 [![Elemento DateTime](images/image22.png)](images/image22.png#lightbox)

Quando a célula correspondente ao DateTimeelement é selecionada, um seletor de data e hora é apresentado como mostrado abaixo:

 [![Quando a célula correspondente ao DateTimeelement é selecionada, um seletor de data e hora é apresentado conforme mostrado](images/image23.png)](images/image23.png#lightbox)

### <a name="html-element"></a>Elemento HTML

 [![Elemento HTML](images/image24.png)](images/image24.png#lightbox)

O `HTMLElement` exibe o valor de sua `Caption` Propriedade na célula da tabela. Quando selecionado, o `Url` atribuído ao elemento é carregado em um `UIWebView` controle, conforme mostrado abaixo:

 [![Quando selecionado, a URL atribuída ao elemento é carregada em um controle UIWebView, conforme mostrado abaixo](images/image25.png)](images/image25.png#lightbox)

### <a name="message-element"></a>Elemento Message

 [![Elemento Message](images/image26.png)](images/image26.png#lightbox)

### <a name="load-more-element"></a>Carregar mais elemento

Use este elemento para permitir que os usuários carreguem mais itens na lista. Você pode personalizar as legendas normal e de carregamento, bem como a fonte e a cor do texto.
O `UIActivity` indicador começa a animação e a legenda de carregamento é exibida quando um usuário toca na célula e, em seguida, o `NSAction` passado para o construtor é executado. Depois que o código no `NSAction` for concluído, o `UIActivity` indicador parará de animação e a legenda normal será exibida novamente.

### <a name="uiview-element"></a>Elemento UIView

Além disso, qualquer personalizado `UIView` pode ser exibido usando o `UIViewElement` .

### <a name="owner-drawn-element"></a>Elemento desenhado pelo proprietário

Esse elemento deve ser subclasse, pois é uma classe abstrata. Você deve substituir o `Height(RectangleF bounds)` método no qual deve retornar a altura do elemento, bem como `Draw(RectangleF bounds, CGContext context, UIView view)` no qual você deve fazer todo o desenho personalizado dentro dos limites fornecidos, usando os parâmetros context e View. Esse elemento faz o trabalho pesado de subclasse a `UIView` e o coloca na célula a ser retornada, deixando que você só precise implementar duas substituições simples. Você pode ver uma implementação de exemplo melhor no aplicativo de exemplo no `DemoOwnerDrawnElement.cs` arquivo.

Veja um exemplo muito simples de implementação da classe:

```csharp
public class SampleOwnerDrawnElement : OwnerDrawnElement
{
    public SampleOwnerDrawnElement (string text) : base(UITableViewCellStyle.Default, "sampleOwnerDrawnElement")
    {
        this.Text = text;
    }

    public string Text { get; set; }

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

O `JsonElement` é uma subclasse de `RootElement` que estende um `RootElement` para poder carregar o conteúdo de filho aninhado de uma URL local ou remota.

O `JsonElement` é um `RootElement` que pode ser instanciado em duas formas. Uma versão cria um `RootElement` que carregará o conteúdo sob demanda. Eles são criados usando os `JsonElement` construtores que usam um argumento extra no final, a URL para carregar o conteúdo:

```csharp
var je = new JsonElement ("Dynamic Data", "https://tirania.org/tmp/demo.json");
```

O outro formulário cria os dados de um arquivo local ou de um existente `System.Json.JsonObject` que já foi analisado:

```csharp
var je = JsonElement.FromFile ("json.sample");
using (var reader = File.OpenRead ("json.sample"))
    return JsonElement.FromJson (JsonObject.Load (reader) as JsonObject, arg);
```

Para obter mais informações sobre como usar JSON com MT. D, consulte o tutorial sobre o [elemento JSON](./json-element-walkthrough.md) .

## <a name="other-features"></a>Outros recursos

### <a name="pull-to-refresh-support"></a>Suporte de pull para atualização

 O *pull para* *atualização* é um efeito visual originalmente encontrado no aplicativo *Tweetie2* , que se tornou um efeito popular entre muitos aplicativos.

Para adicionar suporte automático de pull para atualização às suas caixas de diálogo, você só precisa fazer duas coisas: conectar um manipulador de eventos para ser notificado quando o usuário efetuar pull dos dados e notificar `DialogViewController` quando os dados tiverem sido carregados para voltar ao estado padrão.

A conexão de uma notificação é simples; Basta conectar-se ao `RefreshRequested` evento no `DialogViewController` , da seguinte maneira:

```csharp
dvc.RefreshRequested += OnUserRequestedRefresh;
```

Em seguida, em seu método `OnUserRequestedRefresh` , você colocaria um carregamento de dados em fila, solicitaria alguns dados da rede ou giraria um thread para computar os dados. Depois que os dados tiverem sido carregados, você deverá notificar a `DialogViewController` que os novos dados estão e restaurar a exibição para seu estado padrão, fazendo isso chamando `ReloadComplete` :

```csharp
dvc.ReloadComplete ();
```

### <a name="search-support"></a>Pesquisar suporte

Para dar suporte à pesquisa, defina a `EnableSearch` propriedade no seu `DialogViewController` . Você também pode definir a `SearchPlaceholder` propriedade para usar como o texto de marca-d ' água na barra de pesquisa.

A pesquisa alterará o conteúdo da exibição conforme o usuário digitar. Ele pesquisa os campos visíveis e os mostra para o usuário. O `DialogViewController` expõe três métodos para iniciar, encerrar ou disparar de forma programática uma nova operação de filtro nos resultados. Esses métodos estão listados abaixo:

- `StartSearch`
- `FinishSearch`
- `PerformFilter`

O sistema é extensível, portanto, você pode alterar esse comportamento se desejar.

### <a name="background-image-loading"></a>Carregamento de imagem em segundo plano

O MonoTouch. Dialog incorpora o carregador de imagem do aplicativo [TweetStation](https://github.com/migueldeicaza/TweetStation) . Esse carregador de imagem pode ser usado para carregar imagens em segundo plano, dá suporte ao Caching e pode notificar seu código quando a imagem for carregada.

Ele também limitará o número de conexões de rede de saída.

O carregador de imagem é implementado na `ImageLoader` classe, tudo o que você precisa fazer é chamar o `DefaultRequestImage` método, será necessário fornecer o URI para a imagem que você deseja carregar, bem como uma instância da `IImageUpdated` interface que será invocada quando a imagem for carregada.

Por exemplo, o código a seguir carrega uma imagem de uma URL em um `BadgeElement` :

```csharp
string uriString = "http://some-server.com/some image url";

var rootElement = new RootElement("Image Loader") {
    new Section() {
        new BadgeElement( ImageLoader.DefaultRequestImage( new Uri(uriString), this), "Xamarin")
    }
};
```

A classe ImageLoader expõe um método Purge que você pode chamar quando deseja liberar todas as imagens que estão armazenadas em cache na memória no momento. O código atual tem um cache de 50 imagens. Se você quiser usar um tamanho de cache diferente (por exemplo, se você estiver esperando que as imagens sejam muito grandes que 50 imagens seriam muito longas), você pode apenas criar instâncias de ImageLoader e passar o número de imagens que deseja manter no cache.

## <a name="using-linq-to-create-element-hierarchy"></a>Usando o LINQ para criar a hierarquia de elementos

Por meio do uso inteligente da sintaxe de inicialização do LINQ e do C#, o LINQ pode ser usado para criar uma hierarquia de elementos. Por exemplo, o código a seguir cria uma tela de algumas matrizes de cadeia de caracteres e manipula a seleção de célula por meio de uma função anônima que é passada em cada uma delas `StringElement` :

```csharp
var rootElement = new RootElement ("LINQ root element") {
    from x in new string [] { "one", "two", "three" }
    select new Section (x) {
        from y in "Hello:World".Split (':')
        select (Element) new StringElement (y, delegate { Debug.WriteLine("cell tapped"); })
    }
};
```

Isso poderia ser facilmente combinado com um armazenamento de dados XML ou dados de um banco de dado para criar aplicativos complexos quase inteiramente dos dados.

## <a name="extending-mtd"></a>Estendendo o MT. 3D

### <a name="creating-custom-elements"></a>Criando elementos personalizados

Você pode criar seu próprio elemento herdando de um elemento existente ou derivando do elemento da classe raiz.

Para criar seu próprio elemento, você desejará substituir os seguintes métodos:

```csharp
// To release any heavy resources that you might have
void Dispose (bool disposing);

// To retrieve the UITableViewCell for your element
// you would need to prepare the cell to be reused, in the
// same way that UITableView expects reusable cells to work
UITableViewCell GetCell (UITableView tv);

// To retrieve a "summary" that can be used with
// a root element to render a summary one level up.  
string Summary ();

// To detect when the user has tapped on the cell
void Selected (DialogViewController dvc, UITableView tableView, NSIndexPath path);

// If you support search, to probe if the cell matches the user input
bool Matches (string text);
```

Se o elemento puder ter um tamanho variável, você precisará implementar a `IElementSizing` interface, que contém um método:

```csharp
// Returns the height for the cell at indexPath.Section, indexPath.Row
float GetHeight (UITableView tableView, NSIndexPath indexPath);
```

Se você estiver planejando implementar seu `GetCell` método chamando `base.GetCell(tv)` e personalizando a célula retornada, você também precisará substituir a `CellKey` propriedade para retornar uma chave que será exclusiva do seu elemento, da seguinte maneira:

```csharp
static NSString MyKey = new NSString ("MyKey");
protected override NSString CellKey {
    get {
        return MyKey;
    }
}
```

Isso funciona para a maioria dos elementos, mas não para o `StringElement` e `StyledStringElement` como eles usam seu próprio conjunto de chaves para vários cenários de renderização. Você precisaria replicar o código nessas classes.

### <a name="dialogviewcontrollers-dvcs"></a>DialogViewControllers (DVCs)

A API de reflexão e os elementos usam o mesmo `DialogViewController` . Às vezes, você desejará personalizar a aparência da exibição ou talvez queira usar alguns recursos do `UITableViewController` que vão além da criação básica de interfaces do uso.

O `DialogViewController` é meramente uma subclasse do `UITableViewController` e você pode personalizá-lo da mesma maneira que personalizaria um `UITableViewController` .

Por exemplo, se você quisesse alterar o estilo da lista para ser `Grouped` ou `Plain` , poderá definir esse valor alterando a propriedade ao criar o controlador, da seguinte forma:

```csharp
var myController = new DialogViewController (root, true) {
    Style = UITableViewStyle.Grouped;
}
```

Para obter personalizações mais avançadas do `DialogViewController` , como definir seu plano de fundo, você iria criar uma subclasse e substituir os métodos apropriados, conforme mostrado no exemplo abaixo:

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

Outro ponto de personalização são os seguintes métodos virtuais no `DialogViewController` :

```csharp
public override Source CreateSizingSource (bool unevenRows)
```

Esse método deve retornar uma subclasse de `DialogViewController.Source` para casos em que as células são igualmente dimensionadas ou uma subclasse de `DialogViewController.SizingSource` se as células forem desiguais.

Você pode usar essa substituição para capturar qualquer um dos `UITableViewSource` métodos. Por exemplo, [TweetStation](https://github.com/migueldeicaza/TweetStation) usa isso para rastrear quando o usuário rola para a parte superior e atualiza adequadamente o número de tweets não lidos.

## <a name="validation"></a>Validação

Os elementos não fornecem validação por conta própria, pois os modelos adequados para páginas da Web e aplicativos de área de trabalho não são mapeados diretamente para o modelo de interação do iPhone.

Se você quiser fazer a validação de dados, deverá fazer isso quando o usuário disparar uma ação com os dados inseridos. Por exemplo, um botão **concluído** ou **próximo** na barra de ferramentas superior ou alguns `StringElement` usados como um botão para ir para o próximo estágio.

É aí que você executaria a validação de entrada básica e, talvez, uma validação mais complicada, como verificar a validade de uma combinação de usuário/senha com um servidor.

A forma como você notifica o usuário sobre um erro é específico do aplicativo. Você pode `UIAlertView` Exibir ou mostrar uma dica.

## <a name="summary"></a>Resumo

Este artigo abordou muitas informações sobre o MonoTouch. Dialog. Ele abordou os conceitos básicos do como o MT. D trabalha e abordou os vários componentes que compõem MT. D. Ele também mostrou a ampla matriz de elementos e personalizações de tabela com suporte do MT. D e discutiu como o MT. A D pode ser estendida com elementos personalizados. Além disso, ele explicou o suporte a JSON em MT. D que permite criar elementos dinamicamente do JSON.

## <a name="related-links"></a>Links Relacionados

- [Passo a passo: criar um aplicativo usando a API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Passo a passo: criar um aplicativo usando a API de reflexão](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Passo a passo: usar um elemento JSON para criar uma interface do usuário](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [Marcação JSON de caixa de diálogo MonoTouch.](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Caixa de diálogo MonoTouch no github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Referência de classe UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referência de classe UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)