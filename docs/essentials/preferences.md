---
title: 'Xamarin.Essentials: preferências'
description: Este documento descreve a classe de preferências Xamarin.Essentials, que salva as preferências de aplicativo em um repositório de chave/valor. Ele aborda como usar a classe e os tipos de dados que podem ser armazenados.
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 4a45587c79cfbbcd1198f100915e698289f74950
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353744"
---
# <a name="xamarinessentials-preferences"></a>Xamarin.Essentials: preferências

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **preferências** classe ajuda a armazenar as preferências de aplicativo em um armazenamento de chave/valor.

## <a name="using-preferences"></a>Usando as preferências

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Para salvar um valor para um determinado _chave_ nas preferências:

```csharp
Preferences.Set("my_key", "my_value");
```

Para recuperar um valor de preferências ou um padrão se não for definido:

```csharp
var myValue = Preferences.Get("my_key", "default_value");
```

Para remover o _chave_ das preferências:

```csharp
Preferences.Remove("my_key");
```

Para remover todas as preferências de:

```csharp
Preferences.Clear();
```

Além desses métodos utilizar cada um em um recurso opcional `sharedName` que pode ser usado para criar recipientes adicionais de preferência. Leia as especificidades da implementação de plataforma abaixo.

## <a name="supported-data-types"></a>Tipos de dados com suporte

Os seguintes tipos de dados têm suporte no **preferências**:

- **bool**
- **double**
- **int**
- **float**
- **long**
- **string**
- **DateTime**

## <a name="implementation-details"></a>Detalhes de implementação

Os valores de `DateTime` são armazenados em um formato binário de 64 bits (inteiro longo) usando dois métodos definidos pelo `DateTime` classe: os [ `ToBinary` ](xref:System.DateTime.ToBinary) método é usado para codificar o `DateTime` valor e o [ `FromBinary` ](xref:System.DateTime.FromBinary(System.Int64)) método decodifica o valor. Consulte a documentação desses métodos para ajustes que podem ser feitos para decodificada valores quando um `DateTime` é armazenado, ou seja, não é um valor de tempo Universal Coordenado (UTC).

## <a name="platform-implementation-specifics"></a>Particularidades de implementação de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Todos os dados são armazenados no [preferências compartilhadas](https://developer.android.com/training/data-storage/shared-preferences.html). Se nenhum `sharedName` for especificado, as preferências compartilhadas padrão são usadas, caso contrário, o nome é usado para obter uma **privada** compartilhado preferências com o nome especificado.

# <a name="iostabios"></a>[iOS](#tab/ios)

[NSUserDefaults](https://docs.microsoft.com/en-us/xamarin/ios/app-fundamentals/user-defaults) é usado para armazenar valores em dispositivos iOS. Se nenhum `sharedName` for especificado o `StandardUserDefaults` são usados, ou o nome será usado para criar uma nova `NSUserDefaults` com o nome especificado usado para o `NSUserDefaultsType.SuiteName`.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[ApplicationDataContainer](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdatacontainer) é usado para armazenar os valores no dispositivo. Se nenhum `sharedName` for especificado as `LocalSettings` são usados, ou o nome será usado para criar um novo contêiner dentro de `LocalSettings`.

--------------

## <a name="persistence"></a>Persistência

Desinstalar o aplicativo fará com que todos os _preferências_ a ser removido. Há uma exceção a isso, o que, para aplicativos que se destinam e executam no Android 6.0 (API nível 23) ou posterior, que usam [ __Backup automático__](https://developer.android.com/guide/topics/data/autobackup). Esse recurso é ativado por padrão e preserva os dados do aplicativo, incluindo __preferências compartilhadas__, que é o que o **preferências** utiliza a API. É possível desabilitar isso pelo seguinte do Google [documentação](https://developer.android.com/guide/topics/data/autobackup).

## <a name="limitations"></a>Limitações

Ao armazenar uma cadeia de caracteres, essa API destina-se para armazenar pequenas quantidades de texto.  Desempenho pode ser abaixo da média se você tentar usá-lo para armazenar grandes quantidades de texto.

## <a name="api"></a>API

- [Preferências de código-fonte](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Preferences)
- [Documentação da API de preferências](xref:Xamarin.Essentials.Preferences)
