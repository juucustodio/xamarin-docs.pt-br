---
ms.topic: include
author: jamesmontemagno
ms.author: jamont
ms.date: 05/11/2020
ms.openlocfilehash: b5396061de657690fa3f4cdf68e8a94382918613
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83149753"
---
Essa API usa permissões de tempo de execução no Android. Verifique se o Xamarin. Essentials foi totalmente inicializado e se o tratamento de permissões está configurado em seu aplicativo.

No `MainLauncher` do projeto do Android ou qualquer `Activity` que é iniciada, o Xamarin.Essentials deve ser inicializado no método `OnCreate`:

```csharp
protected override void OnCreate(Bundle savedInstanceState) 
{
    //...
    base.OnCreate(savedInstanceState);
    Xamarin.Essentials.Platform.Init(this, savedInstanceState); // add this line to your code, it may also be called: bundle
    //...
}    
```

Para lidar com permissões de runtime no Android, o Xamarin.Essentials deve receber qualquer `OnRequestPermissionsResult`. Adicione o seguinte código a todas as classes `Activity`:

```csharp
public override void OnRequestPermissionsResult(int requestCode, string[] permissions, Android.Content.PM.Permission[] grantResults)
{
    Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

    base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
}
```
