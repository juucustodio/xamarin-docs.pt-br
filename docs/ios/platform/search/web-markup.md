---
title: Pesquisa com a marcação da Web no xamarin. IOS
description: Este documento descreve como criar resultados de pesquisa baseado na web que têm links para um aplicativo xamarin. IOS. Ele aborda como habilitar o conteúdo da web de indexação, tornando o site do seu aplicativo detectáveis, usando faixas de aplicativos inteligentes, links universais e muito mais.
ms.prod: xamarin
ms.assetid: 876315BA-2EF9-4275-AE33-A3A494BBF7FD
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 243408ce6e2236b75ea35dfd17633a9a24493c1b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102789"
---
# <a name="search-with-web-markup-in-xamarinios"></a>Pesquisa com a marcação da Web no xamarin. IOS

Para aplicativos que fornecem acesso a seu conteúdo por meio de um site da web (não apenas de dentro do aplicativo), conteúdo da web pode ser marcado com links especiais que serão rastreados pela Apple e fornecer vinculação profunda ao seu aplicativo no dispositivo iOS 9 do usuário.

Se seu aplicativo iOS já oferece suporte a vinculação profunda móvel e seu site apresentados links profundos para conteúdos dentro do seu aplicativo, da Apple _Applebot_ rastreador da web será esse conteúdo de índice e automaticamente adicioná-lo ao seu índice de nuvem:

[![](web-markup-images/webmarkup01.png "Visão geral do índice de nuvem")](web-markup-images/webmarkup01.png#lightbox)

Apple será superficial esses resultados nos resultados da pesquisa de destaque e pesquisa do Safari.
Se o usuário toca em um desses resultados (e eles tiverem o aplicativo instalado) e em seguida, será levado para o conteúdo em seu aplicativo:

[![](web-markup-images/webmarkup02.png "Vinculação de um site nos resultados da pesquisa profunda")](web-markup-images/webmarkup02.png#lightbox)

## <a name="enabling-web-content-indexing"></a>Habilitando a indexação de conteúdo da Web

Há quatro etapas necessárias para fazer com que você conteúdo do aplicativo podem ser pesquisados usando a marcação da Web:

1. Certifique-se de que a Apple pode descobrir e indexar o site do seu aplicativo definindo-o como o **suporte** ou **Marketing** site no iTunes Connect.
2. Certifique-se de que o site do seu aplicativo contém a marcação necessária para implementar a vinculação profunda móvel. Consulte as seções abaixo para obter mais detalhes.
3. Habilite o link profundo de tratamento em seu aplicativo iOS.
4. Adicione a marcação para os dados estruturados exibidos pelo site do seu aplicativo para fornecer um resultado avançado e envolvente para o usuário final. Embora esta etapa não seja estritamente necessária, é altamente recomendável pela Apple.

As seções a seguir apresentará essas etapas em detalhes.

## <a name="make-your-apps-website-discoverable"></a>Tornar o site do seu aplicativo localizável

A maneira mais fácil ter Apple encontrar o site do seu aplicativo é usá-lo como o **suporte** ou **Marketing** site quando você envia seu aplicativo para a Apple por meio do iTunes Connect.

## <a name="using-smart-app-banners"></a>Usando faixas de aplicativos inteligentes

Forneça uma faixa de aplicativos inteligentes em seu site para apresentar um link criptografado em seu aplicativo. Se o aplicativo já não estiver instalado, o Safari automaticamente solicitará ao usuário para instalar seu aplicativo. Caso contrário, o uso pode tocar a **exibição** link para iniciar seu aplicativo do site. Por exemplo, para criar uma faixa de aplicativos inteligentes, você pode usar o código a seguir:

```xml
<meta name="AppName" content="app-id=123456, app-argument=http://company.com/AppName">
```

Para obter mais informações, consulte da Apple [promover aplicativos com faixas de aplicativos inteligentes](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html) documentação.

## <a name="using-universal-links"></a>Usando Links universais

Novo para o iOS 9, Links universais fornecem uma alternativa melhor para faixas de aplicativos inteligentes ou esquemas de URL personalizadas existentes, fornecendo o seguinte:

- **Exclusivo** – a mesma URL não pode ser exigida por mais de um site da Web.
- **Proteger** – um certificado assinado é necessário para o site que garante que o site é de propriedade por você e devidamente vinculado ao seu aplicativo.
- **Flexível** – o usuário final pode controlar se a URL inicia o site ou aplicativo.
- **Universal** – a mesma URL pode ser usada para definir o conteúdo de seu site e seu aplicativo.

## <a name="using-twitter-cards"></a>Uso de cartões do Twitter

Você pode fornecer links profundos para o conteúdo do seu aplicativo usando um cartão do Twitter. Por exemplo:

```xml
<meta name="twitter:app:name:iphone" content="AppName">
<meta name="twitter:app:id:iphone" content="AppNameID">
<meta name="twitter:app:url:iphone" content="AppNameURL">
```

Para obter mais informações, consulte do Twitter [Twitter cartão protocolo](http://dev.twitter.com/cards/mobile) documentação.

## <a name="using-facebook-app-links"></a>Usando Links de aplicativo do Facebook

Você pode fornecer links profundos para o conteúdo do seu aplicativo usando um Link de App do Facebook. Por exemplo:

```xml
<meta property="al:ios:app_name" content="AppName">
<meta property="al:ios:app_store_id" content="AppNameID">
<meta property="al:ios:url" content="AppNameURL">
```

Para obter mais informações, consulte do Facebook [Links aplicativo](http://applinks.org) documentação.

## <a name="opening-deep-links"></a>Abrir Links profundos

Você precisa adicionar suporte para abrir e exibir Links profundos em seu aplicativo xamarin. IOS. Editar o **AppDelegate.cs** do arquivo e substituir o `OpenURL` método para lidar com o formato de URL personalizado. Por exemplo:

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

No código acima, estamos procurando uma URL que contém `/appname` e passando o valor da `query` (`123` neste exemplo) para um controlador de exibição personalizada no nosso aplicativo para exibir o conteúdo solicitado ao usuário.

## <a name="providing-rich-results-with-structured-data"></a>Fornecendo os resultados de Rich com dados estruturados

Com a inclusão de marcação estruturado de dados, você pode fornecer os resultados da pesquisa avançada para o usuário final que vão além de simplesmente um título e uma descrição. Inclua imagens, dados específicos do aplicativo (por exemplo, classificações) e ações para os resultados usando a marcação de dados estruturados.

Resultados de avançados são mais interessantes e podem ajudar a melhorar o índice de pesquisa com base em sua classificação na nuvem por atraentes aos usuários mais interagir com eles.

Uma opção para fornecer marcação estruturado de dados é por meio de Open Graph. Por exemplo:

```xml
<meta property="og:image" content="http://company.com/appname/icon.jpg">
<meta property="og:audio" content="http://company.com/appname/theme.m4a">
<meta property="og:video" content="http://company.com/appname/tutorial.mp4">
```

Para obter mais informações, consulte o [Open Graph](http://ogp.me) site.

Outro formato comum para marcação estruturado de dados é o formato de Microdados de schema.org. Por exemplo:

```xml
<div itemprop="aggregateRating" itemscope itemtype="http://schema.org/AggregateRating">
    <span itemprop="ratingValue">4** stars -
    <span itemprop="reviewCount">255** reviews


```

As mesmas informações podem ser representadas no formato de JSON-LD do schema.org:

```xml
<script type="application/ld+json">
    "@content":"http://schema.org",
    "@type":"AggregateRating",
    "ratingValue":"4",
    "reviewCount":"255"
</script>
```

O exemplo a seguir mostra um exemplo de metadados do seu site que está fornecendo os resultados da pesquisa avançada para o usuário final:

[![](web-markup-images/deeplink01.png "Pesquisa avançada de resultados por meio de marcação estruturado de dados")](web-markup-images/deeplink01.png#lightbox)

Apple atualmente suporta os seguintes tipos de esquema de schema.org:

 - AggregateRating
 - ImageObject
 - InteractionCount
 - Ofertas
 - Organização
 - PriceRange
 - Receita
 - SearchAction

Para obter mais informações sobre esses tipos de esquema, consulte [schema.org](http://schema.org).

## <a name="providing-actions-with-structured-data"></a>Fornecendo ações com os dados estruturados

Tipos específicos de dados estruturados permitirá que um resultado de pesquisa ser acionáveis pelo usuário final. Atualmente, há suporte para as seguintes ações:

 - Discar um número de telefone.
 - Introdução a direção de mapa para um determinado endereço.
 - Execução de um arquivo de áudio ou vídeo.

Por exemplo, definir uma ação para discar um número de telefone pode parecer com o seguinte:

```xml
<div itemscope itemtype="http://schema.org/Organization">
    <span itemprop="telephone">(408) 555-1212**


```

Quando esse resultado de pesquisa é apresentado ao usuário final, um ícone pequeno do telefone será exibido no resultado. Se o usuário toca o ícone, o número especificado será chamado.

Adicione uma ação para reproduzir um arquivo de áudio do resultado da pesquisa o HTML a seguir:

```xml
<div itemscope itemtype="http://schema.org/AudioObject">
    <span itemprop="contentUrl">http://company.com/appname/greeting.m4a**


```

Por fim, o HTML a seguir seria adicionar uma ação para obter direções do resultado da pesquisa:

```xml
<div itemscope itemtype="http://schema.org/PostalAddress">
    <span itemprop="streetAddress">1 Infinite Loop**
    <span itemprop="addressLocality">Cupertino**
    <span itemprop="addressRegion">CA**
    <span itemprop="postalCode">95014**


```

Para obter mais informações, consulte da Apple [Site do desenvolvedor do aplicativo de pesquisa](http://developer.apple.com/ios/search/).



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guia de programação de aplicativo de pesquisa](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
