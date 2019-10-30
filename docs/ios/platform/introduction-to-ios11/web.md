---
title: O WebKit e o Safari são alterados no iOS 11
description: Este documento discute as alterações feitas no WebKit e na estrutura de serviços do Safari no iOS 11. Ele descreve como trabalhar com atualizações de estilo em SFSafariViewController e novos recursos no WKWebView.
ms.prod: xamarin
ms.assetid: C74B2E94-177C-43D4-8D6C-9B528773C120
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/12/2017
ms.openlocfilehash: ef9577aad756ae67ac9fed685d7e40faea33c316
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032061"
---
# <a name="webkit-and-safari-changes-in-ios-11"></a>O WebKit e o Safari são alterados no iOS 11

o iOS 11 apresenta uma nova versão do navegador da Web do safari – Safari 11,0 – que inclui alterações em WebKit e Safariservices. Este guia explora essas alterações.

## <a name="safariservices"></a>Safariservices

`SFSafariViewController` foi introduzido no iOS 9 como uma opção para exibir o conteúdo da Web ou autenticar usuários do seu aplicativo. Mais informações sobre seus recursos podem ser encontradas no guia de [modos de exibição da Web](~/ios/user-interface/controls/uiwebview.md#safariviewcontroller) .

o iOS 11 introduziu atualizações de estilo para o controlador de exibição do Safari, dando a seus usuários uma experiência mais direta entre um aplicativo e a Web. Por exemplo, a remoção da barra de endereços agora dá ao controlador de exibição do Safari a sensação de um navegador no aplicativo, em vez de um mini-navegador. Você também pode personalizar o esquema de cores para se ajustar ao esquema de cores do seu aplicativo definindo as propriedades `preferredBarTintColor` e `PreferredControlTintColor`:

```csharp
sfViewController.PreferredControlTintColor = UIColor.White;
sfViewController.PreferredBarTintColor = UIColor.Purple;
```

O trecho de código a seguir renderiza as barras em roxo e branco, conforme exibido na imagem a seguir:

![Barras SFSafariViewController renderizadas em roxo e branco](web-images/image1.png)

O botão ignorar apresentado no controlador de exibição do Safari também pode ser alterado definindo a propriedade `DismissButtonStyle` como `Done`, `Close`ou `Cancel`:

```csharp
sfViewController.DismissButtonStyle = SFSafariViewControllerDismissButtonStyle.Close;
```

![Ignorar texto do botão alterado](web-images/image2.png)

Esse valor pode ser alterado enquanto `SFSafariViewController` é apresentado.

Dependendo do conteúdo exibido dentro de um controlador de exibição do Safari, pode ser necessário garantir que as barras de menu não sejam recolhidas à medida que o usuário rola. Isso é habilitado definindo a nova propriedade `BarCollapsedEnabled` como `false`:

```csharp
var config = new SFSafariViewControllerConfiguration();
config.BarCollapsingEnabled = false;

var sfViewController = new SFSafariViewController(url, config);
```

![Recolhido de barra desabilitado](web-images/image3.png)

A Apple também fez atualizações de privacidade no controlador de exibição do Safari no iOS 11. Agora, a navegação de dados como cookies e armazenamento local existe somente em uma base por aplicativo, em vez de em todas as instâncias do controlador de exibição do Safari. Isso mantém a atividade de navegação do usuário privada em seu aplicativo.

Recursos adicionais como arrastar e soltar suporte para URLs e suporte para `window.open()` também foram adicionados a `SFSafariViewController` no iOS 11. Você pode encontrar mais informações sobre esses novos recursos na [documentação do SFSafariViewController da Apple](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?changes=latest_minor).

## <a name="webkit"></a>WebKit

`WKWebView` foi introduzido como parte do WebKit no iOS 8 como um meio de exibir o conteúdo da Web para o usuário. É muito mais personalizável do que `SFSafariViewController`, permitindo que você crie sua própria navegação e interface do usuário.

A Apple introduziu três aprimoramentos principais para `WKWebView` com o iOS 11: 

- A capacidade de gerenciar cookies
- Filtragem de conteúdo
- Carregamento de recursos personalizados. 

O gerenciamento de cookies é feito por meio da nova classe [`WKHttpCookieStore`](https://developer.apple.com/documentation/webkit/wkhttpcookiestore) , que permite adicionar e Excluir cookies, obter todos os cookies armazenados em um WKWebView e observar o repositório de cookies para alterações.

A filtragem de conteúdo permite que você gerencie o tipo de conteúdo que o usuário verá, permitindo garantir que ele seja seguro, amigável para a família e, se necessário, disponível somente para um grupo de usuários selecionado. Isso é implementado por meio da nova classe [`WKContentRuleList`](https://developer.apple.com/documentation/webkit/wkcontentrulelist) , fornecendo pares de gatilhos e ações em JSON. Mais informações sobre esses gatilhos e ações podem ser encontradas no guia de [regras de bloqueio de conteúdo](https://developer.apple.com/library/content/documentation/Extensions/Conceptual/ContentBlockingRules/Introduction/Introduction.html) da Apple.

Agora, o iOS 11 permite que você personalize `WKWebView` com o carregamento de recursos personalizados para seu conteúdo da Web. Isso é implementado por meio da interface `IWKUrlSchemeHandler`, que permite lidar com esquemas de URL que não são nativos do kit da Web. Essa interface tem um método Start e Stop que deve ser implementado:

```csharp
public class MyHandler : NSObject, IWKUrlSchemeHandler {

    [Export("webView:startURLSchemeTask:")]
    public void StartUrlSchemeTask(WKWebView webView, IWKUrlSchemeTask urlSchemeTask){
        
        // Implement a IWKUrlSchemeTask here
        var response = new NSUrlResponse(urlSchemeTask.Request.Url, "text/html", ContentLength, null);
        urlSchemeTask.DidReceiveResponse(response);
        urlSchemeTask.DidReceiveData(someData);
        urlSchemeTask.DidFinish();
    }

    [Export("webView:stopURLSchemeTask:")]
    public void StopUrlSchemeTask(WKWebView webView, IWKUrlSchemeTask urlSchemeTask){
        throw new NotImplementedException();
    }

}
``` 

Depois que o manipulador tiver sido implementado, use-o para definir a propriedade `SetUrlSchemeHandler` no `WKWebViewConfiguration`. Em seguida, carregue a URL de algo que usa o esquema personalizado:

```csharp
var config = new WKWebViewConfiguration();
config.SetUrlSchemeHandler(new MyHandler(), "xamarin-asset");

webView = new WKWebView (View.Frame, config);
webView.LoadRequest (new NSUrlRequest("xamarin-asset://xamarin.com"));
```
