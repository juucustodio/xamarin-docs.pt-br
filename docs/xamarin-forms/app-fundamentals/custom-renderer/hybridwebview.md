---
title: Personalizando uma WebView
description: O Xamarin.Forms WebView é uma exibição que exibe conteúdo web e HTML em seu aplicativo. Este artigo explica como criar um renderizador personalizado que estende o WebView para permitir que o código C# seja invocado a partir do JavaScript.
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/31/2020
ms.openlocfilehash: c736c083d4a8c424d3e017dae3cc30e35ad4fa3b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "80419063"
---
# <a name="customizing-a-webview"></a>Personalizando uma WebView

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)

_A Xamarin.Forms `WebView` é uma exibição que exibe conteúdo web e HTML em seu aplicativo. Este artigo explica como criar um renderizador personalizado que estende o código para permitir que o `WebView` código C# seja invocado a partir de JavaScript._

Cada exibição do Xamarin.Forms tem um renderizador que o acompanha para cada plataforma que cria uma instância de um controle nativo. Quando [`WebView`](xref:Xamarin.Forms.WebView) um é renderizado por um aplicativo Xamarin.Forms no iOS, a classe é instanciada, o `WkWebViewRenderer` que, por sua vez, instancia um controle nativo. `WkWebView` Na plataforma Android, a classe `WebViewRenderer` cria uma instância de um controle `WebView` nativo. Na UWP (Plataforma Universal do Windows), a classe `WebViewRenderer` cria uma instância de um controle `WebView` nativo. Para obter mais informações sobre as classes de renderizador e de controle nativo para as quais os controles do Xamarin.Forms são mapeadas, confira [Classes base do renderizador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O diagrama a seguir [`View`](xref:Xamarin.Forms.View) ilustra a relação entre os controles nativos correspondentes que o implementam:

![](hybridwebview-images/webview-classes.png "Relationship Between the WebView Class and its Implementing Native Classes")

O processo de renderização pode ser usado para implementar personalizações de plataforma, criando um renderizador personalizado para um [`WebView`](xref:Xamarin.Forms.WebView) em cada plataforma. O processo para fazer isso é o seguinte:

1. [Criar](#create-the-hybridwebview) o controle personalizado `HybridWebView`.
1. [Consumir](#consume-the-hybridwebview) a `HybridWebView`do Xamarin.Forms.
1. [Criar](#create-the-custom-renderer-on-each-platform) o renderizador personalizado para a página `HybridWebView` em cada plataforma.

Cada item será agora discutido para `HybridWebView` implementar um renderizador que aprimora o Xamarin.Forms [`WebView`](xref:Xamarin.Forms.WebView) para permitir que o código C# seja invocado a partir de JavaScript. A instância de `HybridWebView` será usada para exibir uma página HTML que pede ao usuário para digitar seu nome. Em seguida, quando o usuário clicar em um botão HTML, uma função de JavaScript invocará um `Action` em C# que exibe um pop-up que contém o nome do usuário.

Para obter mais informações sobre o processo de invocação de C# do JavaScript, consulte [Invocar C# de JavaScript](#invoke-c-from-javascript). Para obter mais informações sobre a página HTML, consulte [Criar a página da Web](#create-the-web-page).

> [!NOTE]
> A [`WebView`](xref:Xamarin.Forms.WebView) pode invocar uma função JavaScript a partir de C#e retornar qualquer resultado para o código c# de chamada. Para obter mais informações, consulte [Invocando JavaScript](~/xamarin-forms/user-interface/webview.md#invoking-javascript).

## <a name="create-the-hybridwebview"></a>Crie o HybridWebView

O `HybridWebView` controle personalizado pode ser criado [`WebView`](xref:Xamarin.Forms.WebView) subclassificando a classe:

```csharp
public class HybridWebView : WebView
{
    Action<string> action;

    public static readonly BindableProperty UriProperty = BindableProperty.Create(
        propertyName: "Uri",
        returnType: typeof(string),
        declaringType: typeof(HybridWebView),
        defaultValue: default(string));

    public string Uri
    {
        get { return (string)GetValue(UriProperty); }
        set { SetValue(UriProperty, value); }
    }

    public void RegisterAction(Action<string> callback)
    {
        action = callback;
    }

    public void Cleanup()
    {
        action = null;
    }

    public void InvokeAction(string data)
    {
        if (action == null || data == null)
        {
            return;
        }
        action.Invoke(data);
    }
}
```

O controle personalizado `HybridWebView` é criado no projeto da biblioteca do .NET Standard e define a seguinte API para o controle:

- Uma propriedade `Uri` que especifica o endereço da página da Web a ser carregada.
- Um método `RegisterAction` que registra um `Action` com o controle. A ação registrada será invocada do JavaScript contido no arquivo HTML referenciado por meio da propriedade `Uri`.
- Um método `CleanUp` que remove a referência ao `Action` registrado.
- Um método `InvokeAction` que invoca o `Action` registrado. Este método será chamado de um renderizador personalizado em cada projeto de plataforma.

## <a name="consume-the-hybridwebview"></a>Consumir o HybridWebView

O controle personalizado `HybridWebView` pode ser referenciado em XAML no projeto da biblioteca .NET Standard declarando um namespace para sua localização e usando o prefixo do namespace no controle personalizado. O exemplo de código a seguir mostra como o controle personalizado `HybridWebView` pode ser consumido por uma página XAML:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             x:Class="CustomRenderer.HybridWebViewPage"
             Padding="0,40,0,0">
    <local:HybridWebView x:Name="hybridWebView"
                         Uri="index.html" />
</ContentPage>

```

O prefixo do namespace `local` pode ser qualquer nome. No entanto, os valores de `clr-namespace` e `assembly` devem corresponder aos detalhes do controle personalizado. Quando o namespace é declarado, o prefixo é usado para referenciar o controle personalizado.

O seguinte exemplo de código mostra como o controle personalizado `HybridWebView` pode ser consumido por um página em C#:

```csharp
public HybridWebViewPageCS()
{
    var hybridWebView = new HybridWebView
    {
        Uri = "index.html"
    };
    // ...
    Padding = new Thickness(0, 40, 0, 0);
    Content = hybridWebView;
}
```

A instância `HybridWebView` será usada para exibir um controle da Web nativo em cada plataforma. Sua `Uri` propriedade é definida como um arquivo HTML que é armazenado em cada projeto de plataforma, e que será exibido pelo controle web nativo. O HTML renderizado solicita que o usuário insira seu nome, com uma função de JavaScript invocando um `Action` C# em resposta a um clique no botão HTML.

O `HybridWebViewPage` registra a ação a ser invocada do JavaScript, conforme mostrado no exemplo de código a seguir:

```csharp
public partial class HybridWebViewPage : ContentPage
{
    public HybridWebViewPage()
    {
        // ...
        hybridWebView.RegisterAction(data => DisplayAlert("Alert", "Hello " + data, "OK"));
    }
}
```

Esta ação [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) chama o método para exibir um pop-up modal que apresenta `HybridWebView` o nome inserido na página HTML exibida pela instância.

Um renderizador personalizado agora pode ser adicionado a cada projeto de aplicativo para melhorar os controles da web da plataforma, permitindo que o código C# seja invocado do JavaScript.

## <a name="create-the-custom-renderer-on-each-platform"></a>Crie o renderizador personalizado em cada plataforma

O processo para criar a classe do renderizador personalizado é a seguinte:

1. Crie uma subclasse `WkWebViewRenderer` da classe no `WebViewRenderer` iOS, e a classe no Android e UWP, que renderiza o controle personalizado.
1. Anular o `OnElementChanged` método que [`WebView`](xref:Xamarin.Forms.WebView) torna a lógica e escrever para personalizá-lo. Este método é `HybridWebView` chamado quando um objeto é criado.
1. Adicione `ExportRenderer` um atributo à classe de renderizador personalizado ou *AssemblyInfo.cs,* para especificar que ele será usado para renderizar o controle personalizado Xamarin.Forms. Esse atributo é usado para registrar o renderizador personalizado no Xamarin.Forms.

> [!NOTE]
> Para a maioria dos elementos do Xamarin.Forms, o fornecimento de um renderizador personalizado em cada projeto de plataforma é opcional. Se um renderizador personalizado não estiver registrado, será usado o renderizador padrão da classe base do controle. No entanto, são necessários renderizadores personalizados em cada projeto da plataforma durante a renderização de um elemento [View](xref:Xamarin.Forms.View).

O diagrama a seguir ilustra as responsabilidades de cada projeto no aplicativo de exemplo, bem como as relações entre elas:

![](hybridwebview-images/solution-structure.png "HybridWebView Custom Renderer Project Responsibilities")

O `HybridWebView` controle personalizado é renderizado por classes de `WkWebViewRenderer` renderização de plataforma, `WebViewRenderer` que derivam da classe no iOS, e da classe no Android e UWP. Isso resulta `HybridWebView` em cada controle personalizado sendo renderizado com controles web nativos, como mostrado nas capturas de tela a seguir:

![](hybridwebview-images/screenshots.png "HybridWebView on each Platform")

As `WkWebViewRenderer` `WebViewRenderer` classes expõem o `OnElementChanged` método, que é chamado quando o controle personalizado Xamarin.Forms é criado para renderizar o controle web nativo correspondente. Este método `VisualElementChangedEventArgs` requer um `OldElement` parâmetro `NewElement` que contém e propriedades. Essas propriedades representam o elemento do Xamarin.Forms ao qual o renderizador *estava* anexado e o elemento do Xamarin.Forms ao qual o renderizador *está* anexado, respectivamente. No aplicativo de exemplo, a propriedade `OldElement` será `null` e a propriedade `NewElement` conterá uma referência à instância de `HybridWebView`.

Uma versão substituída do `OnElementChanged` método, em cada classe de renderizadores de plataforma, é o lugar para executar a personalização nativa do controle web. Uma referência ao controle Xamarin.Forms que está sendo renderizado `Element` pode ser obtida através da propriedade.

Cada classe de renderizador personalizado é decorada com um atributo `ExportRenderer` que registra o renderizador no Xamarin.Forms. O atributo aceita dois parâmetros – o nome do tipo de controle personalizado do Xamarin.Forms que está sendo renderizado e o nome do tipo de renderizador personalizado. O prefixo `assembly` do atributo especifica que o atributo se aplica a todo o assembly.

As seções a seguir discutem a estrutura da página web carregada por cada controle web nativo, o processo de invocação de C# do JavaScript e a implementação disso em cada classe de renderizador personalizado da plataforma.

### <a name="create-the-web-page"></a>Crie a página web

O exemplo de código a seguir mostra a página da Web que será exibida pelo controle personalizado `HybridWebView`:

```html
<html>
<body>
    <script src="http://code.jquery.com/jquery-2.1.4.min.js"></script>
    <h1>HybridWebView Test</h1>
    <br />
    Enter name: <input type="text" id="name">
    <br />
    <br />
    <button type="button" onclick="javascript: invokeCSCode($('#name').val());">Invoke C# Code</button>
    <br />
    <p id="result">Result:</p>
    <script type="text/javascript">function log(str) {
            $('#result').text($('#result').text() + " " + str);
        }

        function invokeCSCode(data) {
            try {
                log("Sending Data:" + data);
                invokeCSharpAction(data);
            }
            catch (err) {
                log(err);
            }
        }</script>
</body>
</html>
```

A página da Web permite que um usuário insira seu nome em um elemento `input` e fornece um elemento `button` que invocará o código C# quando clicado. O processo para fazer isso é o seguinte:

- Quando o usuário clica na elemento `button`, a função `invokeCSCode` de JavaScript é chamada, com o valor do elemento `input` sendo passado para a função.
- A função `invokeCSCode` chama a função `log` para exibir os dados que está enviando para o `Action` C#. Em seguida, ela chama o método `invokeCSharpAction` para invocar o `Action` C#, passando o parâmetro recebido do elemento `input`.

A função JavaScript `invokeCSharpAction` não está definida na página da Web e será injetada nela por cada renderizador personalizado.

No iOS, esse arquivo HTML reside na pasta de conteúdo do projeto de plataforma, com uma ação de build **BundleResource**. No Android, esse arquivo HTML reside na pasta de Ativos/Conteúdo do projeto de plataforma, com uma ação de build **AndroidAsset**.

### <a name="invoke-c-from-javascript"></a>Invoque C# de JavaScript

O processo para invocar C# do JavaScript é idêntico em todas as plataformas:

- O renderizador personalizado cria um controle da Web nativo e carrega o arquivo HTML especificado pela propriedade `HybridWebView.Uri`.
- Quando a página da Web é carregada, o renderizador personalizado injeta a função de JavaScript `invokeCSharpAction` na página da Web.
- Quando o usuário insere seu nome e clica no elemento `button` HTML, a função `invokeCSCode` é invocada, que, por sua vez, invoca a função `invokeCSharpAction`.
- A função `invokeCSharpAction` invoca um método no renderizador personalizado, que por sua vez invoca o método `HybridWebView.InvokeAction`.
- O método `HybridWebView.InvokeAction` invoca o `Action` registrado.

As seções a seguir abordam como esse processo é implementado em cada plataforma.

### <a name="create-the-custom-renderer-on-ios"></a>Crie o renderizador personalizado no iOS

O exemplo de código a seguir mostra o renderizador personalizado para a plataforma iOS:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.iOS
{
    public class HybridWebViewRenderer : WkWebViewRenderer, IWKScriptMessageHandler
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.webkit.messageHandlers.invokeAction.postMessage(data);}";
        WKUserContentController userController;

        public HybridWebViewRenderer() : this(new WKWebViewConfiguration())
        {
        }

        public HybridWebViewRenderer(WKWebViewConfiguration config) : base(config)
        {
            userController = config.UserContentController;
            var script = new WKUserScript(new NSString(JavaScriptFunction), WKUserScriptInjectionTime.AtDocumentEnd, false);
            userController.AddUserScript(script);
            userController.AddScriptMessageHandler(this, "invokeAction");
        }

        protected override void OnElementChanged(VisualElementChangedEventArgs e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                userController.RemoveAllUserScripts();
                userController.RemoveScriptMessageHandler("invokeAction");
                HybridWebView hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup();
            }

            if (e.NewElement != null)
            {
                string filename = Path.Combine(NSBundle.MainBundle.BundlePath, $"Content/{((HybridWebView)Element).Uri}");
                LoadRequest(new NSUrlRequest(new NSUrl(filename, false)));
            }
        }

        public void DidReceiveScriptMessage(WKUserContentController userContentController, WKScriptMessage message)
        {
            ((HybridWebView)Element).InvokeAction(message.Body.ToString());
        }

        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                ((HybridWebView)Element).Cleanup();
            }
            base.Dispose(disposing);
        }        
    }
}
```

A `HybridWebViewRenderer` classe carrega a página da `HybridWebView.Uri` Web especificada na propriedade em um controle nativo, [`WKWebView`](xref:WebKit.WKWebView) e a `invokeCSharpAction` função JavaScript é injetada na página da Web. Quando o usuário insere seu nome e clica no elemento HTML `button`, a função de JavaScript `invokeCSharpAction` é executada, com o método `DidReceiveScriptMessage` sendo chamado depois que uma mensagem é recebida da página da Web. Por sua vez, esse método invoca o método `HybridWebView.InvokeAction`, que invoca a ação registrada para exibir o pop-up.

Essa funcionalidade é obtida da seguinte maneira:

- O construtor renderizador `WkWebViewConfiguration` cria um objeto [`WKUserContentController`](xref:WebKit.WKUserContentController) e recupera seu objeto. O `WkUserContentController` objeto permite postar mensagens e injetar scripts de usuário em uma página da Web.
- O construtor de renderizadores cria um [`WKUserScript`](xref:WebKit.WKUserScript) `invokeCSharpAction` objeto, que injeta a função JavaScript na página da Web depois que a página da Web é carregada.
- O construtor de renderizadores chama [`WKUserContentController.AddUserScript`](xref:WebKit.WKUserContentController.AddUserScript(WebKit.WKUserScript)) [`WKUserScript`](xref:WebKit.WKUserScript) o método para adicionar o objeto ao controlador de conteúdo.
- [`WKUserContentController.AddScriptMessageHandler`](xref:WebKit.WKUserContentController.AddScriptMessageHandler(WebKit.IWKScriptMessageHandler,System.String)) O construtor de renderizadores chama o método `invokeAction` para [`WKUserContentController`](xref:WebKit.WKUserContentController) adicionar um manipulador de mensagens de script nomeado ao objeto, o que fará com que a função `window.webkit.messageHandlers.invokeAction.postMessage(data)` JavaScript seja definida em todos os quadros em todas as `WebView` instâncias que usam o `WKUserContentController` objeto.
- Desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms:
  - O [`WKWebView.LoadRequest`](xref:WebKit.WKWebView.LoadRequest(Foundation.NSUrlRequest)) método carrega o arquivo HTML especificado `HybridWebView.Uri` pela propriedade. O código especifica que o arquivo seja armazenado na pasta `Content` do projeto. Após a página da Web ser exibida, a função de JavaScript `invokeCSharpAction` será injetada na página da Web.
- Os recursos são liberados quando o elemento que o renderizador é anexado às alterações.
- O elemento Xamarin.Forms é limpo quando o renderizador é eliminado.

> [!NOTE]
> A classe `WKWebView` tem suporte apenas no iOS 8 e posteriores.

Além disso, **Info.plist** deve ser atualizado para incluir os seguintes valores:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

### <a name="create-the-custom-renderer-on-android"></a>Crie o renderizador personalizado no android

O exemplo de código a seguir mostra o renderizador personalizado para a plataforma Android:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.Droid
{
    public class HybridWebViewRenderer : WebViewRenderer
    {
        const string JavascriptFunction = "function invokeCSharpAction(data){jsBridge.invokeAction(data);}";
        Context _context;

        public HybridWebViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<WebView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                Control.RemoveJavascriptInterface("jsBridge");
                ((HybridWebView)Element).Cleanup();
            }
            if (e.NewElement != null)
            {
                Control.SetWebViewClient(new JavascriptWebViewClient(this, $"javascript: {JavascriptFunction}"));
                Control.AddJavascriptInterface(new JSBridge(this), "jsBridge");
                Control.LoadUrl($"file:///android_asset/Content/{((HybridWebView)Element).Uri}");
            }
        }

        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                ((HybridWebView)Element).Cleanup();
            }
            base.Dispose(disposing);
        }        
    }
}
```

A `HybridWebViewRenderer` classe carrega a página da `HybridWebView.Uri` Web especificada na propriedade em um controle [`WebView`](xref:Android.Webkit.WebView) nativo, e `invokeCSharpAction` a função JavaScript é `OnPageFinished` injetada `JavascriptWebViewClient` na página da Web, depois que a página da Web tiver terminado de carregar, com a substituição na classe:

```csharp
public class JavascriptWebViewClient : FormsWebViewClient
{
    string _javascript;

    public JavascriptWebViewClient(HybridWebViewRenderer renderer, string javascript) : base(renderer)
    {
        _javascript = javascript;
    }

    public override void OnPageFinished(WebView view, string url)
    {
        base.OnPageFinished(view, url);
        view.EvaluateJavascript(_javascript, null);
    }
}
```

Quando o usuário insere seu nome e clica no elemento HTML `button`, a função de JavaScript `invokeCSharpAction` é executada. Essa funcionalidade é obtida da seguinte maneira:

- Desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms:
  - O `SetWebViewClient` método define `JavascriptWebViewClient` um novo `WebViewClient`objeto como a implementação de .
  - O [`WebView.AddJavascriptInterface`](xref:Android.Webkit.WebView.AddJavascriptInterface*) método injeta `JSBridge` uma nova instância no quadro principal do contexto JavaScript do WebView, nomeando-o `jsBridge`. Isso permite que métodos na classe `JSBridge` sejam acessados do JavaScript.
  - O [`WebView.LoadUrl`](xref:Android.Webkit.WebView.LoadUrl*) método carrega o arquivo HTML especificado `HybridWebView.Uri` pela propriedade. O código especifica que o arquivo seja armazenado na pasta `Content` do projeto.
  - Na classe `JavascriptWebViewClient`, a função de JavaScript `invokeCSharpAction` é injetada na página da Web depois que a página termina de ser carregada.
- Os recursos são liberados quando o elemento que o renderizador é anexado às alterações.
- O elemento Xamarin.Forms é limpo quando o renderizador é eliminado.

Quando a função de JavaScript `invokeCSharpAction` é executada, por sua vez, ela invoca o método `JSBridge.InvokeAction`, que é mostrado no exemplo de código a seguir:

```csharp
public class JSBridge : Java.Lang.Object
{
    readonly WeakReference<HybridWebViewRenderer> hybridWebViewRenderer;

    public JSBridge(HybridWebViewRenderer hybridRenderer)
    {
        hybridWebViewRenderer = new WeakReference<HybridWebViewRenderer>(hybridRenderer);
    }

    [JavascriptInterface]
    [Export("invokeAction")]
    public void InvokeAction(string data)
    {
        HybridWebViewRenderer hybridRenderer;

        if (hybridWebViewRenderer != null && hybridWebViewRenderer.TryGetTarget(out hybridRenderer))
        {
            ((HybridWebView)hybridRenderer.Element).InvokeAction(data);
        }
    }
}
```

A classe deve derivar de `Java.Lang.Object`, e os métodos que são expostos para o JavaScript devem ser decorados com os atributos `[JavascriptInterface]` e `[Export]`. Portanto, quando a função de JavaScript `invokeCSharpAction` é injetada na página da Web e é executada, ela chama o método `JSBridge.InvokeAction` por ter sido decorada com os atributos `[JavascriptInterface]` e `[Export("invokeAction")]`. Por sua `InvokeAction` vez, o `HybridWebView.InvokeAction` método invoca o método, que invocará a ação registrada para exibir o pop-up.

> [!IMPORTANT]
> Os projetos androides que usam o atributo `[Export]` devem incluir uma referência a `Mono.Android.Export`, ou um erro de compilador resultará.

Observe que a classe `JSBridge` mantém um `WeakReference` para a classe `HybridWebViewRenderer`. A finalidade é evitar a criação de uma referência circular entre as duas classes. Para obter mais informações, consulte [Referências fracas](/en-us/dotnet/standard/garbage-collection/weak-references).

### <a name="create-the-custom-renderer-on-uwp"></a>Crie o renderizador personalizado no UWP

O seguinte exemplo de código mostra o renderizador personalizado para o UWP:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.UWP
{
    public class HybridWebViewRenderer : WebViewRenderer
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.external.notify(data);}";

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.WebView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                Control.NavigationCompleted -= OnWebViewNavigationCompleted;
                Control.ScriptNotify -= OnWebViewScriptNotify;
            }
            if (e.NewElement != null)
            {
                Control.NavigationCompleted += OnWebViewNavigationCompleted;
                Control.ScriptNotify += OnWebViewScriptNotify;
                Control.Source = new Uri($"ms-appx-web:///Content//{((HybridWebView)Element).Uri}");
            }
        }

        async void OnWebViewNavigationCompleted(Windows.UI.Xaml.Controls.WebView sender, WebViewNavigationCompletedEventArgs args)
        {
            if (args.IsSuccess)
            {
                // Inject JS script
                await Control.InvokeScriptAsync("eval", new[] { JavaScriptFunction });
            }
        }

        void OnWebViewScriptNotify(object sender, NotifyEventArgs e)
        {
            ((HybridWebView)Element).InvokeAction(e.Value);
        }

        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                ((HybridWebView)Element).Cleanup();
            }
            base.Dispose(disposing);
        }        
    }
}
```

A classe `HybridWebViewRenderer` carrega a página da Web especificada na propriedade `HybridWebView.Uri` em um controle `WebView` nativo e a função de JavaScript `invokeCSharpAction` é injetada na página da Web, após a página da Web ser carregada, com o método `WebView.InvokeScriptAsync`. Quando o usuário insere seu nome e clica no elemento HTML `button`, a função de JavaScript `invokeCSharpAction` é executada, com o método `OnWebViewScriptNotify` sendo chamado depois que uma notificação é recebida da página da Web. Por sua vez, esse método invoca o método `HybridWebView.InvokeAction`, que invoca a ação registrada para exibir o pop-up.

Essa funcionalidade é obtida da seguinte maneira:

- Desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms:
  - Manipuladores de eventos para os eventos `NavigationCompleted` e `ScriptNotify` são registrados. O evento `NavigationCompleted` é acionado quando o controle nativo `WebView` termina de carregar o conteúdo atual ou quando a navegação falha. O evento `ScriptNotify` é acionado quando o conteúdo no controle `WebView` nativo usa JavaScript para passar uma cadeia de caracteres para o aplicativo. A página da Web aciona o evento `ScriptNotify` chamando `window.external.notify` ao passar um parâmetro `string`.
  - A propriedade `WebView.Source` é definida como o URI do arquivo HTML que é especificado pela propriedade `HybridWebView.Uri`. O código presume que o arquivo seja armazenado na pasta `Content` do projeto. Depois que a página da Web é exibida, o evento `NavigationCompleted` é acionado e o método `OnWebViewNavigationCompleted` é invocado. A função de JavaScript `invokeCSharpAction`, em seguida, será injetada na página da Web com o método `WebView.InvokeScriptAsync`, desde que a navegação tenha sido concluída com êxito.
- O evento é desinscrito a partir do momento em que o elemento que o renderizador está conectado às alterações.
- O elemento Xamarin.Forms é limpo quando o renderizador é eliminado.

## <a name="related-links"></a>Links relacionados

- [HybridWebView (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)
