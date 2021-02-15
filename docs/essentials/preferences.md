---
title: 'Xamarin.Essentials: Preferências'
description: Este documento descreve a classe Preferences no Xamarin.Essentials , que salva as preferências do aplicativo em um repositório de chave/valor. Ele mostra como usar a classe e os tipos de dados que podem ser armazenados.
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
author: jamesmontemagno
ms.author: jamont
ms.date: 01/15/2019
ms.custom: video
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 07bfcabc7ffef20bee43531bfab3e78155beb9a9
ms.sourcegitcommit: 58247fe066ad271ee43c8967ac3301fdab6ca2d1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629568"
---
# <a name="no-locxamarinessentials-preferences"></a>Xamarin.Essentials: Preferências

A classe **Preferences** ajuda a armazenar as preferências do aplicativo em um repositório de chave/valor.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-preferences"></a>Como usar Preferences

Adicione uma referência a Xamarin.Essentials em sua classe:

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

Para verificar se uma determinada _chave_ existe nas preferências:

```csharp
bool hasKey = Preferences.ContainsKey("my_key");
```

Para remover a _chave_ das preferências:

```csharp
Preferences.Remove("my_key");
```

Para remover todas as preferências:

```csharp
Preferences.Clear();
```

> [!TIP]
> Os métodos acima assumem um `string` parâmetro opcional chamado `sharedName` . Esse parâmetro é usado para criar contêineres adicionais para preferências que são úteis em alguns casos de uso. Um caso de uso é quando seu aplicativo precisa compartilhar preferências entre extensões ou um aplicativo de inspeção. Leia as especificações de implementação da plataforma abaixo.

## <a name="supported-data-types"></a>Tipos de dados com suporte

Os tipos de dados a seguir têm suporte em **Preferences** :

- **bool**
- **double**
- **int**
- **float**
- **longo**
- **cadeia de caracteres**
- **DateTime**

## <a name="integrate-with-system-settings"></a>Integrar com as configurações do sistema

As preferências são armazenadas nativamente, o que permite que você integre suas configurações às configurações do sistema nativo. Siga a documentação e os exemplos da plataforma para integrar-se à plataforma:

* Apple: [implementando um pacote de configurações do IOS](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/UserDefaults/Preferences/Preferences.html)
* [Exemplo de preferências do iOS aplicativo](/samples/xamarin/ios-samples/appprefs/)
* [Configurações de watchOS](https://developer.xamarin.com/guides/ios/watch/working-with/settings/)
* Android: [introdução com telas de configurações](https://developer.android.com/guide/topics/ui/settings.html)

## <a name="implementation-details"></a>Detalhes da implementação

Os valores de `DateTime` são armazenados em um formato binário de 64 bits (inteiro longo) usando dois métodos definidos pela `DateTime` classe: o [`ToBinary`](xref:System.DateTime.ToBinary) método é usado para codificar o `DateTime` valor e o [`FromBinary`](xref:System.DateTime.FromBinary(System.Int64)) método decodifica o valor. Confira a documentação desses métodos para conhecer os ajustes que podem ser feitos em valores decodificados ao armazenar um `DateTime` que não seja um valor UTC (Tempo Universal Coordenado).

## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

# <a name="android"></a>[Android](#tab/android)

Todos os dados são armazenados em [Preferências Compartilhadas](https://developer.android.com/training/data-storage/shared-preferences.html). Se não `sharedName` for especificado, as preferências compartilhadas padrão serão usadas, caso contrário, o nome será usado para obter preferências **particulares** compartilhadas com o nome especificado.

# <a name="ios"></a>[iOS](#tab/ios)

[NSUserDefaults](../ios/app-fundamentals/user-defaults.md) é usado para armazenar valores em dispositivos iOS. Se nenhum `sharedName` for especificado, o `StandardUserDefaults` será usado, caso contrário, o nome será usado para criar uma nova `NSUserDefaults` com o nome especificado usado para o `NSUserDefaultsType.SuiteName`.

# <a name="uwp"></a>[UWP](#tab/uwp)

[ApplicationDataContainer](/uwp/api/windows.storage.applicationdatacontainer) é usado para armazenar os valores no dispositivo. Se não `sharedName` for especificado, o será `LocalSettings` usado, caso contrário, o nome será usado para criar um novo contêiner dentro de `LocalSettings` .

`LocalSettings` também tem a seguinte restrição de que o nome de cada configuração pode ter 255 caracteres de comprimento máximo. Cada configuração pode ter até 8K bytes de tamanho e cada configuração composta pode ter até 64K bytes de tamanho.

--------------

## <a name="persistence"></a>Persistência

A desinstalação do aplicativo fará com que todas as _preferências_ sejam removidas, com a exceção sendo aplicativos direcionados e executados no Android 6,0 (API nível 23) ou posterior que usam o [__backup automático__](https://developer.android.com/guide/topics/data/autobackup). Esse recurso está ativado por padrão e preserva os dados do aplicativo, incluindo __Preferências Compartilhadas__ , que é o que a API de **Preferências** utiliza. É possível desabilitar isso seguindo a [documentação](https://developer.android.com/guide/topics/data/autobackup) do Google.

## <a name="limitations"></a>Limitações

Ao armazenar uma cadeia de caracteres, essa API destina-se a armazenar pequenas quantidades de texto. O desempenho pode ser abaixo da média se você tentar usá-lo para armazenar grandes quantidades de texto.

## <a name="api"></a>API

- [Código-fonte de Preferências](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Preferences)
- [Documentação da API de Preferências](xref:Xamarin.Essentials.Preferences)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Preferences-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
