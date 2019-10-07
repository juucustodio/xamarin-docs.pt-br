---
title: Acesso a arquivos no armazenamento externo com Xamarin. Android
description: Este guia abordará o acesso a arquivos no armazenamento externo no Xamarin. Android
ms.prod: xamarin
ms.assetid: 40da10b2-a207-4f9c-a2dd-165d9b662f33
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/23/2018
ms.openlocfilehash: c67f58a4b1886d3f89f7bd1639e1cd687d329869
ms.sourcegitcommit: 4ff181101d76f048b949c9613b2c72cf02618f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/07/2019
ms.locfileid: "71994808"
---
# <a name="external-storage"></a>Armazenamento externo

O armazenamento externo se refere ao armazenamento de arquivos que não está no armazenamento interno e não pode ser acessado exclusivamente para o aplicativo que é responsável pelo arquivo. A principal finalidade do armazenamento externo é fornecer um local para colocar os arquivos que devem ser compartilhados entre aplicativos ou que são muito grandes para caber no armazenamento interno.

Historicamente falando, o armazenamento externo fez referência a uma partição de disco em mídia removível, como um cartão SD (também conhecido como _armazenamento portátil_). Essa distinção não é mais tão relevante quanto os dispositivos Android evoluíram e muitos dispositivos Android não oferecem mais suporte ao armazenamento removível. Em vez disso, alguns dispositivos alocarão parte de sua memória não volátil interna que o Android executará a mesma mídia removível de função. Isso é conhecido como armazenamento _emulado_ e ainda é considerado um armazenamento externo. Como alternativa, alguns dispositivos Android podem ter várias partições de armazenamento externo. Por exemplo, um tablet Android (além de seu armazenamento interno) pode ter armazenamento emulado e um ou mais slots para um cartão SD. Todas essas partições são tratadas pelo Android como armazenamento externo.

Em dispositivos que têm vários usuários, cada usuário terá um diretório dedicado na partição primária de armazenamento externo para seu armazenamento externo. Os aplicativos em execução como um usuário não terão acesso a arquivos de outro usuário no dispositivo. Os arquivos de todos os usuários ainda são legíveis para o mundo e são graváveis; no entanto, o Android protegerá cada perfil de usuário dos outros.

Ler e gravar em arquivos é quase idêntico no Xamarin. Android como é para qualquer outro aplicativo .NET. O aplicativo Xamarin. Android determina o caminho para o arquivo que será manipulado e, em seguida, usa os idiomas .NET padrão para acesso ao arquivo. Como os caminhos reais para o armazenamento interno e externo podem variar de dispositivo para dispositivo ou da versão do Android para a versão do Android, não é recomendável codificar o caminho para os arquivos. Em vez disso, o Xamarin. Android expõe as APIs nativas do Android que ajudarão a determinar o caminho para os arquivos no armazenamento interno e externo.

Este guia discutirá os conceitos e as APIs no Android que são específicas para o armazenamento externo.

## <a name="public-and-private-files-on-external-storage"></a>Arquivos públicos e privados no armazenamento externo

Há dois tipos diferentes de arquivos que um aplicativo pode manter no armazenamento externo:

* Arquivos **privados @no__t** -1 arquivos privados são arquivos que são específicos para seu aplicativo (mas ainda são legíveis para o mundo e são graváveis). O Android espera que os arquivos privados sejam armazenados em um diretório específico no armazenamento externo. Embora os arquivos sejam chamados de "particular", eles ainda são visíveis e acessíveis por outros aplicativos no dispositivo, eles não recebem nenhuma proteção especial pelo Android.

* Arquivos **públicos** &ndash; são arquivos que não são considerados específicos para o aplicativo e devem ser compartilhados livremente.

As diferenças entre esses arquivos são principalmente conceituais. Os arquivos privados são privados no sentido de que são considerados uma parte do aplicativo, enquanto os arquivos públicos são quaisquer outros arquivos existentes no armazenamento externo. O Android fornece duas APIs diferentes para resolver os caminhos para arquivos públicos e privados, mas, caso contrário, as mesmas APIs .NET são usadas para ler e gravar nesses arquivos. Essas são as mesmas APIs que são discutidas na seção sobre [leitura e gravação](~/android/platform/files/index.md#reading-or-writing-to-files-on-internal-storage).

### <a name="private-external-files"></a>Arquivos externos privados

Os arquivos externos privados são considerados específicos para um aplicativo (semelhante a arquivos internos), mas estão sendo mantidos no armazenamento externo por vários motivos (como muito grandes para o armazenamento interno). Assim como os arquivos internos, esses arquivos serão excluídos quando o aplicativo for desinstalado pelo usuário.

O local principal para arquivos externos privados é encontrado chamando o método `Android.Content.Context.GetExternalFilesDir(string type)`. Esse método retornará um objeto `Java.IO.File` que representa o diretório de armazenamento externo privado para o aplicativo. Passar `null` para esse método retornará o caminho para o diretório de armazenamento do usuário para o aplicativo. Por exemplo, para um aplicativo com o nome do pacote `com.companyname.app`, o diretório "raiz" dos arquivos externos privados seria:

```bash
/storage/emulated/0/Android/data/com.companyname.app/files/
```

Este documento fará referência ao diretório de armazenamento para arquivos particulares no armazenamento externo como _Private @ no__t-1EXTERNAL @ no__t-2STORAGE_.

O parâmetro para `GetExternalFilesDir()` é uma cadeia de caracteres que especifica um _diretório de aplicativo_. Esse é um diretório destinado a fornecer um local padrão para uma organização lógica de arquivos. Os valores de cadeia de caracteres estão disponíveis por meio de constantes na classe `Android.OS.Environment`:

| `Android.OS.Environment` | Diretório |
|-|-|
| DirectoryAlarms | **_PRIVATE\_EXTERNAL\_STORAGE_/Alarms** |
| DirectoryDcim | **_PRIVATE @ NO__T-2EXTERNAL @ NO__T-3STORAGE_/DCIM** |
| DirectoryDownloads | **_Private @ no__t-2EXTERNAL @ no__t-3STORAGE_/download** |
| DirectoryDocuments | **_Private @ no__t-2EXTERNAL @ no__t-3STORAGE_/Documents** |
| DirectoryMovies | **_Private @ no__t-2EXTERNAL @ no__t-3STORAGE_/Movies** |
| DirectoryMusic | **_Private @ no__t-2EXTERNAL @ no__t-3STORAGE_/Music** |
| DirectoryNotifications | **_Private @ no__t-2EXTERNAL @ no__t-3STORAGE_/Notifications** |
| DirectoryPodcasts | **_Private @ no__t-2EXTERNAL @ no__t-3STORAGE_/podcasts** |
| DirectoryRingtones | **_PRIVATE\_EXTERNAL\_STORAGE_/Ringtones** |
| DirectoryPictures | **_Private @ no__t-2EXTERNAL @ no__t-3STORAGE_/Pictures** |

Para dispositivos que têm várias partições de armazenamento externo, cada partição terá um diretório destinado a arquivos privados. O método `Android.Content.Context.GetExternalFilesDirs(string type)` retornará uma matriz de `Java.IO.Files`. Cada objeto representará um diretório particular específico do aplicativo em todos os dispositivos de armazenamento compartilhados/externos, onde o aplicativo pode posicionar os arquivos que ele possui.

> [!IMPORTANT]
> O caminho exato para o diretório de armazenamento externo privado pode variar de dispositivo para dispositivo e entre versões do Android. Por isso, os aplicativos não devem embutir em código o caminho para esse diretório e, em vez disso, usar as APIs do Xamarin. Android, como `Android.Content.Context.GetExternalFilesDir()`.

### <a name="public-external-files"></a>Arquivos externos públicos

Arquivos públicos são arquivos que existem no armazenamento externo que não estão armazenados no diretório que o Android aloca para arquivos particulares. Os arquivos públicos não serão excluídos quando o aplicativo for desinstalado. Os aplicativos Android devem receber permissão antes de poderem ler ou gravar arquivos públicos. É possível que arquivos públicos existam em qualquer lugar no armazenamento externo, mas por convenção, o Android espera que os arquivos públicos existam no diretório identificado pela propriedade `Android.OS.Environment.ExternalStorageDirectory`. Essa propriedade retornará um objeto `Java.IO.File` que representa o diretório de armazenamento externo primário. Por exemplo, `Android.OS.Environment.ExternalStorageDirectory` pode se referir ao seguinte diretório:

```bash
/storage/emulated/0/
```

Este documento fará referência ao diretório de armazenamento para arquivos públicos no armazenamento externo como _Public @ no__t-1EXTERNAL @ no__t-2STORAGE_.

O Android também dá suporte ao conceito de diretórios de aplicativos no _@ no__t-1EXTERNAL @ no__t-2STORAGE_. Esses diretórios são exatamente os mesmos que os diretórios de aplicativo para `PRIVATE_EXTERNAL_STORAGE` e são descritos na tabela na seção anterior. O método `Android.OS.Environment.GetExternalStoragePublicDirectory(string directoryType)` retornará um objeto `Java.IO.File` que corresponde a um diretório de aplicativo público. O parâmetro `directoryType` é um parâmetro obrigatório e não pode ser `null`.

Por exemplo, chamar `Environment.GetExternalStoragePublicDirectory(Environment.DirectoryDocuments).AbsolutePath` retornará uma cadeia de caracteres que se assemelhará a:

```bash
/storage/emulated/0/Documents
```

> [!IMPORTANT]
> O caminho exato para o diretório de armazenamento externo público pode variar de dispositivo para dispositivo e entre versões do Android. Por isso, os aplicativos não devem embutir em código o caminho para esse diretório e, em vez disso, usar as APIs do Xamarin. Android, como `Android.OS.Environment.ExternalStorageDirectory`.

## <a name="working-with-external-storage"></a>Trabalhando com armazenamento externo

Depois que um aplicativo Xamarin. Android obtiver o caminho completo para um arquivo, ele deverá utilizar qualquer uma das APIs padrão do .NET para criar, ler, gravar ou excluir arquivos. Isso maximiza a quantidade de código compatível com plataforma cruzada para um aplicativo. No entanto, antes de tentar acessar um arquivo, um aplicativo Xamarin. Android deve garantir que seja possível acessar esse arquivo.

1. **Verifique se o armazenamento externo** &ndash; dependendo da natureza do armazenamento externo, é possível que ele não seja montado e possa ser usado pelo aplicativo. Todos os aplicativos devem verificar o estado do armazenamento externo antes de tentar usá-lo.
2. **Executar uma verificação de permissão de tempo de execução** &ndash; um aplicativo Android deve solicitar permissão do usuário para acessar o armazenamento externo. Isso significa que uma solicitação de permissão de tempo de execução deve ser executada antes de qualquer acesso ao arquivo. As permissões de guia [no Xamarin. Android](~/android/app-fundamentals/permissions.md) contêm mais detalhes sobre as permissões do Android.

Cada uma dessas duas tarefas será discutida abaixo.

### <a name="verifying-that-external-storage-is-available"></a>Verificando se o armazenamento externo está disponível

A primeira etapa antes de gravar no armazenamento externo é verificar se ele é legível ou gravável. A propriedade `Android.OS.Environment.ExternalStorageState` mantém uma cadeia de caracteres que identifica o estado do armazenamento externo. Essa propriedade retornará uma cadeia de caracteres que representa o estado. Esta tabela é uma lista dos valores `ExternalStorageState` que podem ser retornados por `Environment.ExternalStorageState`:

| ExternalStorageState | Descrição  |
|----------------------|---|
| MediaBadRemoval      | A mídia foi removida abruptamente sem ser corretamente desmontada. |
| MediaChecking        | A mídia está presente, mas está passando por uma verificação de disco.  |
| MediaEjecting        | A mídia está sendo desmontada e ejetada.  |
| MediaMounted         | A mídia está montada e pode ser lida ou gravada.  |
| MediaMountedReadOnly | A mídia está montada, mas só pode ser lida de. |
| MediaNofs            | A mídia está presente, mas não contém um sistema de arquivos adequado para Android. |
| MediaRemoved         | Não há mídia presente. |
| MediaShared          | A mídia está presente, mas não está montada. Ele está sendo compartilhado via USB com outro dispositivo.|
| MediaUnknown         | O estado da mídia não é reconhecido pelo Android. |
| MediaUnmountable     | A mídia está presente, mas não pode ser montada pelo Android. |
| MediaUnmounted       | A mídia está presente, mas não está montada. |

A maioria dos aplicativos Android só precisará verificar se o armazenamento externo está montado. O trecho de código a seguir mostra como verificar se o armazenamento externo está montado para acesso somente leitura ou acesso de leitura/gravação:

```csharp
bool isReadonly = Environment.MediaMountedReadOnly.Equals(Environment.ExternalStorageState);
bool isWriteable = Environment.MediaMounted.Equals(Environment.ExternalStorageState);
```

## <a name="external-storage-permissions"></a>Permissões de armazenamento externo

O Android considera o acesso ao armazenamento externo como uma _permissão perigosa_, que normalmente requer que o usuário conceda a permissão para acessar o recurso. O usuário pode revogar essa permissão a qualquer momento.  Isso significa que uma solicitação de permissão de tempo de execução deve ser executada antes de qualquer acesso ao arquivo. Os aplicativos recebem automaticamente permissões para ler e gravar seus próprios arquivos particulares. É possível que os aplicativos leiam e gravem os arquivos privados que pertencem a outros aplicativos após a [concessão da permissão](~/android/app-fundamentals/permissions.md) pelo usuário.

Todos os aplicativos Android devem declarar uma das duas permissões para o armazenamento externo no **AndroidManifest. xml** . Para identificar as permissões, um dos dois elementos `uses-permission` a seguir deve ser adicionado ao **AndroidManifest. xml**:

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

> [!NOTE]
> Se o usuário conceder `WRITE_EXTERNAL_STORAGE`, o `READ_EXTERNAL_STORAGE` também será concedido implicitamente. Não é necessário solicitar ambas as permissões em **AndroidManifest. xml**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

As permissões também podem ser adicionadas usando a guia **manifesto do Android** das **Propriedades da solução**:

![Gerenciador de Soluções-permissões necessárias para o Visual Studio](./images/required-permissions.w157.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

As permissões também podem ser adicionadas usando a guia **manifesto do Android** do **painel Propriedades da solução**:

[Painel ![Solution-permissões necessárias para Visual Studio para Mac](./images/required-permissions.m752-sml.png)](./images/required-permissions.m752.png#lightbox)

-----

Em geral, todas as permissões perigosas devem ser aprovadas pelo usuário. As permissões para o armazenamento externo são uma anomalia no momento em que há exceções a essa regra, dependendo da versão do Android que o aplicativo está executando:

![Fluxograma de verificações de permissão de armazenamento externo](./images/external-permission-check-flowchart.png)

Para obter mais informações sobre como executar solicitações de permissão em tempo de execução, consulte as permissões de guia [no Xamarin. Android](~/android/app-fundamentals/permissions.md). O **monodroid-Sample** [LocalFiles](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles) também demonstra uma maneira de executar verificações de permissão em tempo de execução.

#### <a name="granting-and-revoking-permissions-with-adb"></a>Concedendo e revogando permissões com o ADB

No decorrer do desenvolvimento de um aplicativo Android, pode ser necessário conceder e revogar permissões para testar os vários fluxos de trabalho envolvidos nas verificações de permissão de tempo de execução. É possível fazer isso no prompt de comando usando o ADB. Os trechos de linha de comando a seguir demonstram como conceder ou revogar permissões usando o ADB para um aplicativo Android cujo nome de pacote é **com. CompanyName. app**:

```bash
$ adb shell pm grant com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE

$ adb shell pm revoke com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE
```

## <a name="deleting-files"></a>Excluindo arquivos

Qualquer uma das APIs C# padrão pode ser usada para excluir um arquivo do armazenamento externo, como [`System.IO.File.Delete`](xref:System.IO.File.Delete*). Também é possível usar as APIs do Java às custas da portabilidade do código. Por exemplo:

```csharp
System.IO.File.Delete("/storage/emulated/0/Android/data/com.companyname.app/files/count.txt");
```

## <a name="related-links"></a>Links relacionados

* [Exemplo de arquivos locais do Xamarin. Android em **monodroid-Samples**](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)
* [Permissões no Xamarin. Android](~/android/app-fundamentals/permissions.md)
