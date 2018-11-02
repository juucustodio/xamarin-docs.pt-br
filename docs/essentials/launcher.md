---
title: Xamarin.Essentials Launcher
description: A classe Launcher no Xamarin.Essentials permite que um aplicativo abra um URI pelo sistema.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: 8f5ef8ef97999e9e85944d9fa9d4e57660779a48
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115777"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials: Launcher

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

A classe **Launcher** permite que um aplicativo abra um URI pelo sistema. Isso geralmente é utilizado quando a vinculação profunda é usada em esquemas de URI personalizados de outro aplicativo. Se quiser abrir o navegador em um site, confira a API do **[Browser](open-browser.md)**.

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

## <a name="platform-differences"></a>Diferenças entre plataformas

# <a name="androidtabandroid"></a>[Android](#tab/android)

A Tarefa retornada de `CanOpenAsync` é concluída imediatamente.

# <a name="iostabios"></a>[iOS](#tab/ios)

Se o aplicativo de destino neste dispositivo nunca tiver sido aberto pelo `OpenAsync` no aplicativo anteriormente, o iOS enviará uma única solicitação ao usuário para que ele permita que o aplicativo o abra.

A Tarefa retornada de `CanOpenAsync` é concluída imediatamente.

Confira [aqui](https://developer.xamarin.com/api/member/UIKit.UIApplication.CanOpenUrl/p/Foundation.NSUrl/) mais informações disponíveis sobre a implementação do iOS

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Sem diferenças entre plataformas.

-----

## <a name="api"></a>API

- [Código-fonte do Launcher](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Launcher)
- [Documentação da API do Launcher](xref:Xamarin.Essentials.Launcher)
