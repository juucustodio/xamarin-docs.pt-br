---
title: Alterações de Web no iOS 11
description: Este documento aborda as alterações feitas WebKit e a estrutura de serviços do Safari no iOS 11. Descreve como trabalhar com atualizações em SFSafariViewController e novos recursos no WKWebView de estilo.
ms.prod: xamarin
ms.assetid: C74B2E94-177C-43D4-8D6C-9B528773C120
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/12/2016
ms.openlocfilehash: f5876a9d201950ebac45e8b1f786b0e97452a7f1
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787442"
---
# <a name="web-changes-in-ios-11"></a>Alterações de Web no iOS 11

iOS 11 apresenta uma nova versão do navegador da web Safari – Safari 11.0 – que inclui alterações WebKit e SafariServices. Este guia explora essas alterações.

## <a name="safariservices"></a>SafariServices

`SFSafariViewController` foi introduzida no iOS 9 como uma opção para exibir o conteúdo da web ou autenticação de usuários do seu aplicativo. Mais informações sobre seus recursos podem ser encontradas no [modos de exibição Web](~/ios/user-interface/controls/uiwebview.md#safariviewcontroller) guia.

iOS 11 introduziu as atualizações de estilo para o controlador de exibição do Safari, dando a seus usuários uma experiência mais consistente entre um aplicativo e o web. Por exemplo, a remoção da barra agora fornece o Safari View Controller a aparência de um navegador no aplicativo, em vez de um minide navegador endereços. Você também pode personalizar o esquema de cores de acordo com o esquema de cores do seu aplicativo, definindo o `preferredBarTintColor` e `PreferredControlTintColor` propriedades:

```csharp
sfViewController.PreferredControlTintColor = UIColor.White;
sfViewController.PreferredBarTintColor = UIColor.Purple;
```

O trecho de código a seguir renderiza as barras em roxo e branco, como exibido na imagem a seguir:

![Barras de SFSafariViewController processadas em roxo e branco](web-images/image1.png)

Botão ignorar apresentado no Safari exibição controlador também pode ser alterado definindo a `DismissButtonStyle` propriedade como `Done`, `Close`, ou `Cancel`:

```csharp
sfViewController.DismissButtonStyle = SFSafariViewControllerDismissButtonStyle.Close;
```

![Ignorar o texto do botão alterado](web-images/image2.png)

Esse valor pode ser alterado enquanto `SFSafariViewController` é apresentado.


Dependendo do conteúdo que é exibido dentro de um controlador de exibição do Safari, pode ser necessário garantir que as barras de menu não recolher como o usuário rolar. Isso é habilitado pela configuração nova `BarCollapsedEnabled` propriedade `false`:

```csharp
var config = new SFSafariViewControllerConfiguration();
config.BarCollapsingEnabled = false;

var sfViewController = new SFSafariViewController(url, config);
```

![Barra recolhendo desabilitado](web-images/image3.png)

Apple também fez atualizações à privacidade no controlador de exibição Safari no iOS 11. Agora, procurando dados, como cookies e armazenamento local só existem em uma base por aplicativo, em vez de em todas as instâncias do controlador de exibição do Safari. Isso mantém a atividade de navegação de usuário particular dentro de seu aplicativo.

Recursos adicionais, como arraste e solte o suporte para URLs e suporte para `window.open()` também foram adicionados ao `SFSafariViewController` no iOS 11. Você pode encontrar mais informações sobre esses novos recursos no [documentação de SFSafariViewController da Apple](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?changes=latest_minor).


## <a name="webkit"></a>WebKit

`WKWebView` foi introduzido como parte da WebKit no iOS 8 como uma forma de exibir o conteúdo da web para o usuário. É muito mais personalizável do que `SFSafariViewController`, permitindo que você crie sua própria interface de usuário e de navegação.

Apple introduziu três melhorias principais para `WKWebView` com iOS 11: 

- A capacidade de gerenciar cookies
- Filtragem de conteúdo
- Carregamento de recursos personalizados. 

Gerenciamento de cookies é feito por meio do novo [ `WKHttpCookieStore` ](https://developer.apple.com/documentation/webkit/wkhttpcookiestore) classe, que permite adicionar e excluir cookies, para obter todos os cookies armazenados em um WKWebView e observar o cookie armazenar alterações.

Conteúdo de filtragem permite que você gerencie o tipo de conteúdo que será exibido para o usuário, permitindo que você verifique se ele é seguro, família amigável e, se necessário, disponível somente para um grupo seleto de usuários. Isso é implementado por meio do novo [ `WKContentRuleList` ](https://developer.apple.com/documentation/webkit/wkcontentrulelist) classe, fornecendo pares de gatilhos e ações em JSON. Para obter mais informações sobre esses gatilhos e ações podem ser encontradas da Apple [regras de bloqueio de conteúdo](https://developer.apple.com/library/content/documentation/Extensions/Conceptual/ContentBlockingRules/Introduction/Introduction.html) guia.

iOS 11 agora permite que você personalize `WKWebView` com o recurso personalizado de carregamento de conteúdo da web. Isso é implementado por meio de `IWKUrlSchemeHandler` interface, que permite que você manipule os esquemas de URL que não são nativos do Kit da Web. Essa interface possui um método de início e parada que deve ser implementado:

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

Depois que o manipulador foi implementado, usada para definir o `SetUrlSchemeHandler` propriedade o `WKWebViewConfiguration`. Em seguida, carregue a URL de algo que usa o esquema personalizado:

```csharp
var config = new WKWebViewConfiguration();
config.SetUrlSchemeHandler(new MyHandler(), "xamarin-asset");

webView = new WKWebView (View.Frame, config);
webView.LoadRequest (new NSUrlRequest("xamarin-asset://xamarin.com"));
```

