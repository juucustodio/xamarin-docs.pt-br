---
title: Aprimoramentos de pesquisa do aplicativo no xamarin. IOS
description: Este artigo aborda os aprimoramentos de Apple fez a pesquisa de aplicativo no iOS 10 e como implementá-los no xamarin. IOS.
ms.prod: xamarin
ms.assetid: 30124DB6-6A02-4F66-A2D9-BBC8008E6B48
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: e61cb20f12f6373ef57beb759b933d3dd9b6e76e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110577"
---
# <a name="app-search-enhancements-in-xamarinios"></a>Aprimoramentos de pesquisa do aplicativo no xamarin. IOS

_Este artigo aborda os aprimoramentos de Apple fez a pesquisa de aplicativo no iOS 10 e como implementá-los no xamarin. IOS._

No iOS 10, a Apple fez várias melhorias à pesquisa de aplicativo, como vinculação profunda de crowdsourcing, pesquisando no aplicativo, continuação da pesquisa e visualização dos resultados da validação. Este artigo abordará a implementação desses recursos em um aplicativo xamarin. IOS.

## <a name="about-app-search-enhancements"></a>Sobre os aprimoramentos de pesquisa do aplicativo

Destaque principal no iOS 10 oferece vários aprimoramentos para o aplicativo de pesquisa, como:

- **Popularidade do Link profundo de crowdsourcing (com a privacidade diferencial)** -fornece uma maneira de promover o conteúdo do aplicativo com vínculo profundo nos resultados da pesquisa.
- **No aplicativo pesquisando** -usar o novo `CSSearchQuery` classe para fornecer capacidade de pesquisa de destaque no aplicativo semelhante a como funcionam os aplicativos de email, mensagens e anotações.
- **Pesquisar continuação** - permite que um usuário iniciar uma pesquisa em destaque ou Safari e, em seguida, abrir um aplicativo e continuar essa pesquisa.
- **Visualização de resultados de validação** – da Apple [ferramenta de validação de API de pesquisa do aplicativo](https://search.developer.apple.com/appsearch-validation-tool) agora exibe uma representação visual de marcação de um site e vinculação profunda ao realizar testes.
- **Aplicativo de compartilhamento de imagem da mensagem** -permite populares imagens no aplicativo fornecidas para o compartilhamento de mensagens (por meio de uma extensão de aplicativo de mensagem) para aparecer em pesquisas de destaque.

As seções a seguir abordarão esses tópicos em mais detalhes.

## <a name="crowdsourced-deep-link-popularity"></a>Link profundo de crowdsourcing popularidade

iOS 10 fornece um mecanismo para contar a frequência que links profundo populares em um aplicativo são seguidas pelo usuário e usa essas informações para melhorar a classificação de um aplicativo do conteúdo nos resultados da pesquisa, e ainda proteger a identidade do usuário usando  *Privacidade diferencial*.

Para aplicativos que usam `NSUserActivity` objetos para fornecer URLs de link profundo e ter o `EligibleForPublicIndexing` propriedade definida como `true`, com iOS 10 envia um subconjunto dos *diferencial Hashes de privacidade* aos servidores da Apple. Essas informações, em seguida, são usadas para promover o conteúdo de no aplicativo popular nos resultados da pesquisa.

Para obter mais informações sobre como implementar a vinculação profunda em um aplicativo xamarin. IOS, consulte nosso [pesquisa com NSUserActivity](~/ios/platform/search/nsuseractivity.md) documentação.

## <a name="in-app-searching"></a>Pesquisar no aplicativo

Implementando o novo [CSSearchQuery](https://developer.apple.com/reference/corespotlight/cssearchquery) classe, um aplicativo pode fornecer a pesquisa e a tecnologia de regra correspondente para localizar o conteúdo em si, sem precisar sair do aplicativo (assim como o email, mensagens e notas de aplicativo em destaque trabalho).

Normalmente, aplicativos que dão suporte a `CSSearchQuery` não precisarão manter sua próprias, índice de pesquisa separadas. 

## <a name="search-continuation"></a>Continuação da pesquisa

No iOS 9, a Apple introduziu as APIs de pesquisa (como destaque de núcleo, `NSUserActivity` e marcação da web) para fornecer profundidade-preferência do conteúdo dentro de um aplicativo para permitir que os usuários procurem esse conteúdo usando as interfaces de pesquisa de destaque e Safari. Consulte nosso [novas APIs de pesquisa](~/ios/platform/search/index.md) documentação para obter mais detalhes.

No iOS 10 Apple aproveita esse recurso, permitindo que o usuário iniciar uma pesquisa em destaque ou Safari e, em seguida, continuar a pesquisa quando ele abrir um aplicativo. 

Para implementar esse recurso, editar o aplicativo `Info.plist` do arquivo, adicione o `CoreSpotlightContinuation` chave do tipo **booliano** e defina seu valor como `YES`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](app-search-enhancements-images/search01.png "Editando CoreSpotlightContinuation no arquivo Info. plist")](app-search-enhancements-images/search01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](app-search-enhancements-images/searchw01.png "Editando CoreSpotlightContinuation no arquivo Info. plist")](app-search-enhancements-images/search01.png#lightbox)

-----

Para responder ao usuário continuar um resultado de pesquisa (`NSUserActivity`), edite o `AppDelegate.cs` do arquivo e substituir o `ContinueUserActivity` método. Por exemplo:

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

Esse código procura o tipo de ação de continuação de consulta (`userActivity.ActivityType == CSSearchQuery.ContinuationActionType`), em seguida, lê a consulta atual do usuário da `NSUserActivity` dicionário de informações do usuário da classe (`userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString)`). A partir daqui, o aplicativo precisa tomar medidas para continuar a pesquisa do usuário.

Para obter mais informações sobre como trabalhar com as pesquisas em um aplicativo xamarin. IOS, consulte nosso [pesquisa de destaque principal](~/ios/platform/search/corespotlight.md) documentação.

## <a name="visualization-of-validation-results"></a>Visualização dos resultados da validação

Da Apple [ferramenta de validação de API de pesquisa do aplicativo](https://search.developer.apple.com/appsearch-validation-tool) agora exibe uma representação visual de marcação de um site e vinculação profunda (inclusive a marcação, como definida no [Schema.org](http://schema.org/)) ao realizar testes.

Usando a ferramenta de validação, um desenvolvedor pode ver as informações que o rastreador da Web Applebot tenha indexado para o site, como título, descrição, URL e qualquer outro suporte para elementos.

Para obter mais informações sobre como trabalhar com marcação da Web, consulte nosso [pesquisar com marcação da Web](~/ios/platform/search/web-markup.md) documentação.

## <a name="message-app-image-sharing"></a>Imagem de mensagem do aplicativo de compartilhamento

Se uma extensão de aplicativo de mensagens fornece imagens para o compartilhamento em mensagens, a extensão pode ser configurada para permitir que o usuário executar pesquisas de destaque para imagens populares nas mensagens, sem precisar sair do aplicativo.

Para habilitar esse recurso, faça o seguinte:

1. Crie uma extensão de aplicativo de mensagem.
2. Adicionar o `com.apple.developer.associated-domains` aos direitos do aplicativo e incluem uma lista de domínios da web que hospedam as imagens de extensão do aplicativo a mensagem está compartilhando. Para cada domínio, especifique o `spotlight-image-search` service.
3. Adicionar um `apple-app-site-association` arquivo para o site que está hospedando as imagens. Esse arquivo contém um dicionário para o `spotlight-image-search` de serviço e inclui a ID do aplicativo, que é o prefixo de ID de equipe ou a ID do aplicativo, seguido de identificação do pacote. O arquivo pode conter até 500 caminhos e padrões que serão indexados pelo destaque e incluídos nas pesquisas de imagens populares. Para obter mais informações, consulte da Apple [criando e carregando o arquivo de associação](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW4) documentação.
4. Permitir que o Applebot rastrear os sites da Web. Consulte da Apple [Applebot sobre](https://support.apple.com/HT204683) documentação.

Consulte nosso [integração de aplicativos de mensagem](~/ios/platform/message-app-integration/index.md) documentação para obter mais detalhes.

## <a name="summary"></a>Resumo

Este artigo abordou os aprimoramentos de Apple fez a pesquisa de aplicativo no iOS 10 e como implementá-los no xamarin. IOS.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
