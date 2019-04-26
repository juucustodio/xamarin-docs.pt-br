---
title: Iniciar o aplicativo de mapas
description: Como iniciar o aplicativo mapas internos no aplicativo xamarin. Android.
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: fa32783617fce99514560677184f17be904cd42d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61186357"
---
# <a name="launching-the-maps-application"></a>Iniciar o aplicativo de mapas

A maneira mais simples para trabalhar com mapas no xamarin. Android é aproveitar o aplicativo de mapas internos mostrado abaixo:

[![Captura de tela de exemplo de aplicativo interno do Google Maps](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

Quando você usa o aplicativo de mapas, o mapa não será parte do seu aplicativo. Em vez disso, seu aplicativo iniciará o aplicativo de mapas e carregar o mapa externamente. A próxima seção examina como usar o xamarin. Android para abrir mapas com o mostrado acima.


## <a name="creating-the-intent"></a>Criando a intenção

Trabalhando com os mapas de aplicativo é tão fácil quanto criar uma intenção com um URI apropriado, definindo a ação como ActionView e chamando o método StartActivity. Por exemplo, o código a seguir inicia o aplicativo de mapas centralizado em uma determinada latitude e longitude:

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

Esse código é tudo o que é necessário para iniciar o mapa mostrado na captura de tela anterior. Além de especificar a latitude e longitude, o esquema de URI para mapas dá suporte a várias outras opções.


## <a name="geo-uri-scheme"></a>Esquema de URI geográfica

O código acima usado o esquema de geográfica para criar um URI. Esse esquema de URI dá suporte a vários formatos, como listado abaixo:

-   `geo:latitude,longitude` &ndash; Abre o aplicativo de mapas centralizado em um lat/lon. 

-   `geo:latitude,longitude?z=zoom` &ndash; Abre os mapas de aplicativos centralizado em um lat/lon e ampliado para o nível especificado. O nível de zoom pode variar de 1 a 23: 1 exibe a Terra inteira e 23 é o mais próximo nível de zoom.

-   `geo:0,0?q=my+street+address` &ndash; Abre o aplicativo de mapas para o local de um endereço. 

-   `geo:0,0?q=business+near+city` &ndash; Abre o aplicativo de mapas e exibe os resultados da pesquisa anotado. 


As versões do URI que usam uma consulta (ou seja, as Rua endereço ou pesquisa de termos) usam o serviço de geocoder do Google para recuperar o local que é exibido no mapa. Por exemplo, o URI `geo:0,0?q=coop+Cambridge` resulta no mapa mostrado abaixo:

[![Captura de tela de exemplo mostrando o Google Maps com um termo de pesquisa](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)



Para obter mais informações sobre esquemas URI geográfica, consulte [mostrar um local em um mapa](https://developer.android.com/guide/components/intents-common.html#Maps).


## <a name="street-view"></a>Modo de exibição Rua

Além do esquema de geográfica, Android também dá suporte ao carregamento ruas modos de exibição de uma tentativa. Abaixo está um exemplo do aplicativo de exibição Rua iniciado a partir do xamarin. Android:

[![Captura de tela de exemplo de uma exibição da rua](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

Para iniciar um modo de exibição de rua, simplesmente use o `google.streetview` esquema URI, conforme demonstrado no código a seguir:

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

O esquema de URI google.streetview usado acima usa o seguinte formato:

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

Como você pode ver, há vários parâmetros com suporte, conforme listado abaixo:

-   `lat` &ndash; A latitude do local a ser mostrado na exibição da rua.

-   `lng` &ndash; A longitude do local a ser mostrado na exibição da rua.

-   `pitch` &ndash; Ângulo do panorama de exibição de rua, medido do centro em graus em que 90 graus é diretamente para baixo e -90 graus é diretamente para cima.

-   `yaw` &ndash; Centro de exibição do panorama de exibição de rua, medido no sentido horário em graus do Norte.

-   `zoom` &ndash; Zoom multiplicador para o panorama de exibição de rua, onde 1,0 = zoom normal, 2.0 = 2 com zoom x 3.0 = 4 com zoom x, etc.

-   `mz` &ndash; O nível de zoom do mapa que será usado ao passar para o aplicativo de mapas de exibição de rua.


Aplicativo de mapas de trabalhar com o interno ou a exibição rua é uma maneira fácil de adicionar rapidamente o suporte de mapeamento. No entanto, a API de mapas do Android oferece um melhor controle sobre a experiência de mapeamento.
