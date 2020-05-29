---
Título: " Xamarin.Essentials mapa" Descrição: "a classe Map no Xamarin.Essentials permite que um aplicativo Abra o aplicativo de mapa instalado em um local específico ou placemark."
MS. AssetID: BABF40CC-8BEE-43FD-BE12-6301DF27DD33 autor: jamesmontemagno MS. Author: Jamont MS. Data: 05/26/2020 MS. Custom: vídeo no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-map"></a>Xamarin.Essentials: Mapa

A classe **Maps** permite que um aplicativo abra o aplicativo de mapas instalado em um local ou marcador específico.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-map"></a>Usando o mapa

Adicione uma referência a Xamarin.Essentials em sua classe:

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

        try
        {
            await Map.OpenAsync(location, options);
        }
        catch (Exception ex)
        {
            // No map application available to open
        }
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

        try
        {
            await Map.OpenAsync(placemark, options);
        }
        catch (Exception ex)
        {
            // No map application available to open or placemark can not be located
        }
    }
}
```

## <a name="extension-methods"></a>Métodos de Extensão

Se você já tiver uma referência a uma `Location` ou `Placemark`, use o método de extensão interno `OpenMapAsync` com `MapLaunchOptions` opcional:

```csharp
public class MapTest
{
    public async Task OpenPlacemarkOnMap(Placemark placemark)
    {
        try
        {
            await placemark.OpenMapAsync();
        }
        catch (Exception ex)
        {
            // No map application available to open
        }
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

O Android usa o esquema de Uri `geo:` para iniciar o aplicativo de mapas no dispositivo. Isso pode levar o usuário a optar por um aplicativo existente que suporte esse esquema de Uri.  Xamarin.Essentialsé testado com o Google Maps, que dá suporte a esse esquema.

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
