---
title: Alterações da Web no iOS 11
description: Este documento aborda as alterações feitas em WebKit e a estrutura de serviços do Safari no iOS 11. Ele descreve como trabalhar com atualizações em ter o SFSafariViewController e novos recursos no WKWebView de definição de estilo.
ms.prod: xamarin
ms.assetid: C74B2E94-177C-43D4-8D6C-9B528773C120
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/12/2017
ms.openlocfilehash: ba691a6605dcf7e86a76ed13d4c8ef5f0984ff6e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61399579"
---
# <a name="web-changes-in-ios-11"></a>Alterações da Web no iOS 11

iOS 11 introduz uma nova versão do navegador da web Safari – Safari 11.0 – que inclui alterações WebKit e SafariServices. Este guia explora essas alterações.

## <a name="safariservices"></a>SafariServices

`SFSafariViewController` foi introduzido no iOS 9 como uma opção para exibir o conteúdo da web ou autenticação de usuários do seu aplicativo. Obter mais informações sobre seus recursos podem ser encontradas na [modos de exibição Web](~/ios/user-interface/controls/uiwebview.md#safariviewcontroller) guia.

iOS 11 introduziu as atualizações de estilo para o controlador de exibição do Safari, dando a seus usuários uma experiência mais dinâmica entre um aplicativo e a web. Por exemplo, a remoção da barra agora oferece o controlador de exibição do Safari a sensação de um navegador no aplicativo, em vez de um mininavegador de endereços. Você também pode personalizar o esquema de cores para estar de acordo com o esquema de cores do seu aplicativo definindo o `preferredBarTintColor` e `PreferredControlTintColor` propriedades:

```csharp
sfViewController.PreferredControlTintColor = UIColor.White;
sfViewController.PreferredBarTintColor = UIColor.Purple;
```

O trecho de código a seguir renderiza as barras em roxo e em branco, conforme exibido na imagem a seguir:

![Barras de ter o SFSafariViewController processadas em roxo e em branco](web-images/image1.png)

O botão Descartar apresentado no controlador de exibição Safari também pode ser alterado definindo a `DismissButtonStyle` propriedade para um `Done`, `Close`, ou `Cancel`:

```csharp
sfViewController.DismissButtonStyle = SFSafariViewControllerDismissButtonStyle.Close;
```

![Ignorar a alteração de texto do botão](web-images/image2.png)

Esse valor pode ser alterado enquanto `SFSafariViewController` é apresentado.


Dependendo do conteúdo que é exibido dentro de um controlador de exibição do Safari, pode ser necessário garantir que as barras de menu não recolher como o usuário rolar. Isso é habilitado pela definição da nova `BarCollapsedEnabled` propriedade para `false`:

```csharp
var config = new SFSafariViewControllerConfiguration();
config.BarCollapsingEnabled = false;

var sfViewController = new SFSafariViewController(url, config);
```

![Barra de recolhimento desabilitado](web-images/image3.png)

Apple também fez atualizações à privacidade no controlador de exibição Safari no iOS 11. Agora, dados de navegação, como o armazenamento local e os cookies só existem em uma base por aplicativo, em vez de em todas as instâncias do controlador de exibição do Safari. Isso mantém a atividade de navegação do usuário privada dentro de seu aplicativo.

Recursos adicionais, como arraste e solte o suporte para URLs e suporte para `window.open()` também foram adicionados ao `SFSafariViewController` no iOS 11. Você pode encontrar mais informações sobre esses novos recursos no [documentação de ter o SFSafariViewController da Apple](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?changes=latest_minor).


## <a name="webkit"></a>WebKit

`WKWebView` foi introduzido como parte do WebKit no iOS 8 como um meio de exibir o conteúdo da web ao seu usuário. Ele é muito mais personalizável do `SFSafariViewController`, permitindo que você crie sua própria interface do usuário e navegação.

Apple introduziu três principais melhorias para `WKWebView` com o iOS 11: 

- A capacidade de gerenciar cookies
- Filtragem de conteúdo
- Carregamento de recursos personalizados. 

Gerenciamento de cookie é feito por meio da nova [ `WKHttpCookieStore` ](https://developer.apple.com/documentation/webkit/wkhttpcookiestore) classe, que lhe permite adicionar e excluir cookies, para obter todos os cookies armazenados em um WKWebView e observar o cookie armazenar para que as alterações.

Conteúdo de filtragem permite que você gerencie o tipo de conteúdo que o usuário verá, permitindo que você verifique se ele é seguro, família amigável e, se necessário, disponível apenas para um grupo seleto de usuários. Isso é implementado por meio da nova [ `WKContentRuleList` ](https://developer.apple.com/documentation/webkit/wkcontentrulelist) classe, fornecendo pares de gatilhos e ações em JSON. Para obter mais informações sobre esses gatilhos e ações podem ser encontradas da Apple [regras de bloqueio de conteúdo](https://developer.apple.com/library/content/documentation/Extensions/Conceptual/ContentBlockingRules/Introduction/Introduction.html) guia.

iOS 11 agora permite que você personalize `WKWebView` com carregamento para o conteúdo da web de recursos personalizados. Isso é implementado por meio de `IWKUrlSchemeHandler` interface, que permite que você manipule esquemas de URL que não são nativas Kit da Web. Essa interface tem um método de início e parada que deve ser implementado:

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

Depois que o manipulador tiver sido implementado, usá-lo para definir a `SetUrlSchemeHandler` propriedade no `WKWebViewConfiguration`. Em seguida, carregue a URL de algo que usa o esquema personalizado:

```csharp
var config = new WKWebViewConfiguration();
config.SetUrlSchemeHandler(new MyHandler(), "xamarin-asset");

webView = new WKWebView (View.Frame, config);
webView.LoadRequest (new NSUrlRequest("xamarin-asset://xamarin.com"));
```

