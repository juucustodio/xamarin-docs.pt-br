---
title: Publicando um aplicativo
ms.prod: xamarin
ms.assetid: 51E19000-040A-2B74-C462-EC57C617085C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 3062afda82be2b45146687b9d77210a42f8f55fa
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487952"
---
# <a name="publishing-an-application"></a>Publicando um aplicativo

Quando um bom aplicativo é criado, as pessoas querem usá-lo. Esta seção aborda as etapas envolvidas com a distribuição pública de um aplicativo criado com o Xamarin.Android por meio de canais, como email, um servidor Web particular, Google Play ou a Amazon App Store para Android.

## <a name="overview"></a>Visão geral

A etapa final no desenvolvimento de um aplicativo Xamarin.Android é publicar o aplicativo. A publicação é o processo de compilação de um aplicativo Xamarin.Android para que ele esteja pronto para que os usuários instalem em seus dispositivos e envolve duas tarefas essenciais:

- **Preparação para publicação** &ndash; Uma versão de lançamento do aplicativo é criada que pode ser implantada em dispositivos Android (consulte [Preparar um aplicativo para lançamento](~/android/deploy-test/release-prep/index.md) para obter mais informações sobre a preparação do lançamento).

- **Distribuição** &ndash; A versão de um aplicativo é disponibilizada por meio de um ou mais dos vários canais de distribuição.

O diagrama a seguir ilustra as etapas envolvidas na publicação de um aplicativo Xamarin.Android:

[![Construir e implantar fluxograma](images/build-and-deploy-steps.png)](images/build-and-deploy-steps.png#lightbox)

Como pode ser visto no diagrama acima, a preparação é a mesma, independentemente do método de distribuição usado. Há várias maneiras que um aplicativo Android pode ser liberado para usuários:

- **Por meio de um site** &ndash; Um aplicativo Xamarin.Android pode ser disponibilidade para download em um site, do qual os usuários podem, em seguida, instalar o aplicativo ao clicar em um link.
- **Por email** &ndash; É possível que os usuários instalem um aplicativo Xamarin.Android pelo email. O aplicativo será instalado quando o anexo for aberto com um dispositivo Android.
- **Por meio de um mercado** &ndash; Há vários mercados de aplicativos que existem para distribuição, como [Google Play](https://play.google.com/) ou [Amazon App Store for Android](https://www.amazon.com/mobile-apps/b?ie=UTF8&node=2350149011).

Usar um marketplace estabelecido é a maneira mais comum para publicar um aplicativo, pois ele fornece um alcance mais amplo do mercado e maior controle sobre a distribuição. No entanto, a publicação de um aplicativo por meio de um marketplace requer esforço adicional.

Vários canais podem distribuir um aplicativo Xamarin.Android simultaneamente. Por exemplo, um aplicativo pode ser publicado no Google Play, Amazon App Store para o Android e também ser baixado de um servidor Web.

Os outros dois métodos de distribuição (download ou email) são mais úteis para um subconjunto controlado de usuários, como um ambiente corporativo ou um aplicativo destina-se apenas um conjunto bem específico ou pequeno de usuários.
Distribuição de email e de servidor também são modelos mais simples de publicação, exigindo menos preparativos para publicar um aplicativo.

O Programa de Distribuição de Aplicativos Móveis da Amazon permite que desenvolvedores de aplicativos móveis distribuam e vendam seus aplicativos no Amazon. Os usuários podem descobrir e comprar aplicativos em seus dispositivos com Android ao usar o aplicativo Amazon App Store. Uma captura de tela do Amazon App Store em execução em um dispositivo Android aparece abaixo:

O Google Play é indiscutivelmente o marketplace mais abrangente e popular para aplicativos Android. O Google Play permite aos usuários descobrir, baixar, classificar e comprar aplicativos ao clicar em um único ícone no dispositivo ou no computador. O Google Play também fornece ferramentas para auxiliar na análise de vendas e tendências do mercado e para controlar quais dispositivos e usuários podem baixar um aplicativo. Uma captura de tela do Google Play em execução em um dispositivo Android aparece abaixo:

[![Captura de tela do Google Play](images/google-play-app.png)](images/google-play-app.png#lightbox)

Esta seção mostrou como carregar o aplicativo em lojas como o Google Play, junto com o material promocional apropriado. Arquivos de expansão APK foram explicados, fornecendo uma visão geral conceitual sobre o que são e como funcionam. Os serviços de licenciamento do Google também foram descritos. Finalmente, meios alternativos de distribuição foram apresentados, incluindo o uso de um servidor Web HTTP, distribuição de email simples e a Amazon App Store para Android.

## <a name="related-links"></a>Links relacionados

- [HelloWorldPublishing (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/helloworldpublishing)
- [Processo de build](~/android/deploy-test/building-apps/build-process.md)
- [Vinculação](~/android/deploy-test/linker.md)
- [Obtenção de uma chave de API do Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [Implantar via Visual Studio App Center](https://docs.microsoft.com/appcenter/distribution/stores/googleplay)
- [Assinatura de aplicativos](https://source.android.com/security/apksigning/)
- [Publicando no Google Play](https://developer.android.com/distribute/googleplay/publish/index.html)
- [Licenciamento de aplicativos do Google](https://developer.android.com/guide/google/play/licensing/index.html)
- [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary)
- [Portal de distribuição de aplicativo móvel](https://developer.amazon.com/welcome.html)
- [Perguntas frequentes da distribuição de aplicativo móvel da Amazon](https://developer.amazon.com/help/faq.html)
