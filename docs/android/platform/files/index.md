---
title: Acesso a arquivos com Xamarin. Android
description: Este guia explicará o acesso ao arquivo no Xamarin. Android
ms.prod: xamarin
ms.assetid: FC1CFC58-B799-4DD6-8ED1-DE36B0E56856
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/23/2018
ms.openlocfilehash: 1bb0fae73a1e3647cdc0e3266c7b44ac04fcc1ee
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020422"
---
# <a name="file-storage-and-access-with-xamarinandroid"></a>Armazenamento de arquivos e acesso com Xamarin. Android

Um requisito comum para aplicativos Android é manipular arquivos &ndash; salvar imagens, baixar documentos ou exportar dados para compartilhar com outros programas. O Android (baseado no Linux) oferece suporte ao fornecimento de espaço para o armazenamento de arquivos. O Android agrupa o sistema de arquivos em dois tipos diferentes de armazenamento:

* O **armazenamento interno** &ndash; essa é uma parte do sistema de arquivos que pode ser acessada somente pelo aplicativo ou pelo sistema operacional.
* O **armazenamento externo** &ndash; essa é uma partição para o armazenamento de arquivos que podem ser acessados por todos os aplicativos, pelo usuário e possivelmente por outros dispositivos. Em alguns dispositivos, o armazenamento externo pode ser removível (como um cartão SD).

Esses agrupamentos são apenas conceituais e não se referem necessariamente a uma única partição ou diretório no dispositivo. Um dispositivo Android sempre fornecerá partição para armazenamento interno e armazenamento externo. É possível que determinados dispositivos possam ter várias partições que são consideradas armazenamento externo. Independentemente da partição, as APIs para leitura, gravação ou criação de arquivos são as mesmas. Há dois conjuntos de APIs que um aplicativo Xamarin. Android pode usar para acesso ao arquivo:

1. **As APIs do .net (fornecidas pelo mono e encapsuladas pelo xamarin. Android)** &ndash; incluem os [auxiliares do sistema de arquivos](~/essentials/file-system-helpers.md?context=xamarin/android) fornecidos pelo [Xamarin. Essentials](~/essentials/index.md?context=xamarin/android). As APIs do .NET fornecem a melhor compatibilidade entre plataformas e, como tal, o foco deste guia estará nessas APIs.
1. **As APIs nativas de acesso a arquivo Java (fornecidas pelo Java e encapsuladas pelo Xamarin. Android)** &ndash; Java fornece suas próprias APIs para ler e gravar arquivos. Essas são uma alternativa totalmente aceitável para as APIs do .NET, mas são específicas do Android e não são adequadas para aplicativos que se destinam a serem de plataforma cruzada.

Ler e gravar em arquivos é quase idêntico no Xamarin. Android como é para qualquer outro aplicativo .NET. O aplicativo Xamarin. Android determina o caminho para o arquivo que será manipulado e, em seguida, usa os idiomas .NET padrão para acesso ao arquivo. Como os caminhos reais para o armazenamento interno e externo podem variar de dispositivo para dispositivo ou da versão do Android para a versão do Android, não é recomendável codificar o caminho para os arquivos. Em vez disso, use as APIs do Xamarin. Android para determinar o caminho para os arquivos. Dessa forma, as APIs do .NET para leitura e gravação de arquivos expõem as APIs nativas do Android que ajudarão a determinar o caminho para os arquivos no armazenamento interno e externo.

Antes de discutir as APIs envolvidas com o acesso ao arquivo, é importante entender alguns dos detalhes em relação ao armazenamento interno e externo. Isso será discutido na próxima seção.

## <a name="internal-vs-external-storage"></a>Armazenamento interno vs externo

Conceitualmente, o armazenamento interno e o armazenamento externo são muito semelhantes &ndash; são ambos locais em que um aplicativo Xamarin. Android pode salvar arquivos. Essa similaridade pode ser confusa para os desenvolvedores que não estão familiarizados com o Android, pois não estão claros quando um aplicativo deve usar armazenamento interno versus armazenamento externo.

O armazenamento interno refere-se à memória não volátil que o Android aloca para o sistema operacional, APKs e para aplicativos individuais. Esse espaço não é acessível, exceto pelo sistema operacional ou por aplicativos. O Android irá alocar um diretório na partição de armazenamento interno para cada aplicativo. Quando o aplicativo é desinstalado, todos os arquivos mantidos no armazenamento interno nesse diretório também serão excluídos. O armazenamento interno é mais adequado para arquivos que só podem ser acessados para o aplicativo e que não serão compartilhados com outros aplicativos ou terão pouco valor quando o aplicativo for desinstalado. No Android 6,0 ou superior, os arquivos no armazenamento interno podem ser submetidos a backup automaticamente pelo Google usando o [recurso de backup automático no Android 6,0](https://developer.android.com/guide/topics/data/autobackup). O armazenamento interno tem as seguintes desvantagens:

* Os arquivos não podem ser compartilhados.
* Os arquivos serão excluídos quando o aplicativo for desinstalado.
* O espaço disponível no armazenamento interno talvez seja limitado.

O armazenamento externo refere-se ao armazenamento de arquivos que não é um armazenamento interno e não é exclusivamente acessível a um aplicativo. A principal finalidade do armazenamento externo é fornecer um local para colocar os arquivos que devem ser compartilhados entre aplicativos ou que são muito grandes para caber no armazenamento interno. A vantagem do armazenamento externo é que ele normalmente tem muito mais espaço para arquivos do que o armazenamento interno. No entanto, o armazenamento externo nem sempre tem a garantia de estar presente em um dispositivo e pode exigir permissão especial do usuário para acessá-lo.

> [!NOTE]
> Para dispositivos que dão suporte a vários usuários, o Android fornecerá a cada usuário seu próprio diretório no armazenamento interno e externo. Esse diretório não pode ser acessado por outros usuários no dispositivo. Essa separação é invisível para os aplicativos, desde que não codifiquem caminhos para arquivos no armazenamento interno ou externo.

Como regra prática, os aplicativos Xamarin. Android devem preferir salvar seus arquivos no armazenamento interno quando for razoável e confiar no armazenamento externo quando os arquivos precisarem ser compartilhados com outros aplicativos, forem muito grandes ou devem ser retidos mesmo se o aplicativo for desinstalado. Por exemplo, um arquivo de configuração é mais adequado para um armazenamento interno, pois não tem importância, exceto para o aplicativo que o cria.  Por outro lado, as fotos são um bom candidato para o armazenamento externo. Eles podem ser muito grandes e, em muitos casos, o usuário talvez queira compartilhá-los ou acessá-los mesmo que o aplicativo seja desinstalado.

Este guia se concentrará no armazenamento interno. Consulte o guia [armazenamento externo](~/android/platform/files/external-storage.md) para obter detalhes sobre como usar o armazenamento externo em um aplicativo Xamarin. Android.

## <a name="working-with-internal-storage"></a>Trabalhando com armazenamento interno

O diretório de armazenamento interno para um aplicativo é determinado pelo sistema operacional e é exposto aos aplicativos Android pela propriedade `Android.Content.Context.FilesDir`. Isso retornará um objeto `Java.IO.File` que representa o diretório que o Android detectou exclusivamente para o aplicativo.  Por exemplo, um aplicativo com o nome do pacote **com. CompanyName** o diretório de armazenamento interno pode ser:

```bash
/data/user/0/com.companyname/files
```

Este documento fará referência ao diretório de armazenamento interno como _armazenamento de\_interno_.

> [!IMPORTANT]
> O caminho exato para o diretório de armazenamento interno pode variar de dispositivo para dispositivo e entre versões do Android. Por isso, os aplicativos não devem embutir em código o caminho para o diretório de armazenamento de arquivos internos e, em vez disso, usar as APIs do Xamarin. Android, como `System.Environment.GetFolderPath()`.

Para maximizar o compartilhamento de código, os aplicativos Xamarin. Android (ou aplicativos Xamarin. Forms destinados ao Xamarin. Android) devem usar o método [`System.Environment.GetFolderPath()`](xref:System.Environment.GetFolderPath*) . No Xamarin. Android, esse método retornará uma cadeia de caracteres para um diretório que é o mesmo local que `Android.Content.Context.FilesDir`. Esse método usa um enum, `System.Environment.SpecialFolder`, que é usado para identificar um conjunto de constantes enumeradas que representam os caminhos de pastas especiais usadas pelo sistema operacional. Nem todos os valores de `System.Environment.SpecialFolder` serão mapeados para um diretório válido no Xamarin. Android. A tabela a seguir descreve qual caminho pode ser esperado para um determinado valor de `System.Environment.SpecialFolder`:

| System. Environment. SpecialFolder | Caminho  |
|----------------------|---|
| `ApplicationData` | **/.Config de _armazenamento interno de\__** |
| `Desktop` | **/Desktop de _armazenamento interno de\__** |
| `LocalApplicationData` | **/.Local/share de _armazenamento interno de\__** |
| `MyDocuments` | **_ARMAZENAMENTO de\_interno_** |
| `MyMusic` | **/Music de _armazenamento interno de\__** |
| `MyPictures` | **/Pictures de _armazenamento interno de\__** |
| `MyVideos` | **/Videos de _armazenamento interno de\__** |
| `Personal` | **_ARMAZENAMENTO de\_interno_** |

### <a name="reading-or-writing-to-files-on-internal-storage"></a>Leitura ou gravação em arquivos no armazenamento interno

Qualquer uma das [ C# APIs para gravar](https://docs.microsoft.com/dotnet/csharp/programming-guide/file-system/how-to-write-to-a-text-file) em um arquivo é suficiente; Tudo o que é necessário é obter o caminho para o arquivo que está no diretório alocado para o aplicativo. É altamente recomendável que as versões assíncronas das APIs do .NET sejam usadas para minimizar os problemas que podem ser associados ao acesso ao arquivo que bloqueia o thread principal.

Esse trecho de código é um exemplo de gravação de um inteiro em um arquivo de texto UTF-8 para o diretório de armazenamento interno de um aplicativo:

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

## <a name="using--xamarinessentials-ndash-file-system-helpers"></a>Usando o Xamarin. Essentials &ndash; auxiliares do sistema de arquivos

O [Xamarin. Essentials](~/essentials/file-system-helpers.md?context=xamarin/android) é um conjunto de APIs para escrever código compatível com várias plataformas. Os [auxiliares do sistema de arquivos](~/essentials/file-system-helpers.md?context=xamarin/android) é uma classe que contém uma série de auxiliares para simplificar a localização do cache e dos diretórios de dados do aplicativo. Este trecho de código fornece um exemplo de como encontrar o diretório de armazenamento interno e o diretório de cache para um aplicativo:

```csharp
// Get the path to a file on internal storage
var backingFile = Path.Combine(Xamarin.Essentials.FileSystem.AppDataDirectory, "count.txt");

// Get the path to a file in the cache directory
var cacheFile = Path.Combine(Xamarin.Essentials.FileSystem.CacheDirectory, "count.txt");
```

## <a name="hiding-files-from-the-mediastore"></a>Ocultando arquivos da `MediaStore`

O `MediaStore` é um componente do Android que coleta metadados sobre arquivos de mídia (vídeos, música, imagens) em um dispositivo Android. Sua finalidade é simplificar o compartilhamento desses arquivos em todos os aplicativos Android no dispositivo.

Os arquivos privados não aparecerão como mídia compartilhável. Por exemplo, se um aplicativo salvar uma imagem em seu armazenamento externo privado, esse arquivo não será selecionado pelo scanner de mídia (`MediaStore`).

Os arquivos públicos serão coletados por `MediaStore`. Diretórios que têm um nome de arquivo de zero byte **. O nomedia** não será verificado pelo `MediaStore`.

## <a name="related-links"></a>Links relacionados

* [Armazenamento externo](~/android/platform/files/external-storage.md)
* [Salvar arquivos no armazenamento do dispositivo](https://developer.android.com/training/data-storage/files)
* [Auxiliares do sistema de arquivos Xamarin. Essentials](~/essentials/file-system-helpers.md?context=xamarin/android)
* [Fazer backup de dados do usuário com o backup automático](https://developer.android.com/guide/topics/data/autobackup)
* [Armazenamento de adoção](https://source.android.com/devices/storage/adoptable)
