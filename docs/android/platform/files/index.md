---
title: Acesso de arquivo com o xamarin. Android
description: Este guia explicará o acesso a arquivos no xamarin. Android
ms.prod: xamarin
ms.assetid: FC1CFC58-B799-4DD6-8ED1-DE36B0E56856
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/23/2018
ms.openlocfilehash: 2978f0b2bcbdd463876784a9addd7dec055b8af9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60949407"
---
# <a name="file-storage-and-access-with-xamarinandroid"></a>O armazenamento de arquivos e acesso com o xamarin. Android

Um requisito comum para aplicativos Android é manipular arquivos &ndash; salvando figuras, baixando documentos ou exportar dados para compartilhar com outros programas. Android (que é baseado no Linux) dá suporte a isso, fornecendo espaço para armazenamento de arquivos. Android agrupa o sistema de arquivos em dois tipos diferentes de armazenamento:

* **Armazenamento interno** &ndash; esta é uma parte do sistema de arquivos que pode ser acessado somente pelo aplicativo ou sistema operacional.
* **Armazenamento externo** &ndash; trata de uma partição para o armazenamento de arquivos que pode ser acessado por todos os aplicativos, o usuário e possivelmente outros dispositivos. Em alguns dispositivos, o armazenamento externo pode ser removível (como um cartão SD).

Esses agrupamentos são conceituais apenas e não necessariamente se referem a uma única partição ou o diretório no dispositivo. Um dispositivo Android sempre fornecerá partição para armazenamento interno e o armazenamento externo. É possível que alguns dispositivos podem ter várias partições que são consideradas como sendo o armazenamento externo. Independentemente da partição, as APIs para leitura, gravação ou criação de arquivos é o mesmo. Há dois conjuntos de APIs que um aplicativo xamarin. Android pode usar para acessar arquivos:

1. **As APIs do .NET (fornecido pelo, Mono e encapsulado pelo xamarin. Android)** &ndash; incluem o [auxiliares do sistema de arquivos](~/essentials/file-system-helpers.md?context=xamarin/android) fornecidas pelo [Xamarin.Essentials](~/essentials/index.md?context=xamarin/android). As APIs do .NET fornecem a melhor compatibilidade de plataforma cruzada e como tal, o foco deste guia será essas APIs.
1. **O acesso ao arquivo Java nativo APIs (fornecido pelo Java e encapsulado pelo xamarin. Android)** &ndash; Java fornece suas próprias APIs para ler e gravar arquivos. Eles são uma alternativa totalmente aceitável para as APIs do .NET, mas são específicos do Android e não são adequados para aplicativos que devem ser de plataforma cruzada.

Lendo e gravando em arquivos é quase idêntico no xamarin. Android quanto de qualquer outro aplicativo .NET. O aplicativo xamarin. Android determina o caminho para o arquivo que será manipulado, em seguida, usa padrão .NET expressões para acesso a arquivos. Como os caminhos reais para armazenamento interno e externo podem variar de um dispositivo para o dispositivo ou de versão do Android à versão do Android, não é recomendável para codificar o caminho para os arquivos. Em vez disso, use as APIs Xamarin.Android para determinar o caminho para arquivos. Dessa forma, as APIs do .NET para ler e gravar arquivos expõe as APIs do Android nativo que ajudarão a determinar o caminho para arquivos no armazenamento interno e externo.

Antes de discutir as APIs envolvidas com o acesso de arquivo, é importante entender alguns dos detalhes que envolvem o armazenamento interno e externo. Isso será discutido na próxima seção.

## <a name="internal-vs-external-storage"></a>Armazenamento externo de vs interno

Conceitualmente, o armazenamento interno e externo são muito semelhantes &ndash; são ambos os locais em que um aplicativo xamarin. Android pode salvar arquivos. Essa semelhança pode ser confusa para desenvolvedores que não estão familiarizados com o Android que não está claro quando um aplicativo deve usar o armazenamento externo de vs de armazenamento interno.

Armazenamento interno refere-se a memória não-volátil Android aloca para o sistema operacional, APKs e para aplicativos individuais. Esse espaço não é acessível, exceto pelo sistema operacional ou aplicativos. Android alocará um diretório na partição de armazenamento interno para cada aplicativo. Quando o aplicativo é desinstalado, todos os arquivos que são mantidos no armazenamento interno no diretório também serão excluídos. Armazenamento interno é mais adequado para arquivos que só são acessíveis para o aplicativo e que não serão compartilhadas com outros aplicativos ou terão muito valor depois que o aplicativo é desinstalado. No Android 6.0 ou superior, arquivos no armazenamento interno podem ser backup automaticamente pelo Google usando o [recurso de Backup automático no Android 6.0](https://developer.android.com/guide/topics/data/autobackup). Armazenamento interno tem as seguintes desvantagens:

* Arquivos não podem ser compartilhados.
* Arquivos serão excluídos quando o aplicativo é desinstalado.
* O espaço disponível no armazenamento interno talvez limitado.

Armazenamento externo refere-se ao armazenamento de arquivos que não seja o armazenamento interno e não exclusivamente acessível a um aplicativo. O objetivo principal de armazenamento externo é fornecer um local para colocar os arquivos que devem ser compartilhados entre aplicativos ou que são muito grande para caber na memória interna. A vantagem de armazenamento externo é que, normalmente, ela tem muito mais espaço para arquivos que o armazenamento interno. No entanto, o armazenamento externo não é garantido sempre estar presente em um dispositivo e pode exigir permissão especial do usuário para acessá-lo.

> [!NOTE]
> Para dispositivos que dão suporte a vários usuários, Android fornece a cada usuário seu próprio diretório no armazenamento interno e externo. Esse diretório é inacessível para outros usuários no dispositivo. Essa separação é invisível para aplicativos, desde que eles fazem não codificar caminhos para arquivos no armazenamento interno ou externo.

Como regra geral, aplicativos xamarin. Android devem preferir salvando seus arquivos no armazenamento interno quando ela é razoável e se baseiam no armazenamento externo quando os arquivos precisam ser compartilhados com outros aplicativos, são muito grandes ou devem ser mantidos, mesmo se o aplicativo é desinstalado. Por exemplo, um arquivo de configuração é ideal para um armazenamento interno que ela tenha sem importância, exceto para o aplicativo que o cria.  Em contraste, as fotos são um bom candidato para armazenamento externo. Eles podem ser muito grandes e, em muitos casos o usuário talvez queira compartilhá-los ou acessá-los, mesmo se o aplicativo é desinstalado.

Este guia se concentrará no armazenamento interno. Consulte o guia [armazenamento externo](~/android/platform/files/external-storage.md) para obter detalhes sobre como usar o armazenamento externo em um aplicativo xamarin. Android.

## <a name="working-with-internal-storage"></a>Trabalhar com o armazenamento interno

O diretório de armazenamento interno para um aplicativo é determinado pelo sistema operacional e é exposto aos aplicativos do Android pelo `Android.Content.Context.FilesDir` propriedade. Isso retornará um `Java.IO.File` objeto que representa o diretório que o Android tem dedicada exclusivamente para o aplicativo.  Por exemplo, um aplicativo com o nome do pacote **com.companyname** o diretório de armazenamento interno pode ser:

```bash
/data/user/0/com.companyname/files
```

Este documento fará referência para o diretório de armazenamento interno como _INTERNAL\_armazenamento_.

> [!IMPORTANT]
> O caminho exato para o diretório de armazenamento interno pode variar de dispositivo para o dispositivo e entre versões do Android. Por isso, aplicativos devem não arduamente o caminho para o diretório de armazenamento de arquivos internos de código e em vez disso, use as APIs de xamarin. Android, como `System.Environment.GetFolderPath()`.

Para maximizar o compartilhamento de código, os aplicativos xamarin. Android (ou aplicativos xamarin. Forms com foco em xamarin. Android) devem usar o [ `System.Environment.GetFolderPath()` ](xref:System.Environment.GetFolderPath*) método. No xamarin. Android, esse método retornará uma cadeia de caracteres para um diretório que é o mesmo local que `Android.Content.Context.FilesDir`. Esse método usa uma enum, `System.Environment.SpecialFolder`, que é usado para identificar um conjunto de constantes enumeradas que representam os caminhos de pastas especiais usados pelo sistema operacional. Nem todos os `System.Environment.SpecialFolder` valores serão mapeadas para um diretório válido no xamarin. Android. A tabela a seguir descreve o caminho pode ser esperado para um determinado valor de `System.Environment.SpecialFolder`:

| System.Environment.SpecialFolder | Caminho  |
|----------------------|---|
| `ApplicationData` | **_INTERNAL\_STORAGE_/.config** |
| `Desktop` | **_INTERNAL\_STORAGE_/Desktop** |
| `LocalApplicationData` | **_INTERNAL\_STORAGE_/.local/share** |
| `MyDocuments` | **_INTERNAL\_STORAGE_** |
| `MyMusic` | **_INTERNAL\_STORAGE_/Music** |
| `MyPictures` | **_INTERNAL\_STORAGE_/Pictures** |
| `MyVideos` | **_INTERNAL\_STORAGE_/Videos** |
| `Personal` | **_INTERNAL\_STORAGE_** |


### <a name="reading-or-writing-to-files-on-internal-storage"></a>Lendo ou gravando em arquivos no armazenamento interno

Qualquer um dos [ C# APIs para gravação](https://docs.microsoft.com/dotnet/csharp/programming-guide/file-system/how-to-write-to-a-text-file) em um arquivo são suficientes; tudo o que é necessário é obter o caminho para o arquivo que está no diretório alocado para o aplicativo. É altamente recomendável que o async versões das APIs do .NET são usadas para minimizar quaisquer problemas que podem ser associar com acesso a arquivos bloquear o thread principal.

Este trecho de código é um exemplo de como escrever um número inteiro em um arquivo de texto UTF-8 para o diretório de armazenamento interno de um aplicativo:

```csharp
public async Task SaveCountAsync(int count)
{
    var backingFile = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "count.txt");
    using (var writer = File.CreateText(backingFile))
    {
        await writer.WriteLineAsync(count.ToString());
    }
}
```

O próximo trecho de código fornece uma maneira de ler um valor inteiro que foi armazenado em um arquivo de texto:

```csharp
public async Task<int> ReadCountAsync()
{
    var backingFile = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), "count.txt");

    if (backingFile == null || !File.Exists(backingFile))
    {
        return 0;
    }

    var count = 0;
    using (var reader = new StreamReader(backingFile, true))
    {
        string line;
        while ((line = await reader.ReadLineAsync()) != null)
        {
            if (int.TryParse(line, out var newcount))
            {
                count = newcount;
            }
        }
    }

    return count;
}
```

## <a name="using--xamarinessentials-ndash-file-system-helpers"></a>Usando Xamarin.Essentials &ndash; auxiliares do sistema de arquivos

[Xamarin.Essentials](~/essentials/file-system-helpers.md?context=xamarin/android) é um conjunto de APIs para escrever código de plataforma cruzada compatível. O [auxiliares do sistema de arquivo](~/essentials/file-system-helpers.md?context=xamarin/android) é uma classe que contém uma série de auxiliares para simplificar a localização de diretórios de cache e os dados do aplicativo. Este trecho de código fornece um exemplo de como localizar o diretório de armazenamento interno e o diretório de cache para um aplicativo:

```csharp
// Get the path to a file on internal storage
var backingFile = Path.Combine(Xamarin.Essentials.FileSystem.AppDataDirectory, "count.txt");

// Get the path to a file in the cache directory
var cacheFile = Path.Combine(Xamarin.Essentials.FileSystem.CacheDirectory, "count.txt");
```

## <a name="hiding-files-from-the-mediastore"></a>Ocultar os arquivos a partir de `MediaStore`

O `MediaStore` é um componente do Android que coleta metadados sobre arquivos de mídia (vídeos, música, imagens) em um dispositivo Android. Sua finalidade é simplificar o compartilhamento desses arquivos em todos os aplicativos Android no dispositivo.

Arquivos privados não aparecerá como mídia podem ser compartilhadas. Por exemplo, se um aplicativo salva uma imagem em seu armazenamento externo privado, em seguida, esse arquivo será não captado pelo scanner de mídia (`MediaStore`).

Arquivos públicos serão captados pelo `MediaStore`. Os diretórios que têm um nome de arquivo zero bytes **. NOMEDIA** não serão verificados pelo `MediaStore`.

## <a name="related-links"></a>Links relacionados

* [Armazenamento externo](~/android/platform/files/external-storage.md)
* [Salvar arquivos no armazenamento do dispositivo](https://developer.android.com/training/data-storage/files)
* [Auxiliares do sistema de arquivo Xamarin.Essentials](~/essentials/file-system-helpers.md?context=xamarin/android)
* [Dados de usuário de backup com Backup automático](https://developer.android.com/guide/topics/data/autobackup)
* [Armazenamento de adoção](https://source.android.com/devices/storage/adoptable)
