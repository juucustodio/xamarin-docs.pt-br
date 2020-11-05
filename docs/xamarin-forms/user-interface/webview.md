---
title: Xamarin.Forms WebView
description: Este artigo explica como usar a Xamarin.Forms classe WebView para apresentar conteúdo da Web local ou de rede e documentos aos usuários.
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9b22b46cb5452e558328c383ed97e66a60cfc166
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374128"
---
# <a name="no-locxamarinforms-webview"></a>Xamarin.Forms WebView

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/workingwithwebview)

[`WebView`](xref:Xamarin.Forms.WebView) é uma exibição para exibir conteúdo Web e HTML em seu aplicativo:

![No navegador de aplicativos](webview-images/in-app-browser.png)

## <a name="content"></a>Conteúdo

`WebView` o oferece suporte aos seguintes tipos de conteúdo:

- O HTML & sites CSS &ndash; da WebView tem suporte completo para sites escritos usando HTML & CSS, incluindo suporte a JavaScript.
- Documentos &ndash; como o WebView é implementado usando componentes nativos em cada plataforma, o WebView é capaz de mostrar documentos nos formatos com suporte na plataforma subjacente.
- Cadeias &ndash; de caracteres HTML podem mostrar cadeias de caracteres HTML da memória.
- Arquivos locais o &ndash; WebView pode apresentar qualquer um dos tipos de conteúdo acima inseridos no aplicativo.

> [!NOTE]
> `WebView` no Windows não oferece suporte a Silverlight, flash ou qualquer controle ActiveX, mesmo que eles tenham suporte do Internet Explorer nessa plataforma.

### <a name="websites"></a>Sites

Para exibir um site da Internet, defina a `WebView` Propriedade do [`Source`](xref:Xamarin.Forms.WebViewSource) como uma URL de cadeia de caracteres:

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> As URLs devem ser totalmente formadas com o protocolo especificado (ou seja, deve ter "http://" ou "https://" anexado a ela).

#### <a name="ios-and-ats"></a>iOS e ATS

Desde a versão 9, o iOS só permitirá que seu aplicativo se comunique com servidores que implementam a segurança de práticas recomendadas por padrão. Os valores devem ser definidos no `Info.plist` para habilitar a comunicação com servidores inseguros.

> [!NOTE]
> Se seu aplicativo exigir uma conexão com um site da Web não seguro, você sempre deverá inserir o domínio como uma exceção usando `NSExceptionDomains` , em vez de desativar completamente o ATS usando `NSAllowsArbitraryLoads` . `NSAllowsArbitraryLoads` Só deve ser usado em situações de emergência extremas.

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

Se você quiser apresentar uma cadeia de caracteres de HTML definida dinamicamente no código, será necessário criar uma instância do [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource) :

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

No código acima, `@` é usado para marcar o HTML como um [literal de cadeia de caracteres textual](/dotnet/csharp/programming-guide/strings/#regular-and-verbatim-string-literals), o que significa que a maioria dos caracteres de escape é ignorada.

> [!NOTE]
> Pode ser necessário definir as `WidthRequest` `HeightRequest` Propriedades e do [`WebView`](xref:Xamarin.Forms.WebView) para ver o conteúdo HTML, dependendo do layout do qual o `WebView` é filho. Por exemplo, isso é necessário em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) .

### <a name="local-html-content"></a>Conteúdo HTML local

O WebView pode exibir conteúdo de HTML, CSS e JavaScript inseridos no aplicativo. Por exemplo:

```html
<html>
  <head>
    <title>Xamarin Forms</title>
  </head>
  <body>
    <h1>Xamarin.Forms</h1>
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

Para exibir o conteúdo local usando um `WebView` , você precisará abrir o arquivo HTML como qualquer outro e, em seguida, carregar o conteúdo como uma cadeia de caracteres na `Html` propriedade de um `HtmlWebViewSource` . Para obter mais informações sobre como abrir arquivos, consulte [trabalhando com arquivos](~/xamarin-forms/data-cloud/data/files.md).

As capturas de tela a seguir mostram o resultado da exibição de conteúdo local em cada plataforma:

![Exibição da Web exibindo conteúdo local](webview-images/local-content.png)

Embora a primeira página tenha sido carregada, o `WebView` não tem conhecimento de onde veio o HTML. Isso é um problema ao lidar com páginas que fazem referência a recursos locais. Exemplos de quando isso pode acontecer incluem quando as páginas locais são vinculadas umas às outras, uma página usa um arquivo JavaScript separado ou um link de página para uma folha de estilos CSS.  

Para resolver isso, você precisa dizer ao `WebView` local onde encontrar os arquivos no sistema de arquivos. Faça isso definindo a `BaseUrl` propriedade no `HtmlWebViewSource` usada pelo `WebView` .

Como o sistema de arquivos em cada um dos sistemas operacionais é diferente, você precisa determinar essa URL em cada plataforma. Xamarin.Forms expõe o `DependencyService` para resolver dependências em tempo de execução em cada plataforma.

Para usar o `DependencyService` , primeiro defina uma interface que possa ser implementada em cada plataforma:

```csharp
public interface IBaseUrl { string Get(); }
```

Observe que, até que a interface seja implementada em cada plataforma, o aplicativo não será executado. No projeto comum, certifique-se de que você se lembra de definir o `BaseUrl` usando o `DependencyService` :

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

As implementações da interface para cada plataforma devem ser fornecidas.

#### <a name="ios"></a>iOS

No iOS, o conteúdo da Web deve estar localizado no diretório raiz do projeto ou na pasta de **recursos** com a ação de compilação *BundleResource* , conforme demonstrado abaixo:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Arquivos locais no iOS](webview-images/ios-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

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

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Arquivos locais no Android](webview-images/android-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![Arquivos locais no Android](webview-images/android-xs.png)

-----

No Android, o `BaseUrl` deve ser definido como `"file:///android_asset/"` :

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

No Android, os arquivos na pasta **ativos** também podem ser acessados por meio do contexto Android atual, que é exposto pela `MainActivity.Instance` Propriedade:

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html")))
{
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Em projetos Plataforma Universal do Windows (UWP), coloque HTML, CSS e imagens na raiz do projeto com a ação de compilação definida como *conteúdo*.

O `BaseUrl` deve ser definido como `"ms-appx-web:///"` :

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

- **GoForward ()** &ndash; Se `CanGoForward` for true, a chamada `GoForward` navegará para a próxima página visitada.
- **GoBack ()** &ndash; Se `CanGoBack` for true, chamar `GoBack` irá navegar para a última página visitada.
- **CanGoBack** &ndash; `true` se houver páginas para as quais navegar de volta, `false` se o navegador estiver na URL inicial.
- **CanGoForward** &ndash; `true` se o usuário navegou para trás e pode avançar para uma página que já foi visitada.

Em páginas, `WebView` o não oferece suporte a gestos multitoque. É importante garantir que o conteúdo seja otimizado para dispositivos móveis e apareça sem a necessidade de zoom.

É comum que os aplicativos mostrem um link dentro de um `WebView` , em vez do navegador do dispositivo. Nessas situações, é útil permitir a navegação normal, mas quando o usuário chega de volta enquanto está no link inicial, o aplicativo deve retornar à exibição de aplicativo normal.

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

É isso!

![Botões de navegação do WebView](webview-images/in-app-browser.png)

## <a name="events"></a>Eventos

O WebView gera os seguintes eventos para ajudá-lo a responder às alterações no estado:

- [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) – evento gerado quando o WebView começa a carregar uma nova página.
- [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) – evento gerado quando a página é carregada e a navegação é interrompida.
- [`ReloadRequested`](xref:Xamarin.Forms.WebView.ReloadRequested) – evento gerado quando uma solicitação é feita para recarregar o conteúdo atual.

O [`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs) objeto que acompanha o [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) evento tem quatro propriedades:

- `Cancel` – indica se a navegação deve ou não ser cancelada.
- `NavigationEvent` – o evento de navegação que foi gerado.
- `Source` – o elemento que realizou a navegação.
- `Url` – o destino de navegação.

O [`WebNavigatedEventArgs`](xref:Xamarin.Forms.WebNavigatedEventArgs) objeto que acompanha o [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) evento tem quatro propriedades:

- `NavigationEvent` – o evento de navegação que foi gerado.
- `Result` – Descreve o resultado da navegação, usando um [`WebNavigationResult`](xref:Xamarin.Forms.WebNavigationResult) membro de enumeração. Os valores válidos são `Cancel`, `Failure`, `Success` e `Timeout`.
- `Source` – o elemento que realizou a navegação.
- `Url` – o destino de navegação.

Se você antecipar o uso de páginas da Web que levam muito tempo para carregar, considere usar [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) os [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) eventos e para implementar um indicador de status. Por exemplo:

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

[`WebView`](xref:Xamarin.Forms.WebView) tem um `Reload` método que pode ser usado para recarregar o conteúdo atual:

```csharp
var webView = new WebView();
...
webView.Reload();
```

Quando o `Reload` método é invocado `ReloadRequested` , o evento é acionado, indicando que uma solicitação foi feita para recarregar o conteúdo atual.

## <a name="performance"></a>Desempenho

Navegadores populares da Web adotam tecnologias como processamento acelerado por hardware e compilação de JavaScript. Antes de Xamarin.Forms 4,4, o Xamarin.Forms `WebView` foi implementado no Ios pela `UIWebView` classe. No entanto, muitas dessas tecnologias não estavam disponíveis nessa implementação. Portanto, desde Xamarin.Forms 4,4, o Xamarin.Forms `WebView` é implementado no Ios pela `WkWebView` classe, que dá suporte à navegação mais rápida.

> [!NOTE]
> No iOS, o `WkWebViewRenderer` tem uma sobrecarga de construtor que aceita um `WkWebViewConfiguration` argumento. Isso permite que o renderizador seja configurado na criação.

Um aplicativo pode retornar ao uso da `UIWebView` classe Ios para implementar o Xamarin.Forms `WebView` , por motivos de compatibilidade. Isso pode ser feito adicionando o seguinte código ao arquivo **AssemblyInfo.cs** no projeto da plataforma Ios para o aplicativo:

```csharp
// Opt-in to using UIWebView instead of WkWebView.
[assembly: ExportRenderer(typeof(Xamarin.Forms.WebView), typeof(Xamarin.Forms.Platform.iOS.WebViewRenderer))]
```

`WebView` no Android, por padrão, é tão rápido quanto o navegador interno.

O [UWP WebView](/windows/uwp/design/controls-and-patterns/web-view) usa o mecanismo de renderização do Microsoft Edge. Os dispositivos desktop e Tablet devem ver o mesmo desempenho que usar o navegador Edge em si.

## <a name="permissions"></a>Permissões

Para que o `WebView` funcione, você deve verificar se as permissões estão definidas para cada plataforma. Observe que, em algumas plataformas, o `WebView` funcionará no modo de depuração, mas não quando compilado para a versão. Isso ocorre porque algumas permissões, como as de acesso à Internet no Android, são definidas por padrão por Visual Studio para Mac no modo de depuração.

- **UWP** &ndash; requer o recurso de Internet (cliente & Server) ao exibir o conteúdo da rede.
- **Android** &ndash; requer `INTERNET`  somente ao exibir o conteúdo da rede. O conteúdo local não requer permissões especiais.
- **Ios** &ndash; Não requer permissões especiais.

## <a name="layout"></a>Layout

Ao contrário da maioria das outras Xamarin.Forms exibições, o `WebView` requer que `HeightRequest` e `WidthRequest` sejam especificados quando contidos em StackLayout ou RelativeLayout. Se você não especificar essas propriedades, o `WebView` não será renderizado.

Os exemplos a seguir demonstram layouts que resultam em funcionamento, Renderizando `WebView` s:

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

[`WebView`](xref:Xamarin.Forms.WebView) inclui a capacidade de invocar uma função JavaScript do C# e retornar qualquer resultado para o código C# de chamada. Isso é feito com o [`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) método, que é mostrado no exemplo a seguir do exemplo do [WebView](/samples/xamarin/xamarin-forms-samples/userinterface-webview) :

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

O [`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) método avalia o JavaScript que é especificado como o argumento e retorna qualquer resultado como um `string` . Neste exemplo, a `factorial` função JavaScript é invocada, que retorna o fatorial de `number` como resultado. Essa função JavaScript é definida no arquivo HTML local que o [`WebView`](xref:Xamarin.Forms.WebView) carrega e é mostrada no exemplo a seguir:

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

## <a name="cookies"></a>Cookies

Os cookies podem ser definidos em um [`WebView`](xref:Xamarin.Forms.WebView) , que são enviados com a solicitação da Web para a URL especificada. Isso é feito adicionando `Cookie` objetos a um `CookieContainer` , que é então definido como o valor da `WebView.Cookies` propriedade vinculável. O código a seguir mostra um exemplo disso:

```csharp
using System.Net;
using Xamarin.Forms;
// ...

CookieContainer cookieContainer = new CookieContainer();
Uri uri = new Uri("https://dotnet.microsoft.com/apps/xamarin", UriKind.RelativeOrAbsolute);

Cookie cookie = new Cookie
{
    Name = "XamarinCookie",
    Expires = DateTime.Now.AddDays(1),
    Value = "My cookie",
    Domain = uri.Host,
    Path = "/"
};
cookieContainer.Add(uri, cookie);
webView.Cookies = cookieContainer;
webView.Source = new UrlWebViewSource { Url = uri.ToString() };
```

Neste exemplo, um único `Cookie` é adicionado ao `CookieContainer` objeto, que é então definido como o valor da `WebView.Cookies` propriedade. Quando o  [`WebView`](xref:Xamarin.Forms.WebView) envia uma solicitação da Web para a URL especificada, o cookie é enviado com a solicitação.

## <a name="uiwebview-deprecation-and-app-store-rejection-itms-90809"></a>Reprovação UIWebView e rejeição da loja de aplicativos (ITMS-90809)

A partir de abril de 2020, a [Apple rejeitará aplicativos](https://developer.apple.com/news/?id=12232019b) que ainda usam a API preterida `UIWebView` . Embora Xamarin.Forms o tenha mudado `WKWebView` como o padrão, ainda há uma referência ao SDK mais antigo nos Xamarin.Forms binários. O comportamento atual do [vinculador do IOS](~/ios/deploy-test/linker.md) não remove isso e, como resultado, a API preterida `UIWebView` ainda parecerá ser referenciada de seu aplicativo quando você enviar para a loja de aplicativos.

Uma versão de visualização do vinculador está disponível para corrigir esse problema. Para habilitar a visualização, você precisará fornecer um argumento adicional `--optimize=experimental-xforms-product-type` para o vinculador.

Os pré-requisitos para isso funcionam são:

- **Xamarin.Forms 4,5 ou superior**. Xamarin.Forms 4,6, ou superior, será necessário se seu aplicativo usar o Visual de material.
- **Xamarin. Ios 13.10.0.17 ou superior**. Verifique sua versão do Xamarin. iOS [no Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#version-information). Esta versão do Xamarin. iOS está incluída no Visual Studio para Mac 8.4.1 e no Visual Studio 16.4.3.
- **Remova as referências `UIWebView` a**. Seu código não deve ter nenhuma referência a `UIWebView` ou nenhuma classe que use `UIWebView` .

Para obter mais informações sobre como detectar e remover `UIWebView` referências, consulte [UIWebView preterition](~/ios/user-interface/controls/webview.md#uiwebview-deprecation).

### <a name="configure-the-linker"></a>Configurar o vinculador

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Siga estas etapas para que o vinculador remova as `UIWebView` referências:

1. **Abrir Propriedades** &ndash; do projeto Ios Clique com o botão direito do mouse no projeto iOS e escolha **Propriedades**.
1. **Navegue até a seção** &ndash; Build do IOS Selecione a seção **Build do IOS** .
1. **Atualizar os argumentos** &ndash; mTouch adicionais Nos **argumentos mTouch adicionais** , adicione esse sinalizador `--optimize=experimental-xforms-product-type` (além de qualquer valor que possa já estar lá). Observação: esse sinalizador funciona junto com o **comportamento do vinculador** definido como **somente SDK** ou **vincular tudo**. Se, por algum motivo, você vir erros ao definir o comportamento do vinculador como todos, isso provavelmente é um problema no código do aplicativo ou em uma biblioteca de terceiros que não é segura para o vinculador. Para obter mais informações sobre o vinculador, consulte [vinculando aplicativos Xamarin. Ios](~/ios/deploy-test/linker.md).
1. **Atualizar todas as configurações** &ndash; de compilação Use as listas de **configuração** e **plataforma** na parte superior da janela para atualizar todas as configurações de compilação. A configuração mais importante a ser atualizada é a configuração de **lançamento/iPhone** , pois ela normalmente é usada para criar compilações para envio da loja de aplicativos.

Você pode ver a janela com o novo sinalizador em vigor nesta captura de tela:

[![Definindo o sinalizador na seção Build do iOS](webview-images/iosbuildblade-vs-sml.png)](webview-images/iosbuildblade-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Siga estas etapas para que o vinculador remova as `UIWebView` referências:

1. **Abrir opções** &ndash; de projeto do IOS Clique com o botão direito do mouse no seu projeto iOS e escolha **Opções**.
1. **Navegue até a seção** &ndash; Build do IOS Selecione a seção **Build do IOS** .
1. **Atualize os argumentos _mTouch_ adicionais** &ndash; nos **argumentos adicionais do _mTouch_** adicione esse sinalizador `--optimize=experimental-xforms-product-type` (além de qualquer valor que possa já estar lá). Observação: esse sinalizador funciona junto com o **comportamento do vinculador** definido como **somente SDK** ou **vincular tudo**. Se, por algum motivo, você vir erros ao definir o comportamento do vinculador como todos, isso provavelmente é um problema no código do aplicativo ou em uma biblioteca de terceiros que não é segura para o vinculador. Para obter mais informações sobre o vinculador, consulte [vinculando aplicativos Xamarin. Ios](~/ios/deploy-test/linker.md).
1. **Atualizar todas as configurações** &ndash; de compilação Use as listas de **configuração** e **plataforma** na parte superior da janela para atualizar todas as configurações de compilação. A configuração mais importante a ser atualizada é a configuração de **lançamento/iPhone** , pois ela normalmente é usada para criar compilações para envio da loja de aplicativos.

Você pode ver a janela com o novo sinalizador em vigor nesta captura de tela:

[![Definindo o sinalizador na seção Build do iOS](webview-images/iosbuildblade-xs-sml.png)](webview-images/iosbuildblade-xs.png#lightbox)

-----

Agora, quando você cria uma nova compilação (versão) e a envia para a loja de aplicativos, não deve haver nenhum aviso sobre a API preterida.

## <a name="related-links"></a>Links Relacionados

- [Trabalhando com o WebView (exemplo)](/samples/xamarin/xamarin-forms-samples/workingwithwebview)
- [WebView (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-webview)
- [Reprovação de UIWebView](~/ios/user-interface/controls/webview.md#uiwebview-deprecation)