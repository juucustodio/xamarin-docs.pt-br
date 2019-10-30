---
title: Visão geral da distribuição de aplicativos tvOS
description: Este documento fornece uma visão geral das técnicas de distribuição disponíveis para o aplicativo Xamarin. tvOS e serve como um ponteiro para documentos mais detalhados no tópico.
ms.prod: xamarin
ms.assetid: D5E0F446-C083-4E21-9788-FC84D32D00C4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: d8c9d6fe36b457a000e96d1ad8990debad8ac915
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030742"
---
# <a name="tvos-app-distribution-overview"></a>Visão geral da distribuição de aplicativos tvOS

_Este documento fornece uma visão geral das técnicas de distribuição disponíveis para o aplicativo Xamarin. tvOS e serve como um ponteiro para documentos mais detalhados no tópico._

Depois que o aplicativo Xamarin. tvOS tiver sido desenvolvido, a próxima etapa no ciclo de vida do desenvolvimento de software será distribuir seu aplicativo aos usuários, conforme mostrado na seção realçada do diagrama abaixo:

[![visão geral do ciclo de vida do desenvolvimento de software](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)

A Apple fornece as seguintes maneiras de distribuir um aplicativo tvOS, que tem suporte do Xamarin. tvOS:

1. [**App Store**](#Apple-TV-App-Store-Distribution)
2. [**Interno (Enterprise)** ](#In-House-Distribution) 
3. [**Ad Hoc**](#Ad_Hoc_Distribution) 

Todos esses cenários exigem que os aplicativos sejam provisionados usando o *perfil de provisionamento* apropriado. Perfis de provisionamento são arquivos que contêm informações de assinatura de código, bem como a identidade do aplicativo e o mecanismo de distribuição desejado. Para a distribuição fora da App Store, também há informações sobre em quais dispositivos o aplicativo pode ser implantado.

<a name="Apple-TV-App-Store-Distribution" />

## <a name="apple-tv-app-store-distribution"></a>Distribuição da loja de aplicativos da Apple TV

Essa é a principal maneira que os aplicativos tvOS são distribuídos para consumidores em dispositivos Apple TV. Todos os aplicativos enviados para a loja de aplicativos da Apple TV exigem aprovação da Apple.

Os aplicativos são enviados à App Store por meio de um portal chamado *iTunes Connect*. Consulte nosso guia [configurar seu aplicativo no iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) para obter informações sobre os seguintes tópicos:

- Gerenciando contratos, impostos e bancários.
- Criando um registro do iTunes Connect.
- Gerenciamento de capturas de tela e vídeos do aplicativo.
- Gerenciando nome do aplicativo, descrição, novidades, palavras-chave e URLs.
- Manter informações gerais do aplicativo.
- Manutenção de informações de Game Center.
- Manutenção das informações de revisão do aplicativo.
- Manutenção de informações de preços.
- Manutenção de informações de compra no aplicativo.
- Exibindo revisões de aplicativos.

Embora não seja escrito especificamente para tvOS, este documento fornece informações sobre como configurar e usar esse portal para preparar seu aplicativo para publicação na loja de aplicativos da Apple TV. Muitas das mesmas etapas são necessárias se você estiver configurando um aplicativo iOS ou tvOS.

Depois de concluir todas as etapas listadas acima, consulte nosso [aplicativo configure Your tvOS no iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) para adicionar as informações específicas do aplicativo tvOS que serão necessárias.

É importante observar que somente os desenvolvedores que pertencem ao **Programa de Desenvolvedores da Apple** têm acesso ao iTunes Connect. Membros do **Programa de Desenvolvedores Corporativos da Apple** não tem acesso.

Se você estiver tendo problemas para enviar seu aplicativo Xamarin. tvOS para a loja de aplicativos da Apple TV, consulte nosso guia de [solução de problemas](~/ios/tvos/troubleshooting.md) . Ele contém vários problemas conhecidos que você pode encontrar e como solucioná-los no Xamarin. tvOS.

Para obter mais informações, visite o guia [publicando na loja de aplicativos da Apple TV](~/ios/tvos/deploy-test/app-distribution/app-store-publishing.md) .

<a name="In-House-Distribution" />

## <a name="in-house-distribution"></a>Distribuição Interna

Às vezes chamada de *Distribuição Corporativa*, a distribuição interna permite que membros do **Programa de Desenvolvedores Corporativos da Apple** distribuam aplicativos internamente para outros membros da mesma organização. A distribuição interna tem as vantagens de não exigir uma análise da App Store e não ter nenhum limite de número de dispositivos nos quais um aplicativo pode ser instalado. No entanto, é importante observar que membros do **Programa de Desenvolvedor Corporativo da Apple** **não** têm acesso ao iTunes Connect e, portanto, o licenciado é responsável por distribuir o aplicativo.

Para obter mais informações sobre como obter a configuração e como distribuir seu aplicativo internamente, consulte o [Guia de distribuição interna](~/ios/deploy-test/app-distribution/in-house-distribution.md). Este documento é específico para iOS, mas as mesmas técnicas são usadas para aplicativos tvOS.

<a name="Ad_Hoc_Distribution"/>

## <a name="ad-hoc-distribution"></a>Distribuição Ad Hoc

Os aplicativos Xamarin. tvOS podem ser testados pelo usuário por meio da distribuição ad hoc, que está disponível no **programa de desenvolvedor da Apple**e no **programa Apple Developer Enterprise**e permite que até 100 dispositivos de TV da Apple sejam testados. O melhor caso de uso para a distribuição ad hoc é a distribuição dentro de sua empresa quando o iTunes Connect não é uma opção.

Para obter mais informações sobre como obter a configuração e como distribuir seu aplicativo internamente, consulte o [Guia de distribuição ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md). Novamente, este documento é específico do iOS, mas as mesmas técnicas são usadas para aplicativos tvOS.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo forneceu uma breve visão geral dos mecanismos de distribuição que estão disponíveis para aplicativos Xamarin. tvOS. Ele introduziu a loja de aplicativos da Apple TV, a implantação ad hoc e a interna e forneceu links para informações mais detalhadas.

## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
