---
title: Como usar o Google Maps e o local com o Xamarin. Android
description: Este artigo discute como usar mapas e localização com o Xamarin. Android. Ele aborda tudo, desde o aproveitamento do aplicativo de mapas interno até o uso direto da API do Google Maps para Android v2.
ms.prod: xamarin
ms.assetid: 425E0ED2-5380-6EBE-7059-256B6E9128B8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 11feb8e3e10e12bfb2c647182ec4ba953ab34e24
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456893"
---
# <a name="how-to-use-google-maps-and-location-with-xamarinandroid"></a>Como usar o Google Maps e o local com o Xamarin. Android

_Este artigo discute como usar mapas e localização com o Xamarin. Android. Ele aborda tudo, desde o aproveitamento do aplicativo de mapas interno até o uso direto da API do Google Maps para Android v2._

## <a name="maps-overview"></a>Visão geral de mapas

As tecnologias de mapeamento são um complemento onipresente aos dispositivos móveis. Computadores desktop e laptops não tendem a ter o reconhecimento de local interno. Por outro lado, os dispositivos móveis usam esses aplicativos para localizar dispositivos e para exibir informações de localização em alteração. O Android tem uma tecnologia integrada e avançada que exibe dados de localização em mapas usando o hardware de local que pode estar disponível no dispositivo. Este artigo aborda um espectro do que os aplicativos de mapas em Xamarin. Android têm a oferecer, incluindo: 

- Usando o aplicativo de mapas interno para adicionar rapidamente a funcionalidade de mapeamento.
- Trabalhar com a API Maps para controlar a exibição de um mapa.
- Usando uma variedade de técnicas para adicionar sobreposições gráficas.

Os tópicos desta seção abrangem uma ampla gama de recursos de mapeamento.
Primeiro, eles explicam como aproveitar o aplicativo de mapas interno do Android e como exibir uma visão panorâmica de um local. Em seguida, eles abordam como usar a API Maps para incorporar recursos de mapeamento diretamente dentro de um aplicativo, cobrindo como controlar a posição e a exibição de um mapa, além de como adicionar sobreposições gráficas.

## <a name="related-links"></a>Links Relacionados

- [MapsAndLocationDemo_v3 (exemplo)](/samples/xamarin/monodroid-samples/mapsandlocationdemo-v3)
- [Ciclo de vida de atividade](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Obter uma chave de API do Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [Lista de tentativas: invocando aplicativos do Google em dispositivos Android](https://developer.android.com/guide/appendix/g-app-intents.html)
- [Local e mapas](https://developer.android.com/guide/topics/location/index.html)