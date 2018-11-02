---
title: Xamarin.Essentials Maps
description: A classe Maps no Xamarin.Essentials permite que um aplicativo abra o aplicativo de mapas instalado em um local ou marcador específico.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: fb4cbc2fd334d574abc57a3359fa346bc6795408
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674733"
---
# <a name="xamarinessentials-maps"></a>Xamarin.Essentials: Maps

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

A classe **Maps** permite que um aplicativo abra o aplicativo de mapas instalado em um local ou marcador específico.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-maps"></a>Uso dos mapas

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade Maps funciona chamando o método `OpenAsync` com `Location` e `Placemark` para abrir com `MapsLaunchOptions` opcional.

```csharp
public class MapsTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapsLaunchOptions { Name = "Microsoft Building 25" };

        await Maps.OpenAsync(location, options);
    }
}
```

Ao abrir com um `Placemark`, as seguintes informações são necessárias:

- `CountryName`
- `AdminArea`
- `Thoroughfare`
- `Locality`

```csharp
public class MapsTest
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
        var options =  new MapsLaunchOptions { Name = "Microsoft Building 25" };

        await Maps.OpenAsync(placemark, options);
    }
}
```

## <a name="extension-methods"></a>Métodos de extensão

Se você já tiver uma referência a uma `Location` ou `Placemark`, use o método de extensão interno `OpenMapsAsync` com `MapsLaunchOptions` opcional:

```csharp
public class MapsTest
{
    public async Task OpenPlacemarkOnMaps(Placemark placemark)
    {
        await placemark.OpenMapsAsync();
    }
}
```

## <a name="directions-mode"></a>Modo de direções

Se você chamar `OpenMapsAsync` sem `MapsLaunchOptions`, o mapa será iniciado no local especificado. Se quiser, tenha uma rota de navegação calculada a partir da posição atual do dispositivo. Isso é feito definindo o `MapDirectionsMode` nas `MapsLaunchOptions`:

```csharp
public class MapsTest
{
    public async Task NavigateToBuilding25()
    {
        var location = new Location(47.645160, -122.1306032);
        var options =  new MapsLaunchOptions { MapDirectionsMode = MapDirectionsMode.Driving };

        await Maps.OpenAsync(location, options);
    }
}
```

## <a name="platform-differences"></a>Diferenças entre plataformas

# <a name="androidtabandroid"></a>[Android](#tab/android)

- `MapDirectionsMode` oferece suporte a Ciclismo, Condução de veículos e Caminhada.

# <a name="iostabios"></a>[iOS](#tab/ios)

- `MapDirectionsMode` oferece suporte a Condução de veículos, Trânsito e Caminhada.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- `MapDirectionsMode` oferece suporte a Condução de veículos, Trânsito e Caminhada.

--------------

## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

O Android usa o esquema de Uri `geo:` para iniciar o aplicativo de mapas no dispositivo. Isso pode levar o usuário a optar por um aplicativo existente que suporte esse esquema de Uri.  O Xamarin.Essentials é testado com o Google Maps, que é compatível com este esquema.

# <a name="iostabios"></a>[iOS](#tab/ios)

Sem detalhes da implementação específica da plataforma.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Sem detalhes da implementação específica da plataforma.

--------------

## <a name="api"></a>API

- [Código-fonte do Maps](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Maps)
- [Documentação da API do Maps](xref:Xamarin.Essentials.Maps)
