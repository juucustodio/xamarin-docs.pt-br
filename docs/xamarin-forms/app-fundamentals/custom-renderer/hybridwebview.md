---
title: Personalizando uma WebView
description: Um WebView Xamarin. Forms é uma exibição que exibe conteúdo Web e HTML em seu aplicativo. Este artigo explica como criar um renderizador personalizado que estende a exibição da Web C# para permitir que o código seja invocado do JavaScript.
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/03/2019
ms.openlocfilehash: e3c042a82a9870e68c94741ae4bdfaa728a40cb0
ms.sourcegitcommit: 27e77acd0139c099f6592085a5ea5aabcaeedc7f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74823903"
---
# <a name="customizing-a-hybridwebview"></a>Personalizando um HybridWebView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)

_Uma `WebView` do Xamarin. Forms é uma exibição que exibe conteúdo Web e HTML em seu aplicativo. Este artigo explica como criar um renderizador personalizado que estende o `WebView` para permitir C# que o código seja invocado do JavaScript._

Cada exibição do Xamarin.Forms tem um renderizador que o acompanha para cada plataforma que cria uma instância de um controle nativo. Quando um [`WebView`](xref:Xamarin.Forms.WebView) é processado por um aplicativo Xamarin. Forms no Ios, a classe `WkWebViewRenderer` é instanciada, que por sua vez instancia um controle de `WkWebView` nativo. Na plataforma Android, a classe `WebViewRenderer` cria uma instância de um controle `WebView` nativo. Na UWP (Plataforma Universal do Windows), a classe `WebViewRenderer` cria uma instância de um controle `WebView` nativo. Para obter mais informações sobre as classes de renderizador e de controle nativo para as quais os controles do Xamarin.Forms são mapeadas, confira [Classes base do renderizador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O diagrama a seguir ilustra a relação entre o [`View`](xref:Xamarin.Forms.View) e os controles nativos correspondentes que o implementam:

![](hybridwebview-images/webview-classes.png "Relationship Between the WebView Class and its Implementing Native Classes")

O processo de renderização pode ser usado para implementar personalizações de plataforma criando um renderizador personalizado para um [`WebView`](xref:Xamarin.Forms.WebView) em cada plataforma. O processo para fazer isso é o seguinte:

1. [Criar](#create-the-hybridwebview) o controle personalizado `HybridWebView`.
1. [Consumir](#consume-the-hybridwebview) a `HybridWebView`do Xamarin.Forms.
1. [Criar](#create-the-custom-renderer-on-each-platform) o renderizador personalizado para a página `HybridWebView` em cada plataforma.

Cada item agora será discutido, por sua vez, para implementar um processador de `HybridWebView` que aprimora a [`WebView`](xref:Xamarin.Forms.WebView) Xamarin. Forms C# para permitir que o código seja invocado do JavaScript. A instância de `HybridWebView` será usada para exibir uma página HTML que pede ao usuário para digitar seu nome. Em seguida, quando o usuário clicar em um botão HTML, uma função de JavaScript invocará um `Action` em C# que exibe um pop-up que contém o nome do usuário.

Para obter mais informações sobre o processo de invocação C# do JavaScript, consulte [invocar C# do JavaScript](#invoke-c-from-javascript). Para obter mais informações sobre a página HTML, consulte [criar a página da Web](#create-the-web-page).

> [!NOTE]
> Um [`WebView`](xref:Xamarin.Forms.WebView) pode invocar uma função JavaScript C#de e retornar qualquer resultado para o código C# de chamada. Para obter mais informações, consulte [invocando JavaScript](~/xamarin-forms/user-interface/webview.md#invoking-javascript).

## <a name="create-the-hybridwebview"></a>Criar o HybridWebView

O `HybridWebView` controle personalizado pode ser criado por meio da subclasse da classe [`WebView`](xref:Xamarin.Forms.WebView) :

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
- Um método `InvokeAction` que invoca o `Action` registrado. Esse método será chamado de um renderizador personalizado em cada projeto de plataforma.

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

A instância `HybridWebView` será usada para exibir um controle da Web nativo em cada plataforma. É `Uri` propriedade é definida como um arquivo HTML que é armazenado em cada projeto de plataforma e que será exibido pelo controle da Web nativo. O HTML renderizado solicita que o usuário insira seu nome, com uma função de JavaScript invocando um `Action` C# em resposta a um clique no botão HTML.

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

Essa ação chama o método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) para exibir um pop-up modal que apresenta o nome inserido na página HTML exibida pela instância de `HybridWebView`.

Um renderizador personalizado agora pode ser adicionado a cada projeto de aplicativo para aprimorar os controles da C# Web da plataforma, permitindo que o código seja invocado do JavaScript.

## <a name="create-the-custom-renderer-on-each-platform"></a>Criar o renderizador personalizado em cada plataforma

O processo para criar a classe do renderizador personalizado é a seguinte:

1. Crie uma subclasse da classe `WkWebViewRenderer` no iOS e a classe `WebViewRenderer` no Android e UWP, que renderiza o controle personalizado.
1. Substitua o método `OnElementChanged` que renderiza o [`WebView`](xref:Xamarin.Forms.WebView) e a lógica de gravação para personalizá-lo. Esse método é chamado quando um objeto de `HybridWebView` é criado.
1. Adicione um atributo `ExportRenderer` à classe de processador personalizado ou *AssemblyInfo.cs*, para especificar que ele será usado para renderizar o controle personalizado Xamarin. Forms. Este atributo é usado para registrar o renderizador personalizado no Xamarin.Forms.

> [!NOTE]
> Para a maioria dos elementos do Xamarin.Forms, o fornecimento de um renderizador personalizado em cada projeto de plataforma é opcional. Se um renderizador personalizado não estiver registrado, será usado o renderizador padrão da classe base do controle. No entanto, são necessários renderizadores personalizados em cada projeto da plataforma durante a renderização de um elemento [View](xref:Xamarin.Forms.View).

O diagrama a seguir ilustra as responsabilidades de cada projeto no aplicativo de exemplo, bem como as relações entre elas:

![](hybridwebview-images/solution-structure.png "HybridWebView Custom Renderer Project Responsibilities")

O `HybridWebView` controle personalizado é renderizado por classes de processador de plataforma, que derivam da classe `WkWebViewRenderer` no iOS e da classe `WebViewRenderer` no Android e UWP. Isso resulta em cada `HybridWebView` controle personalizado sendo renderizado com controles da Web nativos, conforme mostrado nas capturas de tela a seguir:

![](hybridwebview-images/screenshots.png "HybridWebView on each Platform")

As classes `WkWebViewRenderer` e `WebViewRenderer` expõem o método `OnElementChanged`, que é chamado quando o controle personalizado Xamarin. Forms é criado para renderizar o controle da Web nativo correspondente. Esse método usa um parâmetro `VisualElementChangedEventArgs` que contém `OldElement` e `NewElement` Propriedades. Essas propriedades representam o elemento do Xamarin.Forms a que o renderizador *estava* anexado e o elemento a que o renderizador *está* anexado, respectivamente. No aplicativo de exemplo, a propriedade `OldElement` será `null` e a propriedade `NewElement` conterá uma referência à instância de `HybridWebView`.

Uma versão substituída do método `OnElementChanged`, em cada classe de processador de plataforma, é o local para executar a personalização do controle da Web nativo. Uma referência ao controle Xamarin. Forms que está sendo renderizado pode ser obtida por meio da propriedade `Element`.

Cada classe de renderizador personalizado é decorada com um atributo `ExportRenderer` que registra o renderizador no Xamarin.Forms. O atributo aceita dois parâmetros – o nome do tipo de controle personalizado do Xamarin.Forms que está sendo renderizado e o nome do tipo de renderizador personalizado. O prefixo `assembly` do atributo especifica que o atributo se aplica a todo o assembly.

As seções a seguir discutem a estrutura da página da Web carregada por cada controle da Web nativo, o processo C# de invocação do JavaScript e a implementação disso em cada classe de processador personalizado da plataforma.

### <a name="create-the-web-page"></a>Criar a página da Web

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

### <a name="invoke-c-from-javascript"></a>Invocar C# do JavaScript

O processo para invocar C# do JavaScript é idêntico em todas as plataformas:

- O renderizador personalizado cria um controle da Web nativo e carrega o arquivo HTML especificado pela propriedade `HybridWebView.Uri`.
- Quando a página da Web é carregada, o renderizador personalizado injeta a função de JavaScript `invokeCSharpAction` na página da Web.
- Quando o usuário insere seu nome e clica no elemento `button` HTML, a função `invokeCSCode` é invocada, que, por sua vez, invoca a função `invokeCSharpAction`.
- A função `invokeCSharpAction` invoca um método no renderizador personalizado, que por sua vez invoca o método `HybridWebView.InvokeAction`.
- O método `HybridWebView.InvokeAction` invoca o `Action` registrado.

As seções a seguir abordam como esse processo é implementado em cada plataforma.

### <a name="create-the-custom-renderer-on-ios"></a>Criar o renderizador personalizado no iOS

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
    }
}
```

A classe `HybridWebViewRenderer` carrega a página da Web especificada na propriedade `HybridWebView.Uri` em um controle [`WKWebView`](xref:WebKit.WKWebView) nativo e a função de JavaScript `invokeCSharpAction` é injetada na página da Web. Quando o usuário insere seu nome e clica no elemento HTML `button`, a função de JavaScript `invokeCSharpAction` é executada, com o método `DidReceiveScriptMessage` sendo chamado depois que uma mensagem é recebida da página da Web. Por sua vez, esse método invoca o método `HybridWebView.InvokeAction`, que invoca a ação registrada para exibir o pop-up.

Essa funcionalidade é obtida da seguinte maneira:

- O construtor do processador cria um objeto `WkWebViewConfiguration` e recupera seu objeto [`WKUserContentController`](xref:WebKit.WKUserContentController) . O objeto `WkUserContentController` permite postar mensagens e injetar scripts de usuário em uma página da Web.
- O construtor do processador cria um objeto [`WKUserScript`](xref:WebKit.WKUserScript) , que injeta a `invokeCSharpAction` função JavaScript na página da Web depois que a página da Web é carregada.
- O construtor do processador chama o método [`WKUserContentController.AddUserScript`](xref:WebKit.WKUserContentController.AddUserScript(WebKit.WKUserScript)) para adicionar o objeto [`WKUserScript`](xref:WebKit.WKUserScript) ao controlador de conteúdo.
- O construtor do processador chama o método [`WKUserContentController.AddScriptMessageHandler`](xref:WebKit.WKUserContentController.AddScriptMessageHandler(WebKit.IWKScriptMessageHandler,System.String)) para adicionar um manipulador de mensagens de script chamado `invokeAction` ao objeto [`WKUserContentController`](xref:WebKit.WKUserContentController) , o que fará com que a função JavaScript `window.webkit.messageHandlers.invokeAction.postMessage(data)` seja definida em todos os quadros em todas as instâncias de `WebView` que usam o objeto `WKUserContentController`.
- Desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms:
  - O método [`WKWebView.LoadRequest`](xref:WebKit.WKWebView.LoadRequest(Foundation.NSUrlRequest)) carrega o arquivo HTML que é especificado pela propriedade `HybridWebView.Uri`. O código especifica que o arquivo seja armazenado na pasta `Content` do projeto. Após a página da Web ser exibida, a função de JavaScript `invokeCSharpAction` será injetada na página da Web.
- Quando o elemento a que o renderizador está anexado é alterado:
  - Recursos são liberados.

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

### <a name="create-the-custom-renderer-on-android"></a>Criar o renderizador personalizado no Android

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
                Control.SetWebViewClient(new JavascriptWebViewClient($"javascript: {JavascriptFunction}"));
                Control.AddJavascriptInterface(new JSBridge(this), "jsBridge");
                Control.LoadUrl($"file:///android_asset/Content/{((HybridWebView)Element).Uri}");
            }
        }
    }
}
```

A classe `HybridWebViewRenderer` carrega a página da Web especificada na propriedade `HybridWebView.Uri` em um controle [`WebView`](xref:Android.Webkit.WebView) nativo e a função de JavaScript `invokeCSharpAction` é injetada na página da Web, após a página da Web terminar de ser carregada, com a substituição `OnPageFinished` na classe `JavascriptWebViewClient`:

```csharp
public class JavascriptWebViewClient : WebViewClient
{
    string _javascript;

    public JavascriptWebViewClient(string javascript)
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
  - O método `SetWebViewClient` define um novo objeto `JavascriptWebViewClient` como a implementação de `WebViewClient`.
  - O método [`WebView.AddJavascriptInterface`](xref:Android.Webkit.WebView.AddJavascriptInterface*) injeta uma nova instância de `JSBridge` no quadro principal do contexto do JavaScript do WebView, denominando-a `jsBridge`. Isso permite que métodos na classe `JSBridge` sejam acessados do JavaScript.
  - O método [`WebView.LoadUrl`](xref:Android.Webkit.WebView.LoadUrl*) carrega o arquivo HTML que é especificado pela propriedade `HybridWebView.Uri`. O código especifica que o arquivo seja armazenado na pasta `Content` do projeto.
  - Na classe `JavascriptWebViewClient`, a função de JavaScript `invokeCSharpAction` é injetada na página da Web depois que a página termina de ser carregada.
- Quando o elemento a que o renderizador está anexado é alterado:
  - Recursos são liberados.

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

A classe deve derivar de `Java.Lang.Object`, e os métodos que são expostos para o JavaScript devem ser decorados com os atributos `[JavascriptInterface]` e `[Export]`. Portanto, quando a função de JavaScript `invokeCSharpAction` é injetada na página da Web e é executada, ela chama o método `JSBridge.InvokeAction` por ter sido decorada com os atributos `[JavascriptInterface]` e `[Export("invokeAction")]`. Por sua vez, o método `InvokeAction` invoca o método `HybridWebView.InvokeAction`, que invocará a ação registrada para exibir o pop-up.

> [!IMPORTANT]
> Os projetos do Android que usam o atributo `[Export]` devem incluir uma referência a `Mono.Android.Export`ou um erro do compilador resultará.

Observe que a classe `JSBridge` mantém um `WeakReference` para a classe `HybridWebViewRenderer`. A finalidade é evitar a criação de uma referência circular entre as duas classes. Para obter mais informações, consulte [referências fracas](/en-us/dotnet/standard/garbage-collection/weak-references).

### <a name="create-the-custom-renderer-on-uwp"></a>Criar o renderizador personalizado no UWP

O exemplo de código a seguir mostra o renderizador personalizado para a UWP:

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
    }
}
```

A classe `HybridWebViewRenderer` carrega a página da Web especificada na propriedade `HybridWebView.Uri` em um controle `WebView` nativo e a função de JavaScript `invokeCSharpAction` é injetada na página da Web, após a página da Web ser carregada, com o método `WebView.InvokeScriptAsync`. Quando o usuário insere seu nome e clica no elemento HTML `button`, a função de JavaScript `invokeCSharpAction` é executada, com o método `OnWebViewScriptNotify` sendo chamado depois que uma notificação é recebida da página da Web. Por sua vez, esse método invoca o método `HybridWebView.InvokeAction`, que invoca a ação registrada para exibir o pop-up.

Essa funcionalidade é obtida da seguinte maneira:

- Desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms:
  - Manipuladores de eventos para os eventos `NavigationCompleted` e `ScriptNotify` são registrados. O evento `NavigationCompleted` é acionado quando o controle nativo `WebView` termina de carregar o conteúdo atual ou quando a navegação falha. O evento `ScriptNotify` é acionado quando o conteúdo no controle `WebView` nativo usa JavaScript para passar uma cadeia de caracteres para o aplicativo. A página da Web aciona o evento `ScriptNotify` chamando `window.external.notify` ao passar um parâmetro `string`.
  - A propriedade `WebView.Source` é definida como o URI do arquivo HTML que é especificado pela propriedade `HybridWebView.Uri`. O código presume que o arquivo seja armazenado na pasta `Content` do projeto. Depois que a página da Web é exibida, o evento `NavigationCompleted` é acionado e o método `OnWebViewNavigationCompleted` é invocado. A função de JavaScript `invokeCSharpAction`, em seguida, será injetada na página da Web com o método `WebView.InvokeScriptAsync`, desde que a navegação tenha sido concluída com êxito.
- Quando o elemento a que o renderizador está anexado é alterado:
  - A assinatura dos eventos é cancelada.

## <a name="related-links"></a>Links relacionados

- [HybridWebView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)
