---
title: Lançamento do Aplicativo Mapas
description: Como iniciar o aplicativo Maps incorporado dentro do seu aplicativo Xamarin.Android.
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: 7b74f564f2b6e9613874a774258a7e999002e61a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027075"
---
# <a name="launching-the-maps-application"></a>Lançamento do Aplicativo Mapas

A maneira mais simples de trabalhar com mapas no Xamarin.Android é aproveitar o aplicativo de mapas incorporados mostrado abaixo:

[![Exemplo de captura de tela do aplicativo integrado do Google Maps](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

Quando você usa o aplicativo mapas, o mapa não fará parte do seu aplicativo. Em vez disso, seu aplicativo iniciará o aplicativo de mapas e carregará o mapa externamente. A próxima seção examina como usar o Xamarin.Android para lançar mapas como o acima.

## <a name="creating-the-intent"></a>Criando a Intenção

Trabalhar com o aplicativo de mapas é tão fácil quanto criar uma Intenção com um URI apropriado, definir a ação para O ActionView e chamar o método StartActivity. Por exemplo, o código a seguir inicia o aplicativo de mapas centrado em uma dada latitude e longitude:

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

Este código é tudo o que é necessário para iniciar o mapa mostrado na captura de tela anterior. Além de especificar latitude e longitude, o esquema URI para mapas suporta várias outras opções.

## <a name="geo-uri-scheme"></a>Esquema Geo URI

O código acima usou o esquema geográfico para criar um URI. Este esquema URI suporta vários formatos, conforme listado abaixo:

- `geo:latitude,longitude`&ndash; Abre a aplicação de mapas centrada em um lat/lon. 

- `geo:latitude,longitude?z=zoom`&ndash; Abre o aplicativo de mapas centrado em um lat/lon e ampliado para o nível especificado. O nível de zoom pode variar de 1 a 23: 1 exibe toda a Terra e 23 é o nível de zoom mais próximo.

- `geo:0,0?q=my+street+address`&ndash; Abre o aplicativo de mapas para a localização de um endereço de rua. 

- `geo:0,0?q=business+near+city`&ndash; Abre o aplicativo de mapas e exibe os resultados de pesquisa anotados. 

As versões do URI que fazem uma consulta (ou seja, o endereço da rua ou os termos de pesquisa) usam o serviço de geocodificador do Google para recuperar o local que é exibido no mapa. Por exemplo, `geo:0,0?q=coop+Cambridge` o URI resulta no mapa mostrado abaixo:

[![Exemplo de captura de tela mostrando o Google Maps com um termo de pesquisa](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)

Para obter mais informações sobre esquemas geo URI, consulte [Mostrar um local em um mapa](https://developer.android.com/guide/components/intents-common.html#Maps).

## <a name="street-view"></a>Street View

Além do esquema geográfico, o Android também suporta o carregamento de vistas de rua a partir de uma Intenção. Um exemplo do aplicativo street view lançado do Xamarin.Android é mostrado abaixo:

[![Exemplo de captura de tela de uma vista de rua](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

Para iniciar uma vista de `google.streetview` rua, basta usar o esquema URI, conforme demonstrado no seguinte código:

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

O esquema URI google.streetview usado acima toma a seguinte forma:

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

Como você pode ver, existem vários parâmetros suportados, conforme listado abaixo:

- `lat`&ndash; A latitude do local a ser mostrado na vista da rua.

- `lng`&ndash; A longitude do local a ser mostrado na vista da rua.

- `pitch`&ndash; Ângulo de visão de rua panorama, medido a partir do centro em graus onde 90 graus é para baixo e -90 graus é para cima.

- `yaw`&ndash; Centro de visão do panorama da visão de rua, medido no sentido horário em graus do Norte.

- `zoom`&ndash; Multiplicador de zoom para panorama de visão de rua, onde 1,0 = zoom normal, 2,0 = zoom 2x, 3,0 = zoom 4x, etc.

- `mz`&ndash; O nível de zoom do mapa que será usado ao ir para o aplicativo de mapas a partir da vista de rua.

Trabalhar com o aplicativo de mapas incorporados ou a vista de rua é uma maneira fácil de adicionar rapidamente suporte de mapeamento. No entanto, a API do Maps do Android oferece um controle mais fino sobre a experiência de mapeamento.
