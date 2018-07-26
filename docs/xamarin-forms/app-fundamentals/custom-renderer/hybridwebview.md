---
title: Implementar um HybridWebView
description: Este artigo demonstra como criar um renderizador personalizado para um controle personalizado HybridWebView, que demonstra como aprimorar os controles específicos da plataforma web para permitir que o código c# a ser chamado do JavaScript.
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 4715685fdf417ba2d08f9ae3d36d6e691fa701fa
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242453"
---
# <a name="implementing-a-hybridwebview"></a>Implementar um HybridWebView

_Controles de interface de usuário personalizada do xamarin. Forms devem derivar da classe de exibição, que é usado para colocar os layouts e controles na tela. Este artigo demonstra como criar um renderizador personalizado para um controle personalizado HybridWebView, que demonstra como aprimorar os controles específicos da plataforma web para permitir que o código c# a ser chamado do JavaScript._

Cada modo de exibição do xamarin. Forms tem um renderizador que acompanha este artigo para cada plataforma que cria uma instância de um controle nativo. Quando um [ `View` ](xref:Xamarin.Forms.View) é processado por um aplicativo xamarin. Forms no iOS, o `ViewRenderer` classe é instanciada, que por sua vez cria uma instância de um nativo `UIView` controle. Na plataforma Android, o `ViewRenderer` cria uma instância de classe um `View` controle. Na Universal Windows Platform (UWP), o `ViewRenderer` classe instancia um nativo `FrameworkElement` controle. Para obter mais informações sobre as classes de controle nativo que mapeiam controles xamarin. Forms e o renderizador, consulte [Classes de Base do renderizador e controles nativos](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

O diagrama a seguir ilustra o relacionamento entre o [ `View` ](xref:Xamarin.Forms.View) e controles nativos correspondentes que implementação-lo:

![](hybridwebview-images/view-classes.png "Relação entre a classe de exibição e suas implementação Classes nativas")

O processo de renderização pode ser usado para implementar personalizações específicas de plataforma, criando um renderizador personalizado para um [ `View` ](xref:Xamarin.Forms.View) em cada plataforma. O processo para fazer isso é o seguinte:

1. [Crie](#Creating_the_HybridWebView) o `HybridWebView` controle personalizado.
1. [Consumir](#Consuming_the_HybridWebView) o `HybridWebView`do xamarin. Forms.
1. [Crie](#Creating_the_Custom_Renderer_on_each_Platform) o renderizador personalizado para o `HybridWebView` em cada plataforma.

Cada item agora será discutida por sua vez para implementar um `HybridWebView` renderizador que aprimora os controles específicos da plataforma web para permitir que o código c# a ser chamado do JavaScript. O `HybridWebView` instância será usada para exibir uma página HTML que pede ao usuário para digitar seu nome. Em seguida, quando o usuário clica em um botão HTML, invoca uma função JavaScript c# `Action` que exibe um pop-up que contém o nome dos usuários.

Para obter mais informações sobre o processo para invocar c# do JavaScript, consulte [invocar c# do JavaScript](#Invoking_C_from_JavaScript). Para obter mais informações sobre a página HTML, consulte [criando a página da Web](#Creating_the_Web_Page).

<a name="Creating_the_HybridWebView" />

## <a name="creating-the-hybridwebview"></a>Criando o HybridWebView

O `HybridWebView` controle personalizado pode ser criado pela criação de subclasses a [ `View` ](xref:Xamarin.Forms.View) de classe, conforme mostrado no exemplo de código a seguir:

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

O `HybridWebView` controle personalizado é criado no projeto da biblioteca .NET Standard e define a seguinte API para o controle:

- Um `Uri` propriedade que especifica o endereço da página da web a ser carregado.
- Um `RegisterAction` método registra uma `Action` com o controle. A ação registrada será invocada de JavaScript contido no arquivo HTML referenciado por meio de `Uri` propriedade.
- Um `CleanUp` método que remove a referência ao nome registrado `Action`.
- Uma `InvokeAction` método que invoca o registrado `Action`. Esse método será chamado de um renderizador personalizado em cada projeto específico da plataforma.

<a name="Consuming_the_HybridWebView" />

## <a name="consuming-the-hybridwebview"></a>Consumindo o HybridWebView

O `HybridWebView` controle personalizado pode ser referenciado em XAML no projeto da biblioteca .NET Standard, declarando um namespace para seu local e usando o prefixo de namespace no controle personalizado. O seguinte exemplo de código mostra como o `HybridWebView` controle personalizado pode ser consumido por uma página XAML:

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

O `local` prefixo de namespace pode ser qualquer nome. No entanto, o `clr-namespace` e `assembly` valores devem corresponder aos detalhes de controle personalizado. Depois que o namespace é declarado, o prefixo é usado para referenciar o controle personalizado.

O seguinte exemplo de código mostra como o `HybridWebView` controle personalizado pode ser consumido por uma página do c#:

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

O `HybridWebView` instância será usada para exibir um controle de web nativos em cada plataforma. Ele tem `Uri` estiver definida como um arquivo HTML que são armazenados em cada projeto específico da plataforma, e que será exibido pelo controle nativa da web. O HTML renderizado solicita que o usuário insira seu nome, com uma função JavaScript que invocar um c# `Action` em resposta a um clique de botão HTML.

O `HybridWebViewPage` registra a ação a ser invocado a partir de JavaScript, conforme mostrado no exemplo de código a seguir:

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

Essa ação chama o [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) método para exibir um pop-up modal que apresenta o nome inserido na página HTML exibida pelo `HybridWebView` instância.

Agora pode ser adicionado a um renderizador personalizado para cada projeto de aplicativo para aprimorar os controles de web específicos da plataforma, permitindo que o código c# a ser invocado a partir de JavaScript.

<a nane="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Criando o renderizador personalizado em cada plataforma

O processo para criar a classe de renderizador personalizado é da seguinte maneira:

1. Criar uma subclasse do `ViewRenderer<T1,T2>` classe que renderiza o controle personalizado. O primeiro argumento de tipo deve ser o controle personalizado que é o renderizador, neste caso `HybridWebView`. O segundo argumento de tipo deve ser o controle nativo que implementará o modo de exibição personalizado.
1. Substituir o `OnElementChanged` método que processa a lógica personalizada de controle e gravar para personalizá-lo. Esse método é chamado quando o controle personalizado do xamarin. Forms correspondente é criado.
1. Adicionar um `ExportRenderer` atributo à classe de renderizador personalizado para especificar que será usada para renderizar o controle personalizado do xamarin. Forms. Este atributo é usado para registrar o renderizador personalizado com o xamarin. Forms.

> [!NOTE]
> Para a maioria dos elementos de xamarin. Forms, é opcional fornecer um renderizador personalizado em cada projeto de plataforma. Se um renderizador personalizado não estiver registrado, o renderizador padrão para a classe base do controle será usado. No entanto, os renderizadores personalizados são necessárias em cada projeto da plataforma durante a renderização de um [exibição](xref:Xamarin.Forms.View) elemento.

O diagrama a seguir ilustra as responsabilidades de cada projeto no aplicativo de exemplo, juntamente com as relações entre eles:

![](hybridwebview-images/solution-structure.png "Responsabilidades do projeto de renderizador personalizado HybridWebView")

O `HybridWebView` controle personalizado é renderizado por classes de renderizador específica da plataforma, que derivam de `ViewRenderer` classe para cada plataforma. Isso resulta em cada `HybridWebView` controle personalizado que está sendo renderizado com controles específicos da plataforma web, conforme mostrado nas capturas de tela seguir:

![](hybridwebview-images/screenshots.png "HybridWebView em cada plataforma")

O `ViewRenderer` classe expõe o `OnElementChanged` método, que é chamado quando o controle personalizado do xamarin. Forms é criado para renderizar o controle de web nativo correspondente. Esse método usa um `ElementChangedEventArgs` parâmetro que contém `OldElement` e `NewElement` propriedades. Essas propriedades representam o elemento do xamarin. Forms que o renderizador *foi* associada e o elemento do xamarin. Forms que o renderizador *é* anexado, respectivamente. No aplicativo de exemplo do `OldElement` propriedade será `null` e o `NewElement` propriedade conterá uma referência para o `HybridWebView` instância.

Uma versão de substituição a `OnElementChanged` método em cada classe de renderizador específica da plataforma é o lugar para executar a instanciação de controle da web nativos e personalização. O `SetNativeControl` método deve ser usado para instanciar o controle da web nativos, e esse método também atribuirá a referência de controle para o `Control` propriedade. Além disso, uma referência para o controle de xamarin. Forms que está sendo renderizado pode ser obtida por meio de `Element` propriedade.

Em algumas circunstâncias a `OnElementChanged` método pode ser chamado várias vezes. Portanto, para evitar vazamentos de memória, é necessário ter cuidado ao instanciar um novo controle nativo. A abordagem a ser usada ao instanciar um novo controle nativo em um renderizador personalizado é mostrada no exemplo de código a seguir:

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

Um novo controle nativo deve ser instanciado apenas uma vez, quando a propriedade `Control` é `null`. O controle deve ser configurado e os manipuladores de eventos devem ser inscritos apenas quando o renderizador personalizado for anexado a um novo elemento Xamarin.Forms. Da mesma forma, a inscrição de quaisquer manipuladores de evento inscritos só deve ser cancelada quando o elemento ao qual o renderizador está anexado for alterado. Adotar essa abordagem ajudará a criar um renderizador personalizado de alto desempenho que não sofra perdas de memória.

Cada classe de renderizador personalizado é decorado com um `ExportRenderer` atributo que registra o renderizador com xamarin. Forms. O atributo utiliza dois parâmetros – o nome do tipo do controle personalizado xamarin. Forms que está sendo renderizado e o nome do tipo de renderizador personalizado. O `assembly` prefixo para o atributo especifica que o atributo se aplica a todo o assembly.

As seções a seguir discutem a estrutura da página da web carregados por cada controle nativa da web, o processo para invocar c# do JavaScript e a implementação disso em cada classe de renderizador personalizado específica da plataforma.

<a name="Creating_the_Web_Page" />

### <a name="creating-the-web-page"></a>Criando a página da Web

O exemplo de código a seguir mostra a página da web que será exibido pelo `HybridWebView` controle personalizado:

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

A página da web permite que um usuário insira seu nome de um `input` elemento e fornece um `button` elemento que invocará o código c# quando clicado. O processo para fazer isso é da seguinte maneira:

- Quando o usuário clica na `button` elemento, o `invokeCSCode` função de JavaScript é chamada, com o valor da `input` elemento que está sendo passado para a função.
- O `invokeCSCode` chamadas de função do `log` função para exibir os dados que ele está enviando para o c# `Action`. Em seguida, ele chama o `invokeCSharpAction` método para invocar o c# `Action`, passando o parâmetro recebido do `input` elemento.

O `invokeCSharpAction` função JavaScript não está definida na página da web e será injetada nela por cada renderizador personalizado.

<a name="Invoking_C_from_JavaScript" />

### <a name="invoking-c-from-javascript"></a>Invocar c# do JavaScript

O processo para invocar c# do JavaScript é idêntico em cada plataforma:

- O renderizador personalizado cria um controle da web nativos e carrega o arquivo HTML especificado pelo `HybridWebView.Uri` propriedade.
- Quando a página da web é carregada, o renderizador personalizado injeta o `invokeCSharpAction` função JavaScript na página da web.
- Quando o usuário insere seu nome e clica em HTML `button` elemento, o `invokeCSCode` função é invocada, que por sua vez chama o `invokeCSharpAction` função.
- O `invokeCSharpAction` função invocará um método em um renderizador personalizado, que por sua vez chama o `HybridWebView.InvokeAction` método.
- O `HybridWebView.InvokeAction` método invoca o registrado `Action`.

As seções a seguir discutirá como esse processo é implementado em cada plataforma.

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

O `HybridWebViewRenderer` classe carrega a página da web especificada na `HybridWebView.Uri` propriedade em um nativo [ `WKWebView` ](https://developer.xamarin.com/api/type/WebKit.WKWebView/) controle e o `invokeCSharpAction` função JavaScript é injetada na página da web. Quando o usuário insere seu nome e clicar em HTML `button` elemento, o `invokeCSharpAction` função JavaScript é executada, com o `DidReceiveScriptMessage` método ser chamado depois que uma mensagem é recebida da página da web. Por sua vez, esse método invoca o `HybridWebView.InvokeAction` método, que invoca a ação registrada para exibir o pop-up.

Essa funcionalidade é obtida da seguinte maneira:

- Desde que o `Control` é de propriedade `null`, as seguintes operações são executadas:
  - Um [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) instância é criada, que permite que as mensagens de lançamento e injeção de scripts do usuário em uma página da web.
  - Um [ `WKUserScript` ](https://developer.xamarin.com/api/type/WebKit.WKUserScript/) instância é criada para injetar o `invokeCSharpAction` função JavaScript na página da web depois que a página da web for carregada.
  - O [ `WKUserContentController.AddScript` ](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddUserScript/p/WebKit.WKUserScript/) método adiciona o [ `WKUserScript` ](https://developer.xamarin.com/api/type/WebKit.WKUserScript/) instância para o controlador de conteúdo.
  - O [ `WKUserContentController.AddScriptMessageHandler` ](https://developer.xamarin.com/api/member/WebKit.WKUserContentController.AddScriptMessageHandler/p/WebKit.IWKScriptMessageHandler/System.String/) método adiciona um manipulador de mensagens de script denominado `invokeAction` para o [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) instância, o que fará com que a função JavaScript `window.webkit.messageHandlers.invokeAction.postMessage(data)` seja definido em todos os quadros em todas as exibições da web que usarão o `WKUserContentController` instância.
  - Um [ `WKWebViewConfiguration` ](https://developer.xamarin.com/api/type/WebKit.WKWebViewConfiguration/) instância é criada, com o [ `WKUserContentController` ](https://developer.xamarin.com/api/type/WebKit.WKUserContentController/) instância que está sendo definida como o controlador de conteúdo.
  - Um [ `WKWebView` ](https://developer.xamarin.com/api/type/WebKit.WKWebView/) controle é instanciado e o `SetNativeControl` método é chamado para atribuir uma referência para o `WKWebView` o controle para o `Control` propriedade.
- Desde que o renderizador personalizado é anexado a um novo elemento xamarin. Forms:
  - O [ `WKWebView.LoadRequest` ](https://developer.xamarin.com/api/member/WebKit.WKWebView.LoadRequest/p/Foundation.NSUrlRequest/) método carrega o arquivo HTML que é especificado pelo `HybridWebView.Uri` propriedade. O código especifica que o arquivo é armazenado no `Content` pasta do projeto. Depois que a página da web é exibida, o `invokeCSharpAction` função JavaScript será injetada na página da web.
- Quando o elemento o renderizador está anexado for alterado:
  - Recursos são liberados.

> [!NOTE]
> O `WKWebView` classe só tem suporte no iOS 8 e posterior.

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

O `HybridWebViewRenderer` classe carrega a página da web especificada na `HybridWebView.Uri` propriedade em um nativo [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) controle e o `invokeCSharpAction` função JavaScript é injetada na página da web, depois que a página da web for carregada, com o `InjectJS` método. Quando o usuário insere seu nome e clicar em HTML `button` elemento, o `invokeCSharpAction` função JavaScript é executada. Essa funcionalidade é obtida da seguinte maneira:

- Desde que o `Control` é de propriedade `null`, as seguintes operações são executadas:
  - Um nativo [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) instância é criada e o JavaScript esteja habilitado no controle.
  - O `SetNativeControl` método é chamado para atribuir uma referência ao nativo [ `WebView` ](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) o controle para o `Control` propriedade.
- Desde que o renderizador personalizado é anexado a um novo elemento xamarin. Forms:
  - O [ `WebView.AddJavascriptInterface` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.AddJavascriptInterface/p/Java.Lang.Object/System.String/) método injeta um novo `JSBridge` instância no quadro principal do contexto do WebView do JavaScript, nomeando- `jsBridge`. Isso permite que os métodos no `JSBridge` classe a ser acessado do JavaScript.
  - O [ `WebView.LoadUrl` ](https://developer.xamarin.com/api/member/Android.Webkit.WebView.LoadUrl/p/System.String/) método carrega o arquivo HTML que é especificado pelo `HybridWebView.Uri` propriedade. O código especifica que o arquivo é armazenado no `Content` pasta do projeto.
  - O `InjectJS` método é invocado para injetar o `invokeCSharpAction` função JavaScript na página da web.
- Quando o elemento o renderizador está anexado for alterado:
  - Recursos são liberados.

Quando o `invokeCSharpAction` função JavaScript é executada, por sua vez, ele invoca o `JSBridge.InvokeAction` método, que é mostrado no exemplo de código a seguir:

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

A classe deve derivar de `Java.Lang.Object`, e métodos que são expostos para o JavaScript devem ser decorados com o `[JavascriptInterface]` e `[Export]` atributos. Portanto, quando o `invokeCSharpAction` função JavaScript é injetada na página da web e é executada, ele chamará o `JSBridge.InvokeAction` método devido ao que está sendo decorada com o `[JavascriptInterface]` e `[Export("invokeAction")]` atributos. Por sua vez, o `InvokeAction` método invoca o `HybridWebView.InvokeAction` método, que será chamado a ação registrada para exibir o pop-up.

> [!NOTE]
> Projetos que usam o `[Export]` atributo deve incluir uma referência ao `Mono.Android.Export`, ou ocorrerá um erro do compilador.

Observe que o `JSBridge` classe mantém uma `WeakReference` para o `HybridWebViewRenderer` classe. Isso é para evitar a criação de uma referência circular entre as duas classes. Para obter mais informações, consulte [referências fracas](https://msdn.microsoft.com/library/ms404247(v=vs.110).aspx) no MSDN.

> [!IMPORTANT]
> No Android Oreo Certifique-se de que o manifesto do Android define o **versão de destino do Android** à **automático**. Caso contrário, a execução desse código resultará no erro mensagem "invokeCSharpAction não está definida".

### <a name="creating-the-custom-renderer-on-uwp"></a>Criando o renderizador personalizado na UWP

O exemplo de código a seguir mostra o renderizador personalizado para UWP:

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

O `HybridWebViewRenderer` classe carrega a página da web especificada na `HybridWebView.Uri` propriedade em um nativo `WebView` controle e o `invokeCSharpAction` função JavaScript é injetada na página da web, depois que a página da web for carregada, com o `WebView.InvokeScriptAsync` método. Quando o usuário insere seu nome e clicar em HTML `button` elemento, o `invokeCSharpAction` função JavaScript é executada, com o `OnWebViewScriptNotify` método ser chamado depois que uma notificação é recebida da página da web. Por sua vez, esse método invoca o `HybridWebView.InvokeAction` método, que invoca a ação registrada para exibir o pop-up.

Essa funcionalidade é obtida da seguinte maneira:

- Desde que o `Control` é de propriedade `null`, as seguintes operações são executadas:
  - O `SetNativeControl` método é chamado para instanciar um novo nativo `WebView` controlar e atribua uma referência a ele para o `Control` propriedade.
- Desde que o renderizador personalizado é anexado a um novo elemento xamarin. Forms:
  - Manipuladores de eventos para o `NavigationCompleted` e `ScriptNotify` os eventos são registrados. O `NavigationCompleted` evento é acionado quando o nativo `WebView` controle terminou de carregar o conteúdo atual ou se a navegação falhou. O `ScriptNotify` evento é acionado quando o conteúdo nativo `WebView` controle usa JavaScript para passar uma cadeia de caracteres para o aplicativo. A página da web aciona a `ScriptNotify` evento chamando `window.external.notify` durante a passagem um `string` parâmetro.
  - O `WebView.Source` estiver definida como o URI do arquivo HTML que é especificado pelo `HybridWebView.Uri` propriedade. O código pressupõe que o arquivo é armazenado no `Content` pasta do projeto. Depois que a página da web é exibida, o `NavigationCompleted` evento será acionado e o `OnWebViewNavigationCompleted` método será invocado. O `invokeCSharpAction` função JavaScript, em seguida, será injetada na página da web com o `WebView.InvokeScriptAsync` método, desde que a navegação foi concluída com êxito.
- Quando o elemento o renderizador está anexado for alterado:
  - Os eventos são cancelados.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um renderizador personalizado para um `HybridWebView` controle personalizado, que demonstra como aprimorar os controles específicos da plataforma web para permitir que o código c# a ser chamado do JavaScript.


## <a name="related-links"></a>Links relacionados

- [CustomRendererHybridWebView (amostra)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/hybridwebview/)
- [Chamada de c# do JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript)
