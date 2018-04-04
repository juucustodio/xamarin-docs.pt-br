---
title: Armazenar e acessar dados no armazenamento do Azure
description: Armazenamento do Azure é uma solução de armazenamento de nuvem escalonáveis que pode ser usada para armazenar dados estruturados e não estruturados. Este artigo demonstra como usar o xamarin. Forms para armazenar texto e dados binários no armazenamento do Azure e como acessar os dados.
ms.prod: xamarin
ms.assetid: 5B10D37B-839B-4CD0-9C65-91014A93F3EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 63afeec81eff350b034e8dd3a13da52801937826
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="storing-and-accessing-data-in-azure-storage"></a>Armazenar e acessar dados no armazenamento do Azure

_Armazenamento do Azure é uma solução de armazenamento de nuvem escalonáveis que pode ser usada para armazenar dados estruturados e não estruturados. Este artigo demonstra como usar o xamarin. Forms para armazenar texto e dados binários no armazenamento do Azure e como acessar os dados._

## <a name="overview"></a>Visão geral

Armazenamento do Azure fornece quatro serviços de armazenamento:

- Armazenamento de blob. Um blob pode ser texto ou dados binários, como backups, máquinas virtuais, arquivos de mídia ou documentos.
- Armazenamento de tabela é um repositório de atributo de chave NoSQL.
- Armazenamento de fila é um serviço de mensagens para processamento de fluxo de trabalho e comunicação entre serviços de nuvem.
- Armazenamento de arquivos fornece armazenamento compartilhado usando o protocolo SMB.

Há dois tipos de contas de armazenamento:

- Contas de armazenamento de uso geral fornece acesso aos serviços de armazenamento do Azure de uma única conta.
- Uma conta de armazenamento de Blob é uma conta de armazenamento especializado para armazenamento de blobs. Esse tipo de conta é recomendado quando você precisa armazenar dados blob.

Neste artigo e que acompanha o aplicativo de exemplo demonstra carregar arquivos de texto e imagem para o armazenamento de blob e baixá-los. Além disso, ele também demonstra recuperando uma lista de arquivos do armazenamento de blob e excluir arquivos.

Para obter mais informações sobre o armazenamento do Azure, consulte [Introdução ao armazenamento](https://azure.microsoft.com/documentation/articles/storage-introduction/).

## <a name="introduction-to-blob-storage"></a>Introdução ao armazenamento de Blob

Armazenamento de blob consiste em três componentes que são mostrados no diagrama a seguir:

![](azure-storage-images/blob-storage.png "Conceitos de armazenamento de blob")

Todo o acesso ao armazenamento do Azure é por meio de uma conta de armazenamento. Uma conta de armazenamento pode conter um número ilimitado de contêineres, e um contêiner pode armazenar um número ilimitado de blobs, até o limite de capacidade da conta de armazenamento.

Um blob é um arquivo de qualquer tipo e tamanho. Armazenamento do Azure dá suporte a três tipos diferentes de blob:

- Blobs de bloco são otimizados para streaming e armazenar objetos de nuvem e são uma boa opção para armazenar backups, arquivos de mídia, documentos etc. Blobs de bloco podem ter até 195Gb de tamanho.
- Acrescentar blobs são semelhantes aos blobs de bloco, mas são otimizados para operações, como log de acréscimo. Acrescentar blobs podem ser 195Gb de tamanho.
- Blobs de página são otimizados para operações de leitura/gravação frequentes e normalmente são usados para armazenar máquinas virtuais e seus discos. Blobs de página podem ser de até 1Tb de tamanho.

> [!NOTE]
> Observe que as contas de armazenamento de blob têm suporte para bloquear e acrescentar blobs, mas não os blobs de página.

Um blob é carregado no armazenamento do Azure e baixado do armazenamento do Azure, como um fluxo de bytes. Portanto, os arquivos devem ser convertidos em um fluxo de bytes antes de carregar e convertido de volta para sua representação original após o download.

Cada objeto que é armazenado no armazenamento do Azure tem um endereço de URL exclusivo. O nome da conta de armazenamento de formulários do subdomínio de endereço e a combinação de formatos de nome de subdomínio e domínio um *ponto de extremidade* para a conta de armazenamento. Por exemplo, se sua conta de armazenamento é chamada *mystorageaccount*, o ponto de extremidade de blob padrão para a conta de armazenamento `https://mystorageaccount.blob.core.windows.net`.

A URL para acessar um objeto em uma conta de armazenamento é criada, acrescentando o local do objeto na conta de armazenamento para o ponto de extremidade. Por exemplo, um endereço de blob terá o formato `https://mystorageaccount.blob.core.windows.net/mycontainer/myblob`.

## <a name="setup"></a>Configuração

O processo para a integração de uma conta de armazenamento do Azure em um aplicativo xamarin. Forms é da seguinte maneira:

1. Crie uma conta de armazenamento. Para obter mais informações, consulte [criar uma conta de armazenamento](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account).
1. Adicionar o [biblioteca de cliente de armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage/) para o aplicativo xamarin. Forms.
1. Configure a cadeia de caracteres de conexão de armazenamento. Para obter mais informações, consulte [se conectar ao armazenamento do Azure](#connecting).
1. Adicionar `using` diretivas para o `Microsoft.WindowsAzure.Storage` e `Microsoft.WindowsAzure.Storage.Blob` namespaces para classes que irá acessar o armazenamento do Azure.

> [!NOTE]
> Embora este exemplo usa um projeto de acesso compartilhado, a biblioteca de cliente de armazenamento do Azure agora também oferece suporte sendo consumidos a partir de um projeto de biblioteca de classe portátil (PCL).

<a name="connecting" />

## <a name="connecting-to-azure-storage"></a>Conectar ao armazenamento do Azure

Cada solicitação feita nos recursos da conta de armazenamento deve ser autenticada. Enquanto blobs podem ser configurados para dar suporte a autenticação anônima, há duas abordagens principais para que um aplicativo pode usar para autenticar com uma conta de armazenamento:

- Chave compartilhada. Essa abordagem usa a chave de nome e uma conta da conta de armazenamento do Azure para acessar os serviços de armazenamento. Uma conta de armazenamento tem duas chaves privadas em criação que podem ser usados para autenticação de chave compartilhada.
- Assinatura de acesso compartilhado. Este é um token que pode ser anexado a uma URL que permite acesso delegado a um recurso de armazenamento, com as permissões Especifica, para o período de tempo em que ele é válido.

Cadeias de caracteres de Conexão podem ser especificadas que incluem as informações de autenticação necessárias para acessar os recursos de armazenamento do Azure em um aplicativo. Além disso, uma cadeia de caracteres de conexão pode ser configurada para se conectar ao emulador de armazenamento do Azure do Visual Studio.

> [!NOTE]
> Armazenamento do Azure dá suporte a HTTP e HTTPS em uma cadeia de caracteres de conexão. No entanto, usar HTTPS é recomendado.

### <a name="connecting-to-the-azure-storage-emulator"></a>Conexão com o emulador de armazenamento do Azure

O emulador de armazenamento do Azure fornece um ambiente local que emula os serviços de tabela para fins de desenvolvimento, fila e BLOBs do Azure.

A seguinte cadeia de conexão deve ser usada para se conectar ao emulador de armazenamento do Azure:

```csharp
UseDevelopmentStorage=true
```

Para obter mais informações sobre o emulador de armazenamento do Azure, consulte [usar o emulador de armazenamento do Azure para desenvolvimento e teste](https://azure.microsoft.com/documentation/articles/storage-use-emulator/).

### <a name="connecting-to-azure-storage-using-a-shared-key"></a>Conectar ao armazenamento do Azure usando uma chave compartilhada

O seguinte formato de cadeia de caracteres de conexão deve ser usado para se conectar ao armazenamento do Azure com uma chave compartilhada:

```csharp
DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey
```

`myAccountName` deve ser substituído pelo nome da sua conta de armazenamento e `myAccountKey` deve ser substituído por uma das suas duas chaves de acesso de conta.

> [!NOTE]
> Usando compartilhado quando a autenticação de chave, o nome da conta e chave de conta será distribuído para cada pessoa que utiliza seu aplicativo, que fornece acesso de leitura/gravação completa para a conta de armazenamento. Portanto, usar a autenticação de chave compartilhada para fins de teste somente e nunca distribuir as chaves a outros usuários.

### <a name="connecting-to-azure-storage-using-a-shared-access-signature"></a>Conectar ao armazenamento do Azure usando uma assinatura de acesso compartilhado

O seguinte formato de cadeia de caracteres de conexão deve ser usado para se conectar ao armazenamento do Azure com uma SAS:

`BlobEndpoint=myBlobEndpoint;SharedAccessSignature=mySharedAccessSignature`

`myBlobEndpoint` deve ser substituído com a URL do seu ponto de extremidade do blob e `mySharedAccessSignature` deve ser substituído pelo seu SAS. A SAS fornece o protocolo, o ponto de extremidade de serviço e as credenciais para acessar o recurso.

> [!NOTE]
> Autenticação de SAS é recomendada para aplicativos de produção. No entanto, em um aplicativo de produção as associações de segurança devem ser recuperadas de um back-end serviço sob demanda, em vez de ser agrupado com o aplicativo.

Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [usando assinaturas (SAS de acesso compartilhado)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

## <a name="creating-a-container"></a>Criando um contêiner

O `GetContainer` método é usado para recuperar uma referência a um contêiner nomeado, que pode ser usada para recuperar os blobs do contêiner ou adicionar blobs no contêiner. O seguinte exemplo de código mostra o `GetContainer` método:

```csharp
static CloudBlobContainer GetContainer(ContainerType containerType)
{
  var account = CloudStorageAccount.Parse(Constants.StorageConnection);
  var client = account.CreateCloudBlobClient();
  return client.GetContainerReference(containerType.ToString().ToLower());
}
```

O `CloudStorageAccount.Parse` método analisa uma cadeia de caracteres de conexão e retorna um `CloudStorageAccount` instância que representa a conta de armazenamento. Um `CloudBlobClient` instância, que é usada para recuperar os contêineres e blobs, em seguida, é criada pelo `CreateCloudBlobClient` método. O `GetContainerReference` método recupera o contêiner especificado como um `CloudBlobContainer` de instância, antes de ser retornado para o método de chamada. Neste exemplo, o nome do contêiner é o `ContainerType` valor de enumeração, convertido em uma cadeia de caracteres em minúsculas.

> [!NOTE]
> Os nomes de contêiner devem estar em minúsculos e devem começar com uma letra ou número. Além disso, eles podem conter apenas letras, números e o caractere de traço e devem estar entre 3 e 63 caracteres.

O `GetContainer` método é invocado da seguinte maneira:

```csharp
var container = GetContainer(containerType);
```

O `CloudBlobContainer` instância pode ser usada para criar um contêiner se ele ainda não existir:

```csharp
await container.CreateIfNotExistsAsync();
```

Por padrão, um contêiner recém-criado é privado. Isso significa que uma chave de acesso de armazenamento deve ser especificada para recuperar os blobs do contêiner. Para obter informações sobre como tornar os blobs dentro de um contêiner público, consulte [criar um contêiner](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#create-a-container).

## <a name="uploading-data-to-a-container"></a>Carregando dados em um contêiner

O `UploadFileAsync` método é usado para carregar um fluxo de bytes de dados no armazenamento de blob e é mostrado no exemplo de código a seguir:

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

Depois de recuperar uma referência de contêiner, o método cria o contêiner se ele ainda não existir. Um novo `Guid` é criado para agir como um nome exclusivo de blob, e uma referência de blocos de blob é recuperada como um `CloudBlockBlob` instância. O fluxo de dados, em seguida, é carregado para o blob usando o `UploadFromStreamAsync` método, que cria o blob se ele ainda não existe ou substitui-lo se ele existir.

Antes de um arquivo pode ser carregado usando esse método de armazenamento de blob, ele deve primeiro ser convertido para um fluxo de bytes. Isso é demonstrado no exemplo de código a seguir:

```csharp
var byteData = Encoding.UTF8.GetBytes(text);
uploadedFilename = await AzureStorage.UploadFileAsync(ContainerType.Text, new MemoryStream(byteData));
```

O `text` dados são convertidos em uma matriz de bytes que é fornecida como um fluxo que é passado para o `UploadFileAsync` método.

## <a name="downloading-data-from-a-container"></a>Baixando dados de um contêiner

O `GetFileAsync` método é usado para baixar dados de blob do armazenamento do Azure e é mostrado no exemplo de código a seguir:

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

Depois de recuperar uma referência de contêiner, o método recupera uma referência de blob para os dados armazenados. Se o blob existir, suas propriedades são recuperadas, o `FetchAttributesAsync` método. Uma matriz de bytes do tamanho correto é criada e o blob é baixado como uma matriz de bytes que é retornada para o método de chamada.

Depois de baixar os dados de bytes de blob, ele deve ser convertido em sua representação original. Isso é demonstrado no exemplo de código a seguir:

```csharp
var byteData = await AzureStorage.GetFileAsync(ContainerType.Text, uploadedFilename);
string text = Encoding.UTF8.GetString(byteData);
```

A matriz de bytes é recuperada do armazenamento do Azure, o `GetFileAsync` cadeia de caracteres codificada de método, antes de ser convertido para um UTF8.

## <a name="listing-data-in-a-container"></a>Listando dados em um contêiner

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

Depois de recuperar uma referência de contêiner, o método usa o contêiner `ListBlobsSegmentedAsync` método para recuperar as referências a blobs dentro do contêiner. Os resultados retornados pelo `ListBlobsSegmentedAsync` método são enumeradas enquanto o `BlobContinuationToken` instância não é `null`. Cada blob é convertido de retornado `IListBlobItem` para um `CloudBlockBlob` no acesso de ordem de `Name` propriedade do blob, antes que seja o valor é adicionada ao `allBlobsList` coleção. Uma vez o `BlobContinuationToken` instância é `null`, o último nome de blob foi retornado e execução sai do loop.

## <a name="deleting-data-from-a-container"></a>Excluindo dados de um contêiner

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

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar xamarin. Forms para armazenar texto e dados binários no armazenamento do Azure e como acessar os dados. Armazenamento do Azure é uma solução de armazenamento de nuvem escalonáveis que pode ser usada para armazenar dados estruturados e não estruturados.


## <a name="related-links"></a>Links relacionados

- [Armazenamento do Azure (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureStorage/)
- [Introdução ao armazenamento](https://azure.microsoft.com/documentation/articles/storage-introduction/)
- [Como usar o armazenamento de Blob do Xamarin](https://azure.microsoft.com/documentation/articles/storage-xamarin-blob-storage/)
- [Usando assinaturas de acesso compartilhado (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
- [Windows Azure Storage](https://www.nuget.org/packages/WindowsAzure.Storage/)
