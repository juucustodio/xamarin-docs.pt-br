---
Título: " Xamarin.Essentials iniciador" Descrição: "a classe do iniciador no Xamarin.Essentials permite que um aplicativo Abra um URI pelo sistema".
MS. AssetID: BABF40CC-8BEE-43FD-BE12-6301DF27DD33 autor: jamesmontemagno MS. Custom: vídeo MS. Author: Jamont MS. Date: 08/20/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials: Iniciador

A classe **Launcher** permite que um aplicativo abra um URI pelo sistema. Isso geralmente é utilizado quando a vinculação profunda é usada em esquemas de URI personalizados de outro aplicativo. Se quiser abrir o navegador em um site, confira a API do **[Browser](open-browser.md)**.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-launcher"></a>Uso do Launcher

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Para usar a funcionalidade Launcher, chame o método `OpenAsync` e passe uma `string` ou `Uri` para abrir. Opcionalmente, o método `CanOpenAsync` pode ser usado para verificar se o esquema de URI pode ser tratado por um aplicativo no dispositivo.

```csharp
public class LauncherTest
{
    public async Task OpenRideShareAsync()
    {
        var supportsUri = await Launcher.CanOpenAsync("lyft://");
        if (supportsUri)
            await Launcher.OpenAsync("lyft://ridetype?id=lyft_line");
    }
}
```

Isso pode ser combinado em uma única chamada com `TryOpenAsync`, que verifica se o parâmetro pode ser aberto e, em caso afirmativo, abre-o.

```csharp
public class LauncherTest
{
    public async Task<bool> OpenRideShareAsync()
    {
        return await Launcher.TryOpenAsync("lyft://ridetype?id=lyft_line");
    }
}
```

### <a name="additional-platform-setup"></a>Configuração de plataforma adicional

# <a name="android"></a>[Android](#tab/android)

Nenhuma configuração adicional.

# <a name="ios"></a>[iOS](#tab/ios)

No iOS 9 e superior, a Apple impõe o esquema que um aplicativo pode consultar. Para especificar quais esquemas você gostaria de usar, você deve especificar `LSApplicationQueriesSchemes` em seu `Info.plist` arquivo.

```
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>lyft</string>  
    <string>fb</string>
</array>
```

# <a name="uwp"></a>[UWP](#tab/uwp)

Nenhuma configuração adicional.

-----

## <a name="files"></a>Arquivos

Esse recurso permite que um aplicativo solicite que outros aplicativos abram e exibam um arquivo. Xamarin.Essentialso detectará automaticamente o tipo de arquivo (MIME) e solicitará que o arquivo seja aberto.

Aqui está um exemplo de gravação de texto em disco e solicitação de abertura:

```csharp
var fn = "File.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

await Launcher.OpenAsync(new OpenFileRequest
{
    File = new ReadOnlyFile(file)
});
```

## <a name="platform-differences"></a>Diferenças entre plataformas

# <a name="android"></a>[Android](#tab/android)

A Tarefa retornada de `CanOpenAsync` é concluída imediatamente.

# <a name="ios"></a>[iOS](#tab/ios)

Se o aplicativo de destino neste dispositivo nunca tiver sido aberto pelo `OpenAsync` no aplicativo anteriormente, o iOS enviará uma única solicitação ao usuário para que ele permita que o aplicativo o abra.

A Tarefa retornada de `CanOpenAsync` é concluída imediatamente.

Confira [aqui](xref:UIKit.UIApplication.CanOpenUrl*) mais informações disponíveis sobre a implementação do iOS

# <a name="uwp"></a>[UWP](#tab/uwp)

Sem diferenças entre plataformas.

-----

## <a name="api"></a>API

- [Código-fonte do Inicializador](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Launcher)
- [Documentação da API do Inicializador](xref:Xamarin.Essentials.Launcher)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Launcher-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
