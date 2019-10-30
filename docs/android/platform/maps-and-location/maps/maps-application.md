---
title: Iniciando o aplicativo Maps
description: Como iniciar o aplicativo de mapas interno de dentro de seu aplicativo Xamarin. Android.
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 7b74f564f2b6e9613874a774258a7e999002e61a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027075"
---
# <a name="launching-the-maps-application"></a>Iniciando o aplicativo Maps

A maneira mais simples de trabalhar com mapas no Xamarin. Android é aproveitar o aplicativo de mapas interno mostrado abaixo:

[![captura de tela de exemplo do aplicativo interno do Google Maps](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

Quando você usa o aplicativo Maps, o mapa não fará parte do seu aplicativo. Em vez disso, seu aplicativo iniciará o aplicativo Maps e carregará o mapa externamente. A próxima seção examina como usar o Xamarin. Android para iniciar mapas como aquele acima.

## <a name="creating-the-intent"></a>Criando a intenção

Trabalhar com o aplicativo Maps é tão fácil quanto criar uma intenção com um URI apropriado, definir a ação como ActionView e chamar o método StartActivity. Por exemplo, o código a seguir inicia o aplicativo Maps centralizado em uma determinada latitude e Longitude:

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

Esse código é tudo o que é necessário para iniciar o mapa mostrado na captura de tela anterior. Além de especificar a latitude e a longitude, o esquema de URI para mapas oferece suporte a várias outras opções.

## <a name="geo-uri-scheme"></a>Esquema de URI geográfica

O código acima usou o esquema geográfico para criar um URI. Esse esquema de URI dá suporte a vários formatos, conforme listado abaixo:

- `geo:latitude,longitude` &ndash; abre o aplicativo Maps centralizado em uma Lat/Lon. 

- `geo:latitude,longitude?z=zoom` &ndash; abre o aplicativo Maps centralizado em uma Lat/Lon e zoom no nível especificado. O nível de zoom pode variar de 1 a 23:1 exibe toda a terra e 23 é o nível mais próximo de zoom.

- `geo:0,0?q=my+street+address` &ndash; abre o aplicativo Maps para o local de um endereço. 

- `geo:0,0?q=business+near+city` &ndash; abre o aplicativo Maps e exibe os resultados da pesquisa anotada. 

As versões do URI que tomam uma consulta (ou seja, o endereço ou os termos de pesquisa) usam o serviço de geocodificador do Google para recuperar o local que é exibido no mapa. Por exemplo, o URI `geo:0,0?q=coop+Cambridge` resulta no mapa mostrado abaixo:

[![captura de tela de exemplo mostrando o Google Maps com um termo de pesquisa](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)

Para obter mais informações sobre esquemas de URI geográfica, consulte [Mostrar um local em um mapa](https://developer.android.com/guide/components/intents-common.html#Maps).

## <a name="street-view"></a>Exibição da rua

Além do esquema geográfico, o Android também dá suporte ao carregamento de exibições de rua de uma intenção. Um exemplo do aplicativo de exibição de rua iniciado no Xamarin. Android é mostrado abaixo:

[captura de tela de exemplo![de uma exibição de rua](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

Para iniciar uma exibição de rua, basta usar o esquema de URI de `google.streetview`, conforme demonstrado no código a seguir:

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

O esquema de URI do Google. StreetView usado acima usa o seguinte formato:

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

Como você pode ver, há vários parâmetros com suporte, conforme listado abaixo:

- `lat` &ndash; a latitude do local a ser mostrado na exibição de rua.

- `lng` &ndash; a longitude do local a ser mostrado na exibição de rua.

- `pitch` &ndash; ângulo da exibição de rua panorama, medido do centro em graus, em que 90 graus é reto e-90 graus é reto.

- `yaw` &ndash; central de visão do panorama da exibição da rua, medida no sentido horário em graus do Norte.

- `zoom` multiplicador de zoom &ndash; para o panorama da exibição de rua, em que 1,0 = Zoom normal, 2,0 = zoom 2x, 3,0 = zoom 4x, etc.

- `mz` &ndash; o nível de zoom do mapa que será usado ao ir para o aplicativo Maps da exibição de rua.

Trabalhar com o aplicativo de mapas interno ou a exibição de rua é uma maneira fácil de adicionar suporte de mapeamento rapidamente. No entanto, a API de mapas do Android oferece um controle mais preciso sobre a experiência de mapeamento.
