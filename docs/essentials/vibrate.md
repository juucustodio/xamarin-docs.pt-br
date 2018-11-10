---
title: 'Xamarin.Essentials: Vibração'
description: Este documento descreve a classe Vibration no Xamarin.Essentials, que permite iniciar e parar a funcionalidade de vibrar por um tempo desejado.
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: ff2e718953d86eb59b28fcaa8640e04f6bf422f3
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675296"
---
# <a name="xamarinessentials-vibration"></a>Xamarin.Essentials: Vibração

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

A classe **Vibration** permite iniciar e interromper a funcionalidade de vibrar por um tempo desejado.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

Para acessar a funcionalidade de **Vibração**, a seguinte configuração específica da plataforma é necessária.

# <a name="androidtabandroid"></a>[Android](#tab/android)

A permissão de Vibração é necessária e deve ser configurada no projeto do Android. Isso pode ser usado das seguintes maneiras:

Abra o arquivo **AssemblyInfo.cs** na pasta **Propriedades** e adicione:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

OU Atualize o Manifesto do Android:

Abra o arquivo **AndroidManifest.xml** na pasta **Propriedades** e adicione o seguinte dentro do nó do **manifesto**.

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

Ou clique com o botão direito do mouse no projeto do Android e abra as propriedades do projeto. Em **Manifesto do Android**, localize a área **Permissões necessárias:** e marque a permissão **VIBRAR**. Isso atualizará automaticamente o arquivo **AndroidManifest.xml**.

# <a name="iostabios"></a>[iOS](#tab/ios)

Não exige mais configurações.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Sem diferenças entre plataformas.

-----

## <a name="using-vibration"></a>Como usar a Vibração

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de vibração pode ser solicitada por um determinado tempo ou pelo padrão de 500 milissegundos.

```csharp
try
{
    // Use default vibration length
    Vibration.Vibrate();

    // Or use specified time
    var duration = TimeSpan.FromSeconds(1);
    Vibration.Vibrate(duration);
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

O cancelamento da vibração do dispositivo pode ser solicitado com o método `Cancel`:

```csharp
try
{
    Vibration.Cancel();
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

## <a name="platform-differences"></a>Diferenças entre plataformas

# <a name="androidtabandroid"></a>[Android](#tab/android)

Sem diferenças entre plataformas.

# <a name="iostabios"></a>[iOS](#tab/ios)

* Somente vibra quando o dispositivo estiver definido como "Vibrar ao tocar".
* Sempre vibra por 500 milissegundos.
* Não é possível cancelar a vibração.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Sem diferenças entre plataformas.

-----

## <a name="api"></a>API

- [Código-fonte de Vibração](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Vibration)
- [Documentação da API de Vibração](xref:Xamarin.Essentials.Vibration)
