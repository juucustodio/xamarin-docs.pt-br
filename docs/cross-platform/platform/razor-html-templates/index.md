---
title: Modos de exibição HTML de construção usando modelos Razor
description: " Usar uma página da Web de tela inteira para renderizar HTML pode ser uma maneira simple e eficiente para processar a formatação complexa de uma maneira de plataforma cruzada, especialmente se você já tiver o HTML, CSS e Javascript de um projeto de site."
ms.prod: xamarin
ms.assetid: D8B87C4F-178E-48D9-BE43-85066C46F05C
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: fa361e48f8f7e236a3295deda2d80a02ef06b34d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="building-html-views-using-razor-templates"></a>Modos de exibição HTML de construção usando modelos Razor

No mundo desenvolvimento móvel o termo "aplicativo de híbrida" normalmente refere-se a um aplicativo que apresenta alguns (ou todos) das suas telas como páginas HTML dentro de um controle do Visualizador da web hospedado.

Há alguns ambientes de desenvolvimento que permitem a você criar seu aplicativo móvel inteiramente em HTML e Javascript, no entanto, esses aplicativos podem sofrer de problemas de desempenho ao tentar realizar processamento complexo ou efeitos de interface do usuário e são também limitado na plataforma eles podem acessar os recursos.

Xamarin oferece o melhor de ambos os mundos, especialmente ao usar o mecanismo de modelagem de HTML do Razor. Com o Xamarin, você tem a flexibilidade de criar várias plataformas modelo HTML exibições que usam Javascript e CSS, mas também possuem acesso completo aos APIs da plataforma subjacente e processamento rápido usando c#.

Este documento explica como usar o Razor modos de exibição HTML + Javascript + CSS que podem ser usados em plataformas móveis usando o Xamarin de compilação do mecanismo de modelagem.

## <a name="using-web-views-programmatically"></a>Usando modos de exibição Web programaticamente

Antes de aprendemos sobre Razor esta seção aborda como usar os modos de exibição da web para exibir o conteúdo HTML diretamente – especificamente conteúdo HTML que é gerado dentro de um aplicativo.

Xamarin fornece acesso completo a APIs da plataforma subjacente no iOS e Android, portanto, é fácil criar e exibir HTML usando c#. A sintaxe básica para cada plataforma é mostrada abaixo.

### <a name="ios"></a>iOS

Exibindo o HTML em um controle de UIWebView em xamarin também leva apenas algumas linhas de código:

```csharp
var webView = new UIWebView (View.Bounds);
View.AddSubview(webView);
string contentDirectoryPath = Path.Combine (NSBundle.MainBundle.BundlePath, "Content/");
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadHtmlString(html, NSBundle.MainBundle.BundleUrl);
```

Consulte o [iOS UIWebView](http://docs.xamarin.com/recipes/ios/content_controls/web_view/) receitas para obter mais detalhes sobre como usar o controle UIWebView.

### <a name="android"></a>Android

Exibição de HTML em um controle WebView usando xamarin é realizada em apenas algumas linhas de código:

```csharp
// webView is declared in an AXML layout file
var webView = FindViewById<WebView> (Resource.Id.webView);
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadDataWithBaseURL("file:///android_asset/", html, "text/html", "UTF-8", null);
```

Consulte o [WebView Android](http://docs.xamarin.com/recipes/android/controls/webview/) receitas para obter mais detalhes sobre como usar o controle do WebView.

### <a name="specifying-the-base-directory"></a>Especificar o diretório Base

Em ambas as plataformas, há um parâmetro que especifica o diretório base para a página HTML. Esse é o local no sistema de arquivos do dispositivo que é usado para resolver as referências relativas a recursos como imagens e arquivos CSS. Por exemplo, como marcas

```html
<link rel="stylesheet" href="style.css" />
<img src="monkey.jpg" />
<script type="text/javascript" src="jscript.js">
```

consulte estes arquivos: **style.css**, **monkey.jpg** e **jscript.js**. A configuração do diretório base informa a exibição da web onde esses arquivos estão localizados para que eles possam ser carregados na página.

#### <a name="ios"></a>iOS

A saída do modelo é renderizada no iOS com o código c# a seguir:

```csharp
webView.LoadHtmlString (page, NSBundle.MainBundle.BundleUrl);
```

O diretório base é especificado como `NSBundle.MainBundle.BundleUrl` que se refere a que o aplicativo está instalado no diretório. Todos os arquivos de **recursos** pasta são copiados para este local, como o **style.css** arquivo mostrado aqui:

 ![solução de iPhoneHybrid](images/image1_240x163.png)

A ação de compilação para todos os arquivos de conteúdo estáticos deve ser **BundleResource**:

 ![ação de compilação de projeto iOS: BundleResource](images/image2_250x131.png)

#### <a name="android"></a>Android

Android também exige um diretório base a ser passado como um parâmetro quando cadeias de caracteres de html são exibidas em uma exibição da web.

```csharp
webView.LoadDataWithBaseURL("file:///android_asset/", page, "text/html", "UTF-8", null);
```

A cadeia de caracteres especial **file:///android_asset/** refere-se à pasta ativos Android em seu aplicativo, mostrado aqui que contém o **style.css** arquivo.

 ![Solução de AndroidHybrid](images/image3_240x167.png)

A ação de compilação para todos os arquivos de conteúdo estáticos deve ser **AndroidAsset**.

 ![Ação de compilação de projeto Android: AndroidAsset](images/image4_250x71.png)

### <a name="calling-c-from-html-and-javascript"></a>Chamando c# de HTML e Javascript

Quando uma página html é carregada em uma exibição da web, ele tratará os links e formulários como se a página foi carregada de um servidor. Isso significa que, se o usuário clica em um link ou envia um formulário de exibição da web tentará navegar para o destino especificado.

Se o link é um servidor externo (como google.com) a exibição da web tentará carregar o site externo (supondo que haja uma conexão de internet).

```html
<a href="http://google.com/">Google</a>
```

Se o link é relativo a exibição da web tentará carregar o conteúdo do diretório base. Obviamente, nenhuma conexão de rede é necessária para este trabalho, como o conteúdo é armazenado no aplicativo no dispositivo.

```html
<a href="somepage.html">Local content</a>
```

Ações de formulário seguem a mesma regra.

```html
<form method="get" action="http://google.com/"></form>
<form method="get" action="somepage.html"></form>
```

Você não vai para hospedar um servidor web no cliente; No entanto, você pode usar as mesmas técnicas de comunicação de servidor utilizadas nos padrões de design responsivo atuais para chamar serviços em HTTP GET e manipulam respostas de forma assíncrona pelo emissor Javascript (ou chamada Javascript já hospedado no modo de exibição da web). Isso permite que você facilmente passar dados do HTML para o código c# para processamento e exibir que os resultados de volta na página HTML.

IOS e Android fornecem um mecanismo para o código do aplicativo para interceptar esses eventos de navegação para que o código do aplicativo pode responder (se necessário). Esse recurso é crucial para criar aplicativos híbridos porque ele permite que o código nativo interagem com a exibição da web.

#### <a name="ios"></a>iOS

O evento ShouldStartLoad na exibição da web no iOS pode ser substituído para permitir que o código do aplicativo para lidar com uma solicitação de navegação (como clicar no link). Os parâmetros do método fornecem todas as informações

```csharp
bool HandleShouldStartLoad (UIWebView webView, NSUrlRequest request, UIWebViewNavigationType navigationType) {
    // return true if handled in code
    // return false to let the web view follow the link
}
```

e, em seguida, atribua o manipulador de eventos:

```csharp
webView.ShouldStartLoad += HandleShouldStartLoad;
```

#### <a name="android"></a>Android

No Android simplesmente subclasse WebViewClient e, em seguida, implemente código para responder à solicitação de navegação.

```csharp
class HybridWebViewClient : WebViewClient {
    public override bool ShouldOverrideUrlLoading (WebView webView, string url) {
        // return true if handled in code
        // return false to let the web view follow the link
    }
}
```

e, em seguida, defina o cliente no modo de exibição da web:

```csharp
webView.SetWebViewClient (new HybridWebViewClient ());
```

### <a name="calling-javascript-from-c"></a>Chamar de Javascript em c#

Além de informando uma exibição da web para carregar uma nova página HTML, código c# também pode executar o Javascript dentro da página exibida no momento. Todos blocos de código Javascript podem ser criados usando o c# cadeias de caracteres e executados ou pode criar chamadas de método Javascript já disponível na página via `script` marcas.

#### <a name="android"></a>Android

Criar o código Javascript para ser executado e, em seguida, um prefixo com "javascript:" e instruir a exibição da web para carregar essa cadeia de caracteres:

```csharp
var js = "alert('test');";
webView.LoadUrl ("javascript:" + js);
```

#### <a name="ios"></a>iOS

modos de exibição do iOS web fornecem um método especificamente para chamar Javascript:

```csharp
var js = "alert('test');";
webView.EvaluateJavascript (js);
```

### <a name="summary"></a>Resumo

Esta seção introduziu os recursos dos controles de exibição da web no Android e iOS que vamos construir aplicativos híbridos com o Xamarin, incluindo:

-  A capacidade de carregar HTML de cadeias de caracteres geradas no código,
-  A capacidade de fazer referência a arquivos locais (CSS, Javascript, imagens ou outros arquivos HTML)
-  A capacidade de interceptar solicitações de navegação de código c#,
-  A capacidade de chamar de Javascript do código c#.


A próxima seção apresenta Razor, o que torna mais fácil criar o HTML para usar em aplicativos híbridos.

## <a name="what-is-razor"></a>O que é Razor?

Razor é um mecanismo de modelagem que foi introduzido com o ASP.NET MVC, originalmente para executar no servidor e gerar HTML a serem disponibilizados para os navegadores da web.

O mecanismo de modelagem Razor estende a sintaxe HTML padrão com o c# para que você possa express o layout e incorporar folhas de estilo CSS e Javascript facilmente. O modelo pode fazer referência a uma classe de modelo, que pode ser qualquer tipo personalizado e cujas propriedades podem ser acessadas diretamente a partir do modelo. Uma das suas principais vantagens é a capacidade de misturar sintaxe HTML e c# facilmente.

Modelos Razor não estão limitados ao uso do lado do servidor, eles também podem ser incluídos em aplicativos Xamarin. Usando modelos de Razor junto com a capacidade de trabalhar com exibições da web por meio de programação permite que os aplicativos sofisticados híbrida de plataforma cruzada a ser construído com o Xamarin.

### <a name="razor-template-basics"></a>Noções básicas de modelo do Razor

Os arquivos de modelo do Razor têm um **. cshtml** extensão de arquivo. Pode ser adicionados a um projeto de Xamarin da seção de modelagem de texto de **novo arquivo** caixa de diálogo:

 ![Novo arquivo - modelo do Razor](images/image5_400x201.png)

Um modelo simples do Razor ( **RazorView.cshtml**) é mostrado abaixo.

```html
@model string
<html>
    <body>
    <h1>@Model</h1>
    </body>
</html>
```

Observe as seguintes diferenças de um arquivo HTML regular:

-  O `@` símbolo tem um significado especial em modelos Razor – indica que a expressão a seguir é c# a ser avaliada.
- `@model` diretiva sempre aparece como a primeira linha de um arquivo de modelo do Razor.
-  O `@model` diretiva deve ser seguida por um tipo. Neste exemplo, uma cadeia de caracteres simple é passada para o modelo, mas isso pode ser qualquer classe personalizada.
-  Quando `@Model` é referenciada em todo o modelo, ele fornece uma referência para o objeto passado para o modelo quando ele é gerado (por exemplo, será uma cadeia de caracteres).
-  O IDE gerará automaticamente uma classe parcial para modelos (arquivos com a **. cshtml** extensão). Você pode exibir esse código, mas não deve ser editada.
 ![RazorView.cshtml](images/image6_125x34.png) a classe parcial é nomeada RazorView para corresponder ao nome do arquivo de modelo. cshtml. É este nome que é usado para referenciar o modelo de código c#.
- `@using` instruções também podem ser incluídas na parte superior de um modelo do Razor para incluir namespaces adicionais.


A saída HTML final, em seguida, pode ser gerada com o código c# a seguir. Observe que especificamos o modelo para ser uma cadeia de caracteres "Hello World" que serão incorporados na saída renderizada de modelo.

```csharp
var template = new RazorView () { Model = "Hello World" };
var page = template.GenerateString ();
```

Aqui está a saída mostrada uma exibição da web no iOS simulador e o emulador Android:

 ![Hello World](images/image7_523x135.png)

### <a name="more-razor-syntax"></a>Sintaxe do Razor mais

Nesta seção, vamos apresentar algumas sintaxe Razor básica para ajudá-lo a começar a usá-lo. Os exemplos nesta seção popular a seguinte classe com dados e exibem-lo usando o Razor:

```csharp
public class Monkey {
    public string Name { get; set; }
    public DateTime Birthday { get; set; }
    public List<string> FavoriteFoods { get; set; }
}
```

Todos os exemplos usam o seguinte código de inicialização de dados

```csharp
var animal = new Monkey {
    Name = "Rupert",
    Birthday=new DateTime(2011, 04, 01),
    FavoriteFoods = new List<string>
        {"Bananas", "Banana Split", "Banana Smoothie"}
};
```

#### <a name="displaying-model-properties"></a>Exibindo propriedades de modelo

Quando o modelo é uma classe com propriedades, eles são facilmente consultados no modelo do Razor conforme mostrado neste modelo de exemplo:

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    </body>
</html>
```

Isso pode ser renderizado em uma cadeia de caracteres usando o seguinte código:

```csharp
var template = new RazorView () { Model = animal };
var page = template.GenerateString ();
```

O resultado final é mostrado aqui em uma exibição da web no iOS simulador e o emulador Android:

 ![Rupert](images/image8_516x160.png)

#### <a name="c-statements"></a>Instruções de c#

Mais complexos c# podem ser incluídos no modelo, como as atualizações das propriedades do modelo e o cálculo da idade neste exemplo:

```html
@model Monkey
<html>
    <body>
    @{
        Model.Name = "Rupert X. Monkey";
        Model.Birthday = new DateTime(2011,3,1);
    }
    <h1>@Model.Name</h1>
    <p>Birthday: @Model.Birthday.ToString("d MMMM yyyy")</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    </body>
</html>
```

Você pode escrever expressões c# linha complexas (como a idade de formatação), envolvendo o código com `@()`.

Várias c# instruções podem ser gravadas, envolvendo-los com `@{}`.

#### <a name="if-else-statements"></a>Instruções if-else

Ramificações de código podem ser expressas com `@if` conforme mostrado neste exemplo de modelo.

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    <p>Favorite Foods:</p>
    @if (Model.FavoriteFoods.Count == 0) {
        <p>No favorites</p>
    } else {
        <p>@Model.FavoriteFoods.Count favorites</p>
    }
    </body>
</html>
```

#### <a name="loops"></a>Loops

Como as construções de loop `foreach` também pode ser adicionado. O `@` prefixo pode ser usado na variável de loop ( `@food` nesse caso) para renderizá-lo em HTML.

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @Model.Birthday.ToString("d MMMM yyyy")</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    <p>Favorite Foods:</p>
    @if (Model.FavoriteFoods.Count == 0) {
        <p>No favorites</p>
    } else {
        <ul>
            @foreach (var food in @Model.FavoriteFoods) {
                <li>@food</li>
            }
        </ul>
    }
    </body>
</html>
```

A saída do modelo acima é mostrada em execução no iOS simulador e o emulador Android:

 ![Rupert X Monkey](images/image9_520x277.png)

Esta seção abordou os fundamentos de como usar modelos Razor para renderizar exibições somente leitura simples. A próxima seção explica como criar aplicativos mais completos usando o Razor que aceitam entrada do usuário e interoperar entre Javascript no modo de exibição HTML e c#.

## <a name="using-razor-templates-with-xamarin"></a>Usando modelos Razor com Xamarin

Esta seção explica como usar a construir seu próprio aplicativo híbrido usando os modelos de solução do Visual Studio para Mac. Existem três modelos disponíveis do **arquivo > Novo > solução...**  janela:

- **Android > aplicativo > aplicativo WebView Android**
- **iOS > aplicativo > aplicativo WebView**
- **ASP.NET MVC Project**



O **nova solução** janela tem esta aparência para iPhone e projetos Android - a descrição da solução à direita realça o suporte para o mecanismo de modelagem do Razor.

 ![Criando soluções Android e iPhone](images/image13_1139x959.png)

Observe que você pode adicionar facilmente um **. cshtml** modelo do Razor para *qualquer* existente projeto Xamarin, não é necessário usar esses modelos de solução. projetos do iOS não exigem um Storyboard usar Razor Basta adicionar um controle UIWebView a qualquer exibição programaticamente e você pode renderizar os modelos Razor inteira no código c#.

O conteúdo de solução de modelo padrão para iPhone e projetos Android é mostrado abaixo:

 ![iPhone e modelos de Android](images/image10_428x310.png)

Os modelos oferecem infraestrutura de aplicativo pronto para ir para carregar um modelo do Razor com um objeto de modelo de dados, processar a entrada do usuário e se comunicar com o usuário por meio de Javascript.

As partes importantes da solução são:

-  Conteúdo estático, como o **style.css** arquivo.
-  Arquivos de modelo do Razor. cshtml como **RazorView.cshtml** .
-  Modelo de classes que são referenciados nos modelos Razor como **ExampleModel.cs** .
-  A classe específica de plataforma que cria a exibição da web e processa o modelo, como o `MainActivity` no Android e o `iPhoneHybridViewController` no iOS.


A seção a seguir explica como funcionam os projetos.

### <a name="static-content"></a>Conteúdo Estático

Conteúdo estático inclui folhas de estilo CSS, imagens, arquivos Javascript ou outros tipos de conteúdo que podem ser vinculados ou referenciados por um arquivo HTML que está sendo exibido em uma exibição da web.

Os projetos de modelo incluem uma folha de estilos mínima para demonstrar como incluir conteúdo estático em seu aplicativo híbrido. Folha de estilos CSS é referenciada no modelo como este:

```html
<link rel="stylesheet" href="style.css" />
```

Você pode adicionar qualquer folha de estilo e arquivos Javascript que você precisa, incluindo estruturas, como JQuery.

### <a name="razor-cshtml-templates"></a>Razor cshtml modelos

O modelo inclui uma Razor **. cshtml** arquivos que previamente gravado código para ajudar a comunicar dados entre o HTML/Javascript e c#. Isso permitirá que você construir aplicativos híbridos sofisticados que não apenas exibir dados somente leitura a partir do modelo, mas também aceita entrada de usuário em HTML e passá-lo de volta para o código c# para processamento ou armazenamento.

#### <a name="rendering-the-template"></a>O modelo de processamento

Chamando o `GenerateString` em um modelo renderiza HTML pronto para exibição em uma exibição da web. Se o modelo usa um modelo deve ser fornecido antes da renderização. Este diagrama ilustra como renderização funciona – não os recursos estáticos resolvidos pelo modo de exibição da web em tempo de execução, usando o diretório base fornecido para localizar os arquivos especificados.

 ![Fluxograma do Razor](images/image12_700x421.png)

#### <a name="calling-c-code-from-the-template"></a>Chamando código c# do modelo

Comunicação de um modo de exibição renderizado web chamar de volta para c# é feita definindo a URL para o modo de exibição da web e, em seguida, interceptando a solicitação em c# para manipular a solicitação nativo sem recarregar a exibição da web.

Um exemplo pode ser visto em como o botão do RazorView é manipulado. O botão tem o HTML a seguir:

```html
<input type="button" name="UpdateLabel" value="Click" onclick="InvokeCSharpWithFormValues(this)" />
```

O `InvokeCSharpWithFormValues` função Javascript lê todos os valores do formulário HTML e define o `location.href` para a exibição da web:

```javascript
location.href = "hybrid:" + elm.name + "?" + qs;
```

Essa ação tentará navegar o modo de exibição da web para uma URL com um esquema personalizado (por exemplo. `hybrid:`)

```
hybrid:UpdateLabel?textbox=SomeValue&UpdateLabel=Click
```

Quando a exibição da web nativo processa a solicitação de navegação, temos a oportunidade de interceptar a ele. No iOS, isso é feito ao manipular o evento de HandleShouldStartLoad do UIWebView. No Android, podemos simplesmente subclasse o WebViewClient usado no formulário e substituir ShouldOverrideUrlLoading.

Os recursos internos desses dois interceptores de navegação é essencialmente o mesmo.

Primeiro, verifique a URL de exibição da web está tentando carregar, e se ele não começa com o esquema personalizado (`hybrid:`), permitir a navegação para ocorrer como normal.

Para o esquema de URL personalizado, tudo na URL entre o esquema e o "?" é o nome do método a ser manipulada (nesse caso, "UpdateLabel"). Tudo na cadeia de caracteres de consulta será tratado como os parâmetros para a chamada do método:

```csharp
var resources = url.Substring(scheme.Length).Split('?');
var method = resources [0];
var parameters = System.Web.HttpUtility.ParseQueryString(resources[1]);
```

`UpdateLabel` Neste exemplo, não uma quantidade mínima de manipulação de cadeia de caracteres no parâmetro de caixa de texto (acrescentando "c# diz" para a cadeia de caracteres) e, em seguida, chama de volta para o modo de exibição da web.

Depois de tratar a URL, o método anula a navegação para que a exibição da web não tenta concluir navegando até a URL personalizada.

#### <a name="manipulating-the-template-from-c"></a>Manipulando o modelo do c#

Comunicação em uma exibição da web HTML renderizada de c# é feita chamando Javascript no modo de exibição da web. No iOS, isso é feito chamando `EvaluateJavascript` no UIWebView:

```csharp
webView.EvaluateJavascript (js);
```

No Android, Javascript pode ser invocado no modo de exibição web carregando o Javascript como uma URL usando o `"javascript:"` esquema de URL:

```csharp
webView.LoadUrl ("javascript:" + js);
```

## <a name="making-an-app-truly-hybrid"></a>Fazer um aplicativo verdadeiramente híbrido

Esses modelos não fazem uso de controles nativos em cada plataforma – a tela inteira é preenchida com uma exibição única web.

HTML pode ser ótimo para criação de protótipos e exibir os tipos de coisas web é melhor como rich text e layout responsivo. Por exemplo, no entanto, nem todas as tarefas são adequadas para HTML e Javascript – rolar por listas longas de dados, executa melhor usando controles de interface do usuário nativo como (UITableView no iOS) ou ListView no Android.

Os modos de exibição da web no modelo podem ser aumentados facilmente com controles específicos de plataforma – simplesmente editar os **MainStoryboard.storyboard** no designer de iOS ou **Resources/layout/Main.axml** no Android.

### <a name="razortodo-sample"></a>RazorTodo Sample

O [RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo) repositório contém duas soluções separadas para mostrar as diferenças entre um aplicativo totalmente controlados por HTML e um aplicativo que combina o HTML com controles nativos:

-  **RazorTodo** -aplicativo orientado completamente HTML usando modelos Razor.
-  **RazorNativeTodo** - usa os controles de exibição de lista nativa para iOS e Android, mas exibe a tela de edição com HTML e Razor.


Esses aplicativos Xamarin executados em iOS e Android, utilizando as bibliotecas de classes portáteis (PCLs) para compartilhar código comum, como as classes de banco de dados e modelo. Razor **. cshtml** modelos também podem ser incluídos no PCL para facilmente compartilhado entre plataformas.

Os dois aplicativos de exemplo incorporam o compartilhamento do Twitter e texto em fala APIs da plataforma nativo, demonstrando que aplicativos híbridos com o Xamarin ainda têm o acesso a toda a funcionalidade subjacente de modos de exibição de HTML do Razor controlado por modelo.

O **RazorTodo** aplicativo usa modelos de HTML do Razor para os modos de exibição de lista e editar. Isso significa que é possível criar o aplicativo quase que totalmente em uma biblioteca de classes portátil compartilhado (incluindo o banco de dados e **. cshtml** modelos Razor). Capturas de tela abaixo mostram a aplicativos iOS e Android.

 ![RazorTodo](images/Both_700x290.png)

O **RazorNativeTodo** aplicativo usa um modelo de HTML do Razor para o modo de exibição de edição, mas implementa uma lista de rolagem nativo em cada plataforma. Isso fornece uma série de benefícios incluem:

-  Desempenho - os controles de rolagem nativo usam virtualização para garantir a perfeita rolagem mesmo com muito listas de dados.
-  Experiência nativa - elementos de interface do usuário específica de plataforma são facilmente habilitada, como o suporte à rolagem fast índice no iOS e Android.


Uma vantagem importante da criação de aplicativos híbridos com o Xamarin é que você pode iniciar com uma interface de usuário totalmente controlados por HTML (como o primeiro exemplo) e, em seguida, adiciona a funcionalidade de plataforma específica quando necessário (como o segundo exemplo mostra). As telas de lista nativo e HTML do Razor editar telas no iOS e Android são mostrados abaixo.

 ![RazorNativeTodo](images/BothNative_700x290.png)

## <a name="summary"></a>Resumo

Este artigo explicou os recursos dos controles de exibição da web disponíveis no iOS e Android que facilitam a construção aplicativos híbridos.

Ele discutido, em seguida, o mecanismo de modelagem Razor e a sintaxe que pode ser usada para gerar HTML facilmente em aplicativos Xamarin usando. **cshtml** arquivos de modelo do Razor. Ele também descrito do Visual Studio para Mac os modelos de solução que permitem que você rapidamente começar a criar aplicativos híbridos com o Xamarin.

Finalmente, ele introduzido RazorTodo exemplos que demonstram como combinar os modos de exibição da web com as APIs e interfaces de usuário nativo.

### <a name="related-links"></a>Links relacionados

- [RazorTodo Sample](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)
- [MVC 3 - mecanismo de exibição Razor (Microsoft)](http://www.asp.net/mvc/videos/mvc-3/mvc-3-razor-view-engine)
- [Introdução à programação da Web do ASP.NET usando a sintaxe do Razor (Microsoft)](http://www.asp.net/web-pages/tutorials/basics/2-introduction-to-asp-net-web-programming-using-the-razor-syntax)