---
title: APIs de pesquisa em xamarin
description: Este artigo aborda usando as novas APIs de pesquisa do aplicativo fornecido pelo iOS 9 para permitir que os usuários procurar informações e recursos em seus aplicativos xamarin.
ms.prod: xamarin
ms.assetid: 7323EB3D-A78F-4BF0-9990-3160C7E83CF0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: bc62ad34af0b9b98f0475599a08946122badd21e
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788170"
---
# <a name="search-apis-in-xamarinios"></a>APIs de pesquisa em xamarin

_Este artigo aborda usando as APIs de pesquisa do aplicativo fornecido pelo iOS 9 para permitir que usuários procurar informações e recursos em seus aplicativos xamarin._

A pesquisa foi expandida no iOS 9 para fornecer excelentes maneiras de acessar informações e recursos dentro de um aplicativo xamarin. Conteúdo do aplicativo usando as novas APIs de pesquisa do aplicativo, é feito pesquisável através de destaque e Safari resultados da pesquisa, entrega e Siri lembretes e sugestões. Isso permite que os usuários a acessar rapidamente informações profundas dentro de seu aplicativo e atividades.

Além disso, as novas APIs de pesquisa facilitam a integração de pesquisa em seu aplicativo sem experiência de implementação de pesquisa anterior. Por isso, a Apple declarações que geralmente leva apenas algumas horas para tornar o conteúdo de um aplicativo iOS 9 universalmente podem ser pesquisados usando a pesquisa do aplicativo.

[![](images/intro01.png "Um exemplo de conteúdo de aplicativo do iOS 9 universalmente podem ser pesquisado usando a pesquisa do aplicativo")](images/intro01.png#lightbox)

Aplicativo de pesquisa é composta de três APIs separadas:

1. [**NSUserActivity** ](nsuseractivity.md) -essa é uma extensão da API de entrega Apple lançada no iOS 8. Ele é usado para tornar o histórico de interação de aplicativo pesquisável publicamente e em particular) pelo usuário.

2. [**Destaque de núcleo** ](corespotlight.md) -permite que um aplicativo indexar seu conteúdo a serem apresentados nos resultados da pesquisa. Ele funciona como um banco de dados em que itens podem ser adicionados e removidos e é a melhor maneira de conteúdo particular do índice dentro de um aplicativo de API.

3. [**WebMarkup** ](web-markup.md) - para aplicativos que fornecem acesso a seu conteúdo por meio de uma interface da web (não apenas de dentro do aplicativo). Conteúdo da Web pode ser marcado com links especiais que serão rastreadas pela Apple e fornecer a vinculação profunda para seu aplicativo no dispositivo do iOS 9 do usuário.

## <a name="selecting-an-app-search-approach"></a>Selecionando um método de pesquisa do aplicativo

Decidir qual desses métodos para implementar depende dos tipos de interação fornecido pelo seu aplicativo e o tipo de conteúdo apresenta.

Use as seguintes diretrizes:

- [**NSUserActivity** ](nsuseractivity.md) – Use essa estrutura para fornecer capacidade de pesquisa para conteúdo público e privado e também a capacidade de pesquisa de pontos de navegação dentro de seu aplicativo.

- [**Destaque de núcleo** ](corespotlight.md) – Use essa estrutura para fornecer capacidade de pesquisa para privada dados armazenados no dispositivo.

- [**Marcação de Web** ](web-markup.md) – Use essa estrutura para fornecer capacidade de pesquisa para aplicativos que apresentam o seu conteúdo não somente de dentro do aplicativo, mas do site do aplicativo também.

Cada aplicativo pesquisa abordagens são diferentes e podem ser usado individualmente, porém Apple criados para funcionar em conjunto. Ao usar mais de uma abordagem para um item específico de índice, certifique-se de que você use o mesmo **Item ID** cada abordagem, portanto essa pessoa vincula trabalho.

Usar mais de uma abordagem não garante que o conteúdo será encontrado pelo usuário final também ajuda a melhorar a classificação do item de dentro de pesquisa.

Durante o processo de classificação no principalmente transparente para o desenvolvedor, interação do usuário com um determinado item avalia muito após essa classificação (por exemplo, o usuário colar um link).
Fornecendo itens sofisticadas, informativos, você pode garantir que um usuário será atraído para interagir com o seu conteúdo, aumentando, portanto, sua classificação.

## <a name="what-content-to-index"></a>O conteúdo de índice

Apple fornece as seguintes sugestões sobre o conteúdo e as ações para fornecer os índices de pesquisa para seu aplicativo:

 - Qualquer conteúdo exibido, criada ou curadoria pelo usuário de dentro de seu aplicativo.
 - Pontos de navegação e recursos no aplicativo.
 - Coisas como novas mensagens, o conteúdo ou outros tipos de itens exibidos pelo seu aplicativo que recentemente foi baixados no dispositivo.

## <a name="app-search-enhancements"></a>Aprimoramentos da pesquisa do aplicativo

Destaque de núcleo no iOS 10 oferece vários aprimoramentos à pesquisa do aplicativo, como:

- **Crowdsourced Link profundo popularidade (com a privacidade diferencial)** -fornece uma maneira de promover o conteúdo do aplicativo com vínculo profundo nos resultados da pesquisa.
- **Pesquisar no aplicativo** -usar o novo `CSSearchQuery` classe para fornecer capacidade de pesquisa de destaque no aplicativo semelhante a como funcionam os aplicativos de email, mensagens e anotações.
- **Pesquisar continuação** - permite que um usuário iniciar uma pesquisa em destaque ou Safari e, em seguida, abrir um aplicativo e continuar a pesquisa.
- **Visualização de resultados de validação** -da Apple [ferramenta de validação de API de pesquisa do aplicativo](https://search.developer.apple.com/appsearch-validation-tool) agora exibe uma representação visual de marcação de um site e vinculando profundo quando preforming testes.
- **Imagem de aplicativo de compartilhamento de mensagem** -permite populares imagens no aplicativo fornecidas para o compartilhamento de mensagens (por meio de uma extensão de aplicativo de mensagem) apareçam na pesquisa do Spotlight.

Para obter mais informações, consulte nosso [aprimoramentos de pesquisa do aplicativo](~/ios/platform/search/app-search-enhancements.md) guia.

### <a name="proactive-suggestions"></a>Sugestões proativos

iOS 10 apresenta novas maneiras de um contrato a um aplicativo, permitindo que o sistema de forma proativa apresentar informações úteis automaticamente para o usuário em momentos apropriados. Assim como o iOS 9 fornecido a capacidade de adicionar a busca detalhada para o aplicativo com destaque, entrega e sugestões de Siri iOS 10, que um aplicativo pode expor a funcionalidade que pode ser apresentada ao usuário pelo sistema de dentro dos seguintes locais:

- O seletor de exibição do aplicativo
- A tela de bloqueio
- CarPlay
- Mapas
- Interações de Siri
- Sugestões de QuickType 

Um aplicativo expõe essa funcionalidade no sistema usando uma coleção de tecnologias como [NSUserActivity](https://developer.xamarin.com/api/type/Foundation.NSUserActivity/), marcação de web, destaque de núcleo, MapKit, reprodutor de mídia e UIKit.

Para obter mais informações, consulte nosso [sugestões pró-ativo](~/ios/platform/search/proactive-suggestions.md) guia.

## <a name="summary"></a>Resumo

Este artigo abordou os novos recursos da API de pesquisa que iOS 9 fornece para aplicativos xamarin. Ele coberto [NSUserActivity](nsuseractivity.md), [Core Spotlight](corespotlight.md) e [Web marcação](web-markup.md) métodos para indexação de conteúdo. Ele terminar com uma breve discussão de quando uma abordagem de pesquisa fornecida deve ser usada e quais tipos de conteúdo devem ser indexados.



## <a name="related-links"></a>Links relacionados

- [Exemplos do iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [iOS 9 para desenvolvedores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guia de programação de aplicativo de pesquisa](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
