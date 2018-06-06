---
title: 'Xamarin.Essentials: preferências'
description: Este documento descreve a classe de preferências Xamarin.Essentials, que salva as preferências de aplicativo em um repositório de chave/valor. Ele discute como usar a classe e os tipos de dados que podem ser armazenados.
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: e453c04a953e60be2508670723d175bde3dc7c42
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782834"
---
# <a name="xamarinessentials-preferences"></a>Xamarin.Essentials: preferências

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **preferências** classe ajuda a armazenar preferências de aplicativo em um armazenamento de chave/valor.

## <a name="using-secure-storage"></a>Usando o armazenamento seguro

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Para salvar um valor para um determinado _chave_ nas preferências de:

```csharp
Preferences.Set("my_key", "my_value");
```

Para recuperar um valor de preferências ou um padrão se não definida:

```csharp
var myValue = Preferences.Get("my_key", "default_value");
```

Para remover o _chave_ em Preferências de:

```csharp
Preferences.Remove("my_key");
```

Para remover todas as preferências de:

```csharp
Preferences.Clear();
```

Além desses métodos recebem em um recurso opcional `sharedName` que pode ser usado para criar mais contêineres de preferência. Leia os detalhes de implementação de plataforma abaixo.

## <a name="supported-data-types"></a>Tipos de dados com suporte

Os seguintes tipos de dados têm suporte em **preferências**:

- **bool**
- **double**
- **int**
- **float**
- **long**
- **string**

## <a name="platform-implementation-specifics"></a>Detalhes de implementação de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Todos os dados são armazenados em [compartilhado preferências](https://developer.android.com/training/data-storage/shared-preferences.html). Se nenhum `sharedName` for especificado, as preferências de compartilhada padrão são usadas, ou o nome será usado para obter um **privada** compartilhado preferências com o nome especificado.

# <a name="iostabios"></a>[iOS](#tab/ios)

[NSUserDefaults](https://docs.microsoft.com/en-us/xamarin/ios/app-fundamentals/user-defaults) é usado para armazenar valores em dispositivos iOS. Se nenhum `sharedName` for especificado o `StandardUserDefaults` são usadas, ou o nome será usado para criar um novo `NSUserDefaults` com o nome especificado usado para o `NSUserDefaultsType.SuiteName`.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[ApplicationDataContainer](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdatacontainer) é usado para armazenar os valores no dispositivo. Se nenhum `sharedName` for especificado o `LocalSettings` são usadas, ou o nome será usado para criar um novo contêiner dentro do `LocalSettings`.

--------------

## <a name="limitations"></a>Limitações

Ao armazenar uma cadeia de caracteres, essa API destina-se para armazenar pequenas quantidades de texto.  O desempenho pode ser subpar se você tentar usá-lo para armazenar grandes quantidades de texto.

## <a name="api"></a>API

- [Preferências de código-fonte](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Preferences)
- [Documentação da API de preferências](xref:Xamarin.Essentials.Preferences)
