---
title: Store e acessar dados no armazenamento do Azure do xamarin. Forms
description: Armazenamento do Azure é uma solução de armazenamento de nuvem escalonável que pode ser usada para armazenar dados não estruturados e estruturados. Este artigo explica como usar o xamarin. Forms para armazenar texto e dados binários no armazenamento do Azure e como acessar os dados.
ms.prod: xamarin
ms.assetid: 5B10D37B-839B-4CD0-9C65-91014A93F3EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/28/2018
ms.openlocfilehash: 044ff7448cc302da4d0efdf88325c40b9db0315c
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658623"
---
# <a name="store-and-access-data-in-azure-storage-from-xamarinforms"></a>Store e acessar dados no armazenamento do Azure do xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureStorage/)

_Armazenamento do Azure é uma solução de armazenamento de nuvem escalonável que pode ser usada para armazenar dados não estruturados e estruturados. Este artigo demonstra como usar o xamarin. Forms para armazenar texto e dados binários no armazenamento do Azure e como acessar os dados._

O armazenamento do Azure fornece quatro serviços de armazenamento:

- Armazenamento de BLOBs. Um blob pode ser texto ou dados binários, como backups, máquinas virtuais, arquivos de mídia ou documentos.
- Armazenamento de tabela é um repositório de atributos de chave NoSQL.
- Armazenamento de filas é um serviço de mensagens para processamento de fluxo de trabalho e comunicação entre serviços de nuvem.
- O armazenamento de arquivos oferece armazenamento compartilhado usando o protocolo SMB.

Há dois tipos de contas de armazenamento:

- Contas de armazenamento de uso geral fornece acesso aos serviços de armazenamento do Azure de uma única conta.
- Uma conta de armazenamento de BLOBs é uma conta de armazenamento especializada para armazenamento de blobs. Esse tipo de conta é recomendado quando você só precisa armazenar dados blob.

Neste artigo e que acompanha o aplicativo de exemplo demonstra a carregar arquivos de imagem e texto para o armazenamento de blob e baixá-los. Além disso, ele também demonstra recuperando uma lista de arquivos do armazenamento de BLOBs e excluir arquivos.

Para obter mais informações sobre o armazenamento do Azure, consulte [Introdução ao armazenamento](https://azure.microsoft.com/documentation/articles/storage-introduction/).

## <a name="introduction-to-blob-storage"></a>Introdução ao armazenamento de Blob

O armazenamento de BLOBs consiste em três componentes, que são mostrados no diagrama a seguir:

![](azure-storage-images/blob-storage.png "Conceitos de armazenamento de blob")

Todo o acesso ao armazenamento do Azure é por meio de uma conta de armazenamento. Uma conta de armazenamento pode conter um número ilimitado de contêineres, e um contêiner pode armazenar um número ilimitado de blobs, até o limite de capacidade da conta de armazenamento.

Um blob é um arquivo de qualquer tipo e tamanho. O armazenamento do Azure dá suporte a três tipos diferentes de blob:

- Blobs de blocos são otimizados para streaming e armazenamento de objetos de nuvem e são uma boa opção para armazenar backups, arquivos de mídia, documentos etc. Blobs de bloco podem ser até 195Gb de tamanho.
- Acrescentar blobs são semelhantes aos blobs de blocos, mas são otimizados para operações de acréscimo, como registro em log. Acrescentar blobs podem ser de até 195Gb.
- Blobs de página são otimizados para operações frequentes de leitura/gravação e normalmente são usados para armazenar as máquinas virtuais e seus discos. Blobs de página podem ter até 1Tb de tamanho.

> [!NOTE]
> Observe que as contas de armazenamento de BLOBs têm suporte para bloquear e acrescentar blobs, mas não os blobs de página.

Um blob é carregado no armazenamento do Azure e baixado do armazenamento do Azure, como um fluxo de bytes. Portanto, os arquivos devem ser convertidos em um fluxo de bytes antes de carregar e convertido de volta para sua representação original após o download.

Cada objeto que é armazenado no armazenamento do Azure tem um endereço de URL exclusivo. O nome da conta de armazenamento forma o subdomínio desse endereço e a combinação de formulários de nome de domínio e subdomínio uma *ponto de extremidade* da conta de armazenamento. Por exemplo, se sua conta de armazenamento é nomeada *mystorageaccount*, o ponto de extremidade de blob padrão para a conta de armazenamento `https://mystorageaccount.blob.core.windows.net`.

A URL para acessar um objeto em uma conta de armazenamento é criada, acrescentando o local do objeto na conta de armazenamento para o ponto de extremidade. Por exemplo, um endereço de blob terá o formato `https://mystorageaccount.blob.core.windows.net/mycontainer/myblob`.

## <a name="setup"></a>Configuração

O processo para integrar uma conta de armazenamento do Azure em um aplicativo xamarin. Forms é da seguinte maneira:

1. Crie uma conta de armazenamento. Para obter mais informações, consulte [criar uma conta de armazenamento](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account).
1. Adicione a [biblioteca de cliente de armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage/) ao aplicativo xamarin. Forms.
1. Configure a cadeia de caracteres de conexão de armazenamento. Para obter mais informações, consulte [conectar-se ao armazenamento do Azure](#connecting).
1. Adicione `using` diretivas para o `Microsoft.WindowsAzure.Storage` e `Microsoft.WindowsAzure.Storage.Blob` namespaces para classes que vai acessar o armazenamento do Azure.

<a name="connecting" />

## <a name="connecting-to-azure-storage"></a>Conectar-se ao armazenamento do Azure

Todas as solicitações feitas em relação aos recursos da conta de armazenamento devem ser autenticada. Embora os blobs podem ser configurados para dar suporte à autenticação anônima, há duas abordagens principais para que um aplicativo pode usar para autenticar com uma conta de armazenamento:

- Chave compartilhada. Essa abordagem usa a chave de nome e da conta de armazenamento do Azure para acessar os serviços de armazenamento. Uma conta de armazenamento recebe duas chaves privadas na criação do que pode ser usado para autenticação de chave compartilhada.
- Assinatura de acesso compartilhado. Esse é um token que pode ser anexado a uma URL que permite acesso delegado a um recurso de armazenamento, com as permissões ele especifica, para o período de tempo em que ele é válido.

Cadeias de caracteres de Conexão podem ser especificadas, que incluem as informações de autenticação necessárias para acessar os recursos de armazenamento do Azure de um aplicativo. Além disso, uma cadeia de caracteres de conexão pode ser configurada para se conectar ao emulador de armazenamento do Azure do Visual Studio.

> [!NOTE]
> O armazenamento do Azure dá suporte a HTTP e HTTPS em uma cadeia de conexão. No entanto, é recomendável usar HTTPS.

### <a name="connecting-to-the-azure-storage-emulator"></a>Conectar-se ao emulador de armazenamento do Azure

O emulador de armazenamento do Azure fornece um ambiente local que emula os serviços de tabela para fins de desenvolvimento, fila e BLOBs do Azure.

A seguinte cadeia de conexão deve ser usada para se conectar ao emulador de armazenamento do Azure:

```csharp
UseDevelopmentStorage=true
```

Para obter mais informações sobre o emulador de armazenamento do Azure, consulte [usar o emulador de armazenamento do Azure para desenvolvimento e teste](https://azure.microsoft.com/documentation/articles/storage-use-emulator/).

### <a name="connecting-to-azure-storage-using-a-shared-key"></a>Conectar-se ao armazenamento do Azure usando uma chave compartilhada

O seguinte formato de cadeia de caracteres de conexão deve ser usado para se conectar ao armazenamento do Azure com uma chave compartilhada:

```csharp
DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey
```

`myAccountName` deve ser substituído pelo nome da sua conta de armazenamento e `myAccountKey` deve ser substituído por uma das suas duas chaves de acesso de conta.

> [!NOTE]
> Ao usar compartilhada autenticação de chave, o nome da conta e chave de conta será distribuída para cada pessoa que usa seu aplicativo, que fornece acesso completo de leitura/gravação à conta de armazenamento. Portanto, usar a autenticação de chave compartilhada somente para testes e nunca distribuir chaves para outros usuários.

### <a name="connecting-to-azure-storage-using-a-shared-access-signature"></a>Conectar-se ao armazenamento do Azure usando uma assinatura de acesso compartilhado

O seguinte formato de cadeia de caracteres de conexão deve ser usado para se conectar ao armazenamento do Azure com uma SAS:

`BlobEndpoint=myBlobEndpoint;SharedAccessSignature=mySharedAccessSignature`

`myBlobEndpoint` deve ser substituído pela URL do seu ponto de extremidade do blob e `mySharedAccessSignature` deve ser substituído com a SAS. A SAS fornece o protocolo, o ponto de extremidade de serviço e as credenciais para acessar o recurso.

> [!NOTE]
> Autenticação de SAS é recomendada para aplicativos de produção. No entanto, em um aplicativo de produção as SAS devem ser recuperadas de um back-end serviço sob demanda, em vez de ser agrupado com o aplicativo.

Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [usando acesso assinaturas compartilhado (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

## <a name="creating-a-container"></a>Criar um contêiner

O `GetContainer` método é usado para recuperar uma referência a um contêiner nomeado, o que, em seguida, pode ser usado para recuperar os blobs do contêiner ou adicionar blobs no contêiner. O seguinte exemplo de código mostra o `GetContainer` método:

```csharp
static CloudBlobContainer GetContainer(ContainerType containerType)
{
  var account = CloudStorageAccount.Parse(Constants.StorageConnection);
  var client = account.CreateCloudBlobClient();
  return client.GetContainerReference(containerType.ToString().ToLower());
}
```

O `CloudStorageAccount.Parse` método analisa uma cadeia de caracteres de conexão e retorna um `CloudStorageAccount` instância que representa a conta de armazenamento. Um `CloudBlobClient` instância, que é usada para recuperar os contêineres e blobs, em seguida, é criada pelo `CreateCloudBlobClient` método. O `GetContainerReference` método recupera o contêiner especificado como um `CloudBlobContainer` da instância, antes de serem retornado para o método de chamada. Neste exemplo, o nome do contêiner é o `ContainerType` valor de enumeração, convertido em uma cadeia de caracteres em minúsculas.

> [!NOTE]
> Os nomes de contêiner devem estar em minúsculos e devem começar com uma letra ou número. Além disso, eles podem conter apenas letras, números e o caractere de traço e devem ter entre 3 e 63 caracteres.

O `GetContainer` método é invocado da seguinte maneira:

```csharp
var container = GetContainer(containerType);
```

O `CloudBlobContainer` instância, em seguida, pode ser usada para criar um contêiner se ele ainda não existir:

```csharp
await container.CreateIfNotExistsAsync();
```

Por padrão, um contêiner recém-criado é privado. Isso significa que uma chave de acesso de armazenamento deve ser especificada para recuperar os blobs do contêiner. Para obter informações sobre como fazer os blobs dentro de um contêiner público, consulte [criar um contêiner](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#create-a-container).

## <a name="uploading-data-to-a-container"></a>Carregar dados em um contêiner

O `UploadFileAsync` método é usado para carregar um fluxo de bytes de dados no armazenamento de BLOBs e é mostrado no exemplo de código a seguir:

```csharp
public static async Task<string> UploadFileAsync(ContainerType containerType, Stream stream)
{
  var container = GetContainer(containerType);
  await container.CreateIfNotExistsAsync();

  var name = Guid.NewGuid().ToString();
  var fileBlob = container.GetBlockBlobReference(name);
  await fileBlob.UploadFromStreamAsync(stream);

  return name;
}
```

Depois de recuperar uma referência de contêiner, o método cria o contêiner se ele ainda não existir. Uma nova `Guid` , em seguida, é criado para atuar como um nome exclusivo de blob, e uma referência de blob de blocos é recuperada como um `CloudBlockBlob` instância. O fluxo de dados é então carregado para o blob usando o `UploadFromStreamAsync` método, que cria o blob se ele ainda não existe ou substitui, se ele existir.

Antes de um arquivo pode ser carregado usando esse método de armazenamento de BLOBs, primeiro ele deve ser convertido para um fluxo de bytes. Isso é demonstrado no exemplo de código a seguir:

```csharp
var byteData = Encoding.UTF8.GetBytes(text);
uploadedFilename = await AzureStorage.UploadFileAsync(ContainerType.Text, new MemoryStream(byteData));
```

O `text` dados são convertidos em uma matriz de bytes, que é empacotada como um fluxo que é passado para o `UploadFileAsync` método.

## <a name="downloading-data-from-a-container"></a>Baixando dados de um contêiner

O `GetFileAsync` método é usado para baixar os dados de blob do armazenamento do Azure e é mostrado no exemplo de código a seguir:

```csharp
public static async Task<byte[]> GetFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);

  var blob = container.GetBlobReference(name);
  if (await blob.ExistsAsync())
  {
    await blob.FetchAttributesAsync();
    byte[] blobBytes = new byte[blob.Properties.Length];

    await blob.DownloadToByteArrayAsync(blobBytes, 0);
    return blobBytes;
  }
  return null;
}
```

Depois de recuperar uma referência de contêiner, o método recupera uma referência de blob para os dados armazenados. Se o blob existir, suas propriedades são recuperadas pelo `FetchAttributesAsync` método. Uma matriz de bytes do tamanho correto é criada e o blob é baixado como uma matriz de bytes que é retornada para o método de chamada.

Depois de baixar os dados de bytes de blob, ele deve ser convertido em sua representação original. Isso é demonstrado no exemplo de código a seguir:

```csharp
var byteData = await AzureStorage.GetFileAsync(ContainerType.Text, uploadedFilename);
string text = Encoding.UTF8.GetString(byteData);
```

A matriz de bytes é recuperada do armazenamento do Azure ao `GetFileAsync` cadeia de caracteres codificada de método, antes de ser convertido para um UTF8.

## <a name="listing-data-in-a-container"></a>A listagem de dados em um contêiner

O `GetFilesListAsync` método é usado para recuperar uma lista de blobs armazenados em um contêiner e é mostrado no exemplo de código a seguir:

```csharp
public static async Task<IList<string>> GetFilesListAsync(ContainerType containerType)
{
  var container = GetContainer(containerType);

  var allBlobsList = new List<string>();
  BlobContinuationToken token = null;

  do
  {
    var result = await container.ListBlobsSegmentedAsync(token);
    if (result.Results.Count() > 0)
    {
      var blobs = result.Results.Cast<CloudBlockBlob>().Select(b => b.Name);
      allBlobsList.AddRange(blobs);
    }
    token = result.ContinuationToken;
  } while (token != null);

  return allBlobsList;
}
```

Depois de recuperar uma referência de contêiner, o método usa o contêiner `ListBlobsSegmentedAsync` método para recuperar as referências para os blobs dentro do contêiner. Os resultados retornados pela `ListBlobsSegmentedAsync` método são enumerados enquanto a `BlobContinuationToken` instância não é `null`. Cada blob é convertido de retornado `IListBlobItem` para um `CloudBlockBlob` no acesso de ordem de `Name` propriedade do blob, antes que seja o valor é adicionada ao `allBlobsList` coleção. Uma vez a `BlobContinuationToken` instância é `null`, o último nome de blob retornou e execução sai do loop.

## <a name="deleting-data-from-a-container"></a>Excluir dados de um contêiner

O `DeleteFileAsync` método é usado para excluir um blob de um contêiner e é mostrado no exemplo de código a seguir:

```csharp
public static async Task<bool> DeleteFileAsync(ContainerType containerType, string name)
{
  var container = GetContainer(containerType);
  var blob = container.GetBlobReference(name);
  return await blob.DeleteIfExistsAsync();
}
```

Depois de recuperar uma referência de contêiner, o método recupera uma referência de blob para o blob especificado. O blob é excluído com o `DeleteIfExistsAsync` método.

## <a name="related-links"></a>Links relacionados

- [Armazenamento do Azure (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureStorage/)
- [Introdução ao armazenamento](https://azure.microsoft.com/documentation/articles/storage-introduction/)
- [Como usar o armazenamento de BLOBs do Xamarin](https://azure.microsoft.com/documentation/articles/storage-xamarin-blob-storage/)
- [Usando assinaturas de acesso compartilhado (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
- [Windows (NuGet) de armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage/)
