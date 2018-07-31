---
title: Mapas de Xamarin.Essentials
description: A classe de mapas em Xamarin.Essentials permite que um aplicativo para abrir o aplicativo de mapas instalados para um local específico ou placemark.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: 445e2da84e9a9aaf1ce4d836af11cfba963b8cbb
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353926"
---
# <a name="xamarinessentials-maps"></a>Xamarin.Essentials: mapas

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **mapeia** classe permite que um aplicativo para abrir o aplicativo de mapas instalados para um local específico ou placemark.

## <a name="using-maps"></a>Uso de mapas

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de mapas funciona chamando o `OpenAsync` método com o `Location` ou `Placemark` para abrir com opcional `MapsLaunchOptions`.

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

Ao abrir com uma `Placemark` são necessárias as seguintes informações:

* `CountryName`
* `AdminArea`
* `Thoroughfare`
* `Locality`

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

Se você já tiver uma referência a um `Location` ou `Placemark` você pode usar o método de extensão internos `OpenMapsAsync` com opcional `MapsLaunchOptions`:

```csharp
public class MapsTest
{
    public async Task OpenPlacemarkOnMaps(Placemark placemark)
    {
        await placemark.OpenMapsAsync();
    }
}
```

## <a name="platform-differences"></a>Diferenças de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

* `MapDirectionsMode` não é suportado e não tem nenhum efeito.

# <a name="iostabios"></a>[iOS](#tab/ios)

* `MapDirectionsMode` há suporte para definir o modo de direção padrão quando o aplicativo de mapas for aberto.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

* `MapDirectionsMode` não é suportado e não tem nenhum efeito.

--------------

## <a name="platform-implementation-specifics"></a>Particularidades de implementação de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android usa o `geo:` esquema de Uri para iniciar o aplicativo de mapas no dispositivo. Isso pode solicitar ao usuário selecionar a partir de um aplicativo existente que dá suporte a esse esquema de Uri.  Xamarin.Essentials é testado com o Google Maps, que dá suporte a este esquema.

# <a name="iostabios"></a>[iOS](#tab/ios)

Nenhum detalhe de implementação específica da plataforma.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Nenhum detalhe de implementação específica da plataforma.

--------------

## <a name="api"></a>API

- [Mapas de código-fonte](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Maps)
- [Documentação da API de mapas](xref:Xamarin.Essentials.Maps)
