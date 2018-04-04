---
title: Unificar o Google Play e serviços de componentes do NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5D962EB4-2CB3-4B7D-9D77-889DEACDAE02
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: e0ba5ee9417917b834ab060a94f72d1f071b4912
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="unifying-google-play-services-components-and-nuget"></a>Unificar o Google Play e serviços de componentes do NuGet

### <a name="history"></a>Histórico

Havia vários componentes de serviços do Google reproduzir e pacotes do NuGet:

-   Serviços do Google Play (Froyo)
-   Serviços do Google Play (biscoito)
-   (ICS) de serviços do Google Play
-   Serviços do Google Play (JellyBean)
-   Serviços do Google Play (KitKat)

Arquivos do Google, na verdade, apenas dois é fornecido. jar de Google executar serviços:

-   `google-play-services-froyo.jar`
-   `google-play-services.jar`

A discrepância existia porque nossas ferramentas corretamente não informam `aapt.exe` qual foi o recurso máximo nível de API a ser usado para um determinado aplicativo. Isso significa, recebemos erros de compilação se tentamos usando a associação de serviços do Google reproduzir (KitKat) em um nível mais baixo de API como biscoito.

### <a name="unifying-google-play-services"></a>Unificar os serviços do Google Play

Em versões mais recentes do xamarin, agora Informamos `aapt.exe` qual versão máxima de recursos para usar, portanto, esse problema desaparece para nós.

Isso significa que há nenhuma razão que pacotes separados para biscoito/ICS/JellyBean/KitKat (no entanto ainda precisamos uma associação separada para Froyo porque é um arquivo. jar diferentes completamente).

Para facilitar as coisas para desenvolvedores, agora você unificado nossos componentes e NuGet pacotes em dois:

-   (Froyo) de serviços do Google Play (associa `google-play-services-froyo.jar`)
-   Serviços do Google Play (associa `google-play-services.jar`)

### <a name="which-one-should-be-used"></a>Qual deve ser usado?

Em quase todos os casos, o Google executar serviços devem ser usados. A única razão para usar o pacote (Froyo) é se você estiver direcionando ativamente Froyo. O único motivo pelo qual que este arquivo. jar separado existe do Google como Froyo está em um pequeno percentual de dispositivos, eles se optar por não dá suporte, de modo que esse arquivo. jar é um instantâneo congelado, sem suporte do Google executar serviços.

### <a name="note-about-gingerbread"></a>Observação sobre biscoito

Biscoito não tem fragmento suporte por padrão e, por isso, algumas das classes na associação não será utilizáveis em um aplicativo em tempo de execução em um dispositivo de biscoito. Classes como `MapFragment` não funcionará em biscoito e seu variante de suporte deve ser usado em vez disso, `SupportMapFragment`. Cabe ao desenvolvedor sabe que deve ser usada. Essa incompatibilidade é indicada pelo Google na sua documentação de serviços de reprodução do Google.

### <a name="what-happens-to-the-old-componentsnugets"></a>O que acontece com o antigo componentes/do NuGet?

Desde que eles não são mais necessários, temos desabilitado/Delisted os seguintes componentes/NuGets:

-   Serviços do Google Play (biscoito)
-   Serviços do Google Play (JellyBean)
-   Serviços do Google Play (KitKat)

Existente _Google executar serviços (ICS)_ componente/Nuget foi renomeado para _Google executar serviços_ e será mantido atualizado no futuro. Todos os projetos fizer referência a um dos pacotes desabilitado/Delisted devem ser atualizados para usar este.

Os componentes desabilitados ainda existem e devem ser recuperáveis para projetos que eles ainda são referenciados, para evitar a interrupção. Da mesma forma os pacotes do NuGet delisted ainda existem e podem ser restaurados. Eles não serão atualizados no futuro.
