---
title: Como usar o Google Maps e Localização com Xamarin.Android
description: Este artigo discute como usar mapas e localização com Xamarin.Android. Ele cobre tudo, desde aproveitar o aplicativo de mapas embutidos até usar diretamente o Google Maps Android API V2.
ms.prod: xamarin
ms.assetid: 425E0ED2-5380-6EBE-7059-256B6E9128B8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: d877f415bb96024bb41edc2be9aec108ae248e88
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020040"
---
# <a name="how-to-use-google-maps-and-location-with-xamarinandroid"></a>Como usar o Google Maps e Localização com Xamarin.Android

_Este artigo discute como usar mapas e localização com Xamarin.Android. Ele cobre tudo, desde aproveitar o aplicativo de mapas embutidos até usar diretamente o Google Maps Android API V2._

## <a name="maps-overview"></a>Visão geral do Maps

As tecnologias de mapeamento são um complemento onipresente para dispositivos móveis. Computadores de mesa e laptops não tendem a ter consciência de localização incorporada. Por outro lado, os dispositivos móveis usam tais aplicativos para localizar dispositivos e exibir informações de localização mutáveis. O Android possui uma tecnologia poderosa e incorporada que exibe dados de localização em mapas usando hardware de localização que pode estar disponível no dispositivo. Este artigo abrange um espectro do que os aplicativos de mapas sob Xamarin.Android têm a oferecer, incluindo: 

- Usando o aplicativo mapas incorporados para adicionar rapidamente a funcionalidade de mapeamento.
- Trabalhando com a API do Maps para controlar a exibição de um mapa.
- Usando uma variedade de técnicas para adicionar sobreposições gráficas.

Os tópicos desta seção abrangem uma ampla gama de recursos de mapeamento.
Primeiro, eles explicam como aproveitar o aplicativo de mapas incorporados do Android e como exibir uma visão panorâmica de um local. Em seguida, eles discutem como usar a API do Maps para incorporar recursos de mapeamento diretamente dentro de um aplicativo, cobrindo tanto como controlar a posição e a exibição de um mapa, quanto como adicionar sobreposições gráficas.

## <a name="related-links"></a>Links relacionados

- [MapsAndLocationDemo_v3 (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/mapsandlocationdemo-v3)
- [Ciclo de vida da atividade](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Obter uma chave de API do Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [Lista de intenções: Invocando aplicativos do Google em dispositivos Android](https://developer.android.com/guide/appendix/g-app-intents.html)
- [Localização e Mapas](https://developer.android.com/guide/topics/location/index.html)
