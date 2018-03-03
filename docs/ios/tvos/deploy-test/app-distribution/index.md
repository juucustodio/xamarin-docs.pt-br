---
title: "Visão geral da distribuição de aplicativos"
description: "Este documento fornece uma visão geral das técnicas de distribuição que estão disponíveis para o aplicativo Xamarin.tvOS e serve como um ponteiro para os documentos mais detalhados sobre o tópico."
ms.topic: article
ms.prod: xamarin
ms.assetid: D5E0F446-C083-4E21-9788-FC84D32D00C4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: c0cfe437b03a1f0dea05a506b1dfce62a4658bb4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="app-distribution-overview"></a>Visão geral da distribuição de aplicativos

_Este documento fornece uma visão geral das técnicas de distribuição que estão disponíveis para o aplicativo Xamarin.tvOS e serve como um ponteiro para os documentos mais detalhados sobre o tópico._


Depois que seu aplicativo Xamarin.tvOS foi desenvolvido, é a próxima etapa no ciclo de vida de desenvolvimento de software distribuir seu aplicativo para usuários, conforme mostrado na seção realçada o diagrama a seguir:


[![A visão geral do ciclo de vida de desenvolvimento de software](images/publishingdiagram.png)](images/publishingdiagram.png)


Apple fornece as seguintes maneiras para distribuir um aplicativo tvOS, que são suportados pelo Xamarin.tvOS:

1. [**App Store**](#Apple-TV-App-Store-Distribution)
2. [**Interno (Enterprise)**](#In-House-Distribution) 
2. [**Ad Hoc**](#Ad_Hoc_Distribution) 

Todos esses cenários exigem que os aplicativos sejam provisionados usando o *perfil de provisionamento* apropriado. Perfis de provisionamento são arquivos que contêm informações de assinatura de código, bem como a identidade do aplicativo e o mecanismo de distribuição desejado. Para a distribuição fora da App Store, também há informações sobre em quais dispositivos o aplicativo pode ser implantado.

<a name="Apple-TV-App-Store-Distribution" />

## <a name="apple-tv-app-store-distribution"></a>Distribuição de loja de aplicativos do Apple TV

Essa é a maneira principal que tvOS aplicativos são distribuídos para os consumidores em dispositivos da Apple TV. Todos os aplicativos enviados à loja de aplicativos Apple TV exigirem aprovação pela Apple.

Os aplicativos são enviados à App Store por meio de um portal chamado *iTunes Connect*. Confira nosso [configurar seu aplicativo na iTunes conectar](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) guia para obter informações sobre os seguintes tópicos:

- Gerenciando contratos, imposto e transações bancárias.
- Criando um iTunes conectar registro.
- Gerenciamento de aplicativo vídeos e capturas de tela.
- Gerenciando o nome do aplicativo, descrição, o que há de novo, palavras-chave e URLs.
- Manutenção de informações do aplicativo geral.
- Manter informações de Game Center.
- Manter informações de revisão do aplicativo.
- Manter informações de preços.
- Manter informações de compra no aplicativo.
- Exibir as revisões de aplicativos.

Enquanto não criado especificamente para tvOS, este documento fornece informações sobre como configurar e usar este portal para preparar o aplicativo para a publicação na loja de aplicativos Apple TV. Muitas das mesmas etapas são necessárias se você estiver configurando um aplicativo iOS ou tvOS.

Depois de concluir todas as etapas listadas acima, consulte a nossa [configurar seu aplicativo tvOS no iTunes conectar](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) para adicionar as informações específicas de aplicativo de tvOS que serão necessárias.

É importante observar que somente os desenvolvedores que pertencem ao **Programa de Desenvolvedores da Apple** têm acesso ao iTunes Connect. Membros do **Programa de Desenvolvedores Corporativos da Apple** não tem acesso.

Se você estiver tendo problemas para enviar seu aplicativo Xamarin.tvOS a loja de aplicativos Apple TV, consulte nosso [solução de problemas](~/ios/tvos/troubleshooting.md) guia. Ele contém vários problemas conhecidos que podem ocorrer e como resolvê-los no Xamarin.tvOS.

Para obter mais informações, visite o [de publicação para a loja de aplicativos Apple TV](~/ios/tvos/deploy-test/app-distribution/app-store-publishing.md) guia.

<a name="In-House-Distribution" />

## <a name="in-house-distribution"></a>Distribuição Interna

Às vezes chamada de *Distribuição Corporativa*, a distribuição interna permite que membros do **Programa de Desenvolvedores Corporativos da Apple** distribuam aplicativos internamente para outros membros da mesma organização. A distribuição interna tem as vantagens de não exigir uma análise da App Store e não ter nenhum limite de número de dispositivos nos quais um aplicativo pode ser instalado. No entanto, é importante observar que membros do **Programa de Desenvolvedor Corporativo da Apple** **não** têm acesso ao iTunes Connect e, portanto, o licenciado é responsável por distribuir o aplicativo.

Para obter mais informações sobre a obtenção de configuração e como distribuir seu aplicativo internamente, consulte o [guia distribuição internamente](~/ios/deploy-test/app-distribution/in-house-distribution.md). Este documento é específico do iOS, mas as mesmas técnicas são usadas para tvOS aplicativos.

<a name="Ad-Hoc-Distribution" />

## <a name="ad-hoc-distribution"></a>Distribuição Ad Hoc

Aplicativos Xamarin.tvOS podem ser testado por usuário por meio da distribuição ad hoc, que está disponível em ambos os o **programa de desenvolvedor da Apple**e o **Apple Developer Enterprise programa**e permite até 100 dispositivos Apple TV a ser testada. O melhor caso de uso para distribuição ad hoc é distribuição dentro da sua empresa ao iTunes conectar não é uma opção.

Para obter mais informações sobre como obter a configuração e a distribuir o aplicativo internamente, consulte o [guia distribuição Ad Hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md). Novamente, este documento é específico do iOS, mas as mesmas técnicas são usadas para tvOS aplicativos.

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo forneceu uma visão geral dos mecanismos de distribuição que estão disponíveis para aplicativos Xamarin.tvOS. Ele introduzidos loja de aplicativos Apple TV, Ad Hoc e internamente, implantação e fornecidos links para informações mais detalhadas.



## <a name="related-links"></a>Links relacionados

- [Exemplos de tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS guias de Interface Humana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
