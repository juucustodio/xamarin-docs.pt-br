---
title: Xamarin.FormsMapeada
description: O controle de mapa exibe um mapa e requer o Xamarin.Forms . Mapeia o pacote NuGet.
ms.prod: xamarin
ms.assetid: B669B5EE-D24C-4C69-93E1-2CA5CC9108B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2461ffa8168207e6a57fae005f752be48772a34a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139822"
---
# <a name="xamarinforms-map"></a>Xamarin.FormsMapeada

## <a name="initialization-and-configuration"></a>[Inicialização e configuração](setup.md)

O [ Xamarin.Forms . ](https://www.nuget.org/packages/Xamarin.Forms.Maps/)O pacote NuGet de mapas é necessário para usar a funcionalidade de mapas em um aplicativo. Além disso, o acesso ao local do usuário exige que as permissões de localização tenham sido concedidas ao aplicativo.

## <a name="map-control"></a>[Controle de Mapeamento](map.md)

O [`Map`](xref:Xamarin.Forms.Maps.Map) controle é uma exibição de plataforma cruzada para exibir e anotar mapas. Ele usa o controle de mapa nativo para cada plataforma, fornecendo uma experiência de mapas rápida e familiar para os usuários.

## <a name="position-and-distance"></a>[Posição e distância](position-distance.md)

A [`Position`](xref:Xamarin.Forms.Maps.Position) struct normalmente é usada ao posicionar um mapa e seus Pins, e a [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct que pode ser usada opcionalmente ao posicionar um mapa.

## <a name="pins"></a>[Marcadores](pins.md)

O [`Map`](xref:Xamarin.Forms.Maps.Map) controle permite que os locais sejam marcados com [`Pin`](xref:Xamarin.Forms.Maps.Pin) objetos. Um `Pin` é um marcador de mapa que abre uma janela de informações quando tocado.

## <a name="polygons-polylines-and-circles"></a>[Polígonos, polilinhas e círculos](polygons.md)

`Polygon`os `Polyline` elementos, e `Circle` permitem realçar áreas específicas em um mapa. Um `Polygon` é uma forma totalmente incluída que pode ter uma cor de preenchimento e de traçado. Um `Polyline` é uma linha que não coloca totalmente uma área. Um `Circle` realça uma área circular do mapa.

## <a name="geocoding"></a>[Geocodificação](geocoder.md)

A [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) classe é convertida entre endereços de cadeia de caracteres e coordenadas de latitude e longitude que são armazenadas em [`Position`](xref:Xamarin.Forms.Maps.Position) objetos.

## <a name="launch-the-native-map-app"></a>[Iniciar o aplicativo de mapas nativos](native-map-app.md)

O aplicativo de mapa nativo em cada plataforma pode ser iniciado de um Xamarin.Forms aplicativo pela Xamarin.Essentials `Launcher` classe.
