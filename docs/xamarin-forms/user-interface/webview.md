---
title: WebView do xamarin. Forms
description: Este artigo explica como usar a classe WebView do xamarin. Forms para apresentar local ou documentos e conteúdo da web de rede para os usuários.
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 02ea94fa67491384e6ca6768e429ee96b46c6143
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171333"
---
# <a name="xamarinforms-webview"></a>WebView do xamarin. Forms

[`WebView`](xref:Xamarin.Forms.WebView) é um modo de exibição da web e o conteúdo HTML em seu aplicativo. Diferentemente `OpenUri`, que leva o usuário para o navegador da web no dispositivo, `WebView` exibe o conteúdo HTML dentro de seu aplicativo.

![](webview-images/in-app-browser.png "No navegador do aplicativo")

## <a name="content"></a>Conteúdo

`WebView` suporta os seguintes tipos de conteúdo:

- Sites HTML e CSS &ndash; WebView tem suporte completo para sites da Web escritos usando HTML e CSS, incluindo suporte a JavaScript.
- Documentos &ndash; como WebView é implementado usando componentes nativos em cada plataforma, WebView é capaz de mostrar os documentos que podem ser exibidos em cada plataforma. Isso significa que os arquivos PDF funcionam no iOS e Android.
- Cadeias de caracteres HTML &ndash; WebView pode mostrar cadeias de caracteres HTML da memória.
- Arquivos locais &ndash; WebView pode apresentar qualquer um dos tipos de conteúdo acima inserido no aplicativo.

> [!NOTE]
> `WebView` no Windows não oferece suporte do Silverlight, Flash ou todos os controles ActiveX, mesmo se eles são suportados pelo Internet Explorer nessa plataforma.

### <a name="websites"></a>Sites

Para exibir um site da internet, defina as `WebView`do [ `Source` ](xref:Xamarin.Forms.WebViewSource) propriedade como uma cadeia de caracteres de URL:

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> URLs devem ser totalmente formadas com o protocolo especificado (ou seja, ele deve ter "http://" ou "https://" acrescentado a ele).

#### <a name="ios-and-ats"></a>iOS e ATS

Desde a versão 9, iOS só permitirá que seu aplicativo para se comunicar com servidores que implementam as práticas recomendadas de segurança por padrão. Valores devem ser definidos `Info.plist` para habilitar a comunicação com servidores inseguros.

> [!NOTE]
> Se seu aplicativo requer uma conexão em um site inseguro, você sempre deve inserir o domínio como uma exceção usando `NSExceptionDomains` em vez de como o ATS desativar completamente usando `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` só deve ser usado em situações extremas de emergências.

O exemplo a seguir demonstra como habilitar um domínio específico (em xamarin.com neste caso) para ignorar os requisitos de ATS:

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSExceptionDomains</key>
        <dict>
            <key>xamarin.com</key>
            <dict>
                <key>NSIncludesSubdomains</key>
                <true/>
                <key>NSTemporaryExceptionAllowsInsecureHTTPLoads</key>
                <true/>
                <key>NSTemporaryExceptionMinimumTLSVersion</key>
                <string>TLSv1.1</string>
            </dict>
        </dict>
    </dict>
    ...
</key>
```

É uma prática recomendada para permitir que somente alguns domínios ignorar a ATS, permitindo que você use sites confiáveis enquanto se beneficia com a segurança adicional em domínios não confiáveis. O exemplo a seguir demonstra o método menos seguro de desabilitar o ATS para o aplicativo:

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
    ...
</key>
```

Ver [segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md) para obter mais informações sobre esse novo recurso no iOS 9.

### <a name="html-strings"></a>Cadeias de caracteres HTML

Se você quiser apresentar uma cadeia de caracteres de HTML definida dinamicamente no código, você precisará criar uma instância do [ `HtmlWebViewSource` ](xref:Xamarin.Forms.HtmlWebViewSource):

```csharp
var browser = new WebView();
var htmlSource = new HtmlWebViewSource();
htmlSource.Html = @"<html><body>
  <h1>Xamarin.Forms</h1>
  <p>Welcome to WebView.</p>
  </body></html>";
browser.Source = htmlSource;
```

![](webview-images/html-string.png "Cadeia de caracteres do WebView exibir HTML")

No código acima, `@` é usado para marcar o HTML como uma cadeia de caracteres literal, o que significa que todos os caracteres de escape comuns são ignorados.

### <a name="local-html-content"></a>Conteúdo HTML local

WebView pode exibir o conteúdo do HTML, CSS e Javascript inserido dentro do aplicativo. Por exemplo:

```html
<html>
  <head>
    <title>Xamarin Forms</title>
  </head>
  <body>
    <h1>Xamrin.Forms</h1>
    <p>This is an iOS web page.</p>
    <img src="XamarinLogo.png" />
  </body>
</html>
```

CSS:

```css
html,body {
  margin:0;
  padding:10;
}
body,p,h1 {
  font-family: Chalkduster;
}
```

Observe que as fontes especificadas no CSS acima precisará ser personalizado para cada plataforma, pois nem todas as plataforma tem as mesmas fontes.

Para exibição local conteúdo usando um `WebView`, você precisará abrir o arquivo HTML como qualquer outro e, em seguida, carregar o conteúdo como uma cadeia de caracteres para o `Html` propriedade de um `HtmlWebViewSource`. Para obter mais informações sobre a abertura de arquivos, consulte [trabalhando com arquivos](~/xamarin-forms/app-fundamentals/files.md).

As capturas de tela a seguir mostram o resultado de exibição de conteúdo local em cada plataforma:

![](webview-images/local-content.png "Exibindo conteúdo Local do WebView")

Embora a primeira página é carregada, o `WebView` não tem conhecimento de onde veio o HTML. Isso é um problema ao lidar com páginas que fazem referência a recursos locais. Quando o link de páginas locais aos outros, uma página faz uso de um arquivo JavaScript separado ou uma página contém links para uma folha de estilos CSS são exemplos de quando isso pode acontecer.  

Para resolver isso, você precisa informar o `WebView` onde localizar os arquivos no sistema de arquivos. Fazer isso definindo a `BaseUrl` propriedade em de `HtmlWebViewSource` usado pelo `WebView`.

Como o sistema de arquivos em cada um dos sistemas operacionais é diferente, você precisa determinar essa URL em cada plataforma. Xamarin. Forms expõe a `DependencyService` para resolução de dependências em tempo de execução em cada plataforma.

Para usar o `DependencyService`, primeiro defina uma interface que pode ser implementada em cada plataforma:

```csharp
public interface IBaseUrl { string Get(); }
```

Observe que, até que a interface é implementada em cada plataforma, o aplicativo não será executado. No projeto comuns, certifique-se de que você se lembrar de definir a `BaseUrl` usando o `DependencyService`:

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

Implementações da interface para cada plataforma, em seguida, devem ser fornecidas.

#### <a name="ios"></a>iOS

No iOS, o conteúdo da web deve estar localizado no diretório raiz do projeto ou **recursos** diretório com a ação de build *BundleResource*, conforme demonstrado a seguir:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](webview-images/ios-vs.png "Arquivos locais no iOS")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](webview-images/ios-xs.png "Arquivos locais no iOS")

-----

O `BaseUrl` deve ser definido como o caminho do pacote principal:

```csharp
[assembly: Dependency (typeof (BaseUrl_iOS))]
namespace WorkingWithWebview.iOS
{
  public class BaseUrl_iOS : IBaseUrl
  {
    public string Get()
    {
      return NSBundle.MainBundle.BundlePath;
    }
  }
}
```

#### <a name="android"></a>Android

No Android, coloque o HTML, CSS e imagens na pasta ativos com a ação de build *AndroidAsset* como demonstrado a seguir:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](webview-images/android-vs.png "Arquivos locais no Android")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](webview-images/android-xs.png "Arquivos locais no Android")

-----

No Android, o `BaseUrl` deve ser definido como `"file:///android_asset/"`:

```csharp
[assembly: Dependency (typeof(BaseUrl_Android))]
namespace WorkingWithWebview.Android
{
  public class BaseUrl_Android : IBaseUrl
  {
    public string Get()
    {
      return "file:///android_asset/";
    }
  }
}
```

No Android, arquivos de **ativos** pasta também pode ser acessada por meio do contexto Android atual, que é exposto pelo `MainActivity.Instance` propriedade:

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html")))
{
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Em projetos de plataforma Universal do Windows (UWP), colocar HTML, CSS e imagens na raiz do projeto com a ação de build definida como *conteúdo*.

O `BaseUrl` deve ser definido como `"ms-appx-web:///"`:

```csharp
[assembly: Dependency(typeof(BaseUrl))]
namespace WorkingWithWebview.UWP
{
    public class BaseUrl : IBaseUrl
    {
        public string Get()
        {
            return "ms-appx-web:///";
        }
    }
}
```

## <a name="navigation"></a>Navegação

WebView dá suporte à navegação por meio de vários métodos e propriedades que ele se torna disponível:

- **GoForward()** &ndash; se `CanGoForward` for true, chamar `GoForward` navega para a próxima página visitada.
- **GoBack()** &ndash; se `CanGoBack` for true, chamar `GoBack` navegará para a última página visitada.
- **CanGoBack** &ndash; `true` se não houver páginas para navegar de volta para `false` se o navegador é a URL inicial.
- **CanGoForward** &ndash; `true` se o usuário navegar para trás e avance até uma página que já foi visitada.

Em páginas, `WebView` não oferece suporte a gestos multitoque. É importante para garantir que o conteúdo é otimizada para celular e é exibido sem a necessidade de aumentar o zoom.

É comum para aplicativos mostrar um link dentro de um `WebView`, em vez do navegador do dispositivo. Nessas situações, é útil permitir a navegação normal, mas quando o usuário optou novamente enquanto estiverem no link inicial, o aplicativo deve retornar ao modo de exibição normal do aplicativo.

Use as propriedades e métodos de navegação interna para habilitar esse cenário.

Comece criando a página para o modo de exibição do navegador:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="WebViewSample.InAppBrowserXaml"
             Title="Browser">
    <StackLayout Margin="20">
        <StackLayout Orientation="Horizontal">
            <Button Text="Back" HorizontalOptions="StartAndExpand" Clicked="OnBackButtonClicked" />
            <Button Text="Forward" HorizontalOptions="EndAndExpand" Clicked="OnForwardButtonClicked" />
        </StackLayout>
        <!-- WebView needs to be given height and width request within layouts to render. -->
        <WebView x:Name="webView" WidthRequest="1000" HeightRequest="1000" />
    </StackLayout>
</ContentPage>
```

No code-behind:

```csharp
public partial class InAppBrowserXaml : ContentPage
{
    public InAppBrowserXaml(string URL)
    {
        InitializeComponent();
        webView.Source = URL;
    }

    async void OnBackButtonClicked(object sender, EventArgs e)
    {
        if (webView.CanGoBack)
        {
            webView.GoBack();
        }
        else
        {
            await Navigation.PopAsync();
        }
    }

    void OnForwardButtonClicked(object sender, EventArgs e)
    {
        if (webView.CanGoForward)
        {
            webView.GoForward();
        }
    }
}
```

É só isso!

![](webview-images/in-app-browser.png "Botões de navegação do WebView")

## <a name="events"></a>Eventos

WebView aciona os eventos a seguir para ajudá-lo a responder a alterações no estado:

- **Navegando** – evento gerado quando o WebView começa a carregar uma nova página.
- **Navegado** – evento gerado quando a página é carregada e navegação foi interrompido.
- **ReloadRequested** – evento gerado quando uma solicitação é feita para recarregar o conteúdo atual.

Se você antecipar o uso de páginas da Web que levam muito tempo para carregar, considere usar o `Navigating` e `Navigated` eventos para implementar um indicador de status. Por exemplo, o XAML fica assim:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="WebViewSample.LoadingLabelXaml"
             Title="Loading Demo">
    <StackLayout>
        <!--Loading label should not render by default.-->
        <Label x:Name="labelLoading" Text="Loading..." IsVisible="false" />
        <WebView HeightRequest="1000" WidthRequest="1000" Source="http://www.xamarin.com" Navigated="webviewNavigated" Navigating="webviewNavigating" />
    </StackLayout>
</ContentPage>
```

Os dois manipuladores de evento:

```csharp
void webviewNavigating(object sender, WebNavigatingEventArgs e)
{
    labelLoading.IsVisible = true;
}

void webviewNavigated(object sender, WebNavigatedEventArgs e)
{
    labelLoading.IsVisible = false;
}
```

Isso resulta na seguinte saída (Carregando):

![](webview-images/loading-start.png "Exemplo de evento de navegação do WebView")

Carregamento concluído:

![](webview-images/loading-end.png "Exemplo de evento de navegação do WebView")

## <a name="reloading-content"></a>Recarregar conteúdo

[`WebView`](xref:Xamarin.Forms.WebView) tem um `Reload` método que pode ser usado para recarregar o conteúdo atual:

```csharp
var webView = new WebView();
...
webView.Reload();
```

Quando o `Reload` método é invocado o `ReloadRequested` evento é acionado, o que indica que uma solicitação foi feita para recarregar o conteúdo atual.

## <a name="performance"></a>Desempenho

Os navegadores da web populares agora adotam tecnologias, como a compilação de JavaScript e renderização acelerada por hardware. No iOS, por padrão, o xamarin. Forms `WebView` é implementado pelo `UIWebView` classe e várias dessas tecnologias não estão disponíveis nesta implementação. No entanto, um aplicativo pode participar de uso do iOS `WkWebView` classe para implementar o xamarin. Forms `WebView`, que dá suporte à navegação mais rápida. Isso pode ser feito adicionando o seguinte código para o **AssemblyInfo.cs** arquivo no projeto da plataforma iOS para o aplicativo:

```csharp
// Opt-in to using WkWebView instead of UIWebView.
[assembly: ExportRenderer(typeof(WebView), typeof(Xamarin.Forms.Platform.iOS.WkWebViewRenderer))]
```

`WebView` no Android por padrão é tão rápido quanto o navegador interno.

O [UWP WebView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view) usa o mecanismo de renderização do Microsoft Edge. Dispositivos de desktop e tablet devem ver o mesmo desempenho quanto usar o próprio navegador Edge.

## <a name="permissions"></a>Permissões

Para que `WebView` para trabalhar, certifique-se de que as permissões são definidas para cada plataforma. Observe que, em algumas plataformas, `WebView` funcionará no modo de depuração, mas não quando compilado para versão. Isso ocorre porque algumas permissões, como aqueles para acesso à internet no Android, são definidos por padrão pelo Visual Studio para Mac quando no modo de depuração.

- **UWP** &ndash; exige a funcionalidade de Internet (cliente e servidor), ao exibir o conteúdo de rede.
- **Android** &ndash; requer `INTERNET` apenas ao exibir o conteúdo da rede. Conteúdo local não requer nenhuma permissão especial.
- **iOS** &ndash; não exige nenhuma permissão especial.

## <a name="layout"></a>Layout

Ao contrário da maioria dos outros modos de xamarin. Forms `WebView` exige que `HeightRequest` e `WidthRequest` são especificados quando contido em StackLayout ou RelativeLayout. Se você não especificar essas propriedades, o `WebView` não será renderizado.

Os exemplos a seguir demonstram os layouts que resultam em funcionamento, renderização `WebView`s:

StackLayout com WidthRequest & HeightRequest:

```xaml
<StackLayout>
    <Label Text="test" />
    <WebView Source="http://www.xamarin.com/"
        HeightRequest="1000"
        WidthRequest="1000" />
</StackLayout>
```

RelativeLayout com WidthRequest & HeightRequest:

```xaml
<RelativeLayout>
    <Label Text="test"
        RelativeLayout.XConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=10}"
        RelativeLayout.YConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=20}" />
    <WebView Source="http://www.xamarin.com/"
        RelativeLayout.XConstraint="{ConstraintExpression Type=Constant,
                                     Constant=10}"
        RelativeLayout.YConstraint="{ConstraintExpression Type=Constant,
                                     Constant=50}"
        WidthRequest="1000" HeightRequest="1000" />
</RelativeLayout>
```

AbsoluteLayout *sem* WidthRequest & HeightRequest:

```xaml
<AbsoluteLayout>
    <Label Text="test" AbsoluteLayout.LayoutBounds="0,0,100,100" />
    <WebView Source="http://www.xamarin.com/"
      AbsoluteLayout.LayoutBounds="0,150,500,500" />
</AbsoluteLayout>
```

Grade *sem* WidthRequest & HeightRequest. A grade é um dos layouts de alguns que não exigem a especificação de larguras e alturas solicitadas.:

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="100" />
        <RowDefinition Height="*" />
    </Grid.RowDefinitions>
    <Label Text="test" Grid.Row="0" />
    <WebView Source="http://www.xamarin.com/" Grid.Row="1" />
</Grid>
```

## <a name="invoking-javascript"></a>Invocação de JavaScript

[`WebView`](xref:Xamarin.Forms.WebView) inclui a capacidade de invocar uma função de JavaScript do C#e retornar qualquer resultado para a chamada C# código. Isso é feito com o [ `WebView.EvaluateJavaScriptAsync` ](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) método, que é mostrado no exemplo a seguir do [WebView](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView) exemplo:

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

O [ `WebView.EvaluateJavaScriptAsync` ](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) método avalia o JavaScript que é especificado como o argumento e retorna qualquer resultado como um `string`. Neste exemplo, o `factorial` função de JavaScript é chamada, que retorna o fatorial de `number` como resultado. Esse JavaScript função é definida no HTML local do arquivo que o [ `WebView` ](xref:Xamarin.Forms.WebView) carrega e é mostrado no exemplo a seguir:

```html
<html>
<body>
<script type="text/javascript">
function factorial(num) {
        if (num === 0 || num === 1)
            return 1;
        for (var i = num - 1; i >= 1; i--) {
            num *= i;
        }
        return num;
}
</script>
</body>
</html>
```

## <a name="related-links"></a>Links relacionados

- [Trabalhando com WebView (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithWebview/)
- [WebView (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView)
