---
title: Aprimoramentos de pesquisa de aplicativo no Xamarin. iOS
description: Este artigo aborda os aprimoramentos que a Apple fez na pesquisa de aplicativo no iOS 10 e como implementá-los no Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 30124DB6-6A02-4F66-A2D9-BBC8008E6B48
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 27eff717fd1390f54a177cc7636e7d107b69cd24
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656278"
---
# <a name="app-search-enhancements-in-xamarinios"></a>Aprimoramentos de pesquisa de aplicativo no Xamarin. iOS

_Este artigo aborda os aprimoramentos que a Apple fez na pesquisa de aplicativo no iOS 10 e como implementá-los no Xamarin. iOS._

No iOS 10, a Apple fez vários aprimoramentos na pesquisa de aplicativos, como crowdsourcing de vinculação profunda, pesquisa no aplicativo, continuação da pesquisa e visualização dos resultados da validação. Este artigo abordará a implementação desses recursos em um aplicativo Xamarin. iOS.

## <a name="about-app-search-enhancements"></a>Sobre aprimoramentos de pesquisa de aplicativo

O principal Spotlight no iOS 10 fornece vários aprimoramentos para a pesquisa de aplicativos, como:

- **Crowdsourcing a popularidade do link profundo (com privacidade diferencial)** – fornece uma maneira de promover o conteúdo do aplicativo vinculado em profundidade nos resultados da pesquisa.
- **Pesquisa no aplicativo** – use a nova `CSSearchQuery` classe para fornecer uma capacidade de pesquisa em destaque no aplicativo, semelhante à forma como os aplicativos mail, messages e Notes funcionam.
- **Continuação da pesquisa** – permite que um usuário inicie uma pesquisa no Spotlight ou no Safari, em seguida, abra um aplicativo e continue a pesquisa.
- **Visualização dos resultados da validação** – a [ferramenta de validação da API de pesquisa de aplicativos](https://search.developer.apple.com/appsearch-validation-tool) da Apple agora exibe uma representação visual da marcação de um site e da vinculação profunda ao preformar testes.
- **Compartilhamento de imagem de aplicativo de mensagem** – permite que imagens populares no aplicativo fornecidas para compartilhamento em mensagens (por meio de uma extensão de aplicativo de mensagem) apareçam em pesquisas de destaque.

As seções a seguir abordarão esses tópicos mais detalhadamente.

## <a name="crowdsourced-deep-link-popularity"></a>Popularidade de link profundo do crowdsourcing

o iOS 10 fornece um mecanismo para contar a frequência com que links detalhados populares em um aplicativo são seguidos pelo usuário e usam essas informações para melhorar a classificação do conteúdo de um aplicativo nos resultados da pesquisa, enquanto ainda protege a identidade do usuário usando *diferencial Privacidade*.

Para o aplicativo que usa `NSUserActivity` objetos para fornecer URLs de link profundo e ter a `EligibleForPublicIndexing` propriedade definida como `true`, o Ios 10 envia um subconjunto de hashes de *privacidade diferencial* para os servidores da Apple. Essas informações são usadas para promover conteúdo popular no aplicativo nos resultados da pesquisa.

Para obter mais informações sobre como implementar a vinculação profunda em um aplicativo Xamarin. iOS, consulte nossa [pesquisa com a](~/ios/platform/search/nsuseractivity.md) documentação do NSUserActivity.

## <a name="in-app-searching"></a>Pesquisa no aplicativo

Ao implementar a nova classe [CSSearchQuery](https://developer.apple.com/reference/corespotlight/cssearchquery) , um aplicativo pode fornecer a pesquisa de destaque e a tecnologia de regra de correspondência para localizar conteúdo dentro de si, sem que o usuário precise sair do aplicativo (semelhante a como o aplicativo de email, mensagens e anotações funciona).

Normalmente, os aplicativos que `CSSearchQuery` oferecem suporte não precisarão manter seu próprio índice de pesquisa separado. 

## <a name="search-continuation"></a>Continuação da pesquisa

No Ios 9, a Apple introduziu as APIs de pesquisa (como o `NSUserActivity` destaque principal e a marcação da Web) para fornecer uma preferência de conteúdo em um aplicativo para permitir que os usuários pesquisem esse conteúdo usando as interfaces de pesquisa do Spotlight e do Safari. Consulte nossa documentação de [novas APIs de pesquisa](~/ios/platform/search/index.md) para obter mais detalhes.

No iOS 10, a Apple se baseia nesse recurso, permitindo que o usuário inicie uma pesquisa em destaque ou Safari e continue a pesquisa quando abrir um aplicativo. 

Para implementar esse recurso, edite o arquivo `Info.plist` do aplicativo, adicione `CoreSpotlightContinuation` a chave do tipo booliano e defina seu `YES`valor como:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](app-search-enhancements-images/search01.png "Editando CoreSpotlightContinuation no arquivo info. plist")](app-search-enhancements-images/search01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](app-search-enhancements-images/searchw01.png "Editando CoreSpotlightContinuation no arquivo info. plist")](app-search-enhancements-images/search01.png#lightbox)

-----

Para responder ao usuário continuando um resultado de pesquisa`NSUserActivity`(), edite o `AppDelegate.cs` arquivo e `ContinueUserActivity` substitua o método. Por exemplo:

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

Esse código procura o tipo de ação de continuação da`userActivity.ActivityType == CSSearchQuery.ContinuationActionType`consulta () e, em seguida, lê a consulta `NSUserActivity` atual do usuário no dicionário de`userActivity.UserInfo.KeyForValue(CSSearchQuery.QueryString)`informações do usuário da classe (). A partir daqui, o aplicativo precisa tomar medidas para continuar a pesquisa do usuário.

Para obter mais informações sobre como trabalhar com pesquisas em um aplicativo Xamarin. iOS, consulte nossa [pesquisa com a documentação do core Spotlight](~/ios/platform/search/corespotlight.md) .

## <a name="visualization-of-validation-results"></a>Visualização dos resultados da validação

A [ferramenta de validação da API de pesquisa de aplicativos](https://search.developer.apple.com/appsearch-validation-tool) da Apple agora exibe uma representação visual da marcação de um site e da vinculação profunda (incluindo marcação como definido em [Schema.org](http://schema.org/)) ao preformar testes.

Usando a ferramenta de validação, um desenvolvedor pode ver as informações que o rastreador da Web do Applebot indexou para o site, como título, descrição, URL e quaisquer outros elementos com suporte.

Para obter mais informações sobre como trabalhar com marcação da Web, consulte nossa documentação [de Pesquisar com marcação da Web](~/ios/platform/search/web-markup.md) .

## <a name="message-app-image-sharing"></a>Compartilhamento de imagens do aplicativo de mensagens

Se uma extensão de aplicativo de mensagem fornecer imagens para compartilhamento em mensagens, a extensão poderá ser configurada para permitir que o usuário realize pesquisas de imagens populares de dentro das mensagens, sem precisar sair do aplicativo.

Para habilitar esse recurso, faça o seguinte:

1. Crie uma extensão de aplicativo de mensagem.
2. Adicione o `com.apple.developer.associated-domains` aos direitos do aplicativo e inclua uma lista de domínios da Web que hospedam as imagens que a extensão do aplicativo de mensagens está compartilhando. Para cada domínio, especifique o `spotlight-image-search` serviço.
3. Adicione um `apple-app-site-association` arquivo ao site que está hospedando as imagens. Esse arquivo inclui um dicionário para o `spotlight-image-search` serviço e inclui a ID do aplicativo, que é a ID da equipe ou o prefixo da ID do aplicativo seguido pela ID do pacote. O arquivo pode conter até 500 caminhos e padrões que serão indexados por destaque e incluídos em pesquisas de imagens populares. Para obter mais informações, consulte [criando e carregando a documentação do arquivo de associação](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW4) da Apple.
4. Permitir que o Applebot rastreie os sites. Consulte a documentação [sobre o Applebot](https://support.apple.com/HT204683) da Apple.

Consulte nossa documentação de [integração do aplicativo de mensagens](~/ios/platform/message-app-integration/index.md) para obter mais detalhes.

## <a name="summary"></a>Resumo

Este artigo abordou os aprimoramentos que a Apple fez na pesquisa de aplicativo no iOS 10 e como implementá-los no Xamarin. iOS.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
