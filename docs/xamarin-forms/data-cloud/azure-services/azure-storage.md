---
Título: "armazenar e acessar dados no armazenamento do Azure de Xamarin.Forms " Descrição: "o armazenamento do Azure é uma solução de armazenamento em nuvem escalonável que pode ser usada para armazenar dados não estruturados e estruturados. Este artigo explica como usar Xamarin.Forms o para armazenar texto e dados binários no armazenamento do Azure e como acessar os dados. "
MS. Prod: xamarin MS. AssetID: 5B10D37B-839B-4CD0-9C65-91014A93F3EB MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 12/28/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="store-and-access-data-in-azure-storage-from-xamarinforms"></a>Armazene e acesse dados no armazenamento do Azure deXamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurestorage)

_O armazenamento do Azure é uma solução de armazenamento em nuvem escalonável que pode ser usada para armazenar dados não estruturados e estruturados. Este artigo demonstra como usar Xamarin.Forms o para armazenar texto e dados binários no armazenamento do Azure e como acessar os dados._

O armazenamento do Azure fornece quatro serviços de armazenamento:

- Armazenamento de BLOBs. Um blob pode ser dados de texto ou binários, como backups, máquinas virtuais, arquivos de mídia ou documentos.
- O armazenamento de tabela é um repositório de atributos de chave NoSQL.
- O armazenamento de filas é um serviço de mensagens para processamento de fluxo de trabalho e comunicação entre serviços de nuvem.
- O armazenamento de arquivos fornece armazenamento compartilhado usando o protocolo SMB.

Existem dois tipos de contas de armazenamento:

- As contas de armazenamento de uso geral fornecem acesso aos serviços de armazenamento do Azure a partir de uma única conta.
- Uma conta de armazenamento de BLOBs é uma conta de armazenamento especializada para armazenar blobs. Esse tipo de conta é recomendado quando você só precisa armazenar dados de BLOB.

Este artigo e o aplicativo de exemplo que o acompanha, demonstra como carregar arquivos de imagem e texto no armazenamento de BLOBs e baixá-los. Além disso, ele também demonstra a recuperação de uma lista de arquivos do armazenamento de BLOBs e a exclusão de arquivos.

Para obter mais informações sobre o armazenamento do Azure, consulte [introdução ao armazenamento](https://azure.microsoft.com/documentation/articles/storage-introduction/).

> [!NOTE]
> Se você não tiver uma [assinatura do Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de começar.

## <a name="introduction-to-blob-storage"></a>Introdução ao armazenamento de BLOBs

O armazenamento de BLOBs consiste em três componentes, que são mostrados no diagrama a seguir:

![](azure-storage-images/blob-storage.png "Blob Storage Concepts")

Todo o acesso ao armazenamento do Azure é por meio de uma conta de armazenamento. Uma conta de armazenamento pode conter um número ilimitado de contêineres, e um contêiner pode armazenar um número ilimitado de BLOBs, até o limite de capacidade da conta de armazenamento.

Um blob é um arquivo de qualquer tipo e tamanho. O armazenamento do Azure dá suporte a três tipos diferentes de blob:

- Blobs de blocos são otimizados para streaming e armazenamento de objetos de nuvem e são uma boa opção para armazenar backups, arquivos de mídia, documentos, etc. Blobs de blocos podem ter até 195 GB de tamanho.
- Os blobs de acréscimo são semelhantes aos blobs de blocos, mas são otimizados para operações de acréscimo, como registro em log. Os blobs de acréscimo podem ter até 195 GB de tamanho.
- Os blobs de páginas são otimizados para operações frequentes de leitura/gravação e normalmente são usados para armazenar máquinas virtuais e seus discos. Os blobs de páginas podem ter até 1 TB de tamanho.

> [!NOTE]
> Observe que as contas de armazenamento de BLOBs dão suporte a blobs de bloco e acréscimo, mas não a blobs de páginas.

Um blob é carregado no armazenamento do Azure e baixado do armazenamento do Azure, como um fluxo de bytes. Portanto, os arquivos devem ser convertidos em um fluxo de bytes antes do carregamento e convertidos de volta para sua representação original após o download.

Cada objeto armazenado no armazenamento do Azure tem um endereço de URL exclusivo. O nome da conta de armazenamento forma o subdomínio desse endereço, e a combinação de subdomínio e nome de domínio forma um *ponto de extremidade* para a conta de armazenamento. Por exemplo, se sua conta de armazenamento for chamada *mystorageaccount*, o ponto de extremidade de blob padrão para a conta de armazenamento será `https://mystorageaccount.blob.core.windows.net` .

A URL para acessar um objeto em uma conta de armazenamento é compilada acrescentando o local do objeto na conta de armazenamento ao ponto de extremidade. Por exemplo, um endereço de blob terá o formato `https://mystorageaccount.blob.core.windows.net/mycontainer/myblob` .

## <a name="setup"></a>Instalação

O processo de integração de uma conta de armazenamento do Azure em um Xamarin.Forms aplicativo é o seguinte:

1. Criar uma conta de armazenamento. Para obter mais informações, consulte [criar uma conta de armazenamento](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account).
1. Adicione a [biblioteca de cliente do armazenamento do Azure](https://www.nuget.org/packages/WindowsAzure.Storage/) ao Xamarin.Forms aplicativo.
1. Configure a cadeia de conexão de armazenamento. Para obter mais informações, consulte [conectando-se ao armazenamento do Azure](#connecting-to-azure-storage).
1. Adicione `using` diretivas para os `Microsoft.WindowsAzure.Storage` `Microsoft.WindowsAzure.Storage.Blob` namespaces e a classes que acessarão o armazenamento do Azure.

## <a name="connecting-to-azure-storage"></a>Conectando-se ao Armazenamento do Azure

Cada solicitação feita nos recursos da conta de armazenamento deve ser autenticada. Embora os BLOBs possam ser configurados para dar suporte à autenticação anônima, há duas abordagens principais que um aplicativo pode usar para autenticar com uma conta de armazenamento:

- Chave compartilhada. Essa abordagem usa o nome da conta de armazenamento do Azure e a chave de conta para acessar os serviços de armazenamento. Uma conta de armazenamento recebe duas chaves privadas na criação que podem ser usadas para a autenticação de chave compartilhada.
- Assinatura de acesso compartilhado. Esse é um token que pode ser anexado a uma URL que habilita o acesso delegado a um recurso de armazenamento, com as permissões que ele especifica, para o período de tempo em que é válido.

É possível especificar cadeias de conexão que incluam as informações de autenticação necessárias para acessar os recursos de armazenamento do Azure de um aplicativo. Além disso, uma cadeia de conexão pode ser configurada para se conectar ao emulador de armazenamento do Azure do Visual Studio.

> [!NOTE]
> O armazenamento do Azure dá suporte a HTTP e HTTPS em uma cadeia de conexão. No entanto, é recomendável usar HTTPS.

### <a name="connecting-to-the-azure-storage-emulator"></a>Conectando-se ao emulador de armazenamento do Azure

O emulador de armazenamento do Azure fornece um ambiente local que emula os serviços BLOB, fila e tabela do Azure para fins de desenvolvimento.

A cadeia de conexão a seguir deve ser usada para se conectar ao emulador de armazenamento do Azure:

```csharp
UseDevelopmentStorage=true
```

Para obter mais informações sobre o emulador de armazenamento do Azure, consulte [usar o emulador de armazenamento do Azure para desenvolvimento e teste](https://azure.microsoft.com/documentation/articles/storage-use-emulator/).

### <a name="connecting-to-azure-storage-using-a-shared-key"></a>Conectando-se ao armazenamento do Azure usando uma chave compartilhada

O seguinte formato de cadeia de conexão deve ser usado para se conectar ao armazenamento do Azure com uma chave compartilhada:

```csharp
DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey
```

`myAccountName`deve ser substituído pelo nome da sua conta de armazenamento e `myAccountKey` deve ser substituído por uma das duas chaves de acesso da conta.

> [!NOTE]
> Ao usar a autenticação de chave compartilhada, o nome da conta e a chave de conta serão distribuídos para cada pessoa que usa seu aplicativo, o que fornecerá acesso completo de leitura/gravação à conta de armazenamento. Portanto, use a autenticação de chave compartilhada somente para fins de teste e nunca distribua chaves para outros usuários.

### <a name="connecting-to-azure-storage-using-a-shared-access-signature"></a>Conectando-se ao armazenamento do Azure usando uma assinatura de acesso compartilhado

O seguinte formato de cadeia de conexão deve ser usado para se conectar ao armazenamento do Azure com uma SAS:

`BlobEndpoint=myBlobEndpoint;SharedAccessSignature=mySharedAccessSignature`

`myBlobEndpoint`deve ser substituído pela URL do seu ponto de extremidade de BLOB e `mySharedAccessSignature` deve ser substituído pela SAS. A SAS fornece o protocolo, o ponto de extremidade de serviço e as credenciais para acessar o recurso.

> [!NOTE]
> A autenticação SAS é recomendada para aplicativos de produção. No entanto, em um aplicativo de produção, a SAS deve ser recuperada de um serviço de back-end sob demanda, em vez de ser agrupada com o aplicativo.

Para obter mais informações sobre assinaturas de acesso compartilhado, consulte [usando SAS (assinaturas de acesso compartilhado)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).

## <a name="creating-a-container"></a>Criando um contêiner

O `GetContainer` método é usado para recuperar uma referência a um contêiner nomeado, que pode ser usado para recuperar blobs do contêiner ou para adicionar BLOBs ao contêiner. O seguinte exemplo de código mostra o método `GetContainer`:

```csharp
static CloudBlobContainer GetContainer(ContainerType containerType)
{
  var account = CloudStorageAccount.Parse(Constants.StorageConnection);
  var client = account.CreateCloudBlobClient();
  return client.GetContainerReference(containerType.ToString().ToLower());
}
```

O `CloudStorageAccount.Parse` método analisa uma cadeia de conexão e retorna uma `CloudStorageAccount` instância que representa a conta de armazenamento. Uma `CloudBlobClient` instância, que é usada para recuperar contêineres e BLOBs, é então criada pelo `CreateCloudBlobClient` método. O `GetContainerReference` método recupera o contêiner especificado como uma `CloudBlobContainer` instância, antes que ele seja retornado ao método de chamada. Neste exemplo, o nome do contêiner é o `ContainerType` valor de enumeração, convertido em uma cadeia de caracteres em minúsculas.

> [!NOTE]
> Os nomes de contêiner devem ser minúsculos e devem começar com uma letra ou número. Além disso, eles podem conter apenas letras, números e o caractere de traço e devem ter entre 3 e 63 caracteres de comprimento.

O `GetContainer` método é invocado da seguinte maneira:

```csharp
var container = GetContainer(containerType);
```

A `CloudBlobContainer` instância pode então ser usada para criar um contêiner, caso ele ainda não exista:

```csharp
await container.CreateIfNotExistsAsync();
```

Por padrão, um contêiner recém-criado é privado. Isso significa que uma chave de acesso de armazenamento deve ser especificada para recuperar blobs do contêiner. Para obter informações sobre como tornar BLOBs em um contêiner público, consulte [criar um contêiner](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#create-a-container).

## <a name="uploading-data-to-a-container"></a>Carregando dados em um contêiner

O `UploadFileAsync` método é usado para carregar um fluxo de dados de byte para o armazenamento de BLOBs e é mostrado no exemplo de código a seguir:

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

Depois de recuperar uma referência de contêiner, o método cria o contêiner, caso ele ainda não exista. Um novo `Guid` é então criado para atuar como um nome de blob exclusivo e uma referência de bloco de blob é recuperada como uma `CloudBlockBlob` instância. O fluxo de dados é então carregado no BLOB usando o `UploadFromStreamAsync` método, que cria o blob, caso ele ainda não exista, ou o substitui, caso exista.

Antes que um arquivo possa ser carregado no armazenamento de BLOB usando esse método, primeiro ele deve ser convertido em um fluxo de bytes. Isso é demonstrado no exemplo de código a seguir:

```csharp
var byteData = Encoding.UTF8.GetBytes(text);
uploadedFilename = await AzureStorage.UploadFileAsync(ContainerType.Text, new MemoryStream(byteData));
```

Os `text` dados são convertidos em uma matriz de bytes, que é então encapsulada como um fluxo que é passado para o `UploadFileAsync` método.

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

Depois de recuperar uma referência de contêiner, o método recupera uma referência de BLOB para os dados armazenados. Se o blob existir, suas propriedades serão recuperadas pelo `FetchAttributesAsync` método. Uma matriz de bytes do tamanho correto é criada e o blob é baixado como uma matriz de bytes que é retornada ao método de chamada.

Depois de baixar os dados de bytes de BLOB, ele deve ser convertido em sua representação original. Isso é demonstrado no exemplo de código a seguir:

```csharp
var byteData = await AzureStorage.GetFileAsync(ContainerType.Text, uploadedFilename);
string text = Encoding.UTF8.GetString(byteData);
```

A matriz de bytes é recuperada do armazenamento do Azure pelo `GetFileAsync` método, antes de ser convertida novamente em uma cadeia de caracteres codificada em UTF8.

## <a name="listing-data-in-a-container"></a>Listando dados em um contêiner

O `GetFilesListAsync` método é usado para recuperar uma lista de BLOBs armazenados em um contêiner e é mostrado no exemplo de código a seguir:

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

Depois de recuperar uma referência de contêiner, o método usa o método do contêiner `ListBlobsSegmentedAsync` para recuperar referências para os BLOBs dentro do contêiner. Os resultados retornados pelo `ListBlobsSegmentedAsync` método são enumerados enquanto a `BlobContinuationToken` instância não é `null` . Cada blob é convertido de retornado `IListBlobItem` para um para `CloudBlockBlob` acessar a `Name` Propriedade do blob, antes de seu valor ser adicionado à `allBlobsList` coleção. Depois que a `BlobContinuationToken` instância é `null` , o último nome de blob foi retornado e a execução sai do loop.

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

Depois de recuperar uma referência de contêiner, o método recupera uma referência de BLOB para o blob especificado. O blob é então excluído com o `DeleteIfExistsAsync` método.

## <a name="related-links"></a>Links relacionados

- [Armazenamento do Azure (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azurestorage)
- [Introdução ao armazenamento](https://azure.microsoft.com/documentation/articles/storage-introduction/)
- [Como usar o Armazenamento de Blobs no Xamarin](https://azure.microsoft.com/documentation/articles/storage-xamarin-blob-storage/)
- [Usando SAS (Assinatura de Acesso Compartilhado)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)
- [Armazenamento do Windows Azure (NuGet)](https://www.nuget.org/packages/WindowsAzure.Storage/)
