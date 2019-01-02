---
title: Implementando um HybridWebView
description: Este artigo demonstra como criar um renderizador personalizado para um controle personalizado HybridWebView, que demonstra como aprimorar os controles da Web específicos a uma plataforma para permitir que código C# seja invocado do JavaScript.
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/19/2018
ms.openlocfilehash: f3b8cf7ec8a42ed031699d8f5e02f32c6eb61458
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53053854"
---
# <a name="implementing-a-hybridwebview"></a>Implementando um HybridWebView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/hybridwebview/)

_Controles de interfaces do usuário personalizadas do Xamarin.Forms devem derivar da classe View, que é usada para colocar os layouts e controles na tela. Este artigo demonstra como criar um renderizador personalizado para um controle personalizado HybridWebView, que demonstra como aprimorar os controles da Web específicos a uma plataforma para permitir que código C# seja invocado do JavaScript._

Cada exibição do Xamarin.Forms tem um renderizador que o acompanha para cada plataforma que cria uma instância de um controle nativo. Quando um [`View`](xref:Xamarin.Forms.View) é renderizado por um aplicativo Xamarin.Forms no iOS, é criada uma instância da classe `ViewRenderer`, que, por sua vez, cria uma instância de um controle `UIView` nativo. Na plataforma Android, a classe `ViewRenderer` cria uma instância de um controle `View`. Na UWP (Plataforma Universal do Windows), a classe `ViewRenderer` cria uma instância de um controle `FrameworkElement` nativo. Para obter mais informações sobre as classes de renderizador e de controle nativo para as quais os controles do Xamarin.Forms são mapeadas, confira [Classes base do renderizador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O seguinte diagrama ilustra a relação entre o [`View`](xref:Xamarin.Forms.View) e os controles nativos correspondentes que o implementam:

![](hybridwebview-images/view-classes.png "Relação entre a classe View e suas classes de implementação nativas")

E possível usar o processo de renderização para implementar personalizações específicas da plataforma criando um renderizador personalizado para um [`View`](xref:Xamarin.Forms.View) em cada plataforma. O processo para fazer isso é o seguinte:

1. [Criar](#Creating_the_HybridWebView) o controle personalizado `HybridWebView`.
1. [Consumir](#Consuming_the_HybridWebView) a `HybridWebView`do Xamarin.Forms.
1. [Criar](#Creating_the_Custom_Renderer_on_each_Platform) o renderizador personalizado para a página `HybridWebView` em cada plataforma.

Agora, abordaremos cada item separadamente para implementar um renderizador de `HybridWebView` que aprimora os controles da Web específicos da plataforma para permitir que código C# seja invocado do JavaScript. A instância de `HybridWebView` será usada para exibir uma página HTML que pede ao usuário para digitar seu nome. Em seguida, quando o usuário clicar em um botão HTML, uma função de JavaScript invocará um `Action` em C# que exibe um pop-up que contém o nome do usuário.

Para obter mais informações sobre o processo de invocar C# do JavaScript, confira [Invocando C# do JavaScript](#Invoking_C_from_JavaScript). Para obter mais informações sobre a página HTML, confira [Criando a página da Web](#Creating_the_Web_Page).

<a name="Creating_the_HybridWebView" />

## <a name="creating-the-hybridwebview"></a>Criando o HybridWebView

É possível criar o controle personalizado `HybridWebView` criando subclasses da classe [`View`](xref:Xamarin.Forms.View), conforme mostrado no exemplo de código a seguir:

```csharp
public class HybridWebView : View
{
  Action<string> action;
  public static readonly BindableProperty UriProperty = BindableProperty.Create (
    propertyName: "Uri",
    returnType: typeof(string),
    declaringType: typeof(HybridWebView),
    defaultValue: default(string));

  public string Uri {
    get { return (string)GetValue (UriProperty); }
    set { SetValue (UriProperty, value); }
  }

  public void RegisterAction (Action<string> callback)
  {
    action = callback;
  }

  public void Cleanup ()
  {
    action = null;
  }

  public void InvokeAction (string data)
  {
    if (action == null || data == null) {
      return;
    }
    action.Invoke (data);
  }
}
```

O controle personalizado `HybridWebView` é criado no projeto da biblioteca do .NET Standard e define a seguinte API para o controle:

- Uma propriedade `Uri` que especifica o endereço da página da Web a ser carregada.
- Um método `RegisterAction` que registra um `Action` com o controle. A ação registrada será invocada do JavaScript contido no arquivo HTML referenciado por meio da propriedade `Uri`.
- Um método `CleanUp` que remove a referência ao `Action` registrado.
- Um método `InvokeAction` que invoca o `Action` registrado. Esse método será chamado de um renderizador personalizado em cada projeto específico da plataforma.

<a name="Consuming_the_HybridWebView" />

## <a name="consuming-the-hybridwebview"></a>Consumindo o HybridWebView

O controle personalizado `HybridWebView` pode ser referenciado em XAML no projeto da biblioteca .NET Standard declarando um namespace para sua localização e usando o prefixo do namespace no controle personalizado. O exemplo de código a seguir mostra como o controle personalizado `HybridWebView` pode ser consumido por uma página XAML:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             x:Class="CustomRenderer.HybridWebViewPage"
             Padding="0,20,0,0">
    <ContentPage.Content>
        <local:HybridWebView x:Name="hybridWebView" Uri="index.html"
          HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
    </ContentPage.Content>
</ContentPage>
```

O prefixo do namespace `local` pode ser qualquer nome. No entanto, os valores de `clr-namespace` e `assembly` devem corresponder aos detalhes do controle personalizado. Quando o namespace é declarado, o prefixo é usado para referenciar o controle personalizado.

O seguinte exemplo de código mostra como o controle personalizado `HybridWebView` pode ser consumido por um página em C#:

```csharp
public class HybridWebViewPageCS : ContentPage
{
  public HybridWebViewPageCS ()
  {
    var hybridWebView = new HybridWebView {
      Uri = "index.html",
      HorizontalOptions = LayoutOptions.FillAndExpand,
      VerticalOptions = LayoutOptions.FillAndExpand
    };
    ...
    Padding = new Thickness (0, 20, 0, 0);
    Content = hybridWebView;
  }
}
```

A instância `HybridWebView` será usada para exibir um controle da Web nativo em cada plataforma. Sua propriedade `Uri` é definida como um arquivo HTML armazenado em cada projeto específico da plataforma, que será exibido pelo controle da Web nativo. O HTML renderizado solicita que o usuário insira seu nome, com uma função de JavaScript invocando um `Action` C# em resposta a um clique no botão HTML.

O `HybridWebViewPage` registra a ação a ser invocada do JavaScript, conforme mostrado no exemplo de código a seguir:

```csharp
public partial class HybridWebViewPage : ContentPage
{
  public HybridWebViewPage ()
  {
    ...
    hybridWebView.RegisterAction (data => DisplayAlert ("Alert", "Hello " + data, "OK"));
  }
}
```

Essa ação chama o método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) para exibir um pop-up modal que apresenta o nome inserido na página HTML exibida pela instância de `HybridWebView`.

Agora, é possível adicionar um renderizador personalizado a cada projeto de aplicativo para aprimorar os controles da Web específicos da plataforma, permitindo que o código C# seja invocado do JavaScript.

<a nane="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Criando o renderizador personalizado em cada plataforma

O processo para criar a classe do renderizador personalizado é o seguinte:

1. Crie uma subclasse da classe `ViewRenderer<T1,T2>` que renderiza o controle personalizado. O primeiro argumento de tipo deve ser o controle personalizado a que o renderizador se aplica, neste caso, `HybridWebView`. O segundo argumento de tipo deve ser o controle nativo que implementará a exibição personalizada.
1. Substitua o método `OnElementChanged` que renderiza o controle personalizado e escreva a lógica para personalizá-lo. Esse método é chamado quando o controle personalizado do Xamarin.Forms correspondente é criado.
1. Adicione um atributo `ExportRenderer` à classe do renderizador personalizado para especificar que ele será usado para renderizar o controle personalizado do Xamarin.Forms. Esse atributo é usado para registrar o renderizador personalizado no Xamarin.Forms.

> [!NOTE]
> Para a maioria dos elementos do Xamarin.Forms, o fornecimento de um renderizador personalizado em cada projeto de plataforma é opcional. Se um renderizador personalizado não estiver registrado, será usado o renderizador padrão da classe base do controle. No entanto, são necessários renderizadores personalizados em cada projeto da plataforma durante a renderização de um elemento [View](xref:Xamarin.Forms.View).

O seguinte diagrama ilustra as responsabilidades de cada projeto no aplicativo de exemplo, bem como as relações entre elas:

![](hybridwebview-images/solution-structure.png "Responsabilidades do projeto de renderizador personalizado de HybridWebView")

O controle personalizado `HybridWebView` é renderizado por classes de renderizador específicas da plataforma, que derivam da classe `ViewRenderer` para cada plataforma. Isso faz com que cada controle personalizado `HybridWebView` seja renderizado com controles da Web específicos da plataforma, conforme mostrado nas capturas de tela seguir:

![](hybridwebview-images/screenshots.png "HybridWebView em cada plataforma")

A classe `ViewRenderer` expõe o método `OnElementChanged`, que é chamado quando um controle personalizado do Xamarin.Forms é criado para renderizar o controle da Web nativo correspondente. Esse método usa um parâmetro `ElementChangedEventArgs`, que contém as propriedades `OldElement` e `NewElement`. Essas propriedades representam o elemento do Xamarin.Forms ao qual o renderizador *estava* anexado e o elemento do Xamarin.Forms ao qual o renderizador *está* anexado, respectivamente. No aplicativo de exemplo, a propriedade `OldElement` será `null` e a propriedade `NewElement` conterá uma referência à instância de `HybridWebView`.

Uma versão de substituição do método `OnElementChanged`, em cada classe de renderizador específica da plataforma, é o lugar para realização da instanciação e da personalização do controle da Web nativo. O método `SetNativeControl` deve ser usado para instanciar o controle da Web nativo e esse método também atribuirá a referência de controle à propriedade `Control`. Além disso, é possível obter uma referência ao controle do Xamarin.Forms que está sendo renderizado por meio da propriedade `Element`.

Em algumas circunstâncias, o método `OnElementChanged` pode ser chamado várias vezes. Portanto, para evitar perdas de memória, é necessário ter cuidado ao instanciar um novo controle nativo. A abordagem a ser usada ao instanciar um novo controle nativo em um renderizador personalizado é mostrada no exemplo de código a seguir:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (Control == null) {
    // Instantiate the native control and assign it to the Control property with
    // the SetNativeControl method
  }

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the control and subscribe to event handlers
  }
}
```

Um novo controle nativo deve ser instanciado apenas uma vez, quando a propriedade `Control` é `null`. O controle deve ser configurado e os manipuladores de eventos devem ser inscritos apenas quando o renderizador personalizado for anexado a um novo elemento Xamarin.Forms. Da mesma forma, a inscrição de quaisquer manipuladores de evento inscritos só deve ser cancelada quando o elemento ao qual o renderizador está anexado for alterado. Adotar essa abordagem ajudará a criar um renderizador personalizado com bom desempenho que não sofre perdas de memória.

Cada classe de renderizador personalizado é decorada com um atributo `ExportRenderer` que registra o renderizador no Xamarin.Forms. O atributo aceita dois parâmetros – o nome do tipo de controle personalizado do Xamarin.Forms que está sendo renderizado e o nome do tipo de renderizador personalizado. O prefixo `assembly` do atributo especifica que o atributo se aplica a todo o assembly.

As seções a seguir abordam a estrutura da página da Web carregada por cada controle da Web nativo, o processo para invocar C# do JavaScript e a implementação disso em cada classe de renderizador personalizado específica da plataforma.

<a name="Creating_the_Web_Page" />

### <a name="creating-the-web-page"></a>Criando a página da Web

O exemplo de código a seguir mostra a página da Web que será exibida pelo controle personalizado `HybridWebView`:

```html
<html>
<body>
<script src="http://code.jquery.com/jquery-1.11.0.min.js"></script>
<h1>HybridWebView Test</h1>
<br/>
Enter name: <input type="text" id="name">
<br/>
<br/>
<button type="button" onclick="javascript:invokeCSCode($('#name').val());">Invoke C# Code</button>
<br/>
<p id="result">Result:</p>
<script type="text/javascript">
function log(str)
{
    $('#result').text($('#result').text() + " " + str);
}

function invokeCSCode(data) {
    try {
        log("Sending Data:" + data);
        invokeCSharpAction(data);
    }
    catch (err){
          log(err);
    }
}
</script>
</body>
</html>
```

A página da Web permite que um usuário insira seu nome em um elemento `input` e fornece um elemento `button` que invocará o código C# quando clicado. O processo para fazer isso é o seguinte:

- Quando o usuário clica na elemento `button`, a função `invokeCSCode` de JavaScript é chamada, com o valor do elemento `input` sendo passado para a função.
- A função `invokeCSCode` chama a função `log` para exibir os dados que está enviando para o `Action` C#. Em seguida, ela chama o método `invokeCSharpAction` para invocar o `Action` C#, passando o parâmetro recebido do elemento `input`.

A função JavaScript `invokeCSharpAction` não está definida na página da Web e será injetada nela por cada renderizador personalizado.

<a name="Invoking_C_from_JavaScript" />

### <a name="invoking-c-from-javascript"></a>Invocando C# do JavaScript

O processo para invocar C# do JavaScript é idêntico em todas as plataformas:

- O renderizador personalizado cria um controle da Web nativo e carrega o arquivo HTML especificado pela propriedade `HybridWebView.Uri`.
- Quando a página da Web é carregada, o renderizador personalizado injeta a função de JavaScript `invokeCSharpAction` na página da Web.
- Quando o usuário insere seu nome e clica no elemento `button` HTML, a função `invokeCSCode` é invocada, que, por sua vez, invoca a função `invokeCSharpAction`.
- A função `invokeCSharpAction` invoca um método no renderizador personalizado, que por sua vez invoca o método `HybridWebView.InvokeAction`.
- O método `HybridWebView.InvokeAction` invoca o `Action` registrado.

As seções a seguir abordam como esse processo é implementado em cada plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Criando o renderizador personalizado no iOS

O seguinte exemplo de código mostra o renderizador personalizado para a plataforma iOS:

```csharp
[assembly: ExportRenderer (typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.iOS
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, WKWebView>, IWKScriptMessageHandler
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.webkit.messageHandlers.invokeAction.postMessage(data);}";
        WKUserContentController userController;

        protected override void OnElementChanged (ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged (e);

            if (Control == null) {
                userController = new WKUserContentController ();
                var script = new WKUserScript (new NSString (JavaScriptFunction), WKUserScriptInjectionTime.AtDocumentEnd, false);
                userController.AddUserScript (script);
                userController.AddScriptMessageHandler (this, "invokeAction");

                var config = new WKWebViewConfiguration { UserContentController = userController };
                var webView = new WKWebView (Frame, config);
                SetNativeControl (webView);
            }
            if (e.OldElement != null) {
                userController.RemoveAllUserScripts ();
                userController.RemoveScriptMessageHandler ("invokeAction");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup ();
            }
            if (e.NewElement != null) {
                string fileName = Path.Combine (NSBundle.MainBundle.BundlePath, string.Format ("Content/{0}", Element.Uri));
                Control.LoadRequest (new NSUrlRequest (new NSUrl (fileName, false)));
            }
        }

        public void DidReceiveScriptMessage (WKUserContentController userContentController, WKScriptMessage message)
        {
            Element.InvokeAction (message.Body.ToString ());
        }
    }
}
```

A classe `HybridWebViewRenderer` carrega a página da Web especificada na propriedade `HybridWebView.Uri` em um controle [`WKWebView`](https://developer.xamarin.com/api/type/WebKit.WKWebView/) nativo e a função de JavaScript `invokeCSharpAction` é injetada na página da Web. Quando o usuário insere seu nome e clica no elemento HTML `button`, a função de JavaScript `invokeCSharpAction` é executada, com o método `DidReceiveScriptMessage` sendo chamado depois que uma mensagem é recebida da página da Web. Por sua vez, esse método invoca o método `HybridWebView.InvokeAction`, que invoca a ação registrada para exibir o pop-up.

Essa funcionalidade é obtida da seguinte maneira:

- Desde que a propriedade `Control` seja `null`, as seguintes operações serão executadas:
  - Uma instância de [`WKUserContentController`](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) é criada, o que permite postar mensagens e injetar scripts do usuário em uma página da Web.
  - Uma instância de [`WKUserScript`](https://developer.xamarin.com/api/type/WebKit.WKUserScript/) é criada para injetar a função de JavaScript `invokeCSharpAction` na página da Web após ela ser carregada.
  - O método [`WKUserContentController.AddScript`](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddUserScript/p/WebKit.WKUserScript/) adiciona a instância de [`WKUserScript`](https://developer.xamarin.com/api/type/WebKit.WKUserScript/) ao controlador de conteúdo.
  - O método [`WKUserContentController.AddScriptMessageHandler`](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddScriptMessageHandler/p/WebKit.IWKScriptMessageHandler/System.String/) adiciona um manipulador de mensagens de script denominado `invokeAction` à instância de [`WKUserContentController`](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/), o que fará com que a função de JavaScript `window.webkit.messageHandlers.invokeAction.postMessage(data)` seja definida em todos os quadros em todas as exibições da Web que usarão a instância de `WKUserContentController`.
  - Uma instância de [`WKWebViewConfiguration`](https://developer.xamarin.com/api/type/WebKit.WKWebViewConfiguration/) é criada, com a instância de [`WKUserContentController`](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) sendo definida como o controlador de conteúdo.
  - Um controle [`WKWebView`](https://developer.xamarin.com/api/type/WebKit.WKWebView/) é instanciado e o método `SetNativeControl` é chamado para atribuir uma referência ao controle `WKWebView` à propriedade `Control`.
- Desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms:
  - O método [`WKWebView.LoadRequest`](https://developer.xamarin.com/api/member/WebKit.WKWebView.LoadRequest/p/Foundation.NSUrlRequest/) carrega o arquivo HTML que é especificado pela propriedade `HybridWebView.Uri`. O código especifica que o arquivo seja armazenado na pasta `Content` do projeto. Após a página da Web ser exibida, a função de JavaScript `invokeCSharpAction` será injetada na página da Web.
- Quando o elemento a que o renderizador está anexado é alterado:
  - Recursos são liberados.

> [!NOTE]
> A classe `WKWebView` tem suporte apenas no iOS 8 e posteriores.

### <a name="creating-the-custom-renderer-on-android"></a>Criando o renderizador personalizado no Android

O seguinte exemplo de código mostra o renderizador personalizado para a plataforma Android:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.Droid
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Android.Webkit.WebView>
    {
        const string JavascriptFunction = "function invokeCSharpAction(data){jsBridge.invokeAction(data);}";
        Context _context;

        public HybridWebViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (Control == null)
            {
                var webView = new Android.Webkit.WebView(_context);
                webView.Settings.JavaScriptEnabled = true;
                webView.SetWebViewClient(new JavascriptWebViewClient($"javascript: {JavascriptFunction}"));
                SetNativeControl(webView);
            }
            if (e.OldElement != null)
            {
                Control.RemoveJavascriptInterface("jsBridge");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup();
            }
            if (e.NewElement != null)
            {
                Control.AddJavascriptInterface(new JSBridge(this), "jsBridge");
                Control.LoadUrl($"file:///android_asset/Content/{Element.Uri}");
            }
        }
    }
}
```

A classe `HybridWebViewRenderer` carrega a página da Web especificada na propriedade `HybridWebView.Uri` em um controle [`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) nativo e a função de JavaScript `invokeCSharpAction` é injetada na página da Web, após a página da Web terminar de ser carregada, com a substituição `OnPageFinished` na classe `JavascriptWebViewClient`:

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

- Desde que a propriedade `Control` seja `null`, as seguintes operações serão executadas:
  - Uma instância de [`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) nativa é criada, o JavaScript é habilitado no controle e uma instância de `JavascriptWebViewClient` é definida como a implementação de `WebViewClient`.
  - O método `SetNativeControl` é chamado para atribuir uma referência ao controle [`WebView`](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) nativo à propriedade `Control`.
- Desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms:
  - O método [`WebView.AddJavascriptInterface`](https://developer.xamarin.com/api/member/Android.Webkit.WebView.AddJavascriptInterface/p/Java.Lang.Object/System.String/) injeta uma nova instância de `JSBridge` no quadro principal do contexto do JavaScript do WebView, denominando-a `jsBridge`. Isso permite que métodos na classe `JSBridge` sejam acessados do JavaScript.
  - O método [`WebView.LoadUrl`](https://developer.xamarin.com/api/member/Android.Webkit.WebView.LoadUrl/p/System.String/) carrega o arquivo HTML que é especificado pela propriedade `HybridWebView.Uri`. O código especifica que o arquivo seja armazenado na pasta `Content` do projeto.
  - Na classe `JavascriptWebViewClient`, a função de JavaScript `invokeCSharpAction` é injetada na página da Web depois que a página termina de ser carregada.
- Quando o elemento a que o renderizador está anexado é alterado:
  - Recursos são liberados.

Quando a função de JavaScript `invokeCSharpAction` é executada, por sua vez, ela invoca o método `JSBridge.InvokeAction`, que é mostrado no exemplo de código a seguir:

```csharp
public class JSBridge : Java.Lang.Object
{
  readonly WeakReference<HybridWebViewRenderer> hybridWebViewRenderer;

  public JSBridge (HybridWebViewRenderer hybridRenderer)
  {
    hybridWebViewRenderer = new WeakReference <HybridWebViewRenderer> (hybridRenderer);
  }

  [JavascriptInterface]
  [Export ("invokeAction")]
  public void InvokeAction (string data)
  {
    HybridWebViewRenderer hybridRenderer;

    if (hybridWebViewRenderer != null && hybridWebViewRenderer.TryGetTarget (out hybridRenderer))
    {
      hybridRenderer.Element.InvokeAction (data);
    }
  }
}
```

A classe deve derivar de `Java.Lang.Object`, e os métodos que são expostos para o JavaScript devem ser decorados com os atributos `[JavascriptInterface]` e `[Export]`. Portanto, quando a função de JavaScript `invokeCSharpAction` é injetada na página da Web e é executada, ela chama o método `JSBridge.InvokeAction` por ter sido decorada com os atributos `[JavascriptInterface]` e `[Export("invokeAction")]`. Por sua vez, o método `InvokeAction` invoca o método `HybridWebView.InvokeAction`, que invoca a ação registrada para exibir o pop-up.

> [!NOTE]
> Projetos que usam o atributo `[Export]` devem incluir uma referência a `Mono.Android.Export`, ou ocorrerá um erro no compilador.

Observe que a classe `JSBridge` mantém um `WeakReference` para a classe `HybridWebViewRenderer`. A finalidade é evitar a criação de uma referência circular entre as duas classes. Para obter mais informações, conste [Referências Fracas](https://msdn.microsoft.com/library/ms404247(v=vs.110).aspx) no MSDN.

### <a name="creating-the-custom-renderer-on-uwp"></a>Criando o renderizador personalizado na UWP

O seguinte exemplo de código mostra o renderizador personalizado para o UWP:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.UWP
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Windows.UI.Xaml.Controls.WebView>
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.external.notify(data);}";

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (Control == null)
            {
                SetNativeControl(new Windows.UI.Xaml.Controls.WebView());
            }
            if (e.OldElement != null)
            {
                Control.NavigationCompleted -= OnWebViewNavigationCompleted;
                Control.ScriptNotify -= OnWebViewScriptNotify;
            }
            if (e.NewElement != null)
            {
                Control.NavigationCompleted += OnWebViewNavigationCompleted;
                Control.ScriptNotify += OnWebViewScriptNotify;
                Control.Source = new Uri(string.Format("ms-appx-web:///Content//{0}", Element.Uri));
            }
        }

        async void OnWebViewNavigationCompleted(WebView sender, WebViewNavigationCompletedEventArgs args)
        {
            if (args.IsSuccess)
            {
                // Inject JS script
                await Control.InvokeScriptAsync("eval", new[] { JavaScriptFunction });
            }
        }

        void OnWebViewScriptNotify(object sender, NotifyEventArgs e)
        {
            Element.InvokeAction(e.Value);
        }
    }
}
```

A classe `HybridWebViewRenderer` carrega a página da Web especificada na propriedade `HybridWebView.Uri` em um controle `WebView` nativo e a função de JavaScript `invokeCSharpAction` é injetada na página da Web, após a página da Web ser carregada, com o método `WebView.InvokeScriptAsync`. Quando o usuário insere seu nome e clica no elemento HTML `button`, a função de JavaScript `invokeCSharpAction` é executada, com o método `OnWebViewScriptNotify` sendo chamado depois que uma notificação é recebida da página da Web. Por sua vez, esse método invoca o método `HybridWebView.InvokeAction`, que invoca a ação registrada para exibir o pop-up.

Essa funcionalidade é obtida da seguinte maneira:

- Desde que a propriedade `Control` seja `null`, as seguintes operações serão executadas:
  - O método `SetNativeControl` é chamado para instanciar um novo controle `WebView` nativo e para atribuir uma referência a ele para a propriedade `Control`.
- Desde que o renderizador personalizado esteja anexado a um novo elemento do Xamarin.Forms:
  - Manipuladores de eventos para os eventos `NavigationCompleted` e `ScriptNotify` são registrados. O evento `NavigationCompleted` é acionado quando o controle nativo `WebView` termina de carregar o conteúdo atual ou quando a navegação falha. O evento `ScriptNotify` é acionado quando o conteúdo no controle `WebView` nativo usa JavaScript para passar uma cadeia de caracteres para o aplicativo. A página da Web aciona o evento `ScriptNotify` chamando `window.external.notify` ao passar um parâmetro `string`.
  - A propriedade `WebView.Source` é definida como o URI do arquivo HTML que é especificado pela propriedade `HybridWebView.Uri`. O código presume que o arquivo seja armazenado na pasta `Content` do projeto. Depois que a página da Web é exibida, o evento `NavigationCompleted` é acionado e o método `OnWebViewNavigationCompleted` é invocado. A função de JavaScript `invokeCSharpAction`, em seguida, será injetada na página da Web com o método `WebView.InvokeScriptAsync`, desde que a navegação tenha sido concluída com êxito.
- Quando o elemento a que o renderizador está anexado é alterado:
  - A assinatura dos eventos é cancelada.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um renderizador personalizado para um controle personalizado `HybridWebView`, que demonstra como aprimorar os controles da Web específicos a uma plataforma para permitir que código C# seja invocado do JavaScript.


## <a name="related-links"></a>Links relacionados

- [CustomRendererHybridWebView (amostra)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/hybridwebview/)
- [Chamar C# do JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript)
