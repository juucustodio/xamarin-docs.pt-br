---
title: Acesso a arquivos com Xamarin.Android
description: Este guia explicará o acesso ao arquivo em Xamarin.Android
ms.prod: xamarin
ms.assetid: FC1CFC58-B799-4DD6-8ED1-DE36B0E56856
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/23/2018
ms.openlocfilehash: 1bb0fae73a1e3647cdc0e3266c7b44ac04fcc1ee
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79304404"
---
# <a name="file-storage-and-access-with-xamarinandroid"></a>Armazenamento e acesso de arquivos com Xamarin.Android

Um requisito comum para aplicativos &ndash; Android é manipular arquivos salvando imagens, baixando documentos ou exportando dados para compartilhar com outros programas. O Android (que é baseado no Linux) suporta isso, fornecendo espaço para armazenamento de arquivos. O Android agrupa o sistema de arquivos em dois tipos diferentes de armazenamento:

* **Armazenamento** &ndash; interno Esta é uma parte do sistema de arquivos que pode ser acessada apenas pelo aplicativo ou pelo sistema operacional.
* **Armazenamento** &ndash; externo Esta é uma partição para o armazenamento de arquivos que é acessível por todos os aplicativos, pelo usuário e possivelmente por outros dispositivos. Em alguns dispositivos, o armazenamento externo pode ser removível (como um cartão SD).

Esses agrupamentos são apenas conceituais e não necessariamente se referem a uma única partição ou diretório no dispositivo. Um dispositivo Android sempre fornecerá partição para armazenamento interno e armazenamento externo. É possível que certos dispositivos possam ter várias partições que são consideradas de armazenamento externo. Independentemente da partição, as APIs para leitura, escrita ou criação de arquivos são as mesmas. Existem dois conjuntos de APIs que um aplicativo Xamarin.Android pode usar para acesso a arquivos:

1. **As APIs .NET (fornecidas pela Mono e embrulhadas por Xamarin.Android)** &ndash; Incluem os [ajudantes](~/essentials/file-system-helpers.md?context=xamarin/android) do sistema de arquivos fornecidos pela [Xamarin.Essentials](~/essentials/index.md?context=xamarin/android). As APIs .NET fornecem a melhor compatibilidade entre plataformas e, como tal, o foco deste guia estará nessas APIs.
1. **As APIs de acesso a arquivos Java nativas (fornecidas por Java e embrulhadas por Xamarin.Android)** &ndash; Java fornece suas próprias APIs para leitura e escrita de arquivos. Estas são uma alternativa completamente aceitável para as APIs .NET, mas são específicas para o Android e não são adequadas para aplicativos que se destinam a ser multiplataforma.

Ler e escrever em arquivos é quase idêntico no Xamarin.Android como é para qualquer outro aplicativo .NET. O aplicativo Xamarin.Android determina o caminho para o arquivo que será manipulado e, em seguida, usa expressões padrão .NET para acesso a arquivos. Como os caminhos reais para o armazenamento interno e externo podem variar de dispositivo para dispositivo ou da versão do Android para a versão Android, não é recomendado codificar o caminho para os arquivos. Em vez disso, use as APIs Xamarin.Android para determinar o caminho para arquivos. Dessa forma, as APIs .NET para leitura e gravação de arquivos expõem as APIs nativas do Android que ajudarão a determinar o caminho para arquivos no armazenamento interno e externo.

Antes de discutir as APIs envolvidas com o acesso a arquivos, é importante entender alguns dos detalhes em torno do armazenamento interno e externo. Isso será discutido na próxima seção.

## <a name="internal-vs-external-storage"></a>Armazenamento interno versus externo

Conceitualmente, o armazenamento interno e &ndash; o armazenamento externo são muito semelhantes, ambos são lugares em que um aplicativo Xamarin.Android pode salvar arquivos. Essa semelhança pode ser confusa para desenvolvedores que não estão familiarizados com o Android, pois não está claro quando um aplicativo deve usar armazenamento interno versus armazenamento externo.

O armazenamento interno refere-se à memória não volátil que o Android aloca para o sistema operacional, APKs e para aplicativos individuais. Este espaço não é acessível, exceto pelo sistema operacional ou aplicativos. O Android alocará um diretório na partição de armazenamento interno para cada aplicativo. Quando o aplicativo for desinstalado, todos os arquivos mantidos no armazenamento interno nesse diretório também serão excluídos. O armazenamento interno é mais adequado para arquivos que só são acessíveis ao aplicativo e que não serão compartilhados com outros aplicativos ou terão muito pouco valor quando o aplicativo for desinstalado. No Android 6.0 ou superior, os arquivos no armazenamento interno podem ser automaticamente copiados pelo Google usando o [recurso Auto Backup no Android 6.0](https://developer.android.com/guide/topics/data/autobackup). O armazenamento interno tem as seguintes desvantagens:

* Os arquivos não podem ser compartilhados.
* Os arquivos serão excluídos quando o aplicativo for desinstalado.
* O espaço disponível no armazenamento interno talvez seja limitado.

O armazenamento externo refere-se ao armazenamento de arquivos que não é armazenamento interno e não é exclusivamente acessível a um aplicativo. O objetivo principal do armazenamento externo é fornecer um lugar para colocar arquivos que devem ser compartilhados entre aplicativos ou que sejam muito grandes para caber no armazenamento interno. A vantagem do armazenamento externo é que ele normalmente tem muito mais espaço para arquivos do que armazenamento interno. No entanto, nem sempre o armazenamento externo é garantido estar presente em um dispositivo e pode exigir permissão especial do usuário para acessá-lo.

> [!NOTE]
> Para dispositivos que suportam vários usuários, o Android fornecerá a cada usuário seu próprio diretório sobre armazenamento interno e externo. Este diretório é inacessível para outros usuários no dispositivo. Essa separação é invisível para os aplicativos, desde que eles não code caminhos rígidos para arquivos em armazenamento interno ou externo.

Como regra geral, os aplicativos Xamarin.Android devem preferir salvar seus arquivos no armazenamento interno quando for razoável, e contar com armazenamento externo quando os arquivos precisam ser compartilhados com outros aplicativos, são muito grandes ou devem ser retidos mesmo que o aplicativo seja desinstalado. Por exemplo, um arquivo de configuração é mais adequado para um armazenamento interno, pois não tem importância, exceto para o aplicativo que o cria.  Em contraste, as fotos são um bom candidato para armazenamento externo. Eles podem ser muito grandes e, em muitos casos, o usuário pode querer compartilhá-los ou acessá-los mesmo se o aplicativo estiver desinstalado.

Este guia se concentrará no armazenamento interno. Consulte o guia [Armazenamento externo](~/android/platform/files/external-storage.md) para obter detalhes sobre como usar o armazenamento externo em um aplicativo Xamarin.Android.

## <a name="working-with-internal-storage"></a>Trabalhando com armazenamento interno

O diretório de armazenamento interno de um aplicativo é determinado pelo sistema `Android.Content.Context.FilesDir` operacional, e é exposto a aplicativos Android pela propriedade. Isso retornará `Java.IO.File` um objeto representando o diretório que o Android dedicou exclusivamente para o aplicativo.  Por exemplo, um aplicativo com o nome do pacote **com.companyname** o diretório de armazenamento interno pode ser:

```bash
/data/user/0/com.companyname/files
```

Este documento se refere ao diretório de armazenamento interno como _ARMAZENAMENTO INTERNO\__.

> [!IMPORTANT]
> O caminho exato para o diretório de armazenamento interno pode variar de dispositivo para dispositivo e entre versões do Android. Por causa disso, os aplicativos não devem codificar o caminho para o diretório de armazenamento de `System.Environment.GetFolderPath()`arquivos internos e, em vez disso, usar as APIs Xamarin.Android, tais como .

Para maximizar o compartilhamento de código, os aplicativos Xamarin.Android (ou Xamarin.Forms, que visam o Xamarin.Android) devem usar o [`System.Environment.GetFolderPath()`](xref:System.Environment.GetFolderPath*) método. No Xamarin.Android, este método retornará uma seqüência para `Android.Content.Context.FilesDir`um diretório que é o mesmo local que . Este método requer um `System.Environment.SpecialFolder`enum, que é usado para identificar um conjunto de constantes enumeradas que representam os caminhos das pastas especiais utilizadas pelo sistema operacional. Nem todos `System.Environment.SpecialFolder` os valores serão mapeados para um diretório válido no Xamarin.Android. A tabela a seguir descreve qual caminho pode `System.Environment.SpecialFolder`ser esperado para um determinado valor de :

| System.Environment.SpecialFolder | Caminho  |
|----------------------|---|
| `ApplicationData` | **_ARMAZENAMENTO\_INTERNO_/.config** |
| `Desktop` | **_ARMAZENAMENTO\_INTERNO_/Desktop** |
| `LocalApplicationData` | **_ARMAZENAMENTO\_INTERNO_/.local/share** |
| `MyDocuments` | **_ARMAZENAMENTO\_INTERNO_** |
| `MyMusic` | **_ARMAZENAMENTO\_INTERNO_/Música** |
| `MyPictures` | **_ARMAZENAMENTO\_INTERNO_/Imagens** |
| `MyVideos` | **_ARMAZENAMENTO\_INTERNO_/Vídeos** |
| `Personal` | **_ARMAZENAMENTO\_INTERNO_** |

### <a name="reading-or-writing-to-files-on-internal-storage"></a>Leitura ou escrita para arquivos sobre armazenamento interno

Qualquer das [APIs C# para escrever](https://docs.microsoft.com/dotnet/csharp/programming-guide/file-system/how-to-write-to-a-text-file) em um arquivo são suficientes; tudo o que é necessário é obter o caminho para o arquivo que está no diretório alocado para o aplicativo. É fortemente recomendável que as versões assincronias das APIs .NET sejam usadas para minimizar quaisquer problemas que possam estar associados ao bloqueio do acesso ao arquivo no segmento principal.

Este trecho de código é um exemplo de escrever um inteiro em um arquivo de texto UTF-8 para o diretório de armazenamento interno de um aplicativo:

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

## <a name="using--xamarinessentials-ndash-file-system-helpers"></a>Usando os ajudantes &ndash; do sistema de arquivos Xamarin.Essentials

[Xamarin.Essentials](~/essentials/file-system-helpers.md?context=xamarin/android) é um conjunto de APIs para escrever código compatível entre plataformas. O [File System Helpers](~/essentials/file-system-helpers.md?context=xamarin/android) é uma classe que contém uma série de ajudantes para simplificar a localização do cache e diretórios de dados do aplicativo. Este trecho de código fornece um exemplo de como encontrar o diretório de armazenamento interno e o diretório de cache de um aplicativo:

```csharp
// Get the path to a file on internal storage
var backingFile = Path.Combine(Xamarin.Essentials.FileSystem.AppDataDirectory, "count.txt");

// Get the path to a file in the cache directory
var cacheFile = Path.Combine(Xamarin.Essentials.FileSystem.CacheDirectory, "count.txt");
```

## <a name="hiding-files-from-the-mediastore"></a>Ocultando arquivos do`MediaStore`

O `MediaStore` é um componente Android que coleta meta dados sobre arquivos de mídia (vídeos, música, imagens) em um dispositivo Android. Seu objetivo é simplificar o compartilhamento desses arquivos em todos os aplicativos Android no dispositivo.

Arquivos privados não aparecerão como mídia compartilhável. Por exemplo, se um aplicativo salvar uma imagem em seu armazenamento externo privado, então`MediaStore`esse arquivo não será captado pelo scanner de mídia ().

Arquivos públicos serão pegos `MediaStore`por . Diretórios que têm um nome de arquivo de byte zero **. A NOMEDIA** não será `MediaStore`escaneada por .

## <a name="related-links"></a>Links relacionados

* [Armazenamento externo](~/android/platform/files/external-storage.md)
* [Salvar arquivos no armazenamento do dispositivo](https://developer.android.com/training/data-storage/files)
* [Ajudantes do sistema de arquivos Xamarin.Essentials](~/essentials/file-system-helpers.md?context=xamarin/android)
* [Backup de dados do usuário com backup automático](https://developer.android.com/guide/topics/data/autobackup)
* [Armazenamento adotável](https://source.android.com/devices/storage/adoptable)
