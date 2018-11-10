---
title: 'Xamarin.Essentials: Auxiliares de sistema de arquivos'
description: A classe FileSystem no Xamarin.Essentials contém uma série de auxiliares para localizar o cache e os diretórios de dados do aplicativo e abrir arquivos dentro do pacote de aplicativos.
ms.assetid: B3EC2DE0-EFC0-410C-AF71-7410AE84CF84
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 74c2066f673d27cf23af139380b45cd4223b1f30
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675478"
---
# <a name="xamarinessentials-file-system-helpers"></a>Xamarin.Essentials: Auxiliares de sistema de arquivos

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

A classe **FileSystem** contém uma série de auxiliares para localizar o cache e os diretórios de dados do aplicativo e abrir arquivos dentro do pacote de aplicativos.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-file-system-helpers"></a>Como usar os auxiliares de sistema de arquivos

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

Para fazer com que o diretório do aplicativo armazene **dados de cache**. Os dados de cache podem ser usados para quaisquer dados que precisem permanecer mais tempo do que os dados temporários, mas que não devem ser dados necessários para uma operação correta.

```csharp
var cacheDir = FileSystem.CacheDirectory;
```

Para obter o diretório de nível superior do aplicativo para todos os arquivos que não são arquivos de dados do usuário. O backup desses arquivos ocorre na estrutura de sincronização do sistema operacional. Veja abaixo as particularidades de implementação da plataforma.

```csharp
var mainDir = FileSystem.AppDataDirectory;
```

Para abrir um arquivo que está incluído no pacote de aplicativos:

```csharp
 using (var stream = await FileSystem.OpenAppPackageFileAsync(templateFileName))
 {
    using (var reader = new StreamReader(stream))
    {
        var fileContents = await reader.ReadToEndAsync();
    }
 }
```

## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

- **CacheDirectory** – Retorna o [CacheDir](https://developer.android.com/reference/android/content/Context.html#getCacheDir) do contexto atual.
- **AppDataDirectory** – Retorna o [FilesDir](https://developer.android.com/reference/android/content/Context.html#getFilesDir) do contexto atual e realiza o backup usando o [Backup Automático](https://developer.android.com/guide/topics/data/autobackup.html) a partir da API 23 e acima.

Adicione qualquer arquivo à pasta **Ativos** no projeto do Android e marque a Ação de Compilação como **AndroidAsset** para usá-la com `OpenAppPackageFileAsync`.

# <a name="iostabios"></a>[iOS](#tab/ios)

- **CacheDirectory** – Retorna o diretório [Library/Caches](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).
- **AppDataDirectory** – Retorna o diretório [Library](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) do qual foi feito backup pelo iTunes e iCloud.

Adicione qualquer arquivo à pasta **Resources** no projeto do iOS e marque a Ação de Compilação como **BundledResource** para usá-la com `OpenAppPackageFileAsync`.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- **CacheDirectory** – Retorna o diretório [LocalCacheFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localcachefolder#Windows_Storage_ApplicationData_LocalCacheFolder)...
- **AppDataDirectory** – Retorna o diretório [LocalFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localfolder#Windows_Storage_ApplicationData_LocalFolder) do qual foi feito backup na nuvem.

Adicionar qualquer arquivo à raiz do projeto UWP e marque a Ação de Compilação como **Conteúdo** para usá-lo com `OpenAppPackageFileAsync`.

--------------

## <a name="api"></a>API

- [Código-fonte de Auxiliares do sistema de arquivos](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/FileSystem)
- [Documentação da API do Sistema de Arquivos](xref:Xamarin.Essentials.FileSystem)
