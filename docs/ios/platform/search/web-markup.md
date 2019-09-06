---
title: Pesquisar com marcação da Web no Xamarin. iOS
description: Este documento descreve como criar resultados de pesquisa baseados na Web que se vinculam de volta a um aplicativo Xamarin. iOS. Ele aborda como habilitar a indexação de conteúdo da Web, tornando o site do seu aplicativo detectável, usando faixas de aplicativos inteligentes, links universais e muito mais.
ms.prod: xamarin
ms.assetid: 876315BA-2EF9-4275-AE33-A3A494BBF7FD
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/20/2017
ms.openlocfilehash: 87037c8c3797c7c305ce2689172bda1babbc26bd
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291713"
---
# <a name="search-with-web-markup-in-xamarinios"></a>Pesquisar com marcação da Web no Xamarin. iOS

Para aplicativos que fornecem acesso ao seu conteúdo por meio de um site (não apenas de dentro do aplicativo), o conteúdo da Web pode ser marcado com links especiais que serão rastreados pela Apple e fornecerá uma vinculação profunda ao seu aplicativo no dispositivo iOS 9 do usuário.

Se seu aplicativo iOS já dá suporte à vinculação profunda móvel e seu site apresentou links profundos para o conteúdo em seu aplicativo, o rastreador da Web _Applebot_ da Apple indexará esse conteúdo e o adicionará automaticamente ao índice de nuvem deles:

[![](web-markup-images/webmarkup01.png "Visão geral do índice de nuvem")](web-markup-images/webmarkup01.png#lightbox)

A Apple fará a superfície desses resultados em pesquisa de destaque e nos resultados da pesquisa do Safari.
Se o usuário toca em um desses resultados (e eles têm seu aplicativo instalado), eles serão levados ao conteúdo em seu aplicativo:

[![](web-markup-images/webmarkup02.png "Vinculação profunda de um site nos resultados da pesquisa")](web-markup-images/webmarkup02.png#lightbox)

## <a name="enabling-web-content-indexing"></a>Habilitando a indexação de conteúdo da Web

Há quatro etapas necessárias para tornar o conteúdo do aplicativo pesquisável usando marcação da Web:

1. Verifique se a Apple pode descobrir e indexar o site do seu aplicativo definindo-o como o site de **suporte** ou **marketing** no iTunes Connect.
2. Verifique se o site do seu aplicativo contém a marcação necessária para implementar a vinculação profunda móvel. Consulte as seções abaixo para obter mais detalhes.
3. Habilite o tratamento de link profundo em seu aplicativo iOS.
4. Adicione marcação para os dados estruturados na superfície do site do seu aplicativo para fornecer um resultado avançado e envolvente ao usuário final. Embora essa etapa não seja estritamente necessária, ela é altamente recomendável pela Apple.

As seções a seguir abordarão essas etapas em detalhes.

## <a name="make-your-apps-website-discoverable"></a>Torne o site do seu aplicativo detectável

A maneira mais fácil de fazer o Apple encontrar o site do seu aplicativo é usá-lo como o site de **suporte** ou **marketing** quando você envia seu aplicativo para a Apple por meio do iTunes Connect.

## <a name="using-smart-app-banners"></a>Usando faixas de aplicativos inteligentes

Forneça uma faixa de aplicativo inteligente em seu site para apresentar um link limpo em seu aplicativo. Se o aplicativo ainda não estiver instalado, o Safari solicitará automaticamente que o usuário instale seu aplicativo. Caso contrário, o uso pode tocar no link de **exibição** para iniciar seu aplicativo no site. Por exemplo, para criar uma faixa de aplicativo inteligente, você pode usar o seguinte código:

```html
<meta name="AppName" content="app-id=123456, app-argument=http://company.com/AppName">
```

Para obter mais informações, consulte a documentação [promovendo aplicativos da Apple com faixas de aplicativos inteligentes](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html) .

## <a name="using-universal-links"></a>Usando links universais

Novo no iOS 9, os links universais fornecem uma alternativa melhor para faixas de aplicativos inteligentes ou esquemas de URL personalizados existentes fornecendo o seguinte:

- **Exclusivo** – a mesma URL não pode ser reivindicada por mais de um site.
- **Seguro** – um certificado assinado é necessário para o site que garante que o site seja de sua propriedade e vinculado de forma válida ao seu aplicativo.
- **Flexível** – o usuário final pode controlar se a URL inicia o site ou o aplicativo.
- **Universal** – a mesma URL pode ser usada para definir o conteúdo do seu site e do seu aplicativo.

## <a name="using-twitter-cards"></a>Usando os cartões do Twitter

Você pode fornecer links profundos para o conteúdo do seu aplicativo usando um cartão do Twitter. Por exemplo:

```html
<meta name="twitter:app:name:iphone" content="AppName">
<meta name="twitter:app:id:iphone" content="AppNameID">
<meta name="twitter:app:url:iphone" content="AppNameURL">
```

Para obter mais informações, consulte a documentação do [protocolo de cartão do Twitter](http://dev.twitter.com/cards/mobile) do Twitter.

## <a name="using-facebook-app-links"></a>Usando links de aplicativo do Facebook

Você pode fornecer links profundos para o conteúdo do aplicativo usando um link de aplicativo do Facebook. Por exemplo:

```html
<meta property="al:ios:app_name" content="AppName">
<meta property="al:ios:app_store_id" content="AppNameID">
<meta property="al:ios:url" content="AppNameURL">
```

Para obter mais informações, consulte a documentação de [links de aplicativo](http://applinks.org) do Facebook.

## <a name="opening-deep-links"></a>Abrindo links profundos

Você precisa adicionar suporte para abrir e exibir links profundos em seu aplicativo Xamarin. iOS. Edite o arquivo **AppDelegate.cs** e substitua `OpenURL` o método para manipular o formato de URL personalizado. Por exemplo:

```csharp
public override bool OpenUrl (UIApplication application, NSUrl url, string sourceApplication, NSObject annotation)
{

  // Handling a URL in the form http://company.com/appname/?123
  try {
    var components = new NSUrlComponents(url,true);
    var path = components.Path;
    var query = components.Query;

    // Is this a known format?
    if (path == "/appname") {
      // Display the view controller for the content
      // specified in query (123)
      return ContentViewController.LoadContent(query);
    }
  } catch {
    // Ignore issue for now
  }

  return false;
}
```

No código acima, estamos procurando uma URL contendo `/appname` e passando o valor de `query` (`123` neste exemplo) para um controlador de exibição personalizado em nosso aplicativo para exibir o conteúdo solicitado ao usuário.

## <a name="providing-rich-results-with-structured-data"></a>Fornecendo resultados avançados com dados estruturados

Ao incluir marcação de dados estruturados, você pode fornecer resultados de pesquisa avançados para o usuário final que vão além de simplesmente um título e uma descrição. Incluir imagens, dados específicos do aplicativo (como classificações) e ações para resultados usando marcação de dados estruturados.

Os resultados avançados são mais atraentes e podem ajudar a melhorar sua classificação no índice de pesquisa baseado em nuvem, convencendo mais usuários a interagir com eles.

Uma opção para fornecer marcação de dados estruturado é usando o Open Graph. Por exemplo:

```html
<meta property="og:image" content="http://company.com/appname/icon.jpg">
<meta property="og:audio" content="http://company.com/appname/theme.m4a">
<meta property="og:video" content="http://company.com/appname/tutorial.mp4">
```

Para obter mais informações, consulte o site de [gráficos aberto](http://ogp.me) .

Outro formato comum para marcação de dados estruturados é o formato de MicroData do esquema. org. Por exemplo:

```html
<div itemprop="aggregateRating" itemscope itemtype="http://schema.org/AggregateRating">
  <span itemprop="ratingValue">4** stars -
  <span itemprop="reviewCount">255** reviews
```

As mesmas informações podem ser representadas no formato JSON-LD do esquema. org:

```html
<script type="application/ld+json">
  "@content":"http://schema.org",
  "@type":"AggregateRating",
  "ratingValue":"4",
  "reviewCount":"255"
</script>
```

Veja a seguir um exemplo de metadados do seu site fornecendo resultados de pesquisa avançados para o usuário final:

[![](web-markup-images/deeplink01.png "Resultados de pesquisa avançados por meio de marcação de dados estruturados")](web-markup-images/deeplink01.png#lightbox)

Atualmente, a Apple dá suporte aos seguintes tipos de esquema do schema.org:

- AggregateRating
- ImageObject
- InteractionCount
- Ofereça
- Organizações
- PriceRange
- Receita
- SearchAction

Para obter mais informações sobre esses tipos de esquema, consulte [Schema.org](http://schema.org).

## <a name="providing-actions-with-structured-data"></a>Fornecendo ações com dados estruturados

Tipos específicos de dados estruturados permitirão que um resultado de pesquisa seja acionável pelo usuário final. Atualmente, há suporte para as seguintes ações:

- Discando um número de telefone.
- Obtendo a direção do mapa para um determinado endereço.
- Reproduzir um arquivo de áudio ou de vídeo.

Por exemplo, definir uma ação para discar um número de telefone pode ser semelhante ao seguinte:

```html
<div itemscope itemtype="http://schema.org/Organization">
  <span itemprop="telephone">(408) 555-1212**
```

Quando esse resultado de pesquisa for apresentado ao usuário final, um pequeno ícone de telefone será exibido no resultado. Se o usuário tocar no ícone, o número especificado será chamado.

O HTML a seguir adicionaria uma ação para reproduzir um arquivo de áudio do resultado da pesquisa:

```html
<div itemscope itemtype="http://schema.org/AudioObject">
  <span itemprop="contentUrl">http://company.com/appname/greeting.m4a**
```

Por fim, o HTML a seguir adicionaria uma ação para obter instruções do resultado da pesquisa:

```html
<div itemscope itemtype="http://schema.org/PostalAddress">
  <span itemprop="streetAddress">1 Infinite Loop**
  <span itemprop="addressLocality">Cupertino**
  <span itemprop="addressRegion">CA**
  <span itemprop="postalCode">95014**
```

Para obter mais informações, consulte o [site do desenvolvedor de pesquisa de aplicativos](https://developer.apple.com/ios/search/)da Apple.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guia de programação de pesquisa de aplicativo](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
