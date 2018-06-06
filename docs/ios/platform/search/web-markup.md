---
title: Pesquisa com a marcação da Web em xamarin
description: Este documento descreve como criar resultados de pesquisa baseada na web que têm links para um aplicativo xamarin. Ele aborda como habilitar o conteúdo da web, indexação, tornando o site do seu aplicativo detectável, usando faixas de aplicativo inteligentes, links universal e muito mais.
ms.prod: xamarin
ms.assetid: 876315BA-2EF9-4275-AE33-A3A494BBF7FD
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 438a65de3eb78f849493e3478bce5522a325d0cd
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787988"
---
# <a name="search-with-web-markup-in-xamarinios"></a>Pesquisa com a marcação da Web em xamarin

Para aplicativos que fornecem acesso a seu conteúdo por meio de um site da web (não apenas de dentro do aplicativo), conteúdo da web pode ser marcado com links especiais que serão rastreadas pela Apple e fornecer a vinculação profunda para seu aplicativo no dispositivo do iOS 9 do usuário.

Se seu aplicativo iOS já oferece suporte a vinculação profunda móvel e seu site apresentados links profundos para conteúdo dentro de seu aplicativo, Apple _Applebot_ rastreador da web será esse conteúdo de índice e adicione-o automaticamente seu índice de nuvem:

[![](web-markup-images/webmarkup01.png "Visão geral do índice de nuvem")](web-markup-images/webmarkup01.png#lightbox)

Apple será superficial esses resultados nos resultados da pesquisa do Spotlight e pesquisa do Safari.
Se o usuário toca em um destes resultados (e têm seu aplicativo instalado), em seguida, eles serão levados para o conteúdo em seu aplicativo:

[![](web-markup-images/webmarkup02.png "Profundidade de vinculação de um site nos resultados da pesquisa")](web-markup-images/webmarkup02.png#lightbox)

## <a name="enabling-web-content-indexing"></a>Habilitar a indexação de conteúdo da Web

Há quatro etapas necessárias para fazer com que você conteúdo do aplicativo podem ser pesquisados usando a marcação da Web:

1. Certifique-se de que a Apple pode descobrir e site do seu aplicativo de índice, definindo-o como o **suporte** ou **Marketing** site na iTunes conectar.
2. Verifique se o site do seu aplicativo contém a marcação necessária para implementar a vinculação profunda móvel. Consulte as seções abaixo para obter mais detalhes.
3. Habilite o link profundo do tratamento em seu aplicativo iOS.
4. Adicione marcação para os dados estruturados apresentados pelo site do seu aplicativo para fornecer um resultado sofisticado e atraente para o usuário final. Embora esta etapa não é estritamente necessária, é altamente recomendável pela Apple.

As seções a seguir apresentará em detalhes essas etapas.

## <a name="make-your-apps-website-discoverable"></a>Verifique o site do seu aplicativo detectável

É a maneira mais fácil ter Apple encontrar o site do seu aplicativo para usá-lo como o **suporte** ou **Marketing** site quando você envia seu aplicativo para a Apple via iTunes conectar.

## <a name="using-smart-app-banners"></a>Usando o aplicativo inteligente faixas

Forneça um Banner inteligente do aplicativo no seu site para apresentar um link criptografado em seu aplicativo. Se o aplicativo já não estiver instalado, o Safari automaticamente solicitará ao usuário para instalar o aplicativo. Caso contrário, o uso pode tocar a **exibição** link para iniciar seu aplicativo do site. Por exemplo, para criar um Smart Banner de aplicativo, você pode usar o código a seguir:

```xml
<meta name="AppName" content="app-id=123456, app-argument=http://company.com/AppName">
```

Para obter mais informações, consulte da Apple [promovendo aplicativos com faixas de aplicativo inteligente](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html) documentação.

## <a name="using-universal-links"></a>Usando Links Universal

Novo para iOS 9, Universal Links fornecem uma alternativa melhor para faixas de aplicativo inteligentes ou esquemas de URL personalizadas existentes, fornecendo o seguinte:

- **Exclusivo** – a mesma URL não pode ser declarada por mais de um site.
- **Proteger** – um certificado autoassinado é necessário para o site que garante que o site é de propriedade por você e devidamente vinculado para o seu aplicativo.
- **Flexível** – o usuário final pode controlar se a URL inicia o site ou o aplicativo.
- **Universal** – a mesma URL pode ser usada para definir o conteúdo do site e seu aplicativo.

## <a name="using-twitter-cards"></a>Usando cartões do Twitter

Você pode fornecer links profundos para conteúdo do seu aplicativo usando um cartão do Twitter. Por exemplo:

```xml
<meta name="twitter:app:name:iphone" content="AppName">
<meta name="twitter:app:id:iphone" content="AppNameID">
<meta name="twitter:app:url:iphone" content="AppNameURL">
```

Para obter mais informações, consulte do Twitter [Twitter cartão protocolo](http://dev.twitter.com/cards/mobile) documentação.

## <a name="using-facebook-app-links"></a>Usando os Links de aplicativo do Facebook

Você pode fornecer links profundos para conteúdo do seu aplicativo usando um Link de App do Facebook. Por exemplo:

```xml
<meta property="al:ios:app_name" content="AppName">
<meta property="al:ios:app_store_id" content="AppNameID">
<meta property="al:ios:url" content="AppNameURL">
```

Para obter mais informações, consulte do Facebook [aplicativo Links](http://applinks.org) documentação.

## <a name="opening-deep-links"></a>Abrir Links profundos

Você precisa adicionar suporte para abrir e exibir Links profundos em seu aplicativo xamarin. Editar o **appdelegate. CS** de arquivo e substituir o `OpenURL` método para controlar o formato de URL personalizado. Por exemplo:

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

No código acima, estamos buscando uma URL que contém `/appname` e passar o valor de `query` (`123` neste exemplo) para um controlador de modo de exibição personalizado no nosso aplicativo para exibir o conteúdo solicitado ao usuário.

## <a name="providing-rich-results-with-structured-data"></a>Fornecer resultados avançada com dados estruturados

Inclusive a marcação de dados estruturados, você pode fornecer os resultados da pesquisa avançada para o usuário final que vão além de simplesmente um título e uma descrição. Inclua imagens, os dados específicos do aplicativo (por exemplo, classificações) e ações para os resultados usando a marcação de dados estruturados.

Resultados de Avançado são mais interessantes e pode ajudar a melhorar o índice de pesquisa com base em sua classificação na nuvem por convencendo mais usuários para interagir com eles.

É uma opção para fornecer a marcação de dados estruturados por meio do Open Graph. Por exemplo:

```xml
<meta property="og:image" content="http://company.com/appname/icon.jpg">
<meta property="og:audio" content="http://company.com/appname/theme.m4a">
<meta property="og:video" content="http://company.com/appname/tutorial.mp4">
```

Para obter mais informações, consulte o [Open Graph](http://ogp.me) site.

Outro comuns para marcação de dados estruturados é Microdados formato do schema.org. Por exemplo:

```xml
<div itemprop="aggregateRating" itemscope itemtype="http://schema.org/AggregateRating">
    <span itemprop="ratingValue">4** stars -
    <span itemprop="reviewCount">255** reviews


```

As mesmas informações podem ser representadas em formato de LD JSON do schema.org:

```xml
<script type="application/ld+json">
    "@content":"http://schema.org",
    "@type":"AggregateRating",
    "ratingValue":"4",
    "reviewCount":"255"
</script>
```

O exemplo a seguir mostra um exemplo de metadados do seu site, fornecendo os resultados de pesquisa avançados para o usuário final:

[![](web-markup-images/deeplink01.png "Pesquisa avançada de resultados por meio de marcação de dados estruturados")](web-markup-images/deeplink01.png#lightbox)

Apple atualmente suporta os seguintes tipos de esquema de schema.org:

 - AggregateRating
 - ImageObject
 - InteractionCount
 - Oferece
 - Organização
 - PriceRange
 - Receita
 - SearchAction

Para obter mais informações sobre esses tipos de esquema, consulte [schema.org](http://schema.org).

## <a name="providing-actions-with-structured-data"></a>Fornecendo ações com dados estruturados

Tipos específicos de dados estruturados permitirá que um resultado de pesquisa ser acionáveis pelo usuário final. Atualmente há suporte para as seguintes ações:

 - Discar um número de telefone.
 - Obtendo a direção de mapa para um endereço específico.
 - Execução de um arquivo de áudio ou vídeo.

Por exemplo, definir uma ação para discar um número de telefone pode parecer com o seguinte:

```xml
<div itemscope itemtype="http://schema.org/Organization">
    <span itemprop="telephone">(408) 555-1212**


```

Quando esse resultado de pesquisa é apresentado ao usuário final, um ícone pequeno telefone será exibido no resultado. Se o usuário tocar no ícone, o número especificado será chamado.

Adicione uma ação para executar um arquivo de áudio do resultado da pesquisa de HTML a seguir:

```xml
<div itemscope itemtype="http://schema.org/AudioObject">
    <span itemprop="contentUrl">http://company.com/appname/greeting.m4a**


```

Por fim, o HTML a seguir seria adicionar uma ação para obter instruções do resultado da pesquisa:

```xml
<div itemscope itemtype="http://schema.org/PostalAddress">
    <span itemprop="streetAddress">1 Infinite Loop**
    <span itemprop="addressLocality">Cupertino**
    <span itemprop="addressRegion">CA**
    <span itemprop="postalCode">95014**


```

Para obter mais informações, consulte da Apple [Site do desenvolvedor de pesquisa de aplicativo](http://developer.apple.com/ios/search/).



## <a name="related-links"></a>Links relacionados

- [Exemplos do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guia de programação de aplicativo de pesquisa](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
