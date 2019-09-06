---
title: Xamarin.Essentials Launcher
description: A classe Launcher no Xamarin.Essentials permite que um aplicativo abra um URI pelo sistema.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 08/20/2019
ms.openlocfilehash: c1c9fc4ce7e7a1dc4ab9573d29407f081e2ad8a1
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199853"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials: Inicializador

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

## <a name="files"></a>Arquivos

Esse recurso permite que um aplicativo solicite que outros aplicativos abram e exibam um arquivo. O Xamarin.Essentials detectará automaticamente o tipo de arquivo (MIME) e solicitará que o arquivo seja aberto.

Veja um exemplo de como gravar texto no disco e solicitar que ele seja aberto:

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
