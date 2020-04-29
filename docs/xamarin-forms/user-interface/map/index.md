---
title: Mapa do Xamarin. Forms
description: O controle de mapa exibe um mapa e requer o pacote NuGet Xamarin. Forms. Maps.
ms.prod: xamarin
ms.assetid: B669B5EE-D24C-4C69-93E1-2CA5CC9108B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
ms.openlocfilehash: ffd8f7cc31707d09bb3442c180a867d31afcef0f
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517501"
---
# <a name="xamarinforms-map"></a>Mapa do Xamarin. Forms

## <a name="initialization-and-configuration"></a>[Inicialização e configuração](setup.md)

O pacote NuGet [Xamarin. Forms. Maps](https://www.nuget.org/packages/Xamarin.Forms.Maps/) é necessário para usar a funcionalidade de mapas em um aplicativo. Além disso, o acesso ao local do usuário exige que as permissões de localização tenham sido concedidas ao aplicativo.

## <a name="map-control"></a>[Controle de Mapeamento](map.md)

O [`Map`](xref:Xamarin.Forms.Maps.Map) controle é uma exibição de plataforma cruzada para exibir e anotar mapas. Ele usa o controle de mapa nativo para cada plataforma, fornecendo uma experiência de mapas rápida e familiar para os usuários.

## <a name="position-and-distance"></a>[Posição e distância](position-distance.md)

A [`Position`](xref:Xamarin.Forms.Maps.Position) struct normalmente é usada ao posicionar um mapa e seus Pins, e [`Distance`](xref:Xamarin.Forms.Maps.Distance) a struct que pode ser usada opcionalmente ao posicionar um mapa.

## <a name="pins"></a>[Afixa](pins.md)

O [`Map`](xref:Xamarin.Forms.Maps.Map) controle permite que os locais sejam marcados [`Pin`](xref:Xamarin.Forms.Maps.Pin) com objetos. Um `Pin` é um marcador de mapa que abre uma janela de informações quando tocado.

## <a name="polygons-polylines-and-circles"></a>[Polígonos, polilinhas e círculos](polygons.md)

`Polygon`os `Polyline`elementos, `Circle` e permitem realçar áreas específicas em um mapa. Um `Polygon` é uma forma totalmente incluída que pode ter uma cor de preenchimento e de traçado. Um `Polyline` é uma linha que não coloca totalmente uma área. Um `Circle` realça uma área circular do mapa.

## <a name="geocoding"></a>[Geocodificação](geocoder.md)

A [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) classe é convertida entre endereços de cadeia de caracteres e coordenadas de latitude e [`Position`](xref:Xamarin.Forms.Maps.Position) longitude que são armazenadas em objetos.

## <a name="launch-the-native-map-app"></a>[Iniciar o aplicativo de mapa nativo](native-map-app.md)

O aplicativo de mapa nativo em cada plataforma pode ser iniciado por meio de um aplicativo Xamarin. Forms pela classe `Launcher` Xamarin. Essentials.
