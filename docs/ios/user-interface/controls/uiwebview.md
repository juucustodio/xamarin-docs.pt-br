---
title: Modos de exibição da Web no xamarin. IOS
description: Este documento descreve as várias maneiras de que um aplicativo xamarin. IOS pode exibir o conteúdo da web. Ele aborda UIWebView, WKWebView, ter o SFSafariViewController, Safari e segurança de transporte de aplicativo.
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 56b0f0e910cc95ca50d1e5e460ce71a1c8f669a2
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242034"
---
# <a name="web-views-in-xamarinios"></a>Modos de exibição da Web no xamarin. IOS

Durante a vida útil do iOS Apple lançou várias maneiras para desenvolvedores de aplicativos incorporar a funcionalidade de exibição da web em seus aplicativos. A maioria dos usuários utilizam o navegador Safari interno em seu dispositivo iOS e, portanto, esperar que a funcionalidade de exibição da web de outros aplicativos esteja consistente com essa experiência. Eles esperam que os mesmos gestos funcione, o desempenho sejam iguais em valor nominal e a funcionalidade.

Neste artigo, vamos explorar cada uma das três exibições da Web fornecido pela Apple: `UIWebView`, `WKWebview`, e `SFSafariViewController`, suas semelhanças e diferenças e como eles podem ser usados. 

iOS 11 introduziu novas alterações para ambos `WKWebView` e `SFSafariViewController`. Para obter mais informações sobre isso, consulte a [Web alterações no iOS 11 guia](~/ios/platform/introduction-to-ios11/web.md) guia.

## <a name="uiwebview"></a>UIWebView

`UIWebView` é a maneira herdada da Apple do fornecimento de conteúdo da web em seu aplicativo. Ele foi lançado no iOS 2.0 e foi preterido a partir de 8.0.

Se você planeja dar suporte a versões do iOS anteriores a 8.0, você terá que usar `UIWebView`. Devido ao fato de que `UIWebView` é menos otimizado para desempenho que as alternativas, é recomendável que você deve verificar a versão do iOS do usuário. Se ele 8.0 ou posterior, usando qualquer uma das opções explicada a seguir criará uma melhor experiência do usuário.
 
Para adicionar um UIWebView ao seu aplicativo xamarin. IOS, use o seguinte código:
 
```
webView = new UIWebView (View.Bounds);
View.AddSubview(webView);

var url = "https://xamarin.com"; // NOTE: https secure request
webView.LoadRequest(new NSUrlRequest(new NSUrl(url)));
```

Isso produz o seguinte modo de exibição da web:

[![](uiwebview-images/webview.png "O efeito de ScalesPagesToFit")](uiwebview-images/webview.png#lightbox)

Para obter mais informações sobre como usar `UIWebView`, consulte as receitas a seguir:


- [Carregar uma página da Web](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_a_web_page)
- [Carregar conteúdo Local](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_local_content)
- [Carregar documentos não Web](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/load_non-web_documents)

## <a name="wkwebview"></a>WKWebView

`WKWebView` foi introduzido no iOS 8, permitindo que desenvolvedores de aplicativos para implementar um interface semelhante do Safari móvel de navegação na web. Isso se deve em parte, ao fato de que `WKWebView` usa o mecanismo Nitro Javascript, o mesmo mecanismo usado pelo Safari móvel. `WKWebView` sempre deve ser usado over UIWebView eram possíveis devido à [maior desempenho](http://blog.initlabs.com/post/100113463211/wkwebview-vs-uiwebview), gestos amigáveis e a facilidade de interação entre seu aplicativo e a página da web internos.
  
`WKWebView` podem ser adicionados ao seu aplicativo de maneira quase idêntica à UIWebView, no entanto, como o desenvolvedor, você tem muito mais controle sobre a interface do usuário/experiência do usuário e a funcionalidade. Criando e exibindo o objeto de exibição da web exibirá a página solicitada, no entanto, você pode controlar como o modo de exibição é apresentado, como o usuário pode navegar e como o usuário sai do modo de exibição.  

O código a seguir pode ser usado para iniciar um `WKWebView` em seu aplicativo xamarin. IOS:

```csharp
    WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
    View.AddSubview(webView);

    var url = new NSUrl("https://xamarin.com");
    var request = new NSUrlRequest(url);
    webView.LoadRequest(request);
```

Isso produz o seguinte modo de exibição da web:

[![](uiwebview-images/wkwebview.png "Uma exibição da web de exemplo sem ScalesPagesToFit")](uiwebview-images/wkwebview.png#lightbox)

É importante observar que `WKWebView` está no namespace WebKit, portanto, você terá que adicioná-lo usando a diretiva para a parte superior da sua classe.

`WKWebView` também pode ser usado em aplicativos xamarin. Mac e, portanto, convém considerar a usá-lo se você estiver criando um aplicativo do Mac/iOS de plataforma cruzada.

O [lidar com alertas de JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/web_view/handle_javascript_alerts) receita também fornece informações sobre como usar WKWebView com Javascript

<a name="safariviewcontroller" />

## <a name="sfsafariviewcontroller"></a>SFSafariViewController
 
 `SFSafariViewController` é a maneira mais recente para fornecer conteúdo da web de seu aplicativo e está disponível no iOS 9 e posterior. Diferentemente `UIWebView` ou `WKWebView`, `SFSafariViewController` é um controlador de exibição e não pode ser usado com outros modos de exibição. Você deve apresentar `SFSafariViewController` como um novo controlador de exibição, da mesma forma você poderia apresentar qualquer controlador de exibição.
 
 `SFSafariViewController` é essencialmente um 'mini safari' que pode ser inserido em seu aplicativo. Como WKWebView ele usa o mesmo mecanismo Javascript Nitro, mas também fornece uma variedade de recursos adicionais do Safari, como preenchimento automático, leitor e a capacidade de compartilhar cookies e dados com o Safari móvel. Interação entre o usuário e o `SFSafariViewController` não está acessível ao seu aplicativo. Seu aplicativo não terá acesso a qualquer um dos recursos padrão Safari.
 
Além disso, por padrão, implementa um **feito** botão, permitindo ao usuário para retornar facilmente ao seu aplicativo e encaminhar e botões de navegação, permitindo que o usuário navegue por meio de uma pilha de páginas da web de volta. Além disso, ele também fornece o usuário com um endereço barra dando-lhes a paz de espírito que estão na página da web esperada. A barra de endereços não permite ao usuário alterar a url. 

Essas implementações não pode ser alterado, isso `SFSafariViewController` é ideal para usar como o navegador padrão, se seu aplicativo deseja apresentar uma página da Web sem nenhuma personalização.

O código a seguir pode ser usado para iniciar um `SFSafariViewController` em seu aplicativo xamarin. IOS:

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

Isso produz o seguinte modo de exibição da web:

[![](uiwebview-images/sfsafariviewcontroller.png "Uma exibição da web de exemplo com ter o SFSafariViewController")](uiwebview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

Também é possível abrir o aplicativo móvel do Safari de dentro de seu aplicativo, usando o código a seguir:

```csharp
var url = new NSUrl("https://xamarin.com");

UIApplication.SharedApplication.OpenUrl(url);

```

Isso produz o seguinte modo de exibição da web:

[![](uiwebview-images/safari.png "Uma página da web apresentado no Safari")](uiwebview-images/safari.png#lightbox)

Navegar usuários para longe de seu aplicativo para o Safari deve sempre ser normalmente evitado. A maioria dos usuários não esperam navegação fora do seu aplicativo, portanto, se você navegar para fora de seu aplicativo, os usuários podem nunca retorná-la, eliminando essencialmente engagement.

melhorias do iOS 9 é permitir que o usuário retornar facilmente a seu aplicativo por meio de um botão Voltar fornecido no canto superior esquerdo da página Safari.

## <a name="app-transport-security"></a>Segurança de transporte de aplicativo

Segurança de transporte de aplicativo, ou *ATS* foi introduzido pela Apple no iOS 9 para garantir que todas as comunicações de internet sigam para proteger as práticas recomendadas de conexão.

Para obter mais informações sobre o ATS, incluindo como implementá-lo em seu aplicativo, consulte o [segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md) guia.

## <a name="related-links"></a>Links relacionados

- [WebViews (amostra)](https://developer.xamarin.com/samples/monotouch/WebView/)
