---
title: 'Xamarin.Essentials: Auxiliares de sistema de arquivo'
description: A classe de sistema de arquivos Xamarin.Essentials contém uma série de auxiliares para localizar o cache do aplicativo e os diretórios de dados e abrir arquivos dentro do pacote do aplicativo.
ms.assetid: B3EC2DE0-EFC0-410C-AF71-7410AE84CF84
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 13293ec05261cbdc1e70fd278002d1af18654851
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782579"
---
# <a name="xamarinessentials-file-system-helpers"></a>Xamarin.Essentials: Auxiliares de sistema de arquivo

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **FileSystem** classe contém uma série de auxiliares para localizar os diretórios de dados e de cache do aplicativo e abrir arquivos dentro do pacote do aplicativo.

## <a name="using-file-system-helpers"></a>Usando auxiliares do sistema de arquivos

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Para obter o diretório do aplicativo para armazenar **dados armazenados em cache**. Dados de cache podem ser usados para todos os dados que precisam persistir mais tempo do que os dados temporários, mas não devem ser dados que é necessário para operar corretamente.

```csharp
var cacheDir = FileSystem.CacheDirectory;
```

Para obter o diretório de nível superior do aplicativo para todos os arquivos que não são arquivos de dados do usuário. Esses arquivos são feitos com o sistema operacional do framework de sincronização. Ver os detalhes de implementação de plataforma abaixo.

```csharp
var mainDir = FileSystem.AppDataDirectory;
```

Para abrir um arquivo que está incluído no pacote do aplicativo:

```csharp
 using (var stream = await FileSystem.OpenAppPackageFileAsync(templateFileName))
 {
    using (var reader = new StreamReader(stream))
    {
        var fileContents = await reader.ReadToEndAsync();
    }
 }
```

## <a name="platform-implementation-specifics"></a>Detalhes de implementação de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

- **CacheDirectory** – retorna a [CacheDir](https://developer.android.com/reference/android/content/Context.html#getCacheDir) do contexto atual.
- **AppDataDirectory** – retorna a [FilesDir](https://developer.android.com/reference/android/content/Context.html#getFilesDir) do contexto atual e tem o backup usando [Backup automático](https://developer.android.com/guide/topics/data/autobackup.html) iniciando no API 23 e acima.

Adicionar qualquer arquivo para o **ativos** pasta do Android do projeto e marcar a ação de compilação como **AndroidAsset** para usá-lo com `OpenAppPackageFileAsync`.

# <a name="iostabios"></a>[iOS](#tab/ios)

- **CacheDirectory** – retorna a [biblioteca/Caches](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) directory.
- **AppDataDirectory** – retorna a [biblioteca](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) directory feito pelo iTunes e iCloud.

Adicionar qualquer arquivo para o **recursos** pasta no iOS do projeto e marcar a ação de compilação como **BundledResource** para usá-lo com `OpenAppPackageFileAsync`.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- **CacheDirectory** – retorna a [LocalCacheFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localcachefolder#Windows_Storage_ApplicationData_LocalCacheFolder) directory.
- **AppDataDirectory** – retorna a [LocalFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localfolder#Windows_Storage_ApplicationData_LocalFolder) diretório de backup para a nuvem.

Adicionar qualquer arquivo na raiz do projeto UWP e marcar a ação de compilação como **conteúdo** para usá-lo com `OpenAppPackageFileAsync`.

--------------

## <a name="api"></a>API

- [Código-fonte arquivos auxiliares do sistema](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/FileSystem)
- [Documentação da API do sistema de arquivos](xref:Xamarin.Essentials.FileSystem)
