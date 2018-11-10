---
title: 'Xamarin.Essentials: Preferências'
description: Este documento descreve a classe Preferences no Xamarin.Essentials, que salva as preferências do aplicativo em um repositório de chave/valor. Ele mostra como usar a classe e os tipos de dados que podem ser armazenados.
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3562ec840f824f6a8aeed1a61c7b27985a5ddf72
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675465"
---
# <a name="xamarinessentials-preferences"></a>Xamarin.Essentials: Preferências

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

A classe **Preferences** ajuda a armazenar as preferências do aplicativo em um repositório de chave/valor.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-preferences"></a>Como usar Preferences

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

Para salvar um valor de uma determinada _chave_ nas preferências:

```csharp
Preferences.Set("my_key", "my_value");
```

Para recuperar um valor das preferências, ou um padrão, se não for definido:

```csharp
var myValue = Preferences.Get("my_key", "default_value");
```

Para remover a _chave_ das preferências:

```csharp
Preferences.Remove("my_key");
```

Para remover todas as preferências:

```csharp
Preferences.Clear();
```

Além desses métodos, cada uma pode receber um `sharedName` opcional que pode ser usado para criar contêineres adicionais para preferência. Leia a seguir as particularidades de implementação da plataforma.

## <a name="supported-data-types"></a>Tipos de dados com suporte

Os tipos de dados a seguir têm suporte em **Preferences**:

- **bool**
- **double**
- **int**
- **float**
- **long**
- **string**
- **DateTime**

## <a name="implementation-details"></a>Detalhes da implementação

Os valores de `DateTime` são armazenados em um formato binário de 64 bits (inteiro longo) usando dois métodos definidos pela classe `DateTime`: o método [`ToBinary`](xref:System.DateTime.ToBinary) é usado para codificar o valor `DateTime` e o método [`FromBinary`](xref:System.DateTime.FromBinary(System.Int64)) decodifica o valor. Confira a documentação desses métodos para conhecer os ajustes que podem ser feitos em valores decodificados ao armazenar um `DateTime` que não seja um valor UTC (Tempo Universal Coordenado).

## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

Todos os dados são armazenados em [Preferências Compartilhadas](https://developer.android.com/training/data-storage/shared-preferences.html). Se não houver um `sharedName` especificado, as preferências compartilhadas padrão serão usadas, caso contrário, o nome será usado para obter preferências compartilhadas **privadas** com o nome especificado.

# <a name="iostabios"></a>[iOS](#tab/ios)

[NSUserDefaults](https://docs.microsoft.com/en-us/xamarin/ios/app-fundamentals/user-defaults) é usado para armazenar valores em dispositivos iOS. Se nenhum `sharedName` for especificado, o `StandardUserDefaults` será usado, caso contrário, o nome será usado para criar uma nova `NSUserDefaults` com o nome especificado usado para o `NSUserDefaultsType.SuiteName`.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[ApplicationDataContainer](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdatacontainer) é usado para armazenar os valores no dispositivo. Se nenhum `sharedName` for especificado, o `LocalSettings` será usado, caso contrário, o nome será usado para criar um novo contêiner dentro do `LocalSettings`.

--------------

## <a name="persistence"></a>Persistência

A desinstalação do aplicativo causará a remoção de todas as _Preferências_. Há uma exceção a isso para aplicativos destinados ao Android 6.0 (API nível 23) ou posterior que usam [__Backup Automático__](https://developer.android.com/guide/topics/data/autobackup). Esse recurso está ativado por padrão e preserva os dados do aplicativo, incluindo __Preferências Compartilhadas__, que é o que a API de **Preferências** utiliza. É possível desabilitar isso seguindo a [documentação](https://developer.android.com/guide/topics/data/autobackup) do Google.

## <a name="limitations"></a>Limitações

Ao armazenar uma cadeia de caracteres, essa API destina-se a armazenar pequenas quantidades de texto.  O desempenho pode ser abaixo da média se você tentar usá-lo para armazenar grandes quantidades de texto.

## <a name="api"></a>API

- [Código-fonte de Preferências](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Preferences)
- [Documentação da API de Preferências](xref:Xamarin.Essentials.Preferences)
