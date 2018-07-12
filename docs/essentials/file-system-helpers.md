---
title: 'Xamarin.Essentials: Auxiliares do sistema de arquivo'
description: A classe de sistema de arquivos no Xamarin.Essentials contém uma série de auxiliares para localizar o cache do aplicativo e diretórios de dados e abrir arquivos dentro do pacote de aplicativo.
ms.assetid: B3EC2DE0-EFC0-410C-AF71-7410AE84CF84
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 13293ec05261cbdc1e70fd278002d1af18654851
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815612"
---
# <a name="xamarinessentials-file-system-helpers"></a>Xamarin.Essentials: Auxiliares do sistema de arquivo

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **FileSystem** classe contém uma série de auxiliares para localizar os diretórios de cache e os dados do aplicativo e abrir arquivos dentro do pacote de aplicativo.

## <a name="using-file-system-helpers"></a>Uso de auxiliares de sistema de arquivo

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Para obter o diretório do aplicativo para armazenar **armazenar em cache dados**. Dados de cache podem ser usados para todos os dados que precisa manter mais de dados temporários, mas não devem ser dados que são necessários para operar corretamente.

```csharp
var cacheDir = FileSystem.CacheDirectory;
```

Para obter o diretório de nível superior do aplicativo para todos os arquivos que não são arquivos de dados do usuário. Esses arquivos são salvos com o sistema operacional do framework de sincronização. Ver detalhes de implementação de plataforma abaixo.

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

## <a name="platform-implementation-specifics"></a>Particularidades de implementação de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

- **CacheDirectory** – retorna o [CacheDir](https://developer.android.com/reference/android/content/Context.html#getCacheDir) do contexto atual.
- **AppDataDirectory** – retorna o [FilesDir](https://developer.android.com/reference/android/content/Context.html#getFilesDir) do contexto atual e tem o backup usando [Backup automático](https://developer.android.com/guide/topics/data/autobackup.html) começando no API 23 e acima.

Adicionar qualquer arquivo para o **ativos** pasta no Android do projeto e marcar a ação de compilação como **AndroidAsset** usá-lo com `OpenAppPackageFileAsync`.

# <a name="iostabios"></a>[iOS](#tab/ios)

- **CacheDirectory** – retorna o [biblioteca/Caches](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) directory.
- **AppDataDirectory** – retorna o [biblioteca](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) diretório que foi feito backup pelo iTunes e iCloud.

Adicionar qualquer arquivo para o **recursos** pasta no iOS do projeto e marcar a ação de compilação como **BundledResource** usá-lo com `OpenAppPackageFileAsync`.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- **CacheDirectory** – retorna o [LocalCacheFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localcachefolder#Windows_Storage_ApplicationData_LocalCacheFolder) diretório...
- **AppDataDirectory** – retorna o [LocalFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localfolder#Windows_Storage_ApplicationData_LocalFolder) diretório cujo backup é feito para a nuvem.

Adicionar qualquer arquivo na raiz do projeto UWP e marcar a ação de compilação como **conteúdo** usá-lo com `OpenAppPackageFileAsync`.

--------------

## <a name="api"></a>API

- [Código-fonte arquivos auxiliares do sistema](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/FileSystem)
- [Documentação da API do sistema de arquivos](xref:Xamarin.Essentials.FileSystem)
