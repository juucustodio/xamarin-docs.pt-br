---
title: WebView do xamarin. Forms
description: Este artigo explica como usar a classe WebView do xamarin. Forms para apresentar local ou documentos e conteúdo da web de rede para os usuários.
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2019
ms.openlocfilehash: 26fbe6af639c67a94408605ba456bb3a100d2355
ms.sourcegitcommit: 3d39bafe4c56b15cbb695b1f7f02b926e1033f58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78291601"
---
# <a name="xamarinforms-webview"></a>WebView do xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)

[`WebView`](xref:Xamarin.Forms.WebView) é uma exibição para exibir conteúdo Web e HTML em seu aplicativo:

![No navegador de aplicativos](webview-images/in-app-browser.png)

## <a name="content"></a>Conteúdo

o `WebView` dá suporte aos seguintes tipos de conteúdo:

- O HTML & sites CSS &ndash; WebView tem suporte completo para sites escritos usando HTML & CSS, incluindo suporte a JavaScript.
- Documentos &ndash; como o WebView é implementado usando componentes nativos em cada plataforma, o WebView é capaz de mostrar documentos que são visíveis em cada plataforma. Isso significa que os arquivos PDF funcionam no iOS e Android.
- As cadeias de caracteres HTML &ndash; WebView podem mostrar cadeias de caracteres HTML da memória.
- Arquivos locais &ndash; o WebView pode apresentar qualquer um dos tipos de conteúdo acima inseridos no aplicativo.

> [!NOTE]
> `WebView` no Windows não oferece suporte a Silverlight, flash ou qualquer controle ActiveX, mesmo que eles tenham suporte do Internet Explorer nessa plataforma.

### <a name="websites"></a>Sites

Para exibir um site da Internet, defina a propriedade [`Source`](xref:Xamarin.Forms.WebViewSource) do `WebView`como uma URL de cadeia de caracteres:

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> URLs devem ser totalmente formadas com o protocolo especificado (ou seja, ele deve ter "http://" ou "https://" acrescentado a ele).

#### <a name="ios-and-ats"></a>iOS e ATS

Desde a versão 9, iOS só permitirá que seu aplicativo para se comunicar com servidores que implementam as práticas recomendadas de segurança por padrão. Os valores devem ser definidos em `Info.plist` para habilitar a comunicação com servidores inseguros.

> [!NOTE]
> Se seu aplicativo exigir uma conexão com um site da Web não seguro, você sempre deverá inserir o domínio como uma exceção usando `NSExceptionDomains` em vez de desativar completamente o ATS usando `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` só deve ser usada em situações de emergência extremas.

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

No código acima, `@` é usado para marcar o HTML como um [literal de cadeia de caracteres textual](/dotnet/csharp/programming-guide/strings/#regular-and-verbatim-string-literals), o que significa que a maioria dos caracteres de escape é ignorada.

> [!NOTE]
> Pode ser necessário definir as propriedades `WidthRequest` e `HeightRequest` do [`WebView`](xref:Xamarin.Forms.WebView) para ver o conteúdo HTML, dependendo do layout do qual o `WebView` é filho. Por exemplo, isso é necessário em uma [`StackLayout`](xref:Xamarin.Forms.StackLayout).

### <a name="local-html-content"></a>Conteúdo HTML local

WebView pode exibir o conteúdo do HTML, CSS e Javascript inserido dentro do aplicativo. Por exemplo:

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

Para exibir o conteúdo local usando um `WebView`, você precisará abrir o arquivo HTML como qualquer outro e, em seguida, carregar o conteúdo como uma cadeia de caracteres na propriedade `Html` de um `HtmlWebViewSource`. Para obter mais informações sobre como abrir arquivos, consulte [trabalhando com arquivos](~/xamarin-forms/data-cloud/data/files.md).

As capturas de tela a seguir mostram o resultado de exibição de conteúdo local em cada plataforma:

![Exibição da Web exibindo conteúdo local](webview-images/local-content.png)

Embora a primeira página tenha sido carregada, a `WebView` não tem conhecimento de onde veio o HTML. Isso é um problema ao lidar com páginas que fazem referência a recursos locais. Quando o link de páginas locais aos outros, uma página faz uso de um arquivo JavaScript separado ou uma página contém links para uma folha de estilos CSS são exemplos de quando isso pode acontecer.  

Para resolver isso, você precisa informar ao `WebView` onde encontrar arquivos no sistema de arquivos. Faça isso definindo a propriedade `BaseUrl` no `HtmlWebViewSource` usado pelo `WebView`.

Como o sistema de arquivos em cada um dos sistemas operacionais é diferente, você precisa determinar essa URL em cada plataforma. O Xamarin. Forms expõe a `DependencyService` para resolver dependências em tempo de execução em cada plataforma.

Para usar a `DependencyService`, primeiro defina uma interface que possa ser implementada em cada plataforma:

```csharp
public interface IBaseUrl { string Get(); }
```

Observe que, até que a interface é implementada em cada plataforma, o aplicativo não será executado. No projeto comum, certifique-se de que você se lembra de definir o `BaseUrl` usando o `DependencyService`:

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

Implementações da interface para cada plataforma, em seguida, devem ser fornecidas.

#### <a name="ios"></a>iOS

No iOS, o conteúdo da Web deve estar localizado no diretório raiz do projeto ou na pasta de **recursos** com a ação de compilação *BundleResource*, conforme demonstrado abaixo:

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

WebView dá suporte à navegação por meio de vários métodos e propriedades que ele se torna disponível:

- **GoForward ()** &ndash; se `CanGoForward` for true, chamar `GoForward` navegará para a próxima página visitada.
- **GoBack ()** &ndash; se `CanGoBack` for true, chamar `GoBack` navegará até a última página visitada.
- **CanGoBack** &ndash; `true` se houver páginas para as quais navegar de volta, `false` se o navegador estiver na URL inicial.
- **CanGoForward** &ndash; `true` se o usuário navegou para trás e pode avançar para uma página que já foi visitada.

Em páginas, `WebView` não dá suporte a gestos multitoque. É importante para garantir que o conteúdo é otimizada para celular e é exibido sem a necessidade de aumentar o zoom.

É comum que os aplicativos mostrem um link dentro de um `WebView`, em vez do navegador do dispositivo. Nessas situações, é útil permitir a navegação normal, mas quando o usuário optou novamente enquanto estiverem no link inicial, o aplicativo deve retornar ao modo de exibição normal do aplicativo.

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

É isso!

![Botões de navegação do WebView](webview-images/in-app-browser.png)

## <a name="events"></a>Eventos

WebView aciona os eventos a seguir para ajudá-lo a responder a alterações no estado:

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

![Exemplo de evento de navegação do WebView](webview-images/loading-start.png)

Carregamento concluído:

![Exemplo de evento de navegação do WebView](webview-images/loading-end.png)

## <a name="reloading-content"></a>Recarregar conteúdo

[`WebView`](xref:Xamarin.Forms.WebView) tem um método `Reload` que pode ser usado para recarregar o conteúdo atual:

```csharp
var webView = new WebView();
...
webView.Reload();
```

Quando o método de `Reload` é invocado, o evento de `ReloadRequested` é disparado, indicando que uma solicitação foi feita para recarregar o conteúdo atual.

## <a name="performance"></a>Desempenho

Navegadores populares da Web adotam tecnologias como processamento acelerado por hardware e compilação de JavaScript. Antes do Xamarin. Forms 4,4, a `WebView` Xamarin. Forms foi implementada no iOS pela classe `UIWebView`. No entanto, muitas dessas tecnologias não estavam disponíveis nessa implementação. Portanto, como o Xamarin. Forms 4,4, a `WebView` Xamarin. Forms é implementada no iOS pela classe `WkWebView`, que dá suporte à navegação mais rápida.

> [!NOTE]
> No iOS, o `WkWebViewRenderer` tem uma sobrecarga de construtor que aceita um argumento de `WkWebViewConfiguration`. Isso permite que o renderizador seja configurado na criação.

Um aplicativo pode retornar ao uso da classe `UIWebView` do iOS para implementar o Xamarin. Forms `WebView`, por motivos de compatibilidade. Isso pode ser feito adicionando o seguinte código ao arquivo **AssemblyInfo.cs** no projeto da plataforma Ios para o aplicativo:

```csharp
// Opt-in to using UIWebView instead of WkWebView.
[assembly: ExportRenderer(typeof(Xamarin.Forms.WebView), typeof(Xamarin.Forms.Platform.iOS.WebViewRenderer))]
```

o `WebView` no Android, por padrão, é tão rápido quanto o navegador interno.

O [UWP WebView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view) usa o mecanismo de renderização do Microsoft Edge. Dispositivos de desktop e tablet devem ver o mesmo desempenho quanto usar o próprio navegador Edge.

## <a name="permissions"></a>Permissões

Para que `WebView` funcionem, você deve verificar se as permissões estão definidas para cada plataforma. Observe que, em algumas plataformas, `WebView` funcionará no modo de depuração, mas não quando compilada para a versão. Isso ocorre porque algumas permissões, como aqueles para acesso à internet no Android, são definidos por padrão pelo Visual Studio para Mac quando no modo de depuração.

- A &ndash; **UWP** requer a capacidade de Internet (servidor de & cliente) ao exibir o conteúdo da rede.
- O **Android** &ndash; requer `INTERNET` somente ao exibir o conteúdo da rede. Conteúdo local não requer nenhuma permissão especial.
- o **iOS** &ndash; não requer permissões especiais.

## <a name="layout"></a>Layout

Ao contrário da maioria das outras exibições do Xamarin. Forms, `WebView` requer que `HeightRequest` e `WidthRequest` sejam especificados quando contidos em StackLayout ou RelativeLayout. Se você não especificar essas propriedades, o `WebView` não será renderizado.

Os exemplos a seguir demonstram layouts que resultam em trabalho, Renderizando `WebView`s:

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

## <a name="uiwebview-deprecation-and-app-store-rejection-itms-90809"></a>Reprovação UIWebView e rejeição da loja de aplicativos (ITMS-90809)

A partir de abril de 2020, a [Apple rejeitará aplicativos](https://developer.apple.com/news/?id=12232019b) que ainda usam a API de `UIWebView` preterida. Embora o Xamarin. Forms tenha alternado para `WKWebView` como o padrão, ainda há uma referência ao SDK mais antigo nos binários do Xamarin. Forms. O comportamento atual do [vinculador do IOS](~/ios/deploy-test/linker.md) não remove isso e, como resultado, a API de `UIWebView` preterida ainda parecerá ser referenciada de seu aplicativo quando você enviar para a loja de aplicativos.

Uma versão de visualização do vinculador está disponível para corrigir esse problema. Para habilitar a visualização, você precisará fornecer um argumento adicional `--optimize=experimental-xforms-product-type` ao vinculador. 

Os pré-requisitos para isso funcionam são:

- **Xamarin. forms 4,5 ou superior** &ndash; versões de pré-lançamento do Xamarin. Forms 4,5 podem ser usadas.
- **Xamarin. Ios 13.10.0.17 ou superior** &ndash; Verifique sua versão do Xamarin. Ios [no Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#version-information). Esta versão do Xamarin. iOS está incluída no Visual Studio para Mac 8.4.1 e no Visual Studio 16.4.3.
- **Remover referências a `UIWebView`** &ndash; seu código não deve ter nenhuma referência a `UIWebView` ou a classes que usam `UIWebView`.

### <a name="configure-the-linker-preview"></a>Configurar a visualização do vinculador

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Siga estas etapas para que o vinculador remova `UIWebView` referências:

1. **Abra as propriedades do projeto ios** &ndash; clique com o botão direito do mouse no seu projeto Ios e escolha **Propriedades**.
1. **Navegue até a seção Build do ios** &ndash; selecione a seção **Build do IOS** .
1. **Atualize os argumentos mTouch adicionais** &ndash; nos **argumentos adicionais do mTouch** adicione esse sinalizador `--optimize=experimental-xforms-product-type` (além de qualquer valor que possa já estar lá). Observação: esse sinalizador funciona junto com o **comportamento do vinculador** definido como **somente SDK** ou **vincular tudo**. Se, por algum motivo, você vir erros ao definir o comportamento do vinculador como todos, isso provavelmente é um problema no código do aplicativo ou em uma biblioteca de terceiros que não é segura para o vinculador. Para obter mais informações sobre o vinculador, consulte [vinculando aplicativos Xamarin. Ios](~/ios/deploy-test/linker.md).
1. **Atualize todas as configurações de compilação** &ndash; use as listas de **configuração** e **plataforma** na parte superior da janela para atualizar todas as configurações de compilação. A configuração mais importante a ser atualizada é a configuração de **lançamento/iPhone** , pois ela normalmente é usada para criar compilações para envio da loja de aplicativos.

Você pode ver a janela com o novo sinalizador em vigor nesta captura de tela:

[![definir o sinalizador na seção Build do iOS](webview-images/iosbuildblade-vs-sml.png)](webview-images/iosbuildblade-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Siga estas etapas para que o vinculador remova `UIWebView` referências

1. **Abra as opções de projeto do ios** &ndash; clique com o botão direito do mouse no seu projeto Ios e escolha **Opções**.
1. **Navegue até a seção Build do ios** &ndash; selecione a seção **Build do IOS** .
1. **Atualize os argumentos _mTouch_ adicionais** &ndash; nos **argumentos adicionais do _mTouch_**  adicione esse sinalizador `--optimize=experimental-xforms-product-type` (além de qualquer valor que possa já estar lá). Observação: esse sinalizador funciona junto com o **comportamento do vinculador** definido como **somente SDK** ou **vincular tudo**. Se, por algum motivo, você vir erros ao definir o comportamento do vinculador como todos, isso provavelmente é um problema no código do aplicativo ou em uma biblioteca de terceiros que não é segura para o vinculador. Para obter mais informações sobre o vinculador, consulte [vinculando aplicativos Xamarin. Ios](~/ios/deploy-test/linker.md).
1. **Atualize todas as configurações de compilação** &ndash; use as listas de **configuração** e **plataforma** na parte superior da janela para atualizar todas as configurações de compilação. A configuração mais importante a ser atualizada é a configuração de **lançamento/iPhone** , pois ela normalmente é usada para criar compilações para envio da loja de aplicativos.

Você pode ver a janela com o novo sinalizador em vigor nesta captura de tela:

[![definir o sinalizador na seção Build do iOS](webview-images/iosbuildblade-xs-sml.png)](webview-images/iosbuildblade-xs.png#lightbox)

-----

Agora, quando você cria uma nova compilação (versão) e a envia para a loja de aplicativos, não deve haver nenhum aviso sobre a API preterida.

## <a name="related-links"></a>Links relacionados

- [Trabalhando com o WebView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)
- [WebView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)
