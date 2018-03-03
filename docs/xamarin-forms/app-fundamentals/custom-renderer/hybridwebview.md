---
title: Implementando um HybridWebView
description: "Controles de interface de usuário personalizada do xamarin. Forms devem derivar da classe de exibição, que é usada para colocar controles na tela e layouts. Este artigo demonstra como criar um renderizador personalizado para um controle personalizado HybridWebView, que demonstra como melhorar os controles da web específico de plataforma para permitir que o código c# a ser chamado do JavaScript."
ms.topic: article
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: ef016d963f710ff54fc57b5e6e57181df030c8f6
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="implementing-a-hybridwebview"></a>Implementando um HybridWebView

_Controles de interface de usuário personalizada do xamarin. Forms devem derivar da classe de exibição, que é usada para colocar controles na tela e layouts. Este artigo demonstra como criar um renderizador personalizado para um controle personalizado HybridWebView, que demonstra como melhorar os controles da web específico de plataforma para permitir que o código c# a ser chamado do JavaScript._

Cada exibição xamarin. Forms tem um processador que o acompanha para cada plataforma que cria uma instância de um controle nativo. Quando um [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) é processado por um aplicativo xamarin. Forms no iOS, o `ViewRenderer` classe é instanciada, que por sua vez instancia um nativo `UIView` controle. Na plataforma Android, o `ViewRenderer` classe instancia um `View` controle. No Windows Phone e o Windows UWP (plataforma Universal), o `ViewRenderer` classe instancia um nativo `FrameworkElement` controle. Para obter mais informações sobre o processador e classes de controle nativo que mapeiam xamarin. Forms controles, consulte [Classes de Base de processador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O diagrama a seguir ilustra o relacionamento entre o [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) e os controles nativo correspondentes que implementação-la:

![](hybridwebview-images/view-classes.png "Relação entre a classe de exibição e suas Classes nativas de implementação")

O processo de renderização pode ser usado para implementar as personalizações específicas da plataforma, criando um renderizador personalizado para um [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) em cada plataforma. O processo para fazer isso é como segue:

1. [Criar](#Creating_the_HybridWebView) o `HybridWebView` controle personalizado.
1. [Consumir](#Consuming_the_HybridWebView) o `HybridWebView`do xamarin. Forms.
1. [Criar](#Creating_the_Custom_Renderer_on_each_Platform) o renderizador personalizado para o `HybridWebView` em cada plataforma.

Cada item agora será discutida sucessivamente para implementar um `HybridWebView` renderizador que aprimora os controles da web específico de plataforma para permitir que o código c# a ser chamado do JavaScript. O `HybridWebView` instância será usada para exibir uma página HTML que pede ao usuário para inserir seu nome. Em seguida, quando o usuário clica em um botão HTML, chamará uma função JavaScript c# `Action` que exibe um janela pop-up que contém o nome dos usuários.

Para obter mais informações sobre o processo para invocar c# do JavaScript, consulte [invocar c# do JavaScript](#Invoking_C_from_JavaScript). Para obter mais informações sobre a página HTML, consulte [criar a página da Web](#Creating_the_Web_Page).

<a name="Creating_the_HybridWebView" />

## <a name="creating-the-hybridwebview"></a>Criando o HybridWebView

O `HybridWebView` controle personalizado pode ser criado por subclassificação o [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) classe, conforme mostrado no exemplo de código a seguir:

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

O `HybridWebView` controle personalizado é criado no projeto de biblioteca (PCL) de classes portátil e define a seguinte API para o controle:

- Um `Uri` propriedade que especifica o endereço da página da web a ser carregado.
- Um `RegisterAction` método registra um `Action` com o controle. A ação registrada será chamada do JavaScript contido no arquivo HTML referenciado por meio de `Uri` propriedade.
- Um `CleanUp` método que remove a referência ao nome registrado `Action`.
- Um `InvokeAction` método que invoca o registrado `Action`. Esse método será chamado de um renderizador personalizado em cada projeto específico da plataforma.

<a name="Consuming_the_HybridWebView" />

## <a name="consuming-the-hybridwebview"></a>Consumindo o HybridWebView

O `HybridWebView` controle personalizado pode ser referenciado em XAML no projeto PCL declarando um namespace para seu local e usando o prefixo de namespace do controle personalizado. O seguinte exemplo de código mostra como o `HybridWebView` controle personalizado pode ser consumido por uma página XAML:

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

O `local` o prefixo do namespace pode ser qualquer nome. No entanto, o `clr-namespace` e `assembly` valores devem coincidir com os detalhes do controle personalizado. Depois que o namespace for declarado, o prefixo é usado para referenciar o controle personalizado.

O seguinte exemplo de código mostra como o `HybridWebView` controle personalizado pode ser consumido por uma página c#:

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

O `HybridWebView` instância será usada para exibir um controle de web nativo em cada plataforma. Ele tem `Uri` propriedade é definida como um arquivo HTML que é armazenado em cada projeto específico de plataforma e que será exibido pelo controle web nativo. O HTML renderizado pede ao usuário para inserir seu nome, com uma função JavaScript invocar c# `Action` em resposta a um clique de botão HTML.

O `HybridWebViewPage` registra a ação a ser chamado do JavaScript, conforme mostrado no exemplo de código a seguir:

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

Essa ação chama o [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert/p/System.String/System.String/System.String/) método para exibir um pop-up modal que apresenta o nome inserido na página HTML exibida pelo `HybridWebView` instância.

Agora é possível adicionar um renderizador personalizado para cada projeto de aplicativo para melhorar os controles da web específico de plataforma, permitindo que o código c# a ser chamado do JavaScript.

<a nane="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Criando o renderizador personalizado em cada plataforma

O processo para criar a classe de renderizador personalizado é o seguinte:

1. Criar uma subclasse do `ViewRenderer<T1,T2>` classe que renderiza o controle personalizado. O primeiro argumento de tipo deve ser um controle personalizado que o processador é nesse caso, `HybridWebView`. O segundo argumento de tipo deve ser um controle nativo que implementará o modo de exibição personalizado.
1. Substituir o `OnElementChanged` método que processa a lógica personalizada de controle e de gravação para personalizá-lo. Esse método é chamado quando o controle personalizado do xamarin. Forms correspondente é criado.
1. Adicionar um `ExportRenderer` de atributo para a classe de renderizador personalizado para especificar que será usada para renderizar o controle personalizado do xamarin. Forms. Este atributo é usado para registrar o renderizador personalizado com xamarin. Forms.

> [!NOTE]
> **Observação**: para a maioria dos elementos de xamarin. Forms, é opcional fornecer um renderizador personalizado em cada projeto da plataforma. Se um renderizador personalizado não estiver registrado, será usado o renderizador padrão para a classe base do controle. No entanto, renderizadores personalizados são necessários em cada projeto de plataforma ao renderizar um [exibição](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) elemento.

O diagrama a seguir ilustra as responsabilidades de cada projeto de aplicativo de exemplo, juntamente com as relações entre eles:

![](hybridwebview-images/solution-structure.png "Responsabilidades de projeto do renderizador HybridWebView personalizado")

O `HybridWebView` controle personalizado é processado por classes de processador específico da plataforma, que derivam de `ViewRenderer` classe para cada plataforma. Isso resulta em cada `HybridWebView` controle personalizado está sendo processado com controles específicos da plataforma web, conforme mostrado nas capturas de tela seguir:

![](hybridwebview-images/screenshots.png "HybridWebView em cada plataforma")

O `ViewRenderer` classe expõe o `OnElementChanged` método, que é chamado quando o controle personalizado do xamarin. Forms é criado para renderizar o controle da web nativo correspondente. Esse método usa um `ElementChangedEventArgs` parâmetro que contém `OldElement` e `NewElement` propriedades. Essas propriedades representam o elemento xamarin. Forms que o renderizador *foi* associada e o elemento xamarin. Forms que o renderizador *é* anexado, respectivamente. No aplicativo de amostra a `OldElement` propriedade será `null` e `NewElement` propriedade conterá uma referência para a `HybridWebView` instância.

Uma versão de substituição de `OnElementChanged` método na classe cada renderizador específico da plataforma, é o local para executar a instanciação do controle da web nativo e personalização. O `SetNativeControl` método deve ser usado para instanciar o controle da web nativo e esse método também atribuirá a referência de controle para o `Control` propriedade. Além disso, uma referência para o controle de xamarin. Forms que está sendo renderizado pode ser obtida por meio de `Element` propriedade.

Em algumas circunstâncias o `OnElementChanged` método pode ser chamado várias vezes. Portanto, para evitar perdas de memória, deve ter cuidado ao instanciar um novo controle nativo. A abordagem a ser usada ao instanciar um novo controle nativo em um renderizador personalizado é mostrada no exemplo de código a seguir:

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

Um novo controle nativo deve ser instanciado apenas uma vez, quando a propriedade `Control` é `null`. O controle deve ser configurado e os manipuladores de eventos devem ser inscritos apenas quando o renderizador personalizado for anexado a um novo elemento Xamarin.Forms. Da mesma forma, a inscrição de quaisquer manipuladores de evento inscritos só deve ser cancelada quando o elemento ao qual o renderizador está anexado for alterado. Adotar essa abordagem ajudará a criar um renderizador personalizado de alto desempenho que não apresenta vazamentos de memória.

Cada classe de renderizador personalizado é decorado com um `ExportRenderer` atributo que registra o renderizador com xamarin. Forms. O atributo utiliza dois parâmetros – o nome do tipo do controle personalizado xamarin. Forms está sendo processado e o nome do tipo de renderizador personalizado. O `assembly` prefixo para o atributo especifica que o atributo se aplica ao assembly inteiro.

As seções a seguir discutem a estrutura da página da web carregado por cada controle da web nativo, o processo para invocar c# do JavaScript e a implementação disso em cada classe de renderizador personalizado específico da plataforma.

<a name="Creating_the_Web_Page" />

### <a name="creating-the-web-page"></a>Criando a página da Web

O exemplo de código a seguir mostra a página da web que será exibido quando o `HybridWebView` controle personalizado:

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

A página da web permite que um usuário inserir seu nome de um `input` elemento e fornece um `button` elemento que invocará o código c# quando clicado. O processo para fazer isso é como segue:

- Quando o usuário clica no `button` elemento, o `invokeCSCode` função JavaScript é chamada, com o valor da `input` elemento que está sendo passado para a função.
- O `invokeCSCode` chamadas de função de `log` função para exibir os dados enviados para o c# `Action`. Depois, ele chama o `invokeCSharpAction` método a ser invocado c# `Action`, passando o parâmetro recebido do `input` elemento.

O `invokeCSharpAction` função JavaScript não está definida na página da web e será injetada nele por cada renderizador personalizado.

<a name="Invoking_C_from_JavaScript" />

### <a name="invoking-c-from-javascript"></a>Invocando c# do JavaScript

O processo para invocar c# do JavaScript é idêntico em cada plataforma:

- O renderizador personalizado cria um controle de web nativo e carrega o arquivo HTML especificado pelo `HybridWebView.Uri` propriedade.
- Quando a página da web é carregada, o renderizador personalizado injeta o `invokeCSharpAction` função JavaScript na página da web.
- Quando o usuário inserir seu nome e clicar em HTML `button` elemento, o `invokeCSCode` função é invocada, que por sua vez chama o `invokeCSharpAction` função.
- O `invokeCSharpAction` função invoca um método em um renderizador personalizado, que por sua vez chama o `HybridWebView.InvokeAction` método.
- O `HybridWebView.InvokeAction` método invoca registrado `Action`.

As seções a seguir, discutiremos como esse processo é implementado em cada plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Criando o renderizador personalizado no iOS

O exemplo de código a seguir mostra o renderizador personalizado para a plataforma iOS:

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

O `HybridWebViewRenderer` classe carrega a página da web especificada no `HybridWebView.Uri` propriedade em um nativo [ `WKWebView` ](https://developer.xamarin.com/api/type/WebKit.WKWebView/) controle e o `invokeCSharpAction` função JavaScript é inserida na página da web. Depois que o usuário insere seu nome e clica em HTML `button` elemento, o `invokeCSharpAction` função JavaScript é executada, com o `DidReceiveScriptMessage` método ser chamado depois que uma mensagem é recebida da página da web. Por sua vez, este método chama o `HybridWebView.InvokeAction` método, que chamará a ação registrada para exibir o pop-up.

Essa funcionalidade é obtida da seguinte maneira:

- Desde que o `Control` é de propriedade `null`, as seguintes operações são executadas:
  - Um [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) instância é criada, o que permite postar mensagens e injeção de scripts do usuário em uma página da web.
  - Um [ `WKUserScript` ](https://developer.xamarin.com/api/type/WebKit.WKUserScript/) instância é criada para injetar o `invokeCSharpAction` função JavaScript na página da web depois que a página da web é carregada.
  - O [ `WKUserContentController.AddScript` ](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddUserScript/p/WebKit.WKUserScript/) método adiciona o [ `WKUserScript` ](https://developer.xamarin.com/api/type/WebKit.WKUserScript/) instância para o controlador de conteúdo.
  - O [ `WKUserContentController.AddScriptMessageHandler` ](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddScriptMessageHandler/p/WebKit.IWKScriptMessageHandler/System.String/) método adiciona um manipulador de mensagens de script denominado `invokeAction` para o [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) instância, o que fará com que a função JavaScript `window.webkit.messageHandlers.invokeAction.postMessage(data)` a ser definido em todos os quadros em todas as exibições da web que usarão o `WKUserContentController` instância.
  - Um [ `WKWebViewConfiguration` ](https://developer.xamarin.com/api/type/WebKit.WKWebViewConfiguration/) instância é criada, com o [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) instância que está sendo definida como o controlador de conteúdo.
  - Um [ `WKWebView` ](https://developer.xamarin.com/api/type/WebKit.WKWebView/) controle é instanciado e o `SetNativeControl` método é chamado para atribuir uma referência para o `WKWebView` o controle para o `Control` propriedade.
- Desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms:
  - O [ `WKWebView.LoadRequest` ](https://developer.xamarin.com/api/member/WebKit.WKWebView.LoadRequest/p/Foundation.NSUrlRequest/) método carrega o arquivo HTML que é especificado pelo `HybridWebView.Uri` propriedade. O código especifica que o arquivo é armazenado no `Content` pasta do projeto. Depois que a página da web é exibida, o `invokeCSharpAction` função JavaScript será injetada na página da web.
- Quando o elemento que o processador é anexado a alterações:
  - Os recursos são liberados.

> [!NOTE]
> **Observação**: O `WKWebView` classe só tem suporte no iOS 8 e posterior.

### <a name="creating-the-custom-renderer-on-android"></a>Criando o renderizador personalizado no Android

O exemplo de código a seguir mostra o renderizador personalizado para a plataforma Android:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.Droid
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Android.Webkit.WebView>
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){jsBridge.invokeAction(data);}";
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
                Control.LoadUrl(string.Format("file:///android_asset/Content/{0}", Element.Uri));
                InjectJS(JavaScriptFunction);
            }
        }

        void InjectJS(string script)
        {
            if (Control != null)
            {
                Control.LoadUrl(string.Format("javascript: {0}", script));
            }
        }
    }
}
```

O `HybridWebViewRenderer` classe carrega a página da web especificada no `HybridWebView.Uri` propriedade em um nativo [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) controle e o `invokeCSharpAction` função JavaScript é inserida na página da web, depois de ter carregado a página da web, com o `InjectJS` método. Depois que o usuário insere seu nome e clica em HTML `button` elemento, o `invokeCSharpAction` função JavaScript é executada. Essa funcionalidade é obtida da seguinte maneira:

- Desde que o `Control` é de propriedade `null`, as seguintes operações são executadas:
  - Um nativo [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) instância é criada e o JavaScript está habilitado no controle.
  - O `SetNativeControl` método é chamado para atribuir uma referência para o nativo [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) o controle para o `Control` propriedade.
- Desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms:
  - O [ `WebView.AddJavascriptInterface` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.AddJavascriptInterface/p/Java.Lang.Object/System.String/) método insere um novo `JSBridge` instância para o quadro principal do contexto de JavaScript do WebView, nomeando- `jsBridge`. Isso permite que os métodos de `JSBridge` classe a ser acessado a partir de JavaScript.
  - O [ `WebView.LoadUrl` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.LoadUrl/p/System.String/) método carrega o arquivo HTML que é especificado pelo `HybridWebView.Uri` propriedade. O código especifica que o arquivo é armazenado no `Content` pasta do projeto.
  - O `InjectJS` método é invocado para injetar o `invokeCSharpAction` função JavaScript na página da web.
- Quando o elemento que o processador é anexado a alterações:
  - Os recursos são liberados.

Quando o `invokeCSharpAction` função JavaScript é executada, por sua vez, ele chama o `JSBridge.InvokeAction` método, que é mostrado no exemplo de código a seguir:

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

    if (hybridWebViewRenderer != null && hybridWebViewRenderer.TryGetTarget (out hybridRenderer)) {
      hybridRenderer.Element.InvokeAction (data);
    }
  }
}
```

A classe deve derivar de `Java.Lang.Object`, e métodos que são expostos para JavaScript devem ser decorados com o `[JavascriptInterface]` e `[Export]` atributos. Portanto, quando o `invokeCSharpAction` função JavaScript é injetada na página da web e é executada, ele chamará o `JSBridge.InvokeAction` método devido a ser decorados com o `[JavascriptInterface]` e `[Export("invokeAction")]` atributos. Por sua vez, o `InvokeAction` método invoca o `HybridWebView.InvokeAction` método, que será chamado a ação registrada para exibir o pop-up.

> [!NOTE]
> **Observação**: projetos que usam o `[Export]` atributo deve incluir uma referência a `Mono.Android.Export`, ou ocorrerá um erro de compilador.

Observe que o `JSBridge` classe mantém um `WeakReference` para o `HybridWebViewRenderer` classe. Isso é para evitar a criação de uma referência circular entre as duas classes. Para obter mais informações, consulte [referências fracas](https://msdn.microsoft.com/library/ms404247(v=vs.110).aspx) no MSDN.

> [!IMPORTANT]
> No Android Oreos Certifique-se de que o manifesto do Android define o **versão destino Android** para **automática**. Caso contrário, executar esse código resultará no erro mensagem "invokeCSharpAction não está definida".

### <a name="creating-the-custom-renderer-on-windows-phone-and-uwp"></a>Criando o renderizador personalizado no Windows Phone e UWP

O exemplo de código a seguir mostra o renderizador personalizado para o Windows Phone e UWP:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.WinPhone81
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

O `HybridWebViewRenderer` classe carrega a página da web especificada no `HybridWebView.Uri` propriedade em um nativo `WebView` controle e o `invokeCSharpAction` função JavaScript é inserida na página da web, depois de ter carregado a página da web, com o `WebView.InvokeScriptAsync` método. Depois que o usuário insere seu nome e clica em HTML `button` elemento, o `invokeCSharpAction` função JavaScript é executada, com o `OnWebViewScriptNotify` método ser chamado depois que uma notificação é recebida da página da web. Por sua vez, este método chama o `HybridWebView.InvokeAction` método, que chamará a ação registrada para exibir o pop-up.

Essa funcionalidade é obtida da seguinte maneira:

- Desde que o `Control` é de propriedade `null`, as seguintes operações são executadas:
  - O `SetNativeControl` método é chamado para instanciar um novo nativo `WebView` controlar e atribuir uma referência a ele para o `Control` propriedade.
- Desde que o renderizador personalizado está anexado a um novo elemento xamarin. Forms:
  - Manipuladores de eventos para o `NavigationCompleted` e `ScriptNotify` eventos são registrados. O `NavigationCompleted` evento é acionado quando qualquer nativo `WebView` controle terminou de carregar o conteúdo atual ou se a navegação falhou. O `ScriptNotify` evento é acionado quando o conteúdo nativo `WebView` controle usa JavaScript para passar uma cadeia de caracteres para o aplicativo. A página da web aciona o `ScriptNotify` evento chamando `window.external.notify` ao passar um `string` parâmetro.
  - O `WebView.Source` propriedade é definida para o URI do arquivo HTML que é especificado pelo `HybridWebView.Uri` propriedade. O código presume que o arquivo é armazenado no `Content` pasta do projeto. Depois que a página da web é exibida, o `NavigationCompleted` evento será acionado e o `OnWebViewNavigationCompleted` método será chamado. O `invokeCSharpAction` função JavaScript, em seguida, será injetada na página da web com o `WebView.InvokeScriptAsync` método, desde que a navegação foi concluída com êxito.
- Quando o elemento que o processador é anexado a alterações:
  - Os eventos são cancelados.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um renderizador personalizado para um `HybridWebView` controle personalizado, que demonstra como melhorar os controles da web específico de plataforma para permitir que o código c# a ser chamado do JavaScript.


## <a name="related-links"></a>Links relacionados

- [CustomRendererHybridWebView (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/hybridwebview/)
- [Chamar c# de JavaScript](https://developer.xamarin.com/recipes/android/controls/webview/call_csharp_from_javascript/)
