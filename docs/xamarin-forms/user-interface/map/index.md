---
title: Mapa do Xamarin. Forms
description: O controle de mapa exibe um mapa e requer o pacote NuGet Xamarin. Forms. Maps.
ms.prod: xamarin
ms.assetid: B669B5EE-D24C-4C69-93E1-2CA5CC9108B5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/29/2019
ms.openlocfilehash: 013e126b76de08442327707cd0502f207826dad8
ms.sourcegitcommit: 3ea19e3a51515b30349d03c70a5b3acd7eca7fe7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2019
ms.locfileid: "73425595"
---
# <a name="xamarinforms-map"></a>Mapa do Xamarin. Forms

## <a name="initialization-and-configurationsetupmd"></a>[Inicialização e configuração](setup.md)

O pacote NuGet [Xamarin. Forms. Maps](https://www.nuget.org/packages/Xamarin.Forms.Maps/) é necessário para usar a funcionalidade de mapas em um aplicativo. Além disso, o acesso ao local do usuário exige que as permissões de localização tenham sido concedidas ao aplicativo.

## <a name="map-controlmapmd"></a>[Controle de Mapeamento](map.md)

O controle de [`Map`](xref:Xamarin.Forms.Maps.Map) é uma exibição de plataforma cruzada para exibir e anotar mapas. Ele usa o controle de mapa nativo para cada plataforma, fornecendo uma experiência de mapas rápida e familiar para os usuários.

## <a name="position-and-distanceposition-distancemd"></a>[Posição e distância](position-distance.md)

A estrutura de [`Position`](xref:Xamarin.Forms.Maps.Position) normalmente é usada ao posicionar um mapa e seus PINs e a [`Distance`](xref:Xamarin.Forms.Maps.Distance) struct que pode, opcionalmente, ser usada ao posicionar um mapa.

## <a name="pinspinsmd"></a>[Pins](pins.md)

O controle de [`Map`](xref:Xamarin.Forms.Maps.Map) permite que os locais sejam marcados com [`Pin`](xref:Xamarin.Forms.Maps.Pin) objetos. Uma `Pin` é um marcador de mapa que abre uma janela de informações quando tocado.

## <a name="polygons-and-polylinespolygonsmd"></a>[Polígonos e polilinhas](polygons.md)

os elementos `Polygon` e `Polyline` permitem realçar áreas específicas em um mapa. Uma `Polygon` é uma forma totalmente fechada que pode ter uma cor de traço e preenchimento. Uma `Polyline` é uma linha que não coloca totalmente uma área.

## <a name="geocodinggeocodermd"></a>[Geocódigo](geocoder.md)

A classe [`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder) converte entre endereços de cadeia de caracteres e coordenadas de latitude e longitude que são armazenadas em objetos [`Position`](xref:Xamarin.Forms.Maps.Position) .

## <a name="launch-the-native-map-appnative-map-appmd"></a>[Iniciar o aplicativo de mapa nativo](native-map-app.md)

O aplicativo de mapa nativo em cada plataforma pode ser iniciado por meio de um aplicativo Xamarin. Forms pela classe Xamarin. Essentials `Launcher`.
