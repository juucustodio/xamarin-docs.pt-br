---
title: WebView
description: "Apresentar local ou conteúdo da web de rede e documentos."
ms.topic: article
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: 7a077a3dcc47de8416abb0c51b23dc07fc1f1f12
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="webview"></a>WebView

[WebView](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) é um modo de exibição para exibição de HTML e da web de conteúdo em seu aplicativo. Ao contrário de `OpenUri`, que leva o usuário para o navegador da web no dispositivo, `WebView` exibe o conteúdo HTML dentro de seu aplicativo.

Este guia é composto das seguintes seções:

- **[Conteúdo](#Content)**  &ndash; WebView dá suporte a várias fontes de conteúdo, incluindo HTML incorporado, páginas da web e cadeias de caracteres HTML.
- **[Navegação](#Navigation)**  &ndash; WebView inclui suporte para navegar para uma página específica e voltar.
- **[Eventos](#Events)**  &ndash; ouça e responder a ações executadas pelo usuário na exibição da Web.
- **[Desempenho](#Performance)**  &ndash; Saiba mais sobre as características de desempenho do WebView em cada plataforma.
- **[Permissões](#Permissions)**  &ndash; saber como definir permissões para que funcione WebView em seu aplicativo.
- **[Layout](#Layout)**  &ndash; WebView tem alguns requisitos muito específico de como ela é apresentada. Saiba como verificar se a que exibição da Web exibe corretamente:

![](webview-images/in-app-browser.png "No navegador de aplicativo")

## <a name="content"></a>Conteúdo

WebView vem com suporte para os seguintes tipos de conteúdo:

- Sites HTML e CSS &ndash; WebView tem total suporte para sites da Web escritos usando HTML e CSS, incluindo suporte a JavaScript.
- Documentos &ndash; como WebView é implementado usando componentes nativos em cada plataforma, WebView é capaz de Mostrar documentos que podem ser exibidos em cada plataforma. Isso significa que os arquivos PDF funcionarão no iOS e Android, mas não o Windows Phone.
- Cadeias de caracteres HTML &ndash; WebView pode mostrar cadeias de caracteres HTML da memória.
- Arquivos locais &ndash; WebView pode apresentar qualquer um dos tipos de conteúdo acima inserido no aplicativo.

> [!NOTE]
> `WebView` no Windows e Windows Phone não dá suporte do Silverlight, Flash ou os controles ActiveX, mesmo se eles são compatíveis com o Internet Explorer nessa plataforma.

### <a name="websites"></a>Sites

Para exibir um site da internet, defina o `WebView`do [ `Source` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebViewSource/) propriedade como uma cadeia de caracteres de URL:

```csharp
var browser = new WebView {
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> URLs devem ser totalmente formadas com o protocolo especificado (ou seja, ele deve ter "http://" ou "https://" acrescentado a ele).

#### <a name="ios-and-ats"></a>iOS e ATS

Desde a versão 9, o iOS só permitirá que seu aplicativo para se comunicar com servidores que implementam as práticas recomendadas de segurança por padrão. Valores devem ser definidos `Info.plist` para habilitar a comunicação com servidores não seguros.

> [!NOTE]
> Se seu aplicativo requer uma conexão para um site inseguro, você deve sempre inserir o domínio como uma exceção usando `NSExceptionDomains` em vez de como ATS desativar completamente usando `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` deve ser usado somente em situações de emergências extremas.

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
```

É recomendável habilitar somente alguns domínios ignorar ATS, permitindo que você use sites confiáveis ao mesmo tempo em que usam a segurança adicional em domínios não confiáveis. A seguir demonstra o método menos seguro de desabilitar ATS para o aplicativo:

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
```

Consulte [segurança de transporte do aplicativo](~/ios/app-fundamentals/ats.md) para obter mais informações sobre esse novo recurso no iOS 9.

### <a name="html-strings"></a>Cadeias de caracteres HTML

Se você quiser apresentar uma cadeia de caracteres de HTML dinamicamente definido no código, você precisará criar uma instância de [ `HtmlWebViewSource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.HtmlWebViewSource/):

```csharp
var browser = new WebView();
var htmlSource = new HtmlWebViewSource();
htmlSource.Html = @"<html><body>
  <h1>Xamarin.Forms</h1>
  <p>Welcome to WebView.</p>
  </body></html>";
browser.Source = htmlSource;
```

![](webview-images/html-string.png "Cadeia de caracteres do WebView exibindo HTML")

No código acima, `@` é usado para marcar o HTML como uma cadeia de caracteres literal, o que significa que todos os caracteres de escape comum são ignorados.

### <a name="local-html-content"></a>Conteúdo HTML local

Exibição da Web pode exibir o conteúdo do HTML, CSS e Javascript inserido dentro do aplicativo. Por exemplo:

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

Observe que as fontes especificadas na folha de estilos acima precisará ser personalizado para cada plataforma, como não cada plataforma tem as mesmas fontes.

Para exibição local conteúdo usando um `WebView`, você precisará abrir o arquivo HTML como qualquer outro e, em seguida, carregar o conteúdo como uma cadeia de caracteres no `Html` propriedade de um `HtmlWebViewSource`. Para obter mais informações sobre como abrir arquivos, consulte [trabalhando com arquivos](~/xamarin-forms/app-fundamentals/files.md).

As capturas de tela a seguir mostram o resultado de exibição de conteúdo local em cada plataforma:

![](webview-images/local-content.png "Exibindo conteúdo Local do WebView")

Embora a primeira página é carregada, o `WebView` não tem conhecimento de onde veio o HTML. Isso é um problema ao lidar com páginas que fazem referência a recursos locais. Quando o link de páginas de local para cada outro, uma página faz uso de um arquivo JavaScript separado ou uma página está vinculada a uma folha de estilos CSS são exemplos de quando isso pode acontecer.  

Para resolver isso, você precisa saber o `WebView` onde localizar os arquivos no sistema de arquivos. Fazer isso definindo o `BaseUrl` propriedade o `HtmlWebViewSource` usado pelo `WebView`.

Como o sistema de arquivos em cada um dos sistemas operacionais é diferente, você precisa determinar essa URL em cada plataforma. Xamarin. Forms expõe o `DependencyService` para resolver as dependências em tempo de execução em cada plataforma.

Para usar o `DependencyService`, primeiro defina uma interface que pode ser implementada em cada plataforma:

```csharp
public interface IBaseUrl { string Get(); }
```

Observe que, até que a interface é implementada em cada plataforma, o aplicativo não será executado. No projeto comum, certifique-se de que você lembre-se de definir o `BaseUrl` usando o `DependencyService`:

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

Em seguida, devem ser fornecidas implementações da interface para cada plataforma.

#### <a name="ios"></a>iOS

No iOS, o conteúdo da web deve estar localizado no diretório raiz do projeto ou **recursos** diretório com a ação de compilação *BundleResource*, conforme demonstrado abaixo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](webview-images/ios-vs.png "Arquivos locais no iOS")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](webview-images/ios-xs.png "Arquivos locais no iOS")

-----

O `BaseUrl` deve ser definido como o caminho do pacote principal:

```csharp
[assembly: Dependency (typeof (BaseUrl_iOS))]
namespace WorkingWithWebview.iOS{
  public class BaseUrl_iOS : IBaseUrl {
    public string Get() {
      return NSBundle.MainBundle.BundlePath;
    }
  }
}
```

#### <a name="android"></a>Android

No Android, coloque o HTML, CSS e imagens na pasta ativos com a ação de compilação *AndroidAsset* conforme demonstrado abaixo:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](webview-images/android-vs.png "Arquivos locais no Android")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![](webview-images/android-xs.png "Arquivos locais no Android")

-----

No Android, o `BaseUrl` deve ser definido como `"file:///android_asset/"`:

```csharp
[assembly: Dependency (typeof(BaseUrl_Android))]
namespace WorkingWithWebview.Android {
  public class BaseUrl_Android : IBaseUrl {
    public string Get() {
      return "file:///android_asset/";
    }
  }
}
```

No Android, arquivos de **ativos** pasta também pode ser acessada por meio do contexto atual de Android, que é exposto pelo `MainActivity.Instance` propriedade:

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html"))) {
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="windows-phone"></a>Windows Phone

No Windows Phone, coloque HTML, CSS e imagens na raiz do projeto com a ação de compilação definida como *conteúdo* conforme demonstrado abaixo:

![](webview-images/windows-vs.png "Arquivos locais no Windows Phone")

No Windows Phone, o `BaseUrl` deve ser definido como `""`:

```csharp
[assembly: Dependency (typeof(BaseUrl_Windows))]
namespace WorkingWithWebview.Windows {
  public class BaseUrl_Windows : IBaseUrl {
    public string Get() {
      return "";
    }
  }
}
```

#### <a name="windows-runtime-and-universal-windows-platform"></a>Tempo de execução do Windows e a plataforma Universal do Windows

Em projetos de tempo de execução do Windows e Windows UWP (plataforma Universal), colocar HTML, CSS e imagens na raiz do projeto com a ação de compilação definida como *conteúdo*.

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

Exibição da Web oferece suporte à navegação por meio de vários métodos e propriedades que torna disponível:

- **GoForward()** &ndash; se `CanGoForward` for true, chamar `GoForward` navega para a frente para a próxima página visitada.
- **GoBack()** &ndash; se `CanGoBack` for true, chamar `GoBack` navegará para a última página visitada.
- **CanGoBack** &ndash; `true` se há páginas para retornar ao `false` se o navegador é a URL inicial.
- **CanGoForward** &ndash; `true` se o usuário tiver navegado para trás e pode Avançar para uma página que já foi visitada.

Em páginas, `WebView` não oferece suporte a gestos multitoque. É importante para garantir que o conteúdo é otimizado para celular e aparece sem a necessidade de zoom.

É comum para aplicativos mostrar um link dentro de um `WebView`, em vez do navegador do dispositivo. Nessas situações, é útil permitir a navegação normal, mas quando o usuário optou faz enquanto eles estão no link inicial, o aplicativo deve retornar à exibição normal do aplicativo.

Use as propriedades e métodos de navegação internos para habilitar esse cenário.

Comece criando a página para o modo de exibição do navegador:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="WebViewDemo.InAppDemo"
Title="In App Browser">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout Orientation="Horizontal" Padding="10,10">
                <Button Text="Back" HorizontalOptions="StartAndExpand" Clicked="backClicked" />
                <Button Text="Forward" HorizontalOptions="End" Clicked="forwardClicked" />
            </StackLayout>
            <WebView x:Name="Browser" WidthRequest="1000" HeightRequest="1000" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

No nosso code-behind:

```csharp
public partial class InAppDemo : ContentPage
{
  //sets the URL for the browser in the page at creation
    public InAppDemo (string URL)
    {
        InitializeComponent ();
        Browser.Source = URL;
    }


    private void backClicked(object sender, EventArgs e)
    {
    // Check to see if there is anywhere to go back to
        if (Browser.CanGoBack) {
            Browser.GoBack ();
        } else { // If not, leave the view
            Navigation.PopAsync ();
        }
    }

    private void forwardClicked(object sender, EventArgs e)
    {
        if (Browser.CanGoForward) {
            Browser.GoForward ();
        }
    }
}
```

É só isso!

![](webview-images/in-app-browser.png "Botões de navegação do WebView")

## <a name="events"></a>Eventos

WebView gera dois eventos para ajudá-lo a responder a alterações no estado:

- **Navegando** &ndash; evento gerado quando o WebView começa a carregar uma nova página.
- **Navegar** &ndash; evento gerado quando a página for carregada e navegação foi interrompido.

Se você antecipar usando as páginas da Web que levam muito tempo para carregar, considere usar esses eventos para implementar um indicador de status. Por exemplo:

Nosso XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="WebViewDemo.LoadingDemo" Title="Loading Demo">
    <ContentPage.Content>
    <StackLayout>
      <Label x:Name="LoadingLabel"
        Text="Loading..."
        HorizontalOptions="Center"
        isVisible="false" />
      <WebView x:Name="Browser"
      HeightRequest="1000"
      WidthRequest="1000"
      Navigating="webOnNavigating"
      Navigated="webOnEndNavigating" />
    </StackLayout>
    </ContentPage.Content>
</ContentPage>
```
Nossos manipuladores de eventos de dois:

```csharp
void webOnNavigating (object sender, WebNavigatingEventArgs e)
{
            LoadingLabel.IsVisible = true;
}

void webOnEndNavigating (object sender, WebNavigatedEventArgs e)
{
            LoadingLabel.IsVisible = false;
}
```

Isso resulta na seguinte saída (carregar):

![](webview-images/loading-start.png "Exemplo de evento de navegação do WebView")

Carregamento concluído:

![](webview-images/loading-end.png "Exemplo de evento navegado WebView")

## <a name="performance"></a>Desempenho

Os avanços recentes viu a cada um dos navegadores da web populares adotar tecnologias como compilação de JavaScript e a renderização acelerada por hardware. Infelizmente, devido a restrições de segurança, a maioria dos avanços não estava disponível no iOS-equaivalent, de `WebView`, `UIWebView`. Xamarin. Forms `WebView` usa `UIWebView`. Se esse for um problema, você precisará escrever um renderizador personalizado que usa `WKWebView`, que oferece suporte à navegação mais rápida. Observe que `WKWebView` só tem suporte no iOS 8 e mais recente.

WebView no Android, por padrão é tão rápida quanto o navegador interno.

O `WebBrowser` controle no Windows Phone 8 e Windows Phone 8.1 oferece recursos de suporte a HTML5 mais recente não e geralmente pode ter um baixo desempenho. Esteja ciente de como os sites serão exibidos no Windows Phone `WebView`. Não é suficiente testar no Internet Explorer.

## <a name="permissions"></a>Permissões

Para que `WebView` para trabalhar, assegure-se de que as permissões são definidas para cada plataforma. Observe que, em algumas plataformas `WebView` funcionará no modo de depuração, mas não quando compilado para versão. Isso ocorre porque algumas permissões, como aquelas para acesso à internet no Android, são definidos por padrão pelo Visual Studio para Mac quando no modo de depuração.

- **Windows Phone 8.0** &ndash; requer `ID_CAP_WEBBROWSERCOMPONENT` para o controle e `ID_CAP_NETWORKING` para acesso à internet.
- **Windows Phone 8.1 e UWP** &ndash; requer a capacidade de Internet (cliente e servidor) ao exibir o conteúdo de rede.
- **Android** &ndash; requer `INTERNET` apenas ao exibir o conteúdo da rede. Conteúdo local não requer nenhuma permissão especial.
- **iOS** &ndash; não exige nenhuma permissão especial.

## <a name="layout"></a>Layout

Ao contrário da maioria dos outros modos de xamarin. Forms, `WebView` requer que `HeightRequest` e `WidthRequest` são especificados quando contido em StackLayout ou RelativeLayout. Se você não especificar essas propriedades, o `WebView` não será processada.

Os exemplos a seguir demonstram layouts que resultam em funcionamento, renderização `WebView`s:

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

Grade *sem* WidthRequest & HeightRequest. Grade é um dos layouts de alguns que não exigem a especificação de larguras e alturas solicitadas.:

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


## <a name="related-links"></a>Links relacionados

- [Trabalhando com WebView (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithWebview/)
- [WebView (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/WebView)
