---
title: Visão geral da distribuição de aplicativos Xamarin.iOS
description: Este documento fornece uma visão geral das técnicas de distribuição disponíveis para aplicativos Xamarin.iOS e funciona como um ponteiro para documentos mais detalhados sobre o tópico.
ms.prod: xamarin
ms.assetid: 341D36DB-BB07-FA94-BCC9-5F8C0B18C179
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 20126849027f735e9ecd3599c290b4e7a57f837e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75886574"
---
# <a name="xamarinios-app-distribution-overview"></a>Visão geral da distribuição de aplicativos Xamarin.iOS

_Este documento fornece uma visão geral das técnicas de distribuição disponíveis para aplicativos Xamarin.iOS e funciona como um ponteiro para documentos mais detalhados sobre o tópico._

Depois que um aplicativo Xamarin.iOS for desenvolvido, a próxima etapa no ciclo de vida de desenvolvimento do software é distribuir o aplicativo para os usuários, conforme mostrado na seção destacada do diagrama abaixo:

[![Depois que o aplicativo para iOS for desenvolvido, o próximo passo é distribuir o aplicativo aos usuários, como mostrado na seção destacada deste diagrama](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)

A Apple fornece as seguintes maneiras de distribuir um aplicativo para iOS:

- [**App Store**](#app-store-distribution)
- [**Internamente (empresa)**](#in-house-distribution)
- [**Ad hoc**](#ad-hoc-distribution)
- [**Aplicativos personalizados para negócios**](#custom-apps-for-business)

Todos esses cenários exigem que os aplicativos sejam provisionados usando o *perfil de provisionamento* apropriado. Perfis de provisionamento são arquivos que contêm informações de assinatura de código, bem como a identidade do aplicativo e o mecanismo de distribuição desejado. Para a distribuição fora da App Store, também há informações sobre em quais dispositivos o aplicativo pode ser implantado.

## <a name="app-store-distribution"></a>Distribuição na App Store

> [!IMPORTANT]
> A Apple [indicou](https://developer.apple.com/ios/submit/) que, a partir de março de 2019, todos os aplicativos e atualizações enviados à App Store deverão ter sido criados com o SDK do iOS 12.1 ou posterior, incluído no Xcode 10.1 ou posterior.
> Aplicativos também devem dar suporte aos tamanhos de tela do iPhone XS e de 12,9 pol. do iPad Pro.

Essa é a maneira principal que aplicativos iOS são distribuídos para consumidores em dispositivos iOS. Todos os aplicativos enviados à App Store exigem aprovação da Apple.

Os aplicativos são enviados à App Store por meio de um portal chamado *iTunes Connect*. O guia [Configurar seu Aplicativo no iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) fornece mais informações sobre como configurar e usar este portal para preparar um aplicativo Xamarin.iOS para publicação na App Store.

É importante observar que somente os desenvolvedores que pertencem ao **Programa de Desenvolvedores da Apple** têm acesso ao iTunes Connect. Membros do **Programa de Desenvolvedores Corporativos da Apple** não tem acesso.

Para obter mais informações, acesse o guia de [Distribuição da App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md).

## <a name="in-house-distribution"></a>Distribuição interna

Às vezes chamada de *Distribuição Corporativa*, a distribuição interna permite que membros do **Programa de Desenvolvedores Corporativos da Apple** distribuam aplicativos internamente para outros membros da mesma organização. A distribuição interna tem as vantagens de não exigir uma análise da App Store e não ter nenhum limite de número de dispositivos nos quais um aplicativo pode ser instalado. No entanto, é importante observar que membros do **Programa de Desenvolvedor Corporativo da Apple****não** têm acesso ao iTunes Connect e, portanto, o licenciado é responsável por distribuir o aplicativo.

Para obter mais informações sobre como obter configuração e como distribuir um aplicativo interno, consulte o [guia de distribuição interno](~/ios/deploy-test/app-distribution/in-house-distribution.md).

## <a name="ad-hoc-distribution"></a>Distribuição ad hoc

Aplicativos Xamarin.iOS podem ser testados pelo usuário por meio da distribuição ad-hoc, que está disponível no **Programa de Desenvolvedores da Apple** e pelo **Programa de Desenvolvedor Corporativo da Apple**, permitindo que até 100 dispositivos iOS sejam testados. O melhor caso de uso para a distribuição ad hoc é distribuição dentro de uma empresa quando iTunes Connect não for uma opção.

Para obter mais informações sobre como obter a configuração e como distribuir um aplicativo internamente, consulte o [guia de distribuição Ad-hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md).

## <a name="custom-apps-for-business"></a>Aplicativos personalizados para negócios

A Apple permite [a distribuição personalizada](https://developer.apple.com/business/custom-apps/) de aplicativos para empresas e educação. Revise o [Guia do Usuário do Gerente de Negócios da Apple](https://support.apple.com/guide/apple-business-manager/welcome/web) para obter informações.

## <a name="related-links"></a>Links relacionados

- [Distribuição na App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Configurando um aplicativo no iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Publicando na App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Distribuição interna](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribuição ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [O arquivo iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Suporte para formato IPA](~/ios/deploy-test/app-distribution/ipa-support.md)
- [Solução de problemas](~/ios/deploy-test/troubleshooting.md)
