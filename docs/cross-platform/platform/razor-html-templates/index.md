---
title: Modos de exibição HTML de construção usando modelos do Razor
description: " Usar uma página da Web em tela inteira para renderizar o HTML pode ser uma maneira simple e eficiente para renderizar a formatação complexa de uma maneira de plataforma cruzada, especialmente se você já tiver o HTML, Javascript e CSS de um projeto de site."
ms.prod: xamarin
ms.assetid: D8B87C4F-178E-48D9-BE43-85066C46F05C
author: asb3993
ms.author: amburns
ms.date: 07/24/2018
ms.openlocfilehash: 7e569aaddef912d9534e98f2f987ad5dfca8a5a6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61167915"
---
# <a name="building-html-views-using-razor-templates"></a>Modos de exibição HTML de construção usando modelos do Razor

No mundo do desenvolvimento móvel o termo "aplicativo de híbrida" geralmente se refere a um aplicativo que apresenta algumas (ou todos) de suas telas, como páginas HTML dentro de um controle de Visualizador da web hospedados.

Há alguns ambientes de desenvolvimento que permitem que você crie seu aplicativo móvel inteiramente em HTML e Javascript, no entanto, esses aplicativos podem sofrer problemas de desempenho durante a tentativa de realizar processamento complexo ou efeitos de interface do usuário e são também está limitada à plataforma eles podem acessar os recursos.

O Xamarin oferece o melhor dos dois mundos, especialmente ao utilizar o mecanismo de modelagem de HTML do Razor. Com o Xamarin, você tem a flexibilidade para criar exibições HTML com modelo multiplataforma que usam Javascript e CSS, mas também têm acesso completo a APIs da plataforma subjacente e processamento rápido usando a linguagem c#.

Este documento explica como usar o Razor modos de exibição HTML + Javascript + CSS que podem ser usados em plataformas móveis usando o Xamarin de compilação do mecanismo de modelagem.

## <a name="using-web-views-programmatically"></a>Usando modos de exibição da Web por meio de programação

Antes que aprendemos Razor esta seção aborda como usar os modos de exibição da web para exibir o conteúdo HTML diretamente – especificamente que o conteúdo HTML é gerado dentro de um aplicativo.

O Xamarin fornece acesso completo a APIs da plataforma subjacente no iOS e Android, portanto, é fácil criar e exibir HTML usando a linguagem c#. A sintaxe básica para cada plataforma é mostrada abaixo.

### <a name="ios"></a>iOS

Também exibem o HTML em um controle UIWebView no xamarin. IOS leva apenas algumas linhas de código:

```csharp
var webView = new UIWebView (View.Bounds);
View.AddSubview(webView);
string contentDirectoryPath = Path.Combine (NSBundle.MainBundle.BundlePath, "Content/");
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadHtmlString(html, NSBundle.MainBundle.BundleUrl);
```

Consulte a [iOS UIWebView](http://docs.xamarin.com/recipes/ios/content_controls/web_view/) receitas para obter mais detalhes sobre como usar o controle UIWebView.

### <a name="android"></a>Android

Exibindo o HTML em um controle de exibição da Web usando o xamarin. Android é realizada em apenas algumas linhas de código:

```csharp
// webView is declared in an AXML layout file
var webView = FindViewById<WebView> (Resource.Id.webView);

// enable Javascript execution in your html view so you can provide "alerts" and other js
webView.SetWebChromeClient(new WebChromeClient());

var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadDataWithBaseURL("file:///android_asset/", html, "text/html", "UTF-8", null);
```

Consulte a [Android WebView](http://docs.xamarin.com/recipes/android/controls/webview/) receitas para obter mais detalhes sobre como usar o controle WebView.

### <a name="specifying-the-base-directory"></a>Especificar o diretório Base

Nas duas plataformas, há um parâmetro que especifica o diretório base para a página HTML. Esse é o local no sistema de arquivos do dispositivo que é usado para resolver referências relativas a recursos como imagens e arquivos CSS. Por exemplo, como marcas

```html
<link rel="stylesheet" href="style.css" />
<img src="monkey.jpg" />
<script type="text/javascript" src="jscript.js">
```

referência a esses arquivos: **Style. CSS**, **monkey.jpg** e **jscript.js**. A configuração do diretório base informa à exibição da web onde esses arquivos estão localizados para que possam ser carregados na página.

#### <a name="ios"></a>iOS

A saída do modelo é processada no iOS com o código c# a seguir:

```csharp
webView.LoadHtmlString (page, NSBundle.MainBundle.BundleUrl);
```

O diretório base é especificado como `NSBundle.MainBundle.BundleUrl` que refere-se para o diretório que o aplicativo é instalado. Todos os arquivos a **recursos** pasta são copiados para esse local, como o **Style. CSS** arquivo mostrado aqui:

 ![solução de iPhoneHybrid](images/image1_240x163.png)

A ação de compilação para todos os arquivos de conteúdo estáticos deve ser **BundleResource**:

 ![ação de build do projeto iOS: BundleResource](images/image2_250x131.png)

#### <a name="android"></a>Android

O Android também exige um diretório base a ser passado como um parâmetro quando cadeias de caracteres html são exibidas em uma exibição da web.

```csharp
webView.LoadDataWithBaseURL("file:///android_asset/", page, "text/html", "UTF-8", null);
```

A cadeia de caracteres especial **file:///android_asset/** refere-se à pasta ativos do Android em seu aplicativo, mostrada aqui que contém o **Style. CSS** arquivo.

 ![Solução de AndroidHybrid](images/image3_240x167.png)

A ação de compilação para todos os arquivos de conteúdo estáticos deve ser **AndroidAsset**.

 ![Ação de build do projeto do Android: AndroidAsset](images/image4_250x71.png)

### <a name="calling-c-from-html-and-javascript"></a>Chamando c# de HTML e Javascript

Quando uma página html é carregada em uma exibição da web, ele trata os links e formulários como faria se a página foi carregada de um servidor. Isso significa que, se o usuário clica em um link ou envia um formulário de exibição da web tentarão navegar até o destino especificado.

Se o link é para um servidor externo (por exemplo, google.com), em seguida, a exibição da web tenta carregar o site externo (supondo que haja uma conexão de internet).

```html
<a href="http://google.com/">Google</a>
```

Se o link é relativo a exibição da web tentará carregar esse conteúdo do diretório base. Obviamente, nenhuma conexão de rede é necessária para este trabalho, como o conteúdo é armazenado no aplicativo no dispositivo.

```html
<a href="somepage.html">Local content</a>
```

Ações do formulário seguem a mesma regra.

```html
<form method="get" action="http://google.com/"></form>
<form method="get" action="somepage.html"></form>
```

Você não vai para hospedar um servidor web no cliente; No entanto, você pode usar as mesmas técnicas de comunicação de servidor empregadas em padrões de design responsivo de hoje para chamar serviços sobre HTTP GET e manipular as respostas de forma assíncrona pela emissão do Javascript (ou chamada Javascript já hospedado no modo de exibição da web). Isso permite que você passar facilmente os dados do HTML volta ao código c# para processamento, em seguida, exibir que os resultados de volta na página HTML.

IOS e Android fornecem um mecanismo para o código do aplicativo para interceptar esses eventos de navegação, de modo que o código do aplicativo pode responder (se necessário). Esse recurso é crucial para criar aplicativos híbridos, porque ele permite que o código nativo interagem com a exibição da web.

#### <a name="ios"></a>iOS

O evento ShouldStartLoad na exibição da web no iOS pode ser substituído para permitir que o código do aplicativo para lidar com uma solicitação de navegação (como um clique de link). Os parâmetros do método fornecem todas as informações

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
    public override bool ShouldOverrideUrlLoading (WebView webView, IWebResourceRequest request) {
        // return true if handled in code
        // return false to let the web view follow the link
    }
}
```

e, em seguida, defina o cliente no modo de exibição da web:

```csharp
webView.SetWebViewClient (new HybridWebViewClient ());
```

### <a name="calling-javascript-from-c"></a>Chamada de Javascript do c#

Além de dizer uma exibição da web para carregar uma nova página HTML, o código c# também pode executar Javascript dentro da página exibida atualmente. Todos blocos de código Javascript podem ser criados usando cadeias de caracteres em C# e executados ou você pode criar Javascript já disponível na página por meio de chamadas de método `script` marcas.

#### <a name="android"></a>Android

Criar o código Javascript para ser executado e, em seguida, coloque o prefixo "javascript:" e instruir a exibição da web para carregar essa cadeia de caracteres:

```csharp
var js = "alert('test');";
webView.LoadUrl ("javascript:" + js);
```

#### <a name="ios"></a>iOS

modos de exibição do iOS web fornecem um método especificamente para chamar o Javascript:

```csharp
var js = "alert('test');";
webView.EvaluateJavascript (js);
```

### <a name="summary"></a>Resumo

Esta seção apresentou os recursos dos controles de exibição da web no Android e iOS que nos permite criar aplicativos híbridos com o Xamarin, incluindo:

-  A capacidade de carregar o HTML de cadeias de caracteres geradas no código,
-  A capacidade de fazer referência a arquivos locais (CSS, Javascript, imagens ou outros arquivos HTML),
-  A capacidade de interceptar solicitações de navegação no código c#
-  A capacidade de chamar Javascript de código c#.


A próxima seção apresenta Razor, o que torna mais fácil de criar o HTML para usar em aplicativos híbridos.

## <a name="what-is-razor"></a>O que é o Razor?

Razor é um mecanismo de modelagem que foi introduzido com o ASP.NET MVC, originalmente para executar no servidor e gerar HTML a serem atendidos para navegadores da web.

O mecanismo de modelagem do Razor estende a sintaxe HTML padrão com o c# para que você pode expressar o layout e incorporar folhas de estilo CSS e Javascript com facilidade. O modelo pode fazer referência a uma classe de modelo, que pode ser qualquer tipo personalizado e cujas propriedades podem ser acessadas diretamente a partir do modelo. Uma das suas principais vantagens é a capacidade de misturar sintaxe HTML e c# com facilidade.

Modelos do Razor não estão limitados ao uso do lado do servidor, eles também podem ser incluídos em aplicativos Xamarin. Usando modelos de Razor junto com a capacidade de trabalhar com exibições da web por meio de programação permite que os aplicativos híbridos de plataforma cruzada sofisticada ser criados com o Xamarin.

### <a name="razor-template-basics"></a>Noções básicas dos modelos do Razor

Arquivos de modelo do Razor têm uma **. cshtml** extensão de arquivo. Eles podem ser adicionados a um projeto do Xamarin na seção na modelagem de texto a **novo arquivo** caixa de diálogo:

 ![Novo arquivo - modelo Razor](images/image5_400x201.png)

Um modelo Razor simples ( **RazorView.cshtml**) é mostrado abaixo.

```html
@model string
<html>
    <body>
    <h1>@Model</h1>
    </body>
</html>
```

Observe as seguintes diferenças de um arquivo HTML regular:

-  O `@` símbolo tem um significado especial em modelos do Razor – indica que a expressão a seguir é c# a ser avaliada.
- `@model` diretiva sempre aparece como a primeira linha de um arquivo de modelo do Razor.
-  O `@model` diretiva deve ser seguida por um tipo. Neste exemplo, uma simple cadeia de caracteres está sendo passada para o modelo, mas isso pode ser qualquer classe personalizada.
-  Quando `@Model` é referenciada em todo o modelo, ele fornece uma referência para o objeto passado para o modelo quando ele é gerado (neste exemplo, ele será uma cadeia de caracteres).
-  O IDE gerará automaticamente uma classe parcial para modelos (arquivos com o **. cshtml** extensão). Você pode exibir esse código, mas não deve ser editado.
 ![RazorView.cshtml](images/image6_125x34.png) a classe parcial é chamada RazorView para corresponder ao nome do arquivo de modelo. cshtml. É este nome que é usado para se referir ao modelo em código c#.
- `@using` instruções também podem ser incluídas na parte superior de um modelo do Razor para incluir namespaces adicionais.


A saída HTML final, em seguida, pode ser gerada com o código c# a seguir. Observe que especificamos o modelo para ser uma cadeia de caracteres "Hello World" que será incorporado a saída renderizada de modelo.

```csharp
var template = new RazorView () { Model = "Hello World" };
var page = template.GenerateString ();
```

Aqui está a saída mostrada em uma exibição da web no iOS Simulator e o emulador do Android:

 ![Hello World](images/image7_523x135.png)

### <a name="more-razor-syntax"></a>Mais sintaxe do Razor

Nesta seção, vamos apresentar uma sintaxe Razor básica para ajudá-lo a começar a usá-lo. Os exemplos nesta seção preencher a classe a seguir com os dados e exibem-lo usando o Razor:

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

Quando o modelo é uma classe com propriedades, elas são facilmente referenciadas no modelo do Razor conforme mostrado neste modelo de exemplo:

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    </body>
</html>
```

Isso pode ser processado para uma cadeia de caracteres usando o seguinte código:

```csharp
var template = new RazorView () { Model = animal };
var page = template.GenerateString ();
```

A saída final é mostrada aqui em uma exibição da web no iOS Simulator e o emulador do Android:

 ![Rupert](images/image8_516x160.png)

#### <a name="c-statements"></a>Instruções em c#

C# mais complexos podem ser incluídos no modelo, como as atualizações de propriedade do modelo e o cálculo da idade neste exemplo:

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

Você pode escrever uma linha c# expressões complexas (como a formatação a idade), envolvendo o código com `@()`.

Várias instruções em c# podem ser gravadas ao colocá-los com `@{}`.

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

Um loop construtos como `foreach` também podem ser adicionados. O `@` prefixo pode ser usado na variável de loop ( `@food` neste caso) para renderizá-lo em HTML.

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

A saída do modelo acima é mostrada em execução no iOS Simulator e o emulador do Android:

 ![Rupert X Monkey](images/image9_520x277.png)

Esta seção abordou os conceitos básicos do uso de modelos do Razor para renderizar exibições simples de somente leitura. A próxima seção explica como criar aplicativos mais completos usando o Razor que podem aceitar a entrada do usuário e interoperar entre Javascript na exibição HTML e c#.

## <a name="using-razor-templates-with-xamarin"></a>Usando modelos do Razor com o Xamarin

Esta seção explica como usar o build seu próprio aplicativo híbrido usando os modelos de solução no Visual Studio para Mac. Há três modelos disponíveis do **arquivo > Novo > solução...**  janela:

- **Android > aplicativo > aplicativo Android WebView**
- **iOS > aplicativo > aplicativo WebView**
- **Projeto ASP.NET MVC**



O **nova solução** janela tem esta aparência para iPhone e projetos do Android – a descrição da solução à direita destaca o suporte para o mecanismo de modelagem do Razor.

 ![Criando o iPhone e Android soluções](images/image13_1139x959.png)

Observe que você pode adicionar facilmente uma **. cshtml** modelo Razor *qualquer* existente projeto Xamarin, não é necessário usar esses modelos de solução. projetos do iOS não exigem um Storyboard para usar o Razor. Basta adicionar um controle UIWebView para qualquer modo de exibição por meio de programação e você pode renderizar os modelos Razor inteiro em código c#.

O conteúdo de solução de modelo padrão para projetos Android e iPhone é mostrado abaixo:

 ![iPhone e Android modelos](images/image10_428x310.png)

Os modelos oferecem infraestrutura de aplicativo pronto para ir para carregar um modelo do Razor com um objeto de modelo de dados, processar entradas do usuário e se comunicar com o usuário por meio de Javascript.

As partes importantes da solução são:

-  Conteúdo estático, como o **Style. CSS** arquivo.
-  Como arquivos de modelo do Razor. cshtml **RazorView.cshtml** .
-  Classes que são referenciadas nos modelos do Razor, como de modelo **ExampleModel.cs** .
-  A classe específica da plataforma que cria a exibição da web e renderiza o modelo, como o `MainActivity` no Android e o `iPhoneHybridViewController` no iOS.


A seção a seguir explica como funcionam os projetos.

### <a name="static-content"></a>Conteúdo Estático

Conteúdo estático inclui as folhas de estilos CSS, imagens, arquivos Javascript ou outros tipos de conteúdo que podem ser vinculado a partir de ou referenciado por um arquivo HTML que está sendo exibido em uma exibição da web.

Os projetos de modelo incluem um mínimo de estilos para demonstrar como incluir conteúdo estático em seu aplicativo híbrido. A folha de estilos CSS é referenciada no modelo como este:

```html
<link rel="stylesheet" href="style.css" />
```

Você pode adicionar qualquer folha de estilo e arquivos Javascript que você precisa, incluindo estruturas como o JQuery.

### <a name="razor-cshtml-templates"></a>Cshtml Razor modelos

O modelo inclui um Razor **. cshtml** arquivo que já escreveu o código para ajudar a comunicar dados entre o HTML/Javascript e c#. Isso permitirá que você construir aplicativos híbridos sofisticados que não apenas exibir dados somente leitura a partir do modelo, mas também aceita entrada do usuário em HTML e passá-lo de volta para o código c# para processamento ou armazenamento.

#### <a name="rendering-the-template"></a>O modelo de renderização

Chamar o `GenerateString` em um modelo renderiza HTML pronto para exibição em uma exibição da web. Se o modelo usa um modelo deve ser fornecido antes da renderização. Este diagrama ilustra como renderização funciona – não que os recursos estáticos são resolvidos pelo modo de exibição da web em tempo de execução, usando o diretório base fornecido para localizar os arquivos especificados.

 ![Fluxograma do Razor](images/image12_700x421.png)

#### <a name="calling-c-code-from-the-template"></a>Chamando código em c# do modelo

A comunicação de um modo de exibição web renderizado retornos de chamada para o c# é feita definindo a URL para a exibição da web e, em seguida, interceptação da solicitação em c# para manipular a solicitação nativa sem recarregar a exibição da web.

Um exemplo pode ser visto em como o botão do RazorView é manipulado. O botão tem o seguinte HTML:

```html
<input type="button" name="UpdateLabel" value="Click" onclick="InvokeCSharpWithFormValues(this)" />
```

O `InvokeCSharpWithFormValues` função Javascript lê todos os valores do formulário HTML e conjuntos de `location.href` para a exibição da web:

```javascript
location.href = "hybrid:" + elm.name + "?" + qs;
```

Isso tenta navegar a exibição da web para uma URL com um esquema personalizado (por exemplo. `hybrid:`)

```
hybrid:UpdateLabel?textbox=SomeValue&UpdateLabel=Click
```

Quando a exibição da web nativos processa a solicitação de navegação, temos a oportunidade de interceptar a ele. No iOS, isso é feito pela manipulação do evento de HandleShouldStartLoad do UIWebView. No Android, podemos simplesmente subclasse o WebViewClient usado no formulário e substituir ShouldOverrideUrlLoading.

As partes internas desses dois interceptores de navegação é essencialmente o mesmo.

Primeiro, verifique a URL de exibição da web está tentando carregar, e se ele não começa com o esquema personalizado (`hybrid:`), permitir a navegação ocorrer como normal.

Para o esquema de URL personalizado, tudo na URL entre o esquema e o "?" é o nome do método a ser manipulado (nesse caso, "UpdateLabel"). Tudo o que a cadeia de caracteres de consulta será tratado como os parâmetros para a chamada de método:

```csharp
var resources = url.Substring(scheme.Length).Split('?');
var method = resources [0];
var parameters = System.Web.HttpUtility.ParseQueryString(resources[1]);
```

`UpdateLabel` Este exemplo faz uma quantidade mínima de manipulação de cadeia de caracteres no parâmetro de caixa de texto (acrescentando "c# diz" na cadeia de caracteres) e, em seguida, chama de volta para a exibição da web.

Após lidar com a URL, o método anula a navegação para que a exibição da web não tenta concluir navegando até a URL personalizada.

#### <a name="manipulating-the-template-from-c"></a>Manipulando o modelo do c#

Comunicação com uma exibição da web em HTML renderizada do c# é feita chamando-se Javascript na exibição da web. No iOS, isso é feito chamando `EvaluateJavascript` sobre o UIWebView:

```csharp
webView.EvaluateJavascript (js);
```

No Android, Javascript pode ser invocado no modo de exibição da web com o carregamento de Javascript como uma URL usando o `"javascript:"` esquema de URL:

```csharp
webView.LoadUrl ("javascript:" + js);
```

## <a name="making-an-app-truly-hybrid"></a>Criação de aplicativos verdadeiramente híbrido

Esses modelos não fazem uso de controles nativos em cada plataforma – tela inteira é preenchida com uma exibição única web.

HTML pode ser ótima para a criação de protótipos e exibir os tipos de coisas na web é melhor como rich text e layout dinâmico. Por exemplo, no entanto, nem todas as tarefas são adequadas para HTML e Javascript – rolar por listas longas de dados, executa melhor usando controles nativos da interface do usuário como (UITableView no iOS) ou ListView no Android.

Os modos de exibição da web no modelo podem ser aumentados facilmente com controles específicos da plataforma – simplesmente editar os **Mainstoryboard** no designer do iOS ou o **Resources/layout/Main.axml** no Android.

### <a name="razortodo-sample"></a>Exemplo de RazorTodo

O [RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo) repositório contém duas soluções separadas para mostrar as diferenças entre um aplicativo totalmente baseado em HTML e um aplicativo que combina o HTML com controles nativos:

-  **RazorTodo** -aplicativo controlado por HTML completamente usando modelos do Razor.
-  **RazorNativeTodo** - usa controles de exibição de lista nativo para iOS e Android, mas exibe a tela de edição com HTML e Razor.


Execute esses aplicativos Xamarin no iOS e Android, utilizando as bibliotecas de classes portáteis (PCLs) para compartilhar código comum, como as classes de banco de dados e o modelo. Razor **. cshtml** modelos também podem ser incluídos na PCL para que eles são facilmente compartilhados entre plataformas.

Ambos os aplicativos de exemplo incorporam o compartilhamento do Twitter e o texto em fala APIs da plataforma nativa, demonstrando que aplicativos híbridos com o Xamarin ainda terá o acesso a toda a funcionalidade subjacente de modos de exibição HTML do Razor controlado por modelo.

O **RazorTodo** aplicativo usa modelos HTML do Razor para os modos de exibição de lista e editar. Isso significa que podemos criar o aplicativo quase que totalmente em uma biblioteca de classes portátil compartilhado (incluindo o banco de dados e **. cshtml** modelos do Razor). As capturas de tela abaixo mostram a aplicativos iOS e Android.

 ![RazorTodo](images/Both_700x290.png)

O **RazorNativeTodo** aplicativo usa um modelo HTML do Razor para a exibição de edição, mas implementa uma lista de rolagem nativa em cada plataforma. Isso fornece inúmeros benefícios, incluindo:

-  Desempenho – os controles nativos de rolagem usam virtualização para garantir a rápida e suave de rolagem, mesmo com muito longas listas de dados.
-  Experiência nativa - elementos de interface do usuário específico da plataforma facilmente são habilitados, como o suporte a índice rolagem rápida no iOS e Android.


Dos principais benefícios da criação de aplicativos híbridos com o Xamarin é que você pode iniciar com uma interface de usuário totalmente orientado a HTML (como o primeiro exemplo) e, em seguida, adicionar funcionalidade específica da plataforma quando necessário (como o segundo aparecerá como exemplo). As telas de lista nativo e o HTML do Razor editar telas em iOS e Android são mostrados abaixo.

 ![RazorNativeTodo](images/BothNative_700x290.png)

## <a name="summary"></a>Resumo

Este artigo explicou os recursos dos controles de exibição da web disponíveis no iOS e Android que facilitam a criação de aplicativos híbridos.

Ele discutidos, em seguida, o mecanismo de modelagem do Razor e a sintaxe que pode ser usada para gerar HTML facilmente em aplicativos do Xamarin usando o. **cshtml** arquivos de modelo do Razor. Também descreveu o Visual Studio para Mac, os modelos de solução que permitem que você rapidamente começar a compilar aplicativos híbridos com o Xamarin.

Por fim, ele introduziu RazorTodo exemplos que demonstram como combinar os modos de exibição da web com as APIs e interfaces do usuário nativas.

### <a name="related-links"></a>Links relacionados

- [RazorTodo Sample](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)
- [MVC 3 – mecanismo de exibição do Razor (Microsoft)](http://www.asp.net/mvc/videos/mvc-3/mvc-3-razor-view-engine)
- [Introdução à programação Web do ASP.NET usando a sintaxe do Razor (Microsoft)](http://www.asp.net/web-pages/tutorials/basics/2-introduction-to-asp-net-web-programming-using-the-razor-syntax)
