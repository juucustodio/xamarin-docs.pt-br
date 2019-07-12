---
title: APIs de pesquisa no xamarin. IOS
description: Este artigo aborda usando as novas APIs de pesquisa de aplicativo fornecido pelo iOS 9 para permitir que os usuários a pesquisar as informações e recursos em seus aplicativos xamarin. IOS.
ms.prod: xamarin
ms.assetid: 7323EB3D-A78F-4BF0-9990-3160C7E83CF0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 87f7689a082c891bc199f4221e68ed7ab19adf85
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832717"
---
# <a name="search-apis-in-xamarinios"></a>APIs de pesquisa no xamarin. IOS

_Este artigo aborda usando as APIs de pesquisa de aplicativos fornecido pelo iOS 9 para permitir que os usuários a pesquisar as informações e recursos em seus aplicativos xamarin. IOS._

Pesquisa foi expandida no iOS 9 para fornecer novas formas de acessar informações e recursos dentro de um aplicativo xamarin. IOS. Conteúdo do aplicativo usando as novas APIs de pesquisa do aplicativo, é feito pesquisável por meio de destaque e Safari resultados da pesquisa, entrega e lembretes Siri e sugestões. Isso permite que os usuários acessem rapidamente atividades e informações profundas dentro de seu aplicativo.

Além disso, as novas APIs de pesquisa de tornar mais fácil integrar a pesquisa em seu aplicativo sem experiência de implementação de pesquisa anterior. Por isso, a Apple declarações que normalmente leva algumas horas para tornar o conteúdo de um aplicativo iOS 9 universalmente pesquisáveis usando a pesquisa do aplicativo.

[![](images/intro01.png "Um exemplo do conteúdo de aplicativo do iOS 9 universalmente pesquisável usando a pesquisa do aplicativo")](images/intro01.png#lightbox)

Pesquisa de aplicativo é composta por três APIs separadas:

1. [**NSUserActivity** ](nsuseractivity.md) -essa é uma extensão da API de entrega que Apple lançado no iOS 8. Ele é usado para tornar o histórico de interação do aplicativo pesquisáveis publicamente e em particular) pelo usuário.

2. [**Destaque de núcleo** ](corespotlight.md) -permite que um aplicativo indexar seu conteúdo a serem apresentados nos resultados da pesquisa. Ele funciona como um banco de dados em que itens podem ser adicionados e removidos e é a melhor maneira de indexar conteúdo privado dentro de um aplicativo de API.

3. [**WebMarkup** ](web-markup.md) – para aplicativos que fornecem acesso a seu conteúdo por meio de uma interface da web (não apenas de dentro do aplicativo). Conteúdo da Web pode ser marcado com links especiais que serão rastreados pela Apple e fornecer vinculação profunda para seu aplicativo no dispositivo iOS 9 do usuário.

## <a name="selecting-an-app-search-approach"></a>Selecionando uma abordagem de pesquisa do aplicativo

Decidir qual desses métodos para implementar depende dos tipos de interação fornecidas pelo seu aplicativo e o tipo de conteúdo, que ele apresenta.

Use as seguintes diretrizes:

- [**NSUserActivity** ](nsuseractivity.md) – Use essa estrutura para fornecer capacidade de pesquisa para conteúdo público e privado e também a capacidade de pesquisa de pontos de navegação dentro do seu aplicativo.

- [**Destaque de núcleo** ](corespotlight.md) – Use essa estrutura para fornecer capacidade de pesquisa para dados particulares armazenados no dispositivo.

- [**Web marcação** ](web-markup.md) – Use essa estrutura para fornecer capacidade de pesquisa para aplicativos que apresentam o seu conteúdo não apenas de dentro do aplicativo, mas de site do aplicativo também.

Cada aplicativo pesquisa abordagens são diferentes e podem ser usado individualmente, porém Apple projetada para que funcionem juntos. Ao usar mais de uma abordagem para um item específico de índice, certifique-se de que você use o mesmo **ID do Item** em cada abordagem, portanto, essa pessoa vincula trabalho.

Usar mais de uma abordagem não apenas garante que seu conteúdo será encontrado pelo usuário final, mas também ajuda a melhorar a classificação do seu item, de dentro de pesquisa.

Durante o processo de classificação em bem transparente ao desenvolvedor de interação do usuário com um determinado item pondera intensamente após essa classificação (por exemplo, o usuário colar um link).
Ao fornecer itens informativos e avançados, você pode garantir que um usuário será atraído para interagir com seu conteúdo, gerando, portanto, sua classificação.

## <a name="what-content-to-index"></a>O conteúdo ao índice

A Apple fornece as seguintes sugestões sobre quais ações e o conteúdo para fornecer os índices de pesquisa para em seu aplicativo:

- Qualquer conteúdo exibido, criado ou fornecida pelo usuário dentro de seu aplicativo.
- Pontos de navegação e os recursos do aplicativo.
- Coisas como novas mensagens, conteúdo ou outros tipos de itens exibidos pelo seu aplicativo que recentemente foram baixados no dispositivo.

## <a name="app-search-enhancements"></a>Aperfeiçoamentos na pesquisa de aplicativo

Destaque principal no iOS 10 oferece vários aprimoramentos para o aplicativo de pesquisa, como:

- **Popularidade do Link profundo de crowdsourcing (com a privacidade diferencial)** -fornece uma maneira de promover o conteúdo do aplicativo com vínculo profundo nos resultados da pesquisa.
- **No aplicativo pesquisando** -usar o novo `CSSearchQuery` classe para fornecer capacidade de pesquisa de destaque no aplicativo semelhante a como funcionam os aplicativos de email, mensagens e anotações.
- **Pesquisar continuação** - permite que um usuário iniciar uma pesquisa em destaque ou Safari e, em seguida, abrir um aplicativo e continuar essa pesquisa.
- **Visualização de resultados de validação** – da Apple [ferramenta de validação de API de pesquisa do aplicativo](https://search.developer.apple.com/appsearch-validation-tool) agora exibe uma representação visual de marcação de um site e vinculação profunda ao realizar testes.
- **Aplicativo de compartilhamento de imagem da mensagem** -permite populares imagens no aplicativo fornecidas para o compartilhamento de mensagens (por meio de uma extensão de aplicativo de mensagem) para aparecer em pesquisas de destaque.

Para obter mais informações, consulte nosso [aprimoramentos de aplicativo de pesquisa](~/ios/platform/search/app-search-enhancements.md) guia.

### <a name="proactive-suggestions"></a>Sugestões proativas

iOS 10 apresenta novas maneiras de condução contrato a um aplicativo, permitindo que o sistema de forma proativa apresentam informações úteis automaticamente para o usuário em momentos apropriados. Assim como o iOS 9 fornecidos a capacidade de adicionar a pesquisa aprofundada ao aplicativo usando o destaque, entrega e sugestões de Siri com iOS 10, que um aplicativo pode expor a funcionalidade que pode ser apresentada ao usuário pelo sistema de dentro dos seguintes locais:

- O seletor de aplicativo
- A tela de bloqueio
- CarPlay
- Mapas
- Interações de Siri
- Sugestões de QuickType 

Um aplicativo expõe essa funcionalidade no sistema usando uma coleção de tecnologias, como [NSUserActivity](xref:Foundation.NSUserActivity), marcação de web, destaque principal, MapKit, Media Player e UIKit.

Para obter mais informações, consulte nosso [sugestões proativas](~/ios/platform/search/proactive-suggestions.md) guia.

## <a name="summary"></a>Resumo

Este artigo abordou os novos recursos da API de pesquisa se iOS 9 fornece para aplicativos xamarin. IOS. Ele coberto [NSUserActivity](nsuseractivity.md), [destaque principal](corespotlight.md) e [marcação da Web](web-markup.md) métodos para indexação de conteúdo. Ele concluído com uma breve descrição de quando uma abordagem de pesquisa fornecido deve ser usada e quais tipos de conteúdo devem ser indexados.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guia de programação de aplicativo de pesquisa](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
