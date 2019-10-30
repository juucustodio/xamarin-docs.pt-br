---
title: Unificar os componentes do Google Play Services e o NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5D962EB4-2CB3-4B7D-9D77-889DEACDAE02
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: 100ef7ffd7e05db0ed8b2af6b9990fc3a0ac1fa9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014143"
---
# <a name="unifying-google-play-services-components-and-nuget"></a>Unificar os componentes do Google Play Services e o NuGet

## <a name="history"></a>Histórico

Você costumava ter vários componentes Google Play Services e pacotes NuGet:

- Google Play Services (Froyo)
- Google Play Services (Gingerbread)
- Google Play Services (ICS)
- Google Play Services (JellyBean)
- Google Play Services (KitKat)

Na verdade, o Google envia apenas dois arquivos. jar para Google Play Services:

- `google-play-services-froyo.jar`
- `google-play-services.jar`

A discrepância existia porque nossas ferramentas não informaram corretamente `aapt.exe` qual era o nível máximo de API de recurso a ser usado para um determinado aplicativo. Isso significa que recebemos erros de compilação se tentarmos usar a associação de Google Play Services (KitKat) em um nível de API inferior, como Gingerbread.

## <a name="unifying-google-play-services"></a>Unificando Google Play Services

Em versões mais recentes do Xamarin. Android, agora dizem `aapt.exe` qual versão de recurso máxima usar, portanto, esse problema desaparece para nós.

Isso significa que não há nenhum motivo real para ter pacotes separados para Gingerbread/ICS/JellyBean/KitKat (no entanto, ainda precisamos de uma associação separada para Froyo, uma vez que é um arquivo. jar diferente).

Para facilitar as coisas para os desenvolvedores, agora nós unificamos os nossos componentes e pacotes NuGet em duas:

- Google Play Services (Froyo) (associa `google-play-services-froyo.jar`)
- Google Play Services (associa `google-play-services.jar`)

### <a name="which-one-should-be-used"></a>Qual delas deve ser usada?

Em quase todos os casos, Google Play Services deve ser usado. O único motivo para usar o pacote (Froyo) é se você estiver pretendendo ativamente a Froyo. O único motivo pelo qual esse arquivo. jar separado existe no Google é que o Froyo está em um pequeno percentual de dispositivos, eles em si decidiram parar de dar suporte a ele, portanto, esse arquivo. jar é um instantâneo congelado e sem suporte de Google Play Services.

### <a name="note-about-gingerbread"></a>Observação sobre Gingerbread

O Gingerbread não tem suporte de fragmento por padrão e, por isso, algumas das classes na associação não serão utilizáveis em um aplicativo em tempo de execução em um dispositivo Gingerbread. Classes como `MapFragment` não funcionarão em Gingerbread, e sua variante de suporte deverá ser usada em vez `SupportMapFragment`. Cabe ao desenvolvedor saber o que usar. Essa incompatibilidade é observada pelo Google em sua documentação Google Play Services.

### <a name="what-happens-to-the-old-componentsnugets"></a>O que acontece com os componentes antigos/NuGet?

Como eles não são mais necessários, desabilitamos/deslistamos os seguintes componentes/NuGets:

- Google Play Services (Gingerbread)
- Google Play Services (JellyBean)
- Google Play Services (KitKat)

O componente/NuGet existente do _Google Play Services (ICS)_ foi renomeado para _Google Play Services_ e será mantido atualizado no futuro. Todos os projetos que fazem referência a um dos pacotes desabilitados/deslistados devem ser atualizados para usar este.

Os componentes desabilitados ainda existem e devem ser restauráveis para projetos nos quais eles ainda são referenciados, para evitar dividi-los. Da mesma forma, os pacotes NuGet deslistados ainda existem e podem ser restaurados. Eles não serão atualizados no futuro.
