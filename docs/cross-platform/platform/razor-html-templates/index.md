---
title: Criando exibições HTML usando modelos do Razor
description: " Usar uma página da Web de tela inteira para renderizar HTML pode ser uma maneira simples e eficaz de processar formatação complexa em uma forma de plataforma cruzada, especialmente se você já tiver o HTML, JavaScript e CSS de um projeto de site."
ms.prod: xamarin
ms.assetid: D8B87C4F-178E-48D9-BE43-85066C46F05C
author: conceptdev
ms.author: crdun
ms.date: 07/24/2018
ms.openlocfilehash: ccad60f749732ae2d0bf8e9852859b13af3a629e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284915"
---
# <a name="building-html-views-using-razor-templates"></a>Criando exibições HTML usando modelos do Razor

No mundo de desenvolvimento móvel, o termo "aplicativo híbrido" normalmente se refere a um aplicativo que apresenta algumas (ou todas) suas telas como páginas HTML dentro de um controle do Visualizador da Web hospedado.

Há alguns ambientes de desenvolvimento que permitem que você crie seu aplicativo móvel inteiramente em HTML e JavaScript, no entanto, esses aplicativos podem sofrer problemas de desempenho ao tentar realizar processamento complexo ou efeitos de interface do usuário e também são limitados na plataforma recursos que eles podem acessar.

O Xamarin oferece o melhor dos dois mundos, especialmente ao utilizar o mecanismo de modelagem HTML do Razor. Com o Xamarin, você tem a flexibilidade de criar exibições HTML modeladas de plataforma cruzada que usam JavaScript e CSS, mas também têm acesso completo às APIs de plataforma C#subjacentes e ao processamento rápido usando o.

Este documento explica como usar o mecanismo de modelagem do Razor para criar modos de exibição HTML + JavaScript + CSS que podem ser usados em plataformas móveis usando o Xamarin.

## <a name="using-web-views-programmatically"></a>Usando exibições da Web programaticamente

Antes de aprendermos sobre o Razor, esta seção aborda como usar exibições da Web para exibir conteúdo HTML diretamente – especificamente o conteúdo HTML que é gerado em um aplicativo.

O Xamarin fornece acesso completo às APIs de plataforma subjacentes no iOS e no Android, portanto, é fácil criar e exibir HTML C#usando. A sintaxe básica para cada plataforma é mostrada abaixo.

### <a name="ios"></a>iOS

A exibição de HTML em um controle UIWebView no Xamarin. iOS também leva apenas algumas linhas de código:

```csharp
var webView = new UIWebView (View.Bounds);
View.AddSubview(webView);
string contentDirectoryPath = Path.Combine (NSBundle.MainBundle.BundlePath, "Content/");
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadHtmlString(html, NSBundle.MainBundle.BundleUrl);
```

Consulte as receitas do [Ios UIWebView](http://docs.xamarin.com/recipes/ios/content_controls/web_view/) para obter mais detalhes sobre como usar o controle UIWebView.

### <a name="android"></a>Android

A exibição de HTML em um controle WebView usando o Xamarin. Android é realizada em apenas algumas linhas de código:

```csharp
// webView is declared in an AXML layout file
var webView = FindViewById<WebView> (Resource.Id.webView);

// enable JavaScript execution in your html view so you can provide "alerts" and other js
webView.SetWebChromeClient(new WebChromeClient());

var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadDataWithBaseURL("file:///android_asset/", html, "text/html", "UTF-8", null);
```

Consulte as receitas da [WebView do Android](http://docs.xamarin.com/recipes/android/controls/webview/) para obter mais detalhes sobre como usar o controle WebView.

### <a name="specifying-the-base-directory"></a>Especificando o diretório base

Em ambas as plataformas, há um parâmetro que especifica o diretório base para a página HTML. Esse é o local no sistema de arquivos do dispositivo que é usado para resolver referências relativas a recursos como imagens e arquivos CSS. Por exemplo, marcas como

```html
<link rel="stylesheet" href="style.css" />
<img src="monkey.jpg" />
<script type="text/javascript" src="jscript.js">
```

consulte estes arquivos: **Style. css**, **macaco. jpg** e **JScript. js**. A configuração do diretório base informa à exibição da Web onde esses arquivos estão localizados para que possam ser carregados na página.

#### <a name="ios"></a>iOS

A saída do modelo é renderizada em iOS com C# o seguinte código:

```csharp
webView.LoadHtmlString (page, NSBundle.MainBundle.BundleUrl);
```

O diretório base é especificado como `NSBundle.MainBundle.BundleUrl` se refere ao diretório no qual o aplicativo está instalado. Todos os arquivos na pasta **recursos** são copiados para esse local, como o arquivo **Style. css** mostrado aqui:

 ![solução iPhoneHybrid](images/image1_240x163.png)

A ação de compilação para todos os arquivos de conteúdo estático deve ser **BundleResource**:

 ![ação de compilação do projeto do iOS: BundleResource](images/image2_250x131.png)

#### <a name="android"></a>Android

O Android também exige que um diretório base seja passado como um parâmetro quando as cadeias de caracteres HTML são exibidas em uma exibição da Web.

```csharp
webView.LoadDataWithBaseURL("file:///android_asset/", page, "text/html", "UTF-8", null);
```

A cadeia de caracteres especial **file:///android_asset/** refere-se à pasta de ativos do Android em seu aplicativo, mostrada aqui contendo o arquivo **Style. css** .

 ![Solução AndroidHybrid](images/image3_240x167.png)

A ação de Build para todos os arquivos de conteúdo estático deve ser **AndroidAsset**.

 ![Ação de compilação do projeto Android: AndroidAsset](images/image4_250x71.png)

### <a name="calling-c-from-html-and-javascript"></a>Chamando C# a partir de HTML e JavaScript

Quando uma página HTML é carregada em uma exibição da Web, ela trata os links e os formulários como faria se a página fosse carregada a partir de um servidor. Isso significa que, se o usuário clicar em um link ou enviar um formulário, a exibição da Web tentará navegar até o destino especificado.

Se o link for para um servidor externo (como google.com), a exibição da Web tentará carregar o site externo (supondo que haja uma conexão com a Internet).

```html
<a href="http://google.com/">Google</a>
```

Se o link for relativo, a exibição da Web tentará carregar esse conteúdo do diretório base. Obviamente, nenhuma conexão de rede é necessária para que isso funcione, pois o conteúdo é armazenado no aplicativo no dispositivo.

```html
<a href="somepage.html">Local content</a>
```

As ações de formulário seguem a mesma regra.

```html
<form method="get" action="http://google.com/"></form>
<form method="get" action="somepage.html"></form>
```

Você não vai hospedar um servidor Web no cliente; no entanto, você pode usar as mesmas técnicas de comunicação de servidor empregadas nos padrões de design responsivos atuais para chamar serviços por HTTP GET e manipular respostas de forma assíncrona emitindo JavaScript (ou chamando JavaScript já hospedado na exibição da Web). Isso permite que você passe facilmente os dados do HTML de volta C# para o código para processamento e, em seguida, exiba os resultados de volta na página HTML.

O iOS e o Android fornecem um mecanismo para o código do aplicativo interceptar esses eventos de navegação para que o código do aplicativo possa responder (se necessário). Esse recurso é crucial para a criação de aplicativos híbridos porque permite que o código nativo interaja com a exibição da Web.

#### <a name="ios"></a>iOS

O evento ShouldStartLoad na exibição da Web no iOS pode ser substituído para permitir que o código do aplicativo manipule uma solicitação de navegação (como um clique de link). Os parâmetros do método fornecem todas as informações

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

No Android, basta WebViewClient a subclasse e, em seguida, implementar o código para responder à solicitação de navegação.

```csharp
class HybridWebViewClient : WebViewClient {
    public override bool ShouldOverrideUrlLoading (WebView webView, IWebResourceRequest request) {
        // return true if handled in code
        // return false to let the web view follow the link
    }
}
```

em seguida, defina o cliente na exibição da Web:

```csharp
webView.SetWebViewClient (new HybridWebViewClient ());
```

### <a name="calling-javascript-from-c"></a>Chamando JavaScript a partir de C\#

Além de informar uma exibição da Web para carregar uma nova página HTML, C# o código também pode executar JavaScript na página atualmente exibida. Blocos de código JavaScript inteiros podem ser criados C# usando cadeias de caracteres e executados, ou você pode criar chamadas de método para o JavaScript `script` já disponível na página por meio de marcas.

#### <a name="android"></a>Android

Crie o código JavaScript a ser executado e, em seguida, Prefixe-o com "JavaScript:" e instrua a exibição da Web para carregar essa cadeia de caracteres:

```csharp
var js = "alert('test');";
webView.LoadUrl ("javascript:" + js);
```

#### <a name="ios"></a>iOS

exibições da Web do iOS fornecem um método especificamente para chamar JavaScript:

```csharp
var js = "alert('test');";
webView.EvaluateJavascript (js);
```

### <a name="summary"></a>Resumo

Esta seção introduziu os recursos dos controles de exibição da Web no Android e no iOS que nos permitem criar aplicativos híbridos com o Xamarin, incluindo:

- A capacidade de carregar HTML de cadeias de caracteres geradas no código,
- A capacidade de fazer referência a arquivos locais (CSS, JavaScript, imagens ou outros arquivos HTML),
- A capacidade de interceptar solicitações de C# navegação no código,
- A capacidade de chamar JavaScript a C# partir do código.


A próxima seção apresenta o Razor, que torna mais fácil criar o HTML a ser usado em aplicativos híbridos.

## <a name="what-is-razor"></a>O que é o Razor?

O Razor é um mecanismo de modelagem que foi introduzido com o ASP.NET MVC, originalmente para execução no servidor e geração de HTML para ser servido em navegadores da Web.

O mecanismo de modelagem do Razor estende a sintaxe C# HTML padrão com para que você possa expressar o layout e incorporar folhas de estilo CSS e JavaScript com facilidade. O modelo pode fazer referência a uma classe de modelo, que pode ser qualquer tipo personalizado e cujas propriedades possam ser acessadas diretamente do modelo. Uma de suas principais vantagens é a capacidade de misturar HTML e C# sintaxe com facilidade.

Os modelos do Razor não estão limitados ao uso do lado do servidor, eles também podem ser incluídos em aplicativos Xamarin. Usar modelos do Razor junto com a capacidade de trabalhar com exibições da Web programaticamente permite que aplicativos híbridos entre plataformas sofisticados sejam criados com o Xamarin.

### <a name="razor-template-basics"></a>Noções básicas do modelo Razor

Os arquivos de modelo do Razor têm uma extensão de arquivo **. cshtml** . Eles podem ser adicionados a um projeto do Xamarin da seção modelagem de texto na caixa de diálogo **novo arquivo** :

 ![Novo arquivo-modelo do Razor](images/image5_400x201.png)

Um modelo Razor simples ( **RazorView. cshtml**) é mostrado abaixo.

```html
@model string
<html>
    <body>
    <h1>@Model</h1>
    </body>
</html>
```

Observe as seguintes diferenças de um arquivo HTML regular:

- O `@` símbolo tem um significado especial em modelos do Razor – ele indica que a expressão C# a seguir deve ser avaliada.
- `@model`a diretiva sempre aparece como a primeira linha de um arquivo de modelo do Razor.
- A `@model` diretiva deve ser seguida por um tipo. Neste exemplo, uma cadeia de caracteres simples está sendo passada para o modelo, mas pode ser qualquer classe personalizada.
- Quando `@Model` é referenciado em todo o modelo, ele fornece uma referência ao objeto passado para o modelo quando ele é gerado (neste exemplo, ele será uma cadeia de caracteres).
- O IDE gerará automaticamente a classe Partial para modelos (arquivos com a extensão **. cshtml** ). Você pode exibir esse código, mas ele não deve ser editado.
 ![RazorView. cshtml](images/image6_125x34.png) a classe parcial é denominada RazorView para corresponder ao nome do arquivo de modelo. cshtml. É esse nome que é usado para fazer referência ao modelo no C# código.
- `@using`as instruções também podem ser incluídas na parte superior de um modelo do Razor para incluir namespaces adicionais.


A saída HTML final pode então ser gerada com o código C# a seguir. Observe que especificamos que o modelo é uma cadeia de caracteres "Olá, Mundo", que será incorporada à saída do modelo renderizado.

```csharp
var template = new RazorView () { Model = "Hello World" };
var page = template.GenerateString ();
```

Aqui está a saída mostrada em uma exibição da Web no simulador de iOS e Android Emulator:

 ![Hello World](images/image7_523x135.png)

### <a name="more-razor-syntax"></a>Mais sintaxe do Razor

Nesta seção, vamos introduzir algumas sintaxe Razor básicas para ajudá-lo a começar a usá-la. Os exemplos nesta seção preenchem a seguinte classe com dados e as exibem usando o Razor:

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

#### <a name="displaying-model-properties"></a>Exibindo Propriedades do modelo

Quando o modelo é uma classe com propriedades, eles são facilmente referenciados no modelo Razor, conforme mostrado neste modelo de exemplo:

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

A saída final é mostrada aqui em uma exibição da Web no simulador de iOS e Android Emulator:

 ![Rupert](images/image8_516x160.png)

#### <a name="c-statements"></a>C#instruções

Mais complexo C# pode ser incluído no modelo, como as atualizações de propriedade de modelo e o cálculo de idade neste exemplo:

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

Você pode gravar expressões de linha C# única complexas (como formatar a idade) ao redor do código com `@()`.

Várias C# instruções podem ser escritas ao redor delas `@{}`.

#### <a name="if-else-statements"></a>Instruções if-else

As ramificações de código podem `@if` ser expressas com como mostrado neste exemplo de modelo.

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

Construções de loop como `foreach` também podem ser adicionadas. O `@` prefixo pode ser usado na variável de loop ( `@food` nesse caso) para renderizá-lo em HTML.

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

A saída do modelo acima é mostrada em execução no simulador de iOS e Android Emulator:

 ![Macaco Rupert X](images/image9_520x277.png)

Esta seção abordou os conceitos básicos do uso de modelos do Razor para renderizar exibições simples somente leitura. A próxima seção explica como criar aplicativos mais completos usando o Razor que pode aceitar a entrada do usuário e interoperar entre JavaScript na exibição HTML e C#.

## <a name="using-razor-templates-with-xamarin"></a>Usando modelos do Razor com o Xamarin

Esta seção explica como usar o compilar seu próprio aplicativo híbrido usando os modelos de solução no Visual Studio para Mac. Há três modelos disponíveis no **arquivo > nova solução de >...** janela:

- **Aplicativo de > Android > aplicativos Android WebView**
- **Aplicativo iOS > > aplicação WebView**
- **Projeto MVC do ASP.NET**



A **nova** janela de solução tem esta aparência para projetos iPhone e Android-a descrição da solução na direita realça o suporte para o mecanismo de modelagem do Razor.

 ![Criando soluções iPhone e Android](images/image13_1139x959.png)

Observe que você pode adicionar facilmente um modelo do Razor **. cshtml** a *qualquer* projeto Xamarin existente, não é necessário usar esses modelos de solução. os projetos do iOS não exigem um Storyboard para usar o Razor; Basta adicionar um controle UIWebView a qualquer modo de exibição programaticamente e você poderá renderizar todos os modelos Razor no C# código.

O conteúdo da solução de modelo padrão para projetos do iPhone e do Android é mostrado abaixo:

 ![modelos do iPhone e do Android](images/image10_428x310.png)

Os modelos fornecem uma infraestrutura de aplicativo pronta para uso para carregar um modelo do Razor com um objeto de modelo de dados, processar a entrada do usuário e se comunicar de volta com o usuário via JavaScript.

As partes importantes da solução são:

- Conteúdo estático, como o arquivo **Style. css** .
- Arquivos de modelo Razor. cshtml como **RazorView. cshtml** .
- Classes de modelo que são referenciadas nos modelos do Razor, como **ExampleModel.cs** .
- A classe específica da plataforma que cria a exibição da Web e renderiza o modelo, como `MainActivity` no Android `iPhoneHybridViewController` e no Ios.


A seção a seguir explica como os projetos funcionam.

### <a name="static-content"></a>Conteúdo Estático

O conteúdo estático inclui folhas de estilo CSS, imagens, arquivos JavaScript ou outro conteúdo que pode ser vinculado ou referenciado por um arquivo HTML que está sendo exibido em uma exibição da Web.

Os projetos de modelo incluem uma folha de estilos mínima para demonstrar como incluir conteúdo estático em seu aplicativo híbrido. A folha de estilos CSS é referenciada no modelo como este:

```html
<link rel="stylesheet" href="style.css" />
```

Você pode adicionar qualquer folha de estilos e arquivos JavaScript necessários, incluindo estruturas como JQuery.

### <a name="razor-cshtml-templates"></a>Modelos Razor cshtml

O modelo inclui um arquivo Razor **. cshtml** que tem código pré-gravados para ajudar a comunicar dados entre o HTML/JavaScript e C#o. Isso permitirá que você crie aplicativos híbridos sofisticados que não apenas exibem dados somente leitura do modelo, mas também aceitam entrada de usuário no HTML e os retransmitem ao C# código para processamento ou armazenamento.

#### <a name="rendering-the-template"></a>Renderizando o modelo

Chamar o `GenerateString` em um modelo renderiza o HTML pronto para exibição em uma exibição da Web. Se o modelo usar um modelo, ele deverá ser fornecido antes da renderização. Este diagrama ilustra como funciona a renderização – não que os recursos estáticos são resolvidos pela exibição da Web em tempo de execução, usando o diretório base fornecido para localizar os arquivos especificados.

 ![Fluxograma do Razor](images/image12_700x421.png)

#### <a name="calling-c-code-from-the-template"></a>Código C# de chamada do modelo

A comunicação de uma exibição da Web renderizada C# voltando para é feita definindo a URL para a exibição da Web e, em seguida, interceptando a solicitação no C# para lidar com a solicitação nativa sem recarregar o modo de exibição da Web.

Um exemplo pode ser visto no modo como o botão RazorView é manipulado. O botão tem o seguinte HTML:

```html
<input type="button" name="UpdateLabel" value="Click" onclick="InvokeCSharpWithFormValues(this)" />
```

A `InvokeCSharpWithFormValues` função JavaScript lê todos os valores do formulário HTML e define o `location.href` para a exibição da Web:

```javascript
location.href = "hybrid:" + elm.name + "?" + qs;
```

Isso tenta navegar na exibição da Web para uma URL com um esquema personalizado (por exemplo, `hybrid:`)

```
hybrid:UpdateLabel?textbox=SomeValue&UpdateLabel=Click
```

Quando a exibição da Web nativa processa essa solicitação de navegação, temos a oportunidade de interceptá-la. No iOS, isso é feito manipulando o evento HandleShouldStartLoad do UIWebView. No Android, simplesmente criamos uma subclasse da WebViewClient usada no formulário e substituem ShouldOverrideUrlLoading.

Os elementos internos desses dois interceptadores de navegação são essencialmente os mesmos.

Primeiro, verifique a URL que a exibição da Web está tentando carregar e, se ela não começar com o esquema personalizado (`hybrid:`), permita que a navegação ocorra normalmente.

Para o esquema de URL personalizado, tudo na URL entre o esquema e o "?" é o nome do método a ser manipulado (neste caso, "UpdateLabel"). Tudo na cadeia de caracteres de consulta será tratado como os parâmetros para a chamada do método:

```csharp
var resources = url.Substring(scheme.Length).Split('?');
var method = resources [0];
var parameters = System.Web.HttpUtility.ParseQueryString(resources[1]);
```

`UpdateLabel`Neste exemplo, há uma quantidade mínima de manipulação de cadeia de caracteres no parâmetro TextBox (preexistente "C# diz" para a cadeia de caracteres) e, em seguida, retorna à exibição da Web.

Depois de manipular a URL, o método anula a navegação para que a exibição da Web não tente terminar de navegar para a URL personalizada.

#### <a name="manipulating-the-template-from-c"></a>Manipulando o modelo a partir de C\#

A comunicação com uma exibição da Web HTML C# renderizada a partir do é feita chamando JavaScript na exibição da Web. No Ios, isso é feito chamando `EvaluateJavascript` o UIWebView:

```csharp
webView.EvaluateJavascript (js);
```

No Android, o JavaScript pode ser invocado no modo de exibição da Web carregando o JavaScript como uma `"javascript:"` URL usando o esquema de URL:

```csharp
webView.LoadUrl ("javascript:" + js);
```

## <a name="making-an-app-truly-hybrid"></a>Tornando um aplicativo realmente híbrido

Esses modelos não fazem uso de controles nativos em cada plataforma – a tela inteira é preenchida com uma única exibição da Web.

O HTML pode ser ótimo para o protótipo e exibir os tipos de coisas que a Web é melhor, como Rich Text e layout responsivo. No entanto, nem todas as tarefas são adequadas para HTML e JavaScript – rolar por longas listas de dados, por exemplo, executa melhor usando controles nativos da interface do usuário (como UITableView no iOS ou ListView no Android).

As exibições da Web no modelo podem ser facilmente aumentadas com controles específicos da plataforma – basta editar o **MainStoryboard. Storyboard** no designer do Ios ou os **recursos/layout/Main. axml** no Android.

### <a name="razortodo-sample"></a>Exemplo de RazorTodo

O repositório [RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo) contém duas soluções separadas para mostrar as diferenças entre um aplicativo baseado em HTML completo e um aplicativo que combina HTML com controles nativos:

- **RazorTodo** – aplicativo baseado em HTML completo usando modelos do Razor.
- **RazorNativeTodo** -usa controles de exibição de lista nativa para IOS e Android, mas exibe a tela de edição com HTML e Razor.


Esses aplicativos Xamarin são executados no iOS e no Android, utilizando PCLs (bibliotecas de classe portátil) para compartilhar um código comum, como as classes de banco de dados e modelo. Os modelos Razor **. cshtml** também podem ser incluídos no PCL para que eles sejam facilmente compartilhados entre plataformas.

Ambos os aplicativos de exemplo incorporam o compartilhamento do Twitter e as APIs de conversão de texto em fala da plataforma nativa, demonstrando que aplicativos híbridos com o Xamarin ainda têm acesso a toda a funcionalidade subjacente de exibições controladas por modelo do Razor em HTML.

O aplicativo **RazorTodo** usa modelos do Razor em HTML para a lista e edita modos de exibição. Isso significa que podemos criar o aplicativo quase que completamente em uma biblioteca de classes portátil compartilhada (incluindo os modelos de banco de dados e **. cshtml** Razor). As capturas de tela abaixo mostram os aplicativos iOS e Android.

 ![RazorTodo](images/Both_700x290.png)

O aplicativo **RazorNativeTodo** usa um modelo HTML Razor para o modo de exibição de edição, mas implementa uma lista de rolagem nativa em cada plataforma. Isso fornece vários benefícios, incluindo:

- Desempenho-os controles de rolagem nativos usam a virtualização para garantir a rolagem rápida e suave, mesmo com listas muito longas de dados.
- Experiência nativa-os elementos da interface do usuário específicos da plataforma são facilmente habilitados, como o suporte ao índice de rolagem rápida no iOS e no Android.


Um dos principais benefícios da criação de aplicativos híbridos com o Xamarin é que você pode começar com uma interface de usuário totalmente controlada por HTML (como o primeiro exemplo) e adicionar funcionalidade específica da plataforma quando necessário (como o segundo exemplo mostra). As telas de lista nativa e as telas de edição do Razor do HTML no iOS e no Android são mostradas abaixo.

 ![RazorNativeTodo](images/BothNative_700x290.png)

## <a name="summary"></a>Resumo

Este artigo explicou os recursos dos controles de exibição da Web disponíveis no iOS e no Android que facilitam a criação de aplicativos híbridos.

Em seguida, discutimos o mecanismo de modelagem do Razor e a sintaxe que pode ser usada para gerar HTML facilmente em aplicativos Xamarin usando o. **cshtml** Arquivos de modelo Razor. Ele também descreveu os modelos de solução Visual Studio para Mac que permitem que você comece rapidamente a criar aplicativos híbridos com o Xamarin.

Por fim, ele introduziu os exemplos de RazorTodo que demonstram como combinar exibições da Web com interfaces de usuário nativas e APIs.

### <a name="related-links"></a>Links relacionados

- [RazorTodo Sample](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)
- [MVC 3 – mecanismo de exibição do Razor (Microsoft)](http://www.asp.net/mvc/videos/mvc-3/mvc-3-razor-view-engine)
- [Introdução à programação da Web do ASP.NET usando a sintaxe do Razor (Microsoft)](http://www.asp.net/web-pages/tutorials/basics/2-introduction-to-asp-net-web-programming-using-the-razor-syntax)
