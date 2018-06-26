---
title: Iniciar o aplicativo de mapas
description: Como iniciar o aplicativo de mapas interno de dentro de seu aplicativo xamarin.
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/25/2018
ms.openlocfilehash: d15b6e544f58f03272c711236b579ca568e09539
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935016"
---
# <a name="launching-the-maps-application"></a>Iniciar o aplicativo de mapas

É a maneira mais simples para trabalhar com mapas em xamarin aproveitar o aplicativo de mapas internos mostrado abaixo:

[![Captura de tela de exemplo de aplicativo interno do Google Maps](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

Quando você usar o aplicativo de mapas, o mapa não farão parte do seu aplicativo. Em vez disso, seu aplicativo iniciará o aplicativo de mapas e carregar o mapa externamente. A próxima seção examina como usar o xamarin para iniciar o maps, como a mostrada acima.


## <a name="creating-the-intent"></a>Criando a intenção

Trabalhando com os mapeamentos de aplicativo é tão fácil quanto a criação de uma tentativa com um URI apropriado, definir a ação para ActionView e chamar o método StartActivity. Por exemplo, o código a seguir inicia o aplicativo de mapas centralizado em uma determinada latitude e longitude:

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

Esse código é tudo o que é necessário para iniciar o mapa mostrado na captura de tela anterior. Além de especificar a latitude e longitude, o esquema de URI para mapas dá suporte a várias outras opções.


## <a name="geo-uri-scheme"></a>Esquema de URI de replicação geográfica

O código anterior usado o esquema de replicação geográfica para criar um URI. Esse esquema URI dá suporte a vários formatos, como listado abaixo:

-   `geo:latitude,longitude` &ndash; Abre o aplicativo de mapas centralizado em um lat/lon. 

-   `geo:latitude,longitude?z=zoom` &ndash; Abre os mapas de aplicativos centralizados em um lat/lon e atingiu o nível especificado. O nível de zoom pode variar de 1 a 23: 1 exibe Terra inteira e 23 é o nível de zoom mais próximo.

-   `geo:0,0?q=my+street+address` &ndash; Abre o aplicativo mapeia para o local de um endereço. 

-   `geo:0,0?q=business+near+city` &ndash; Abre o aplicativo de mapas e exibe os resultados da pesquisa anotado. 


As versões do URI que executar uma consulta (ou seja, as Rua endereço ou pesquisa de termos) usam serviço de geocoder do Google para recuperar o local que é exibido no mapa. Por exemplo, o URI `geo:0,0?q=coop+Cambridge` resulta no mapa mostrado abaixo:

[![Captura de tela de exemplo mostrando Google Maps com um termo de pesquisa](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)



Para obter mais informações sobre esquemas URI de área geográfica, consulte [mostrar um local em um mapa](http://developer.android.com/guide/components/intents-common.html#Maps).


## <a name="street-view"></a>Exibição Rua

Além do esquema de replicação geográfica, Android também dá suporte ao carregamento ruas modos de exibição de uma tentativa. Um exemplo de aplicativo exibição Rua iniciado a partir de xamarin é mostrado abaixo:

[![Captura de tela de exemplo de uma exibição da rua](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

Para iniciar um modo de exibição de rua, simplesmente use o `google.streetview` esquema URI, como demonstrado no código a seguir:

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

O esquema URI google.streetview usado acima usa o seguinte formato:

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

Como você pode ver, há vários parâmetros com suporte, como listado abaixo:

-   `lat` &ndash; A latitude do local a ser mostrado na exibição da rua.

-   `lng` &ndash; A longitude do local a ser mostrado na exibição da rua.

-   `pitch` &ndash; Ângulo de panorama exibição rua, medido do centro em graus que 90 graus é reta para baixo e -90 graus é para cima.

-   `yaw` &ndash; Exibição central do panorama de exibição rua, no sentido horário medidos em graus do Norte.

-   `zoom` &ndash; Zoom multiplicador panorama exibição rua, onde 1,0 = normal zoom, 2.0 = 2 com zoom x 3.0 = 4 com zoom x, etc.

-   `mz` &ndash; O nível de zoom do mapa que será usado quando o aplicativo de mapas do modo de exibição rua.


Trabalhar com o interno mapeia o aplicativo ou a exibição rua é uma maneira fácil de adicionar rapidamente o suporte de mapeamento. No entanto, a API de mapas do Android oferece maior controle sobre a experiência de mapeamento.
