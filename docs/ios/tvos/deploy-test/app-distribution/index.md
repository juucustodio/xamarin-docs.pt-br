---
title: Visão geral da distribuição de aplicativo do tvOS
description: Este documento fornece uma visão geral das técnicas de distribuição que estão disponíveis para o aplicativo tvos e serve como um ponteiro para documentos mais detalhados sobre o tópico.
ms.prod: xamarin
ms.assetid: D5E0F446-C083-4E21-9788-FC84D32D00C4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 0a40d50d02008439e81d5db19bcda0647203e2da
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110694"
---
# <a name="tvos-app-distribution-overview"></a>Visão geral da distribuição de aplicativo do tvOS

_Este documento fornece uma visão geral das técnicas de distribuição que estão disponíveis para o aplicativo tvos e serve como um ponteiro para documentos mais detalhados sobre o tópico._


Depois que seu aplicativo tvos tiver sido desenvolvido, a próxima etapa no ciclo de vida de desenvolvimento de software é distribuir seu aplicativo aos usuários, conforme mostrado na seção destacada do diagrama abaixo:


[![A visão geral do ciclo de vida de desenvolvimento de software](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)


A Apple fornece as seguintes maneiras de distribuir um aplicativo tvOS, que são compatíveis com o xamarin. tvos:

1. [**App Store**](#Apple-TV-App-Store-Distribution)
2. [**Interno (Enterprise)**](#In-House-Distribution) 
2. [**Ad Hoc**](#Ad_Hoc_Distribution) 

Todos esses cenários exigem que os aplicativos sejam provisionados usando o *perfil de provisionamento* apropriado. Perfis de provisionamento são arquivos que contêm informações de assinatura de código, bem como a identidade do aplicativo e o mecanismo de distribuição desejado. Para a distribuição fora da App Store, também há informações sobre em quais dispositivos o aplicativo pode ser implantado.

<a name="Apple-TV-App-Store-Distribution" />

## <a name="apple-tv-app-store-distribution"></a>Distribuição de Apple TV App Store

Essa é a maneira principal que tvOS aplicativos sejam distribuídos para consumidores em dispositivos Apple TV. Todos os aplicativos enviados para a Store de aplicativo da Apple TV exigem aprovação da Apple.

Os aplicativos são enviados à App Store por meio de um portal chamado *iTunes Connect*. Consulte nosso [configurar seu aplicativo no iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) guia para obter informações sobre os tópicos a seguir:

- Gerenciando contratos, impostos e transações bancárias.
- Criando do iTunes Connect registro.
- Gerenciamento de aplicativo vídeos e capturas de tela.
- Gerenciando o nome do aplicativo, descrição, novidades, URLs e palavras-chave.
- Manter informações gerais do aplicativo.
- Manutenção de informações do Game Center.
- Manutenção de informações de revisão do aplicativo.
- Mantendo informações de preço.
- Manutenção de informações de compra no aplicativo.
- Exibindo análises do aplicativo.

Embora não escrito especificamente para tvOS, este documento fornece informações sobre como configurar e usar este portal para preparar o aplicativo para a publicação de Store de aplicativo da Apple TV. Muitas das mesmas etapas são necessárias se você estiver configurando um aplicativo iOS ou tvOS.

Depois de concluir todas as etapas listadas acima, consulte nosso [configurar seu aplicativo tvOS no iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) para adicionar as informações específicas do aplicativo tvOS que serão necessárias.

É importante observar que somente os desenvolvedores que pertencem ao **Programa de Desenvolvedores da Apple** têm acesso ao iTunes Connect. Membros do **Programa de Desenvolvedores Corporativos da Apple** não tem acesso.

Se você estiver tendo problemas enviando seu aplicativo tvos para a Store de aplicativo da Apple TV, consulte nosso [solução de problemas](~/ios/tvos/troubleshooting.md) guia. Ele contém vários problemas conhecidos que podem ocorrer e como resolvê-los no xamarin. tvos.

Para obter mais informações, visite o [de publicação para a Store de aplicativo da Apple TV](~/ios/tvos/deploy-test/app-distribution/app-store-publishing.md) guia.

<a name="In-House-Distribution" />

## <a name="in-house-distribution"></a>Distribuição Interna

Às vezes chamada de *Distribuição Corporativa*, a distribuição interna permite que membros do **Programa de Desenvolvedores Corporativos da Apple** distribuam aplicativos internamente para outros membros da mesma organização. A distribuição interna tem as vantagens de não exigir uma análise da App Store e não ter nenhum limite de número de dispositivos nos quais um aplicativo pode ser instalado. No entanto, é importante observar que membros do **Programa de Desenvolvedor Corporativo da Apple** **não** têm acesso ao iTunes Connect e, portanto, o licenciado é responsável por distribuir o aplicativo.

Para obter mais informações sobre como configurar e distribuir seu aplicativo internamente, consulte o [guia de distribuição interna](~/ios/deploy-test/app-distribution/in-house-distribution.md). Este documento é específico do iOS, mas as mesmas técnicas são usadas para aplicativos de tvOS.

<a name="Ad_Hoc_Distribution"/>

## <a name="ad-hoc-distribution"></a>Distribuição Ad Hoc

Aplicativos xamarin. tvos podem ser testados por usuário por meio da distribuição ad-hoc, que está disponível em ambos o **programa de desenvolvedor da Apple**e o **Apple Developer Enterprise Program**e permite até 100 dispositivos Apple TV a ser testado. O melhor caso de uso para distribuição ad hoc é distribuição dentro de sua empresa quando iTunes Connect não é uma opção.

Para obter mais informações sobre como configurar e distribuir seu aplicativo internamente, consulte o [guia de distribuição Ad Hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md). Novamente, este documento é específico do iOS, mas as mesmas técnicas são usadas para aplicativos de tvOS.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo forneceu uma visão geral dos mecanismos de distribuição que estão disponíveis para aplicativos xamarin. tvos. Apresentou o Store de aplicativo do Apple TV, Ad Hoc e interna, implantação e fornece links para informações mais detalhadas.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
