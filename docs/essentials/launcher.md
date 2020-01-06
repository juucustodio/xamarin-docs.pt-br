---
title: Xamarin.Essentials Launcher
description: A classe Launcher no Xamarin.Essentials permite que um aplicativo abra um URI pelo sistema.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 08/20/2019
ms.openlocfilehash: 88c1450d28b4c94fe8079b8915503cf5de118644
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488511"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials: Launcher

A classe **Launcher** permite que um aplicativo abra um URI pelo sistema. Isso geralmente é utilizado quando a vinculação profunda é usada em esquemas de URI personalizados de outro aplicativo. Se quiser abrir o navegador em um site, confira a API do **[Browser](open-browser.md)** .

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-launcher"></a>Uso do Launcher

Adicione uma referência ao Xamarin.Essentials na classe:

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

# <a name="androidtabandroid"></a>[Android](#tab/android)

Nenhuma configuração adicional.

# <a name="iostabios"></a>[iOS](#tab/ios)

No iOS 9 e superior, a Apple impõe o esquema que um aplicativo pode consultar. Para especificar quais esquemas você gostaria de usar, você deve especificar `LSApplicationQueriesSchemes` em seu arquivo de `Info.plist`.

```
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>lyft</string>  
    <string>fb</string>
</array>
```

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Nenhuma configuração adicional.

-----

## <a name="files"></a>Files

Esse recurso permite que um aplicativo solicite que outros aplicativos abram e exibam um arquivo. O Xamarin.Essentials detectará automaticamente o tipo de arquivo (MIME) e solicitará que o arquivo seja aberto.

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

# <a name="androidtabandroid"></a>[Android](#tab/android)

A Tarefa retornada de `CanOpenAsync` é concluída imediatamente.

# <a name="iostabios"></a>[iOS](#tab/ios)

Se o aplicativo de destino neste dispositivo nunca tiver sido aberto pelo `OpenAsync` no aplicativo anteriormente, o iOS enviará uma única solicitação ao usuário para que ele permita que o aplicativo o abra.

A Tarefa retornada de `CanOpenAsync` é concluída imediatamente.

Confira [aqui](xref:UIKit.UIApplication.CanOpenUrl*) mais informações disponíveis sobre a implementação do iOS

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Sem diferenças entre plataformas.

-----

## <a name="api"></a>API

- [Código-fonte do Launcher](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Launcher)
- [Documentação da API do Launcher](xref:Xamarin.Essentials.Launcher)
