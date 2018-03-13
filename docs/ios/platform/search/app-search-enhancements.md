---
title: Aprimoramentos da pesquisa do aplicativo
description: "Este artigo aborda os aprimoramentos Apple fez a pesquisa do aplicativo no iOS 10 e como implementá-los no xamarin."
ms.topic: article
ms.prod: xamarin
ms.assetid: 30124DB6-6A02-4F66-A2D9-BBC8008E6B48
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: af124c2ae0390c5321e9dd34158c7b53b33b2c48
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="app-search-enhancements"></a>Aprimoramentos da pesquisa do aplicativo

_Este artigo aborda os aprimoramentos Apple fez a pesquisa do aplicativo no iOS 10 e como implementá-los no xamarin._

No iOS 10, Apple fez várias melhorias para pesquisa de aplicativo como vínculo profundo de Crowdsourced, no aplicativo de pesquisa, continuação da pesquisa e visualização dos resultados da validação. Este artigo abordará implementar esses recursos em um aplicativo xamarin.

## <a name="about-app-search-enhancements"></a>Sobre os aprimoramentos de pesquisa do aplicativo

Destaque de núcleo no iOS 10 oferece vários aprimoramentos à pesquisa do aplicativo, como:

- **Crowdsourced Link profundo popularidade (com a privacidade diferencial)** -fornece uma maneira de promover o conteúdo do aplicativo com vínculo profundo nos resultados da pesquisa.
- **Pesquisar no aplicativo** -usar o novo `CSSearchQuery` classe para fornecer capacidade de pesquisa de destaque no aplicativo semelhante a como funcionam os aplicativos de email, mensagens e anotações.
- **Pesquisar continuação** - permite que um usuário iniciar uma pesquisa em destaque ou Safari e, em seguida, abrir um aplicativo e continuar a pesquisa.
- **Visualização de resultados de validação** -da Apple [ferramenta de validação de API de pesquisa do aplicativo](https://search.developer.apple.com/appsearch-validation-tool) agora exibe uma representação visual de marcação de um site e vinculando profundo quando preforming testes.
- **Imagem de aplicativo de compartilhamento de mensagem** -permite populares imagens no aplicativo fornecidas para o compartilhamento de mensagens (por meio de uma extensão de aplicativo de mensagem) apareçam na pesquisa do Spotlight.

As seções a seguir aborda esses tópicos mais detalhadamente.

## <a name="crowdsourced-deep-link-popularity"></a>Link profundo Crowdsourced popularidade

iOS 10 fornece um mecanismo para contar a frequência que links profundo populares em um aplicativo são seguidas pelo usuário e usa essas informações para melhorar a classificação de um aplicativo do conteúdo nos resultados da pesquisa, ainda proteger a identidade do usuário usando  *Privacidade diferencial*.

Para aplicativos que usam `NSUserActivity` objetos para fornecer URLs de link profundo e ter o `EligibleForPublicIndexing` propriedade definida como `true`, iOS 10 envia um subconjunto de *diferencial Hashes de privacidade* para servidores da Apple. Essas informações, em seguida, são usadas para promover popular conteúdo no aplicativo nos resultados da pesquisa.

Para obter mais informações sobre a implementação de vinculação profundo em um aplicativo xamarin, consulte nosso [pesquisa com NSUserActivity](~/ios/platform/search/nsuseractivity.md) documentação.

## <a name="in-app-searching"></a>No aplicativo de pesquisa

Implementando o novo [CSSearchQuery](https://developer.apple.com/reference/corespotlight/cssearchquery) classe, um aplicativo pode fornecer a pesquisa e a tecnologia de regra correspondente para localizar o conteúdo dentro do próprio, sem precisar deixar o aplicativo (semelhante ao modo como o email, mensagens e anotações de aplicativo do Spotlight trabalho).

Normalmente, aplicativos que dão suporte a `CSSearchQuery` não será necessário manter seu próprios, índice de pesquisa separadas. 

## <a name="search-continuation"></a>Continuação da pesquisa

No iOS 9, a Apple introduziu as APIs de pesquisa (como destaque de núcleo, `NSUserActivity` e marcação da web) para fornecer a preferência de profundidade do conteúdo dentro de um aplicativo para permitir que os usuários pesquisar esse conteúdo usando as interfaces de pesquisa do Spotlight e o Safari. Consulte nossa [novas APIs de pesquisa](~/ios/platform/search/index.md) documentação para obter mais detalhes.

No iOS Apple 10 baseia-se esse recurso, permitindo que o usuário iniciar uma pesquisa em destaque ou Safari e continuar a pesquisa ao abrir um aplicativo. 

Para implementar esse recurso, edite o aplicativo `Info.plist` de arquivo, adicione o `CoreSpotlightContinuation` chave do tipo **booliano** e defina seu valor como `YES`:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![](app-search-enhancements-images/search01.png "Editando CoreSpotlightContinuation no arquivo Info. plist")](app-search-enhancements-images/search01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](app-search-enhancements-images/searchw01.png "Editando CoreSpotlightContinuation no arquivo Info. plist")](app-search-enhancements-images/search01.png#lightbox)

-----

Responder ao usuário continuar um resultado de pesquisa (`NSUserActivity`), edite o `AppDelegate.cs` de arquivo e substituir o `ContinueUserActivity` método. Por exemplo:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchQuery.ContinuationActionType) {
            var search = userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString);
            // Continue user's search here...
        }
        break;
    }

    return true;
}
```

Esse código procura o tipo de ação de acompanhamento de consulta (`userActivity.ActivityType == CSSearchQuery.ContinuationActionType`), lê a consulta do usuário atual do `NSUserActivity` dicionário de informações do usuário da classe (`userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString)`). Aqui, o aplicativo precisa executar uma ação para continuar a pesquisa do usuário.

Para obter mais informações sobre como trabalhar com pesquisas em um aplicativo xamarin, consulte nosso [pesquisa com destaque Core](~/ios/platform/search/corespotlight.md) documentação.

## <a name="visualization-of-validation-results"></a>Visualização de resultados de validação

Da Apple [ferramenta de validação de API de pesquisa do aplicativo](https://search.developer.apple.com/appsearch-validation-tool) agora exibe uma representação visual de marcação de um site e profundo de vinculação (inclusive a marcação, como definida no [Schema.org](http://schema.org/)) quando preforming testes.

Usando a ferramenta de validação, um desenvolvedor pode ver as informações que o rastreador Applebot tenha indexado para o site, como título, descrição, URL e quaisquer outros elementos de suporte.

Para obter mais informações sobre como trabalhar com marcação da Web, consulte nosso [pesquisa com marcação Web](~/ios/platform/search/web-markup.md) documentação.

## <a name="message-app-image-sharing"></a>Imagem de aplicativo de compartilhamento de mensagem

Se uma mensagem da extensão do aplicativo fornece imagens para o compartilhamento em mensagens, a extensão pode ser configurada para permitir que o usuário executar pesquisas de destaque para imagens populares de dentro de mensagens, sem precisar deixar o aplicativo.

Para habilitar esse recurso, faça o seguinte:

1. Crie uma extensão de aplicativo de mensagem.
2. Adicionar o `com.apple.developer.associated-domains` autorizações do aplicativo e incluir uma lista de domínios da web que hospedam as imagens que está compartilhando a mensagem da extensão do aplicativo. Para cada domínio, especifique o `spotlight-image-search` service.
3. Adicionar um `apple-app-site-association` arquivo para o site que está hospedando as imagens. Esse arquivo contém um dicionário para a `spotlight-image-search` de serviço e inclui a ID do aplicativo, que é o prefixo de ID de equipe ou aplicativo seguido por identificação do pacote. O arquivo pode conter até 500 caminhos e padrões que serão indexados por destaque e incluídos nas pesquisas de imagem populares. Para obter mais informações, consulte da Apple [criar e carregar o arquivo de associação](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW4) documentação.
4. Permitir Applebot rastrear os sites da Web. Consulte da Apple [sobre Applebot](https://support.apple.com/en-us/HT204683) documentação.

Consulte nossa [integração do aplicativo de mensagens](~/ios/platform/message-app-integration/index.md) documentação para obter mais detalhes.

## <a name="summary"></a>Resumo

Este artigo abordou os aprimoramentos Apple fez a pesquisa do aplicativo no iOS 10 e como implementá-los no xamarin.



## <a name="related-links"></a>Links relacionados

- [Exemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
