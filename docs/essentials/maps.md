---
title: Xamarin.Essentials Mapa
description: A classe Map no Xamarin.Essentials permite que um aplicativo abra o aplicativo de mapas instalado em um local ou marcador específico.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
ms.openlocfilehash: c0875534d88ea5b66b3072c35b9d38894fe98934
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61354563"
---
# <a name="xamarinessentials-map"></a>Xamarin.Essentials: Mapa

A classe **Maps** permite que um aplicativo abra o aplicativo de mapas instalado em um local ou marcador específico.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-map"></a>Usando o mapa

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade Map funciona chamando o método `OpenAsync` com `Location` ou `Placemark` para abrir com `MapLaunchOptions` opcional.

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapLaunchOptions { Name = "Microsoft Building 25" };

        await Map.OpenAsync(location, options);
    }
}
```

Ao abrir com um `Placemark`, as seguintes informações são necessárias:

- `CountryName`
- `AdminArea`
- `Thoroughfare`
- `Locality`

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var placemark = new Placemark
            {
                CountryName = "United States",
                AdminArea = "WA",
                Thoroughfare = "Microsoft Building 25",
                Locality = "Redmond"
            };
        var options =  new MapLaunchOptions { Name = "Microsoft Building 25" };

        await Map.OpenAsync(placemark, options);
    }
}
```

## <a name="extension-methods"></a>Métodos de extensão

Se você já tiver uma referência a uma `Location` ou `Placemark`, use o método de extensão interno `OpenMapAsync` com `MapLaunchOptions` opcional:

```csharp
public class MapTest
{
    public async Task OpenPlacemarkOnMap(Placemark placemark)
    {
        await placemark.OpenMapAsync();
    }
}
```

## <a name="directions-mode"></a>Modo de direções

Se você chamar `OpenMapAsync` sem `MapLaunchOptions`, o mapa será iniciado no local especificado. Se quiser, tenha uma rota de navegação calculada a partir da posição atual do dispositivo. Isso é feito definindo o `NavigationMode` nas `MapLaunchOptions`:

```csharp
public class MapTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapLaunchOptions { NavigationMode = NavigationMode.Driving };

        await Map.OpenAsync(location, options);
    }
}
```

## <a name="platform-differences"></a>Diferenças entre plataformas

# <a name="android"></a>[Android](#tab/android)

- `NavigationMode` oferece suporte a Ciclismo, Condução de veículos e Caminhada.

# <a name="ios"></a>[iOS](#tab/ios)

- `NavigationMode` oferece suporte a Condução de veículos, Trânsito e Caminhada.

# <a name="uwp"></a>[UWP](#tab/uwp)

- `NavigationMode` oferece suporte a Condução de veículos, Trânsito e Caminhada.

--------------

## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

# <a name="android"></a>[Android](#tab/android)

O Android usa o esquema de Uri `geo:` para iniciar o aplicativo de mapas no dispositivo. Isso pode levar o usuário a optar por um aplicativo existente que suporte esse esquema de Uri.  O Xamarin.Essentials é testado com o Google Maps, que é compatível com este esquema.

# <a name="ios"></a>[iOS](#tab/ios)

Sem detalhes da implementação específica da plataforma.

# <a name="uwp"></a>[UWP](#tab/uwp)

Sem detalhes da implementação específica da plataforma.

--------------

## <a name="api"></a>API

- [Código-fonte do Mapa](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Map)
- [Documentação da API do Mapa](xref:Xamarin.Essentials.Map)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Maps-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
