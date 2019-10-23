---
title: WebView Xamarin. Forms
description: Este artigo explica como usar a classe WebView Xamarin. Forms para apresentar conteúdo da Web local ou de rede e documentos aos usuários.
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2019
ms.openlocfilehash: cdb2a9f1a37dc7bca458a4291ce6fe5ac9c120aa
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696050"
---
# <a name="xamarinforms-webview"></a>WebView Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)

[`WebView`](xref:Xamarin.Forms.WebView) é uma exibição para exibir conteúdo Web e HTML em seu aplicativo:

![No navegador de aplicativos](webview-images/in-app-browser.png)

## <a name="content"></a>Conteúdo

o `WebView` dá suporte aos seguintes tipos de conteúdo:

- O HTML & sites CSS &ndash; WebView tem suporte completo para sites escritos usando HTML & CSS, incluindo suporte a JavaScript.
- Documentos &ndash; como o WebView é implementado usando componentes nativos em cada plataforma, o WebView é capaz de mostrar documentos que são visíveis em cada plataforma. Isso significa que os arquivos PDF funcionam no iOS e no Android.
- As cadeias de caracteres HTML &ndash; WebView podem mostrar cadeias de caracteres HTML da memória.
- Arquivos locais &ndash; o WebView pode apresentar qualquer um dos tipos de conteúdo acima inseridos no aplicativo.

> [!NOTE]
> `WebView` no Windows não oferece suporte a Silverlight, flash ou qualquer controle ActiveX, mesmo que eles tenham suporte do Internet Explorer nessa plataforma.

### <a name="websites"></a>Sites

Para exibir um site da Internet, defina a propriedade [`Source`](xref:Xamarin.Forms.WebViewSource) do `WebView` como uma URL de cadeia de caracteres:

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> As URLs devem ser totalmente formadas com o protocolo especificado (ou seja, deve ter "http://" ou "https://" anexado a ela).

#### <a name="ios-and-ats"></a>iOS e ATS

Desde a versão 9, o iOS só permitirá que seu aplicativo se comunique com servidores que implementam a segurança de práticas recomendadas por padrão. Os valores devem ser definidos em `Info.plist` para habilitar a comunicação com servidores inseguros.

> [!NOTE]
> Se seu aplicativo exigir uma conexão com um site da Web não seguro, você sempre deverá inserir o domínio como uma exceção usando `NSExceptionDomains` em vez de desativar completamente o ATS usando `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` só deve ser usada em situações de emergência extremas.

O exemplo a seguir demonstra como habilitar um domínio específico (neste caso, xamarin.com) para ignorar os requisitos de ATS:

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

É recomendável habilitar apenas alguns domínios para ignorar o ATS, permitindo que você use sites confiáveis enquanto beneficiando da segurança adicional em domínios não confiáveis. O seguinte demonstra o método menos seguro de desabilitar o ATS para o aplicativo:

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
    ...
</key>
```

Consulte [segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md) para obter mais informações sobre esse novo recurso no Ios 9.

### <a name="html-strings"></a>Cadeias de caracteres HTML

Se desejar apresentar uma cadeia de caracteres de HTML definida dinamicamente no código, você precisará criar uma instância do [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource):

```csharp
var browser = new WebView();
var htmlSource = new HtmlWebViewSource();
htmlSource.Html = @"<html><body>
  <h1>Xamarin.Forms</h1>
  <p>Welcome to WebView.</p>
  </body></html>";
browser.Source = htmlSource;
```

![WebView exibindo cadeia de caracteres HTML](webview-images/html-string.png)

No código acima, `@` é usado para marcar o HTML como um literal de cadeia de caracteres, o que significa que todos os caracteres de escape usuais são ignorados.

> [!NOTE]
> Pode ser necessário definir as propriedades `WidthRequest` e `HeightRequest` do [`WebView`](xref:Xamarin.Forms.WebView) para ver o conteúdo HTML, dependendo do layout do qual o `WebView` é filho. Por exemplo, isso é necessário em uma [`StackLayout`](xref:Xamarin.Forms.StackLayout).

### <a name="local-html-content"></a>Conteúdo HTML local

O WebView pode exibir conteúdo de HTML, CSS e JavaScript inseridos no aplicativo. Por exemplo:

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

CSS

```css
html,body {
  margin:0;
  padding:10;
}
body,p,h1 {
  font-family: Chalkduster;
}
```

Observe que as fontes especificadas no CSS acima precisarão ser personalizadas para cada plataforma, pois nem todas as plataformas têm as mesmas fontes.

Para exibir o conteúdo local usando um `WebView`, você precisará abrir o arquivo HTML como qualquer outro e, em seguida, carregar o conteúdo como uma cadeia de caracteres na propriedade `Html` de um `HtmlWebViewSource`. Para obter mais informações sobre como abrir arquivos, consulte [trabalhando com arquivos](~/xamarin-forms/data-cloud/data/files.md).

As capturas de tela a seguir mostram o resultado da exibição de conteúdo local em cada plataforma:

![Exibição da Web exibindo conteúdo local](webview-images/local-content.png)

Embora a primeira página tenha sido carregada, a `WebView` não tem conhecimento de onde veio o HTML. Isso é um problema ao lidar com páginas que fazem referência a recursos locais. Exemplos de quando isso pode acontecer incluem quando as páginas locais são vinculadas umas às outras, uma página usa um arquivo JavaScript separado ou um link de página para uma folha de estilos CSS.  

Para resolver isso, você precisa informar ao `WebView` onde encontrar arquivos no sistema de arquivos. Faça isso definindo a propriedade `BaseUrl` no `HtmlWebViewSource` usado pelo `WebView`.

Como o sistema de arquivos em cada um dos sistemas operacionais é diferente, você precisa determinar essa URL em cada plataforma. O Xamarin. Forms expõe a `DependencyService` para resolver dependências em tempo de execução em cada plataforma.

Para usar a `DependencyService`, primeiro defina uma interface que possa ser implementada em cada plataforma:

```csharp
public interface IBaseUrl { string Get(); }
```

Observe que, até que a interface seja implementada em cada plataforma, o aplicativo não será executado. No projeto comum, certifique-se de que você se lembra de definir o `BaseUrl` usando o `DependencyService`:

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

As implementações da interface para cada plataforma devem ser fornecidas.

#### <a name="ios"></a>iOS

No iOS, o conteúdo da Web deve estar localizado no diretório raiz do projeto ou na pasta de **recursos** com a ação de compilação *BundleResource*, conforme demonstrado abaixo:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Arquivos locais no iOS](webview-images/ios-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Arquivos locais no iOS](webview-images/ios-xs.png)

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

No Android, coloque HTML, CSS e imagens na pasta ativos com a ação de compilação *AndroidAsset* , conforme demonstrado abaixo:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Arquivos locais no Android](webview-images/android-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Arquivos locais no Android](webview-images/android-xs.png)

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

No Android, os arquivos na pasta **ativos** também podem ser acessados por meio do contexto Android atual, que é exposto pela propriedade `MainActivity.Instance`:

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html")))
{
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Em projetos Plataforma Universal do Windows (UWP), coloque HTML, CSS e imagens na raiz do projeto com a ação de compilação definida como *conteúdo*.

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

O WebView dá suporte à navegação por meio de vários métodos e propriedades que ele disponibiliza:

- **GoForward ()** &ndash; se `CanGoForward` for true, chamar `GoForward` navegará para a próxima página visitada.
- **GoBack ()** &ndash; se `CanGoBack` for true, chamar `GoBack` navegará até a última página visitada.
- **CanGoBack** &ndash; `true` se houver páginas para as quais navegar de volta, `false` se o navegador estiver na URL inicial.
- **CanGoForward** &ndash; `true` se o usuário navegou para trás e pode avançar para uma página que já foi visitada.

Em páginas, `WebView` não dá suporte a gestos multitoque. É importante garantir que o conteúdo seja otimizado para dispositivos móveis e apareça sem a necessidade de zoom.

É comum que os aplicativos mostrem um link dentro de um `WebView`, em vez do navegador do dispositivo. Nessas situações, é útil permitir a navegação normal, mas quando o usuário chega de volta enquanto está no link inicial, o aplicativo deve retornar à exibição de aplicativo normal.

Use as propriedades e os métodos de navegação internos para habilitar esse cenário.

Comece criando a página para o modo de exibição de navegador:

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

![Botões de navegação do WebView](webview-images/in-app-browser.png)

## <a name="events"></a>Eventos

O WebView gera os seguintes eventos para ajudá-lo a responder às alterações no estado:

- [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) – evento gerado quando o WebView começa a carregar uma nova página.
- [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) – evento gerado quando a página é carregada e a navegação é interrompida.
- [`ReloadRequested`](xref:Xamarin.Forms.WebView.ReloadRequested) – evento gerado quando uma solicitação é feita para recarregar o conteúdo atual.

O objeto [`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs) que acompanha o evento [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) tem quatro propriedades:

- `Cancel` – indica se a navegação deve ser cancelada ou não.
- `NavigationEvent` – o evento de navegação que foi gerado.
- `Source` – o elemento que realizou a navegação.
- `Url` – o destino de navegação.

O objeto [`WebNavigatedEventArgs`](xref:Xamarin.Forms.WebNavigatedEventArgs) que acompanha o evento [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) tem quatro propriedades:

- `NavigationEvent` – o evento de navegação que foi gerado.
- `Result` – descreve o resultado da navegação, usando um membro de enumeração [`WebNavigationResult`](xref:Xamarin.Forms.WebNavigationResult) . Os valores válidos são `Cancel`, `Failure`, `Success` e `Timeout`.
- `Source` – o elemento que realizou a navegação.
- `Url` – o destino de navegação.

Se você antecipar o uso de páginas da Web que demoram muito tempo para carregar, considere usar os eventos [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) e [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) para implementar um indicador de status. Por exemplo:

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

Os dois manipuladores de eventos:

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

Isso resulta na seguinte saída (carregando):

![Exemplo de evento de navegação do WebView](webview-images/loading-start.png)

Carregamento concluído:

![Exemplo de evento de navegação do WebView](webview-images/loading-end.png)

## <a name="reloading-content"></a>Recarregando conteúdo

[`WebView`](xref:Xamarin.Forms.WebView) tem um método `Reload` que pode ser usado para recarregar o conteúdo atual:

```csharp
var webView = new WebView();
...
webView.Reload();
```

Quando o método de `Reload` é invocado, o evento de `ReloadRequested` é disparado, indicando que uma solicitação foi feita para recarregar o conteúdo atual.

## <a name="performance"></a>Desempenho

Os navegadores da Web populares agora adotam tecnologias como processamento acelerado por hardware e compilação de JavaScript. No iOS, por padrão, o `WebView` Xamarin. Forms é implementado pela classe `UIWebView` e muitas dessas tecnologias não estão disponíveis nessa implementação. No entanto, um aplicativo pode optar por usar a classe `WkWebView` do iOS para implementar a `WebView` Xamarin. Forms, que dá suporte à navegação mais rápida. Isso pode ser feito adicionando o seguinte código ao arquivo **AssemblyInfo.cs** no projeto da plataforma Ios para o aplicativo:

```csharp
// Opt-in to using WkWebView instead of UIWebView.
[assembly: ExportRenderer(typeof(WebView), typeof(Xamarin.Forms.Platform.iOS.WkWebViewRenderer))]
```

> [!NOTE]
> No iOS, o `WkWebViewRenderer` tem uma sobrecarga de construtor que aceita um argumento de `WkWebViewConfiguration`. Isso permite que o renderizador seja configurado na criação.

o `WebView` no Android, por padrão, é tão rápido quanto o navegador interno.

O [UWP WebView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view) usa o mecanismo de renderização do Microsoft Edge. Os dispositivos desktop e Tablet devem ver o mesmo desempenho que usar o navegador Edge em si.

## <a name="permissions"></a>Permissões

Para que `WebView` funcionem, você deve verificar se as permissões estão definidas para cada plataforma. Observe que, em algumas plataformas, `WebView` funcionará no modo de depuração, mas não quando compilada para a versão. Isso ocorre porque algumas permissões, como as de acesso à Internet no Android, são definidas por padrão por Visual Studio para Mac no modo de depuração.

- A &ndash; **UWP** requer a capacidade de Internet (servidor de & cliente) ao exibir o conteúdo da rede.
- O **Android** &ndash; requer `INTERNET` somente ao exibir o conteúdo da rede. O conteúdo local não requer permissões especiais.
- o **iOS** &ndash; não requer permissões especiais.

## <a name="layout"></a>Layout

Ao contrário da maioria das outras exibições do Xamarin. Forms, `WebView` requer que `HeightRequest` e `WidthRequest` sejam especificados quando contidos em StackLayout ou RelativeLayout. Se você não especificar essas propriedades, o `WebView` não será renderizado.

Os exemplos a seguir demonstram layouts que resultam em trabalho, renderização `WebView`s:

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

Grade *sem* WidthRequest & HeightRequest. A grade é um dos poucos layouts que não requer a especificação de alturas e larguras solicitadas.:

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

## <a name="invoking-javascript"></a>Invocando JavaScript

[`WebView`](xref:Xamarin.Forms.WebView) inclui a capacidade de invocar uma função JavaScript C#do e retornar qualquer resultado para o código C# de chamada. Isso é feito com o método [`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) , que é mostrado no exemplo a seguir do exemplo do [WebView](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) :

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

O método [`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) avalia o JavaScript que é especificado como o argumento e retorna qualquer resultado como um `string`. Neste exemplo, a função JavaScript `factorial` é invocada, que retorna o fatorial de `number` como resultado. Essa função JavaScript é definida no arquivo HTML local que o [`WebView`](xref:Xamarin.Forms.WebView) carrega e é mostrada no exemplo a seguir:

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

- [Trabalhando com o WebView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)
- [WebView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)
