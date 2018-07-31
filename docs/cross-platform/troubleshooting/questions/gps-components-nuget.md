---
title: Unificar o Google Play Services, componentes e NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5D962EB4-2CB3-4B7D-9D77-889DEACDAE02
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 3f5c5f75ae1c7a44537afa59ff4a15d54b1df50b
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351477"
---
# <a name="unifying-google-play-services-components-and-nuget"></a>Unificar o Google Play Services, componentes e NuGet

## <a name="history"></a>Histórico

Costumava ser de vários componentes do Google Play Services e pacotes do NuGet:

-   Serviços do Google Play (Froyo)
-   Serviços do Google Play (Gingerbread)
-   (ICS) de serviços do Google Play
-   Serviços do Google Play (JellyBean)
-   Serviços do Google Play (KitKat)

. Jar do programa de navios dois do Google, na verdade, somente arquivos do Google Play Services:

-   `google-play-services-froyo.jar`
-   `google-play-services.jar`

A discrepância existia porque nossas ferramentas corretamente não dizia `aapt.exe` qual nível de API máxima de recursos foi a ser usado para um determinado aplicativo. Isso significava, recebemos erros de compilação se, tentamos usando a associação do Google Play Services (KitKat) em um nível mais baixo de API como Gingerbread.

## <a name="unifying-google-play-services"></a>Unificando o Google Play Services

Em versões mais recentes do xamarin. Android, podemos dizer agora `aapt.exe` qual versão máxima de recursos usar, para que esse problema desaparece para nós.

Isso significa que há nenhuma razão para ter pacotes separados para Gingerbread/ICS/JellyBean/KitKat (no entanto ainda precisamos uma associação separada para Froyo, pois ele é um arquivo. jar diferentes completamente).

Para facilitar as coisas para os desenvolvedores, estamos agora já unified nossos componentes e NuGet pacotes em dois:

-   Google Play Services (Froyo) (associa `google-play-services-froyo.jar`)
-   Serviços do Google Play (associa `google-play-services.jar`)

### <a name="which-one-should-be-used"></a>Qual deles deve ser usado?

Em quase todos os casos, o Google Play Services devem ser usado. O único motivo para usar o pacote (Froyo) é se você estiver direcionando ativamente Froyo. A única razão pela qual que esse arquivo. jar separado existe do Google é como Froyo está em um pequeno percentual de dispositivos, eles mesmos optou por parar que dão suporte a ele, portanto, esse arquivo. jar é um instantâneo congelado, sem suporte do Google Play Services.

### <a name="note-about-gingerbread"></a>Observação sobre Gingerbread

Gingerbread não tem fragmento suporte por padrão e, por isso, algumas das classes na associação não poderá ser usadas em um aplicativo em tempo de execução em um dispositivo Gingerbread. Classes como `MapFragment` não funcionará em Gingerbread e suas variantes de suporte devem ser usado em vez disso, `SupportMapFragment`. Cabe ao desenvolvedor de saber qual deles usar. Essa incompatibilidade é indicada pelo Google em sua documentação do Google Play Services.

### <a name="what-happens-to-the-old-componentsnugets"></a>O que acontece com o antigo componentes/do NuGet?

Uma vez que eles não são mais necessários, temos desabilitado/Delisted os seguintes componentes/NuGets:

-   Serviços do Google Play (Gingerbread)
-   Serviços do Google Play (JellyBean)
-   Serviços do Google Play (KitKat)

Existente _Google Play Services ICS ()_ Nuget do componente foi renomeado para _Google Play Services_ e será mantido atualizado no futuro. Todos os projetos de referência a um dos pacotes desabilitado/Delisted devem ser atualizados para usar essa associação.

Os componentes desabilitados ainda existem e devem ser restauráveis para projetos que ainda são referenciados, para evitar a interrupção-los. Da mesma forma os pacotes do NuGet delisted ainda existem e podem ser restaurados. Eles não serão atualizados no futuro.
