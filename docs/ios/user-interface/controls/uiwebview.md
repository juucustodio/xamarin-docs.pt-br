---
title: Exibições da Web
description: Desambiguação opções de exibição da web do iOS
ms.prod: xamarin
ms.assetid: 84886CF4-2B2B-4540-AD92-7F0B791952D1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: 787b5594476b3a1b5b3f6a0e8151a98c97443d00
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="web-views"></a>Exibições da Web

Durante o tempo de vida do iOS Apple lançou várias maneiras para os desenvolvedores de aplicativos incorporar a funcionalidade de modo de exibição da web em seus aplicativos. A maioria dos usuários utilizam o navegador da web Safari interno em seu dispositivo iOS e, portanto, esperar que a funcionalidade de modo de exibição web de outros aplicativos é consistente com essa experiência. Eles esperam que os mesmos movimentos de trabalho, o desempenho para ser o mesmo em par e a funcionalidade.

Neste artigo, iremos explorar cada uma das três exibições da Web fornecidas pela Apple: `UIWebView`, `WKWebview`, e `SFSafariViewController`, suas semelhanças e diferenças e como eles podem ser usados. 

iOS 11 introduziu novas alterações para ambos `WKWebView` e `SFSafariViewController`. Para obter mais informações sobre estas configurações, consulte o [Web alterações na guia iOS 11](~/ios/platform/introduction-to-ios11/web.md) guia.

## <a name="uiwebview"></a>UIWebView

`UIWebView` é a maneira herdada da Apple de fornecimento de conteúdo da web em seu aplicativo. Ele foi lançado em iOS 2.0 e foi substituído a partir de 8.0.

Se você planeja oferecer suporte a versões do iOS anteriores a 8.0, você precisará usar `UIWebView`. Devido ao fato de que `UIWebView` é menor otimizado para desempenho que as alternativas, é recomendável que você deve verificar a versão do iOS do usuário. Se ele 8.0 ou posterior, usando uma das opções explicado abaixo irá criar uma melhor experiência do usuário.
 
Para adicionar um UIWebView para seu aplicativo xamarin, use o seguinte código:
 
```
webView = new UIWebView (View.Bounds);
View.AddSubview(webView);

var url = "https://xamarin.com"; // NOTE: https secure request
webView.LoadRequest(new NSUrlRequest(new NSUrl(url)));
```

Isso gera o seguinte modo de exibição da web:

[![](uiwebview-images/webview.png "O efeito de ScalesPagesToFit")](uiwebview-images/webview.png#lightbox)

Para obter mais informações sobre como usar `UIWebView`, consulte as receitas a seguir:


- [Carregar uma página da Web](https://developer.xamarin.com/recipes/ios/content_controls/web_view/load_a_web_page/)
- [Carregar conteúdo Local](https://developer.xamarin.com/recipes/ios/content_controls/web_view/load_local_content/)
- [Carregar documentos não Web](https://developer.xamarin.com/recipes/ios/content_controls/web_view/load_non-web_documents/)

## <a name="wkwebview"></a>WKWebView

`WKWebView` foi introduzido no iOS 8 permitindo que os desenvolvedores de aplicativos implementa uma interface semelhante do Safari móvel de navegação na web. Isso ocorre, em parte, o fato de que `WKWebView` usa o mecanismo Nitro Javascript, o mesmo mecanismo usado pelo Safari móvel. `WKWebView` sempre deve ser usado por UIWebView foram possíveis devido ao [maior desempenho](http://blog.initlabs.com/post/100113463211/wkwebview-vs-uiwebview), gestos amigáveis e a facilidade de interação entre o aplicativo e a página da web internos.
  
`WKWebView` podem ser adicionados ao seu aplicativo de maneira quase idêntica para UIWebView, no entanto, como desenvolvedor, você tem muito mais controle sobre a funcionalidade e a interface do usuário/UX. Criando e exibindo o objeto de exibição web exibirá a página solicitada, no entanto, você pode controlar como o modo de exibição é apresentado, como o usuário pode navegar e como o usuário sai do modo de exibição.  

O código a seguir pode ser usado para iniciar um `WKWebView` em seu aplicativo xamarin:

```csharp
    WKWebView webView = new WKWebView(View.Frame, new WKWebViewConfiguration());
    View.AddSubview(webView);

    var url = new NSUrl("https://xamarin.com");
    var request = new NSUrlRequest(url);
    webView.LoadRequest(request);
```

Isso gera o seguinte modo de exibição da web:

[![](uiwebview-images/wkwebview.png "Uma exibição da web de exemplo sem ScalesPagesToFit")](uiwebview-images/wkwebview.png#lightbox)

É importante observar que `WKWebView` está no namespace WebKit, portanto você precisará adicionar isso usando a diretiva para a parte superior da sua classe.

`WKWebView` também pode ser usado em aplicativos de Xamarin.Mac e, portanto, convém considerar a usá-lo se você estiver criando um aplicativo do iOS/Mac de plataforma cruzada.

O [lidar com alertas de JavaScript](https://developer.xamarin.com/recipes/ios/content_controls/web_view/handle_javascript_alerts/) receita também fornece informações sobre como usar WKWebView com Javascript

<a name="safariviewcontroller" />

## <a name="sfsafariviewcontroller"></a>SFSafariViewController
 
 `SFSafariViewController` é a maneira mais recente de fornecer conteúdo da web de seu aplicativo e está disponível no iOS 9 e versões posteriores. Ao contrário de `UIWebView` ou `WKWebView`, `SFSafariViewController` é um controlador de exibição e não pode ser usado com outros modos de exibição. Você deve apresentar `SFSafariViewController` como um novo controlador de exibição, apresentar da mesma maneira qualquer controlador de exibição.
 
 `SFSafariViewController` é essencialmente um 'mini safari' que pode ser inserido em seu aplicativo. Como WKWebView ele usa o mesmo mecanismo Javascript Nitro, mas também fornece uma variedade de recursos adicionais do Safari, como preenchimento automático, leitor e a capacidade de compartilhar cookies e dados com o Safari móvel. Interação entre o usuário e o `SFSafariViewController` não está acessível ao seu aplicativo. Seu aplicativo não terá acesso a todos os recursos do Safari padrão.
 
Além disso, por padrão, implementa um **feito** botão, que permite ao usuário para retornar facilmente a seu aplicativo e encaminhar e botões de navegação, permitindo que o usuário navegue por meio de uma pilha de páginas da web de volta. Além disso, ele também fornece o usuário com um endereço barra lhes paz de espírito que estão na página da web esperada. A barra de endereço não permitir que o usuário altere a url. 

Essas implementações não pode ser alterada, isso `SFSafariViewController` é ideal para usar como o navegador padrão se quiser que seu aplicativo apresentar uma página da Web sem nenhuma personalização.

O código a seguir pode ser usado para iniciar um `SFSafariViewController` em seu aplicativo xamarin:

```csharp
var sfViewController = new SFSafariViewController(url);

PresentViewController(sfViewController, true, null);
```

Isso gera o seguinte modo de exibição da web:

[![](uiwebview-images/sfsafariviewcontroller.png "Uma exibição da web de exemplo com SFSafariViewController")](uiwebview-images/sfsafariviewcontroller.png#lightbox)

## <a name="safari"></a>Safari

Também é possível abrir o aplicativo móvel do Safari de dentro de seu aplicativo, usando o código a seguir:

```csharp
var url = new NSUrl("https://xamarin.com");

UIApplication.SharedApplication.OpenUrl(url);

```

Isso gera o seguinte modo de exibição da web:

[![](uiwebview-images/safari.png "Uma página da web apresentadas no Safari")](uiwebview-images/safari.png#lightbox)

Navegar os usuários de seu aplicativo para o Safari deve sempre ser normalmente evitado. A maioria dos usuários não esperam navegação fora de seu aplicativo, portanto, se você navegar para fora de seu aplicativo, os usuários podem nunca retorná-la, eliminando essencialmente contrato.

melhorias do iOS 9 permitir que o usuário retornar facilmente a seu aplicativo por meio de um botão Voltar é fornecida no canto superior esquerdo da página Safari.

## <a name="app-transport-security"></a>Segurança de transporte do aplicativo

Segurança de transporte do aplicativo, ou *ATS* foi introduzido pela Apple no iOS 9 para garantir que todas as comunicações de internet sigam para proteger as práticas recomendadas de conexão.

Para obter mais informações sobre ATS, incluindo como implementá-la em seu aplicativo, consulte o [segurança de transporte do aplicativo](~/ios/app-fundamentals/ats.md) guia.

## <a name="related-links"></a>Links relacionados

- [WebViews (exemplo)](https://developer.xamarin.com/samples/monotouch/WebView/)
