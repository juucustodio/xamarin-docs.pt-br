---
title: Pesquisar APIs no Xamarin. iOS
description: Este artigo aborda o uso das novas APIs de pesquisa de aplicativo fornecidas pelo iOS 9 para permitir que os usuários pesquisem informações e recursos dentro de seus aplicativos Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 7323EB3D-A78F-4BF0-9990-3160C7E83CF0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: ec63407189b635a9586b02e848647518aaf0170f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031559"
---
# <a name="search-apis-in-xamarinios"></a>Pesquisar APIs no Xamarin. iOS

_Este artigo aborda o uso das APIs de pesquisa de aplicativo fornecidas pelo iOS 9 para permitir que os usuários pesquisem informações e recursos dentro de seus aplicativos Xamarin. iOS._

A pesquisa foi expandida no iOS 9 para fornecer excelentes maneiras novas de acessar informações e recursos em um aplicativo Xamarin. iOS. Usando as novas APIs de pesquisa de aplicativo, o conteúdo do aplicativo é tornado pesquisável por meio de resultados da pesquisa do Spotlight e do Safari, lembretes e sugestões de entrega e Siri. Isso permite que os usuários acessem rapidamente as atividades e as informações em seu aplicativo.

Além disso, as novas APIs de pesquisa facilitam a integração da pesquisa em seu aplicativo sem a experiência de implementação anterior da pesquisa. Por isso, a Apple alega que normalmente leva algumas horas para fazer com que o conteúdo de um aplicativo iOS 9 seja pesquisado universalmente usando a pesquisa de aplicativos.

[![](images/intro01.png "An example of iOS 9 app content universally searchable using App Search")](images/intro01.png#lightbox)

A pesquisa de aplicativo é composta por três APIs separadas:

1. [**NSUserActivity**](nsuseractivity.md) -essa é uma extensão da API de entrega que a Apple lançou no Ios 8. Ele é usado para tornar o histórico de interação do aplicativo pesquisável de forma pública e privada) pelo usuário.

2. [**Core Spotlight**](corespotlight.md) – permite que um aplicativo indexe seu conteúdo a ser apresentado nos resultados da pesquisa. Ele funciona como uma API de banco de dados na qual os itens podem ser adicionados e removidos e é a melhor maneira de indexar conteúdo privado em um aplicativo.

3. [**Webmarkup**](web-markup.md) – para aplicativos que fornecem acesso ao seu conteúdo por meio de uma interface da Web (não apenas de dentro do aplicativo). O conteúdo da Web pode ser marcado com links especiais que serão rastreados pela Apple e fornecerá uma vinculação profunda ao seu aplicativo no dispositivo iOS 9 do usuário.

## <a name="selecting-an-app-search-approach"></a>Selecionando uma abordagem de pesquisa de aplicativo

A decisão de quais desses métodos implementar depende dos tipos de interação fornecidos pelo seu aplicativo e do tipo de conteúdo que ele apresenta.

Use as seguintes diretrizes:

- [**NSUserActivity**](nsuseractivity.md) – Use essa estrutura para fornecer a você a sua pesquisa de conteúdo público e privado e também a pesquisa de pontos de navegação em seu aplicativo.

- [**Destaque principal**](corespotlight.md) – Use essa estrutura para fornecer a você a pesquisa de dados privados armazenados no dispositivo.

- [**Marcação da Web**](web-markup.md) – Use essa estrutura para fornecer a você a pesquisa de aplicativos que apresentam seu conteúdo não apenas de dentro do aplicativo, mas também do site do aplicativo.

Cada uma das abordagens de pesquisa de aplicativo é distinta e pode ser usada individualmente, no entanto, a Apple as projetou para trabalhar juntas. Ao usar mais de uma abordagem para indexar um item específico, certifique-se de usar a mesma **ID de item** em cada abordagem, para que os links individuais funcionem juntos.

Usar mais de uma abordagem não só garante que seu conteúdo será encontrado pelo usuário final, mas também ajuda a melhorar a classificação do seu item de dentro da pesquisa.

Embora o processo de classificação, em grande parte, transparente para o desenvolvedor, a interação do usuário com um determinado item se pesa muito com essa classificação (por exemplo, o usuário gravação um link).
Ao fornecer itens sofisticados e informativos, você pode garantir que um usuário seja induzido a interagir com o conteúdo, gerando assim sua classificação.

## <a name="what-content-to-index"></a>Qual conteúdo indexar

A Apple fornece as seguintes sugestões sobre o conteúdo e as ações para fornecer índices de pesquisa para o seu aplicativo:

- Qualquer conteúdo exibido, criado ou organizado pelo usuário de dentro de seu aplicativo.
- Pontos de navegação e recursos no aplicativo.
- Coisas como novas mensagens, conteúdo ou outros tipos de itens exibidos por seu aplicativo que foram recentemente baixados para o dispositivo.

## <a name="app-search-enhancements"></a>Aperfeiçoamentos na pesquisa de aplicativo

O principal Spotlight no iOS 10 fornece vários aprimoramentos para a pesquisa de aplicativos, como:

- **Crowdsourcing a popularidade do link profundo (com privacidade diferencial)** – fornece uma maneira de promover o conteúdo do aplicativo vinculado em profundidade nos resultados da pesquisa.
- **Pesquisa no aplicativo** – use a nova classe `CSSearchQuery` para fornecer uma capacidade de pesquisa no aplicativo em destaque semelhante à forma como os aplicativos mail, messages e Notes funcionam.
- **Continuação da pesquisa** – permite que um usuário inicie uma pesquisa no Spotlight ou no Safari, em seguida, abra um aplicativo e continue a pesquisa.
- **Visualização dos resultados da validação** – a [ferramenta de validação da API de pesquisa de aplicativos](https://search.developer.apple.com/appsearch-validation-tool) da Apple agora exibe uma representação visual da marcação de um site e da vinculação profunda ao preformar testes.
- **Compartilhamento de imagem de aplicativo de mensagem** – permite que imagens populares no aplicativo fornecidas para compartilhamento em mensagens (por meio de uma extensão de aplicativo de mensagem) apareçam em pesquisas de destaque.

Para saber mais, consulte nosso guia de [aprimoramentos de pesquisa de aplicativo](~/ios/platform/search/app-search-enhancements.md) .

### <a name="proactive-suggestions"></a>Sugestões proativas

o iOS 10 apresenta novas maneiras de conduzir o envolvimento a um aplicativo, permitindo que o sistema apresente informações úteis de forma proativa automaticamente para o usuário em momentos apropriados. Assim como o iOS 9 fornecia a capacidade de adicionar uma pesquisa profunda ao aplicativo usando as sugestões de Spotlight, entrega e Siri, com o iOS 10, um aplicativo pode expor a funcionalidade que pode ser apresentada ao usuário pelo sistema de dentro dos seguintes locais:

- O alternador de aplicativo
- A tela de bloqueio
- CarPlay
- Mapas
- Interações de Siri
- Sugestões de apelidos 

Um aplicativo expõe essa funcionalidade ao sistema usando uma coleção de tecnologias como [NSUserActivity](xref:Foundation.NSUserActivity), Web Markup, Core Spotlight, MapKit, Media Player e UIKit.

Para saber mais, confira nosso guia de [sugestões proativo](~/ios/platform/search/proactive-suggestions.md) .

## <a name="summary"></a>Resumo

Este artigo abordou os novos recursos de API de pesquisa que o iOS 9 fornece para aplicativos Xamarin. iOS. Ele abordou os métodos [NSUserActivity](nsuseractivity.md), [Core Spotlight](corespotlight.md) e [marcação da Web](web-markup.md) para indexar conteúdo. Ele terminou com uma breve discussão de quando uma determinada abordagem de pesquisa deve ser usada e quais tipos de conteúdo devem ser indexados.

## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guia de programação de pesquisa de aplicativo](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
