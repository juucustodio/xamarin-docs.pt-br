---
title: Introdução ao MonoTouch para xamarin. IOS
description: 'Este documento descreve MonoTouch (MT. D), uma estrutura para desenvolvimento de interface do usuário rápida e declarativo com xamarin. IOS. Ele aborda como usar as APIs MonoTouch.Dialog para criar uma interface no código ou JSON e usar recursos como puxar para atualizar, pesquisa, o carregamento de imagem de plano de fundo e muito mais.'
ms.prod: xamarin
ms.assetid: 52A35B24-C23B-8461-A8FF-5928A2128FB0
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
---

# <a name="introduction-to-monotouchdialog-for-xamarinios"></a>Introdução ao MonoTouch para xamarin. IOS

MonoTouch, conhecido como MT. D para abreviar, é um kit de ferramentas de desenvolvimento da interface do usuário rápida que permite aos desenvolvedores criar as telas do aplicativo e a navegação por meio de informações, em vez do tédio de criação de controladores de exibição, tabelas, etc. Como tal, ele fornece uma simplificação significativa de redução de desenvolvimento e o código de interface do usuário. Por exemplo, considere a seguinte captura de tela:

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

Ao trabalhar com tabelas no iOS, geralmente há uma tonelada de código redundante.
Por exemplo, sempre que uma tabela é necessário, uma fonte de dados é necessário para popular a tabela. Em um aplicativo que tem duas telas baseadas em tabelas que estão conectadas por meio de um controlador de navegação, cada tela compartilha muitos do mesmo código.

MT. 1!d simplifica que encapsulando todo esse código em uma API genérica para criação de tabela. Em seguida, ele fornece uma abstração sobre o que a API que permite que um objeto declarativo facilita ainda mais a sintaxe de associação. Como tal, há duas APIs disponíveis no MT. UNIDADE D:

-   **API de elementos de nível inferior** – o *elementos API* baseia-se na criação de uma árvore hierárquica de elementos que representam as telas e seus componentes. A API de elementos fornece aos desenvolvedores mais flexibilidade e controle para criar interfaces do usuário. Além disso, a API de elementos tem suporte avançado para a definição declarativa via JSON, que permite que tanto declaração incrivelmente rápida, bem como a geração dinâmica de interface do usuário de um servidor. 
-   **API de reflexão de alto nível** – também conhecido como o *associação* *API* , nos quais classes são anotadas com dicas de interface do usuário e, em seguida, o MT. D automaticamente cria telas com base nos objetos e fornece uma associação entre o que é exibida (e, opcionalmente, editada) na tela e o objeto base fazendo. O exemplo acima ilustrado o uso da API de reflexão. Essa API não fornece um controle refinado do que faz os elementos de API, mas reduz ainda mais complexidade criando automaticamente a hierarquia de elementos com base em atributos de classe. 


MT. 1!d vem empacotado com um grande conjunto de criados em elementos de interface do usuário para a criação de tela, mas ele também reconhece a necessidade de elementos personalizados e layouts de tela Avançado. Como tal, a extensibilidade é que uma primeira classe em destaque preparadas para a API. Os desenvolvedores podem estender os elementos existentes ou criar novos e, em seguida, se integram perfeitamente.

Além disso, a MT. D tem um número de recursos de experiência do usuário iOS comuns internas, como "pull para atualizar" dar suporte a carregamento, da imagem assíncrona e suporte de pesquisa.

Este artigo levará um panorama abrangente trabalhando com MT. 1!d, incluindo:

-   **MT. Componentes de D** – isso nos concentrar em compreender as classes que compõem o MT. 1!d para permitir que você se familiarizar rapidamente. 
-   **Referência de elementos** – uma lista abrangente dos elementos internos do MT. D. 
-   **Uso avançado** – isso abrange recursos avançados como puxar para atualizar, pesquisa, carregamento de imagem em segundo plano, usando o LINQ para criar hierarquias de elemento e criar elementos personalizados, células, e controladores para usam com o MT. D. 

## <a name="setting-up-mtd"></a>Configuração do MT. 1!D

MT. 1!d é distribuído com o xamarin. IOS. Para usá-lo, clique com botão direito no **referências** nó de um xamarin. IOS do projeto no Visual Studio 2017 ou Visual Studio para Mac e adicione uma referência para o **MonoTouch 1** assembly. Em seguida, adicione `using MonoTouch.Dialog` instruções em sua fonte de código conforme necessário.

## <a name="understanding-the-pieces-of-mtd"></a>Noções básicas sobre as partes do MT. 1!D

Mesmo ao usar a reflexão, API, MT. 1!d cria uma hierarquia de elementos nos bastidores, como se tivesse sido criada diretamente por meio da API de elementos. Além disso, o suporte a JSON mencionado na seção anterior cria elementos também. Por esse motivo, é importante ter uma compreensão básica das partes constituintes do MT. D.

MT. 1!d cria telas usando as seguintes quatro partes:

-  **DialogViewController**
-  **RootElement**
-  **Section**
-  **Elemento**


### <a name="dialogviewcontroller"></a>DialogViewController

Um *DialogViewController*, ou *DVC* de forma abreviada, herda `UITableViewController` e, portanto, representa uma tela com uma tabela. DVCs podem ser enviados para um controlador de navegação como uma UITableViewController regular.

### <a name="rootelement"></a>RootElement

Um *RootElement* é o contêiner de nível superior para itens que são colocados em um DVC. Ele contém seções, que, em seguida, podem conter elementos. RootElements não são renderizadas; em vez disso, eles são simplesmente contêineres para o que realmente é renderizado. Um RootElement é atribuído a um DVC e, em seguida, o DVC processa seus filhos.

### <a name="section"></a>Seção

Uma seção é um grupo de células em uma tabela. Como com uma seção de tabela normal, ele pode, opcionalmente, ter um cabeçalho e rodapé que pode ser texto ou até mesmo personalizados modos de exibição, como na seguinte captura de tela:

 [![](images/image2.png "Como com uma seção de tabela normal, ele pode, opcionalmente, ter um cabeçalho e rodapé que pode ser texto ou até mesmo personalizados modos de exibição, como na captura de tela")](images/image2.png#lightbox)

### <a name="element"></a>Elemento

Um elemento representa uma célula da tabela real. MT. 1!d vem repleto com uma ampla variedade de elementos que representam diferentes tipos de dados ou entradas diferentes. Por exemplo, capturas de tela as seguir ilustram alguns dos elementos disponíveis:

 [![](images/image3.png "Por exemplo, este capturas de tela ilustram alguns dos elementos disponíveis")](images/image3.png#lightbox)

## <a name="more-on-sections-and-rootelements"></a>Mais em seções e RootElements

Agora vejamos RootElements e seções mais detalhadamente.

### <a name="rootelements"></a>RootElements

Pelo menos um RootElement é necessária para iniciar o processo de MonoTouch.

Se um RootElement é inicializada com um valor de elemento/seção esse valor é usado para localizar um filho do elemento que fornecerá um resumo da configuração, que é processado no lado direito da tela. Por exemplo, a captura de tela abaixo mostra uma tabela à esquerda, com uma célula que contém o título da tela de detalhes à direita, "Sobremesas", juntamente com o valor do Deserto selecionado.

 [![](images/image4.png "Esta captura de tela mostra uma tabela à esquerda com uma célula que contém o título da tela de detalhes à direita, sobremesas, juntamente com o valor do Deserto selecionado")](images/image4.png#lightbox) [![](images/image5.png "isso captura de tela abaixo mostra uma tabela à esquerda com uma célula que contém o título da tela de detalhes à direita, sobremesas, juntamente com o valor do Deserto selecionado")](images/image5.png#lightbox)

Elementos raiz também podem ser usados dentro de seções para disparar o carregamento de uma nova página de configuração aninhada, conforme mostrado acima. Quando usado nesse modo a legenda fornecida é usada enquanto renderizada dentro de uma seção e também é usada como o título para a subpágina. Por exemplo:

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

No exemplo acima, quando o usuário toca em "Sobremesas" MonoTouch será criar uma nova página e navegue até ele com a raiz, que está sendo "Sobremesas" e ter um grupo de rádio com três valores.

Esse exemplo específico, o grupo de opção selecionará "Bolo de Chocolate" na seção "Sobremesas" porque passamos o valor "2" para o RadioGroup. Isso significa que escolher o item 3 na lista (índice zero).

Chamar o método Add ou usando a sintaxe do inicializador c# 4 adiciona seções.
Os métodos de inserção são fornecidos para inserir seções com uma animação.

Se você criar o RootElement com uma instância de grupo (em vez de um RadioGroup) o valor de resumo da RootElement quando exibido em uma seção será a contagem cumulativa de todas as BooleanElements e CheckboxElements que têm a mesma chave que o valor de Group.Key.

### <a name="sections"></a>Seções

As seções são usadas para agrupar elementos na tela e eles são filhos diretos somente válidos do RootElement. Seções podem conter qualquer um dos elementos padrão, incluindo RootElements novo.

RootElements inseridos em uma seção são usados para navegar até um novo nível mais profundo.

Seções podem ter cabeçalhos e rodapés de páginas como cadeias de caracteres ou como UIViews.
Normalmente você usará somente as cadeias de caracteres, mas para criar interfaces do usuário personalizadas, você pode usar qualquer UIView como o cabeçalho ou rodapé. Você pode usar uma cadeia de caracteres para criá-los como esta:

```csharp
var section = new Section ("Header", "Footer")
```

Para usar os modos de exibição, basta passe os modos de exibição para o construtor:

```csharp
var header = new UIImageView (Image.FromFile ("sample.png"));
var section = new Section (header)
```

### <a name="getting-notified"></a>Recebendo notificações

#### <a name="handling-nsaction"></a>Tratamento de NSAction

MT. As superfícies de D um `NSAction` como um delegado para manipular os retornos de chamada.
Por exemplo, digamos que você deseja manipular um evento de toque de uma célula de tabela criado pelo MT. D. Ao criar um elemento com o MT. D, simplesmente forneça um retorno de chamada de função, conforme mostrado abaixo:

```csharp
new Section () {
        new StringElement ("Demo Callback", 
                delegate { Console.WriteLine ("Handled"); })
}
```

#### <a name="retrieving-element-value"></a>Recuperando o valor do elemento

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

Quando o usuário pressiona a célula inferior da tabela, o código na função anônima que é executado, gravar o valor da `element` da instância para o **saída do aplicativo** painel de soluções do Visual Studio para Mac.

## <a name="built-in-elements"></a>Elementos internos

MT. 1!d vem com um número de itens de célula de tabela interno conhecido como elementos.
Esses elementos são usados para exibir uma série de diferentes tipos em células de tabela, como cadeias de caracteres, floats, datas e até mesma imagens, para mencionar apenas alguns. Cada elemento se encarrega de exibir o tipo de dados adequadamente. Por exemplo, um elemento booliano exibirá uma opção para ativar ou desativar o seu valor. Da mesma forma, um elemento de flutuante exibirá um controle deslizante para alterar o valor de float.

Há ainda mais complexos elementos para dar suporte a tipos de dados mais avançados, como imagens e html. Por exemplo, um elemento html, que abrirá um UIWebView para carregar uma página da web, quando selecionado, exibe uma legenda na célula da tabela.

### <a name="working-with-element-values"></a>Trabalhando com valores de elemento

Elementos que são usados para capturar a entrada do usuário expõem um público `Value` propriedade que contém o valor atual do elemento a qualquer momento. Ele é atualizado automaticamente conforme o usuário usa o aplicativo.

Esse é o comportamento para todos os elementos que fazem parte do MonoTouch, mas ele não é necessário para elementos criados pelo usuário.

### <a name="string-element"></a>Elemento de cadeia de caracteres

Um `StringElement` mostra uma legenda à esquerda de uma célula de tabela e o valor de cadeia de caracteres no lado direito da célula.

 [![](images/image7.png "Um StringElement mostra uma legenda à esquerda de uma célula de tabela e o valor de cadeia de caracteres à direita da célula")](images/image7.png#lightbox)

Para usar um `StringElement` como um botão, fornecer um delegado.

```csharp
new StringElement (
        "Click me",
        () => { new UIAlertView("Tapped", "String Element Tapped"
, null, "ok", null).Show(); })
```

 [![](images/image8.png "Para usar um StringElement como um botão, fornecer um delegado")](images/image8.png#lightbox)

### <a name="styled-string-element"></a>Elemento com estilo de cadeia de caracteres

Um `StyledStringElement` permite que cadeias de caracteres a ser apresentada usando qualquer um dos estilos de célula de tabela interna ou com formatação personalizada.

 [![](images/image9.png "Um StyledStringElement permite que cadeias de caracteres a ser apresentada usando qualquer um dos estilos de célula de tabela interna ou com formatação personalizada")](images/image9.png#lightbox)

O `StyledStringElement` deriva de classe `StringElement`, mas permite que os desenvolvedores personalizar algumas propriedades, como a fonte, cor do texto, cor de fundo, o modo de quebra de linha e número de linhas a serem exibidas, e se um acessório deve ser exibido.

### <a name="multiline-element"></a>Elemento de várias linhas

 [![](images/image10.png "Elemento de várias linhas")](images/image10.png#lightbox)

### <a name="entry-element"></a>Elemento de entrada

O `EntryElement`, como o nome implica, é usado para obter a entrada do usuário. Ele dá suporte a cadeias de caracteres regulares ou senhas, no qual os caracteres ficam ocultos.

 [![](images/image11.png "O EntryElement é usado para obter a entrada do usuário")](images/image11.png#lightbox)

Ele é inicializado com três valores:

-  A legenda para a entrada que será mostrada ao usuário.
-  Texto do espaço reservado (esse é o texto esmaecido que fornece uma dica para o usuário). 
-  O valor do texto.


O espaço reservado e o valor podem ser nulos. No entanto, a legenda é necessária.

Em qualquer momento, acessar sua propriedade de valor pode recuperar o valor da `EntryElement`.

Além do `KeyboardType` propriedade pode ser definida no momento da criação para o estilo de tipo de teclado desejado para a entrada de dados. Isso pode ser usado para configurar o teclado usando os valores de `UIKeyboardType` conforme listado abaixo:

-  Numeric
-  Telefone
-  Url
-  Email


### <a name="boolean-element"></a>Elemento booliano

 [![](images/image12.png "Elemento booliano")](images/image12.png#lightbox)

### <a name="checkbox-element"></a>Elemento de caixa de seleção

 [![](images/image13.png "Elemento de caixa de seleção")](images/image13.png#lightbox)

### <a name="radio-element"></a>Elemento de opção

Um `RadioElement` exige um `RadioGroup` a ser especificado no `RootElement`.

```csharp
mtRoot = new RootElement ("Demos", new RadioGroup("MyGroup", 0))
```

 [![](images/image14.png "Um RadioElement requer um RadioGroup será necessário especificar o RootElement")](images/image14.png#lightbox)

 `RootElements` também são usados para coordenar os elementos de rádio. O `RadioElement` membros podem abranger várias seções (por exemplo implementar algo semelhante ao seletor de tom de anel e toques personalizado separado de toques do sistema). A exibição de resumo mostra o elemento de opção está selecionado no momento. Para usar isso, crie o `RootElement` com o construtor de grupo, como este:

```csharp
var root = new RootElement ("Meals", new RadioGroup ("myGroup", 0))
```

O nome do grupo no `RadioGroup` é usado para mostrar o valor selecionado no conteúdo da página (se houver) e o valor, que é zero, nesse caso, é o índice do primeiro item selecionado.

### <a name="badge-element"></a>Elemento de notificação

 [![](images/image15.png "Elemento de notificação")](images/image15.png#lightbox)

### <a name="float-element"></a>Elemento float

 [![](images/image16.png "Elemento float")](images/image16.png#lightbox)

### <a name="activity-element"></a>Elemento de atividade

 [![](images/image17.png "Elemento de atividade")](images/image17.png#lightbox)

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

Quando a célula que corresponde a DateTimeElement é selecionada, um seletor de data e hora é apresentado como mostrado abaixo:

 [![](images/image23.png "Quando a célula que corresponde a DateTimeElement é selecionada, um seletor de data e hora é apresentado como mostrado")](images/image23.png#lightbox)

### <a name="html-element"></a>Elemento HTML

 [![](images/image24.png "Elemento HTML")](images/image24.png#lightbox)

O `HTMLElement` exibe o valor da sua `Caption` propriedade na célula da tabela. Quando selecionado, o `Url` atribuído ao elemento é carregado em um `UIWebView` controlar conforme mostrado abaixo:

 [![](images/image25.png "Quando selecionada, a Url atribuídos ao elemento é carregado em um controle UIWebView conforme mostrado abaixo")](images/image25.png#lightbox)

### <a name="message-element"></a>Elemento de mensagem

 [![](images/image26.png "Elemento de mensagem")](images/image26.png#lightbox)

### <a name="load-more-element"></a>Carregar mais de elemento

Use esse elemento para permitir aos usuários carregar mais itens em sua lista. Você pode personalizar o normal e legendas de carregamento, bem como a cor de fonte e texto.
O `UIActivity` indicador inicia a animação e a legenda de carregamento será exibida quando um usuário toca na célula e, em seguida, o `NSAction` passado para o construtor é executado. Uma vez em seu código na `NSAction` for concluído, o `UIActivity` indicador interrompe a animação e a legenda normal será exibida novamente.

### <a name="uiview-element"></a>Elemento UIView

Além disso, qualquer personalizado `UIView` podem ser exibidos usando o `UIViewElement`.

### <a name="owner-drawn-element"></a>Elemento desenhado pelo proprietário

Esse elemento deve ser uma subclasse conforme ele é uma classe abstrata. Você deve substituir a `Height(RectangleF bounds)` método no qual você deve retornar a altura do elemento, bem como `Draw(RectangleF bounds, CGContext context, UIView view)` no qual você deve fazer todos os seu desenho personalizado dentro dos limites determinados, usando os parâmetros de contexto e o modo de exibição. Esse elemento faz o trabalho pesado de criação de subclasses um `UIView`e colocá-lo na célula a ser retornado, deixando apenas a necessidade de implementar duas substituições simples. Você pode ver uma melhor implementação de exemplo no aplicativo de exemplo no `DemoOwnerDrawnElement.cs` arquivo.

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

O `JsonElement` é uma subclasse de `RootElement` que estende um `RootElement` ser capaz de carregar o conteúdo do filho aninhados de uma url de local ou remoto.

O `JsonElement` é um `RootElement` que pode ser instanciado em duas formas. Cria uma versão um `RootElement` que carregará o conteúdo sob demanda. Eles são criados usando o `JsonElement` construtores que usam um argumento extra no final, a url para carregar o conteúdo do:

```csharp
var je = new JsonElement ("Dynamic Data", "https://tirania.org/tmp/demo.json");
```

A outra forma cria os dados de um arquivo local ou um existente `System.Json.JsonObject` que você já tiver analisado:

```csharp
var je = JsonElement.FromFile ("json.sample");
using (var reader = File.OpenRead ("json.sample"))
    return JsonElement.FromJson (JsonObject.Load (reader) as JsonObject, arg);
```

Para obter mais informações sobre como usar o JSON com MT. 1!d, consulte o [passo a passo de elemento JSON](http://docs.xamarin.com/guides/ios/user_interface/monotouch.dialog/json_element_walkthrough) tutorial.

## <a name="other-features"></a>Outros recursos

### <a name="pull-to-refresh-support"></a>Suporte de puxar para atualizar

 *Efetuar pull-para-* *atualize* um efeito visual é originalmente encontrado no *Tweetie2* aplicativo, que se tornou um efeito popular entre vários aplicativos.

Para adicionar suporte automático de puxar para atualizar para as caixas de diálogo, você só precisará fazer duas coisas: ligar um manipulador de eventos para ser notificado quando o usuário efetua pull dos dados e notificar o `DialogViewController` quando os dados foram carregados para retornar ao estado padrão.

Vinculando uma notificação é simple; conectar-se apenas para o `RefreshRequested` evento no `DialogViewController`, semelhante a esta:

```csharp
dvc.RefreshRequested += OnUserRequestedRefresh;
```

Em seguida, em seu método `OnUserRequestedRefresh`, seria alguns carregamento de dados da fila, alguns dados de solicitação da rede ou criar um thread para computar os dados. Depois que os dados foram carregados, você deve notificar a `DialogViewController` que os novos dados seja e, para restaurar o modo de exibição para seu estado padrão, você faz isso chamando `ReloadComplete`:

```csharp
dvc.ReloadComplete ();
```

### <a name="search-support"></a>Dá suporte à pesquisa

Para dar suporte à pesquisa, defina as `EnableSearch` propriedade em seu `DialogViewController`. Você também pode definir o `SearchPlaceholder` propriedade a ser usado como o texto de marca d'água na barra de pesquisa.

Pesquisando alterará o conteúdo da exibição conforme o usuário digita. Ele pesquisa os campos visíveis e mostra aquelas para o usuário. O `DialogViewController` expõe três métodos para iniciar por meio de programação, finalizar ou disparar uma nova operação de filtro nos resultados. Esses métodos estão listados abaixo:

-  `StartSearch`
-  `FinishSearch`
-  `PerformFilter`


O sistema é extensível, portanto, você pode alterar esse comportamento, se você quiser.

### <a name="background-image-loading"></a>Carregamento de imagem em segundo plano

MonoTouch incorpora a [TweetStation](https://github.com/migueldeicaza/TweetStation) carregador de imagem do aplicativo. Esse carregador de imagem pode ser usado para carregar imagens em segundo plano, dá suporte a armazenamento em cache e pode notificar seu código quando a imagem foi carregada.

Ele também limitará o número de conexões de rede de saída.

O carregador de imagem é implementado na `ImageLoader` classe, tudo o que você precisa fazer é chamada a `DefaultRequestImage` método, você precisará fornecer o Uri para a imagem que você deseja carregar, bem como uma instância da `IImageUpdated` interface que será invocado quando a imagem de alta disponibilidade s foi carregado.

Por exemplo o código a seguir carrega uma imagem de uma Url em um `BadgeElement`:

```csharp
string uriString = "http://some-server.com/some image url";

var rootElement = new RootElement("Image Loader") {
        new Section(){
                new BadgeElement( ImageLoader.DefaultRequestImage( new Uri(uriString), this), "Xamarin")
        }
};
```

A classe ImageLoader expõe um método de limpeza que você pode chamar, quando você deseja liberar todas as imagens que estão atualmente armazenados em cache na memória. O código atual tem um cache de 50 imagens. Se você deseja usar um tamanho de cache diferentes (por exemplo, se você está esperando as imagens a ser muito grandes que 50 imagens seria muito), assim, você pode criar instâncias de ImageLoader e passar o número de imagens que você deseja manter no cache.

## <a name="using-linq-to-create-element-hierarchy"></a>Usando o LINQ para criar a hierarquia de elementos

Por meio do uso inteligente do LINQ e do # sintaxe de inicialização, o LINQ pode ser usado para criar uma hierarquia de elemento. Por exemplo, o código a seguir cria uma tela de algumas matrizes de cadeia de caracteres e identificadores de seleção por meio de uma função anônima que é passada para cada célula `StringElement`:

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

Isso poderia facilmente ser combinado com um armazenamento de dados XML ou dados de um banco de dados para criar aplicativos complexos quase inteiramente a partir de dados.

## <a name="extending-mtd"></a>Estendendo o MT. 1!D

### <a name="creating-custom-elements"></a>Criar elementos personalizados

Você pode criar seu próprio elemento herdando a um elemento existente ou derivando da classe raiz do elemento.

Para criar seu próprio elemento, você desejará substituir os seguintes métodos:

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

Se você estiver planejando sobre a implementação de sua `GetCell` método chamando `base.GetCell(tv)` e personalizando a célula retornada, você precisará também substituir o `CellKey` semelhante a esta propriedade para retornar uma chave que seja exclusiva para seu elemento:

```csharp
static NSString MyKey = new NSString ("MyKey");
    protected override NSString CellKey {
        get {
            return MyKey;
        }
    }
```

Isso funciona para a maioria dos elementos, mas não para o `StringElement` e `StyledStringElement` pois elas usam o seu próprio conjunto de chaves para vários cenários de renderização. Você teria que duplicar o código nessas classes.

### <a name="dialogviewcontrollers-dvcs"></a>DialogViewControllers (DVCs)

Usam a reflexão e a API de elementos que o mesmo `DialogViewController`. Às vezes você deseja personalizar a aparência do modo de exibição ou você talvez queira usar alguns recursos do `UITableViewController` que vá além da criação básica de interfaces do usuário.

O `DialogViewController` é simplesmente uma subclasse do `UITableViewController` e você pode personalizá-lo da mesma maneira que você faz para personalizar um `UITableViewController`.

Por exemplo, se você quiser alterar o estilo de lista para ser `Grouped` ou `Plain`, você pode definir esse valor, alterando a propriedade quando você cria o controlador, como este:

```csharp
var myController = new DialogViewController (root, true){
        Style = UITableViewStyle.Grouped;
    }
```

Para obter mais avançadas todas as personalizações do `DialogViewController`, como a configuração de seu plano de fundo, você faria subclasse-la e substituir as devidas métodos, conforme mostrado no exemplo a seguir:

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

Outro ponto de personalização é os seguintes métodos virtuais no `DialogViewController`:

```csharp
public override Source CreateSizingSource (bool unevenRows)
```

Esse método deve retornar uma subclasse de `DialogViewController.Source` para casos em que as células são uniformemente dimensionadas ou uma subclasse de `DialogViewController.SizingSource` se suas células são desiguais.

Você pode usar essa substituição é para capturar qualquer uma da `UITableViewSource` métodos. Por exemplo, [TweetStation](https://github.com/migueldeicaza/TweetStation) usa isso para controlar quando o usuário rolou na parte superior e atualize adequadamente o número de tweets não lidas.

## <a name="validation"></a>Validação

Elementos não fornecem validação em si como os modelos que são bem adequados para páginas da web e aplicativos da área de trabalho não são mapeados diretamente para o modelo de interação do iPhone.

Se você quiser fazer a validação de dados, você deve fazer isso quando o usuário dispara uma ação com os dados inseridos. Por exemplo uma <span class="ui">feito</span> ou <span class="ui">próxima</span> botão na barra de ferramentas superior, ou algumas `StringElement` usado como um botão para ir para o próximo estágio.

Isso é onde você poderia executar a validação de entrada básica, e talvez mais complicado de validação, como verificar a validade de uma combinação de senha do usuário com um servidor.

Como você pode notificar o usuário de um erro é específico ao aplicativo. Você poderia pop-up uma `UIAlertView` ou mostrar uma dica.

## <a name="summary"></a>Resumo

Este artigo abordou as muitas informações sobre MonoTouch. Ele discutiu os fundamentos de como MT. D funciona e abordados os vários componentes que compõem o MT. D. Ele também mostrou a ampla variedade de elementos e personalizações de tabela com suporte pelo MT. 1!d e discutiu como MT. 1!d pode ser estendida com elementos personalizados. Além dele explicou o suporte a JSON no MT. 1!d que permite a criação de elementos dinamicamente do JSON.


## <a name="related-links"></a>Links relacionados

- [Screencast - Miguel de Icaza cria uma tela de logon do iOS com MonoTouch](http://youtu.be/3butqB1EG0c)
- [Screencast - criar facilmente iOS interfaces do usuário com MonoTouch](http://youtu.be/j7OC5r8ZkYg)
- [Passo a passo: criando um aplicativo usando a API de elementos](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [Passo a passo: criando um aplicativo usando a API de reflexão](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [Passo a passo: Usando um elemento JSON para criar uma Interface do usuário](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [MonoTouch.Dialog JSON Markup](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Caixa de diálogo MonoTouch no Github](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [Referência de classe UITableViewController](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [Referência de classe UINavigationController](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)
