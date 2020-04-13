---
title: Acesso a arquivos em armazenamento externo com Xamarin.Android
description: Este guia discutirá o acesso a arquivos no armazenamento externo em Xamarin.Android
ms.prod: xamarin
ms.assetid: 40da10b2-a207-4f9c-a2dd-165d9b662f33
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/23/2018
ms.openlocfilehash: 96b0d6a00c7825939b1f89ed63e3e5559ca4ef59
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73020477"
---
# <a name="external-storage"></a>Armazenamento externo

O armazenamento externo refere-se ao armazenamento de arquivos que não está no armazenamento interno e não é exclusivamente acessível ao aplicativo responsável pelo arquivo. O objetivo principal do armazenamento externo é fornecer um lugar para colocar arquivos que devem ser compartilhados entre aplicativos ou que sejam muito grandes para caber no armazenamento interno.

Historicamente falando, o armazenamento externo referia-se a uma partição de disco em mídia removível, como um cartão SD (também conhecido como _armazenamento portátil_). Essa distinção não é mais tão relevante quanto os dispositivos Android evoluíram e muitos dispositivos Android não suportam mais armazenamento removível. Em vez disso, alguns dispositivos alocarão parte de sua memória interna não volátil que o Android para executar a mesma função de mídia removível. Isso é conhecido como armazenamento _emulado_ e ainda é considerado como armazenamento externo. Alternativamente, alguns dispositivos Android podem ter várias partições de armazenamento externas. Por exemplo, um tablet Android (além de seu armazenamento interno) pode ter emulado armazenamento e um ou mais slots para um cartão SD. Todas essas partições são tratadas pelo Android como armazenamento externo.

Em dispositivos que possuem vários usuários, cada usuário terá um diretório dedicado na partição de armazenamento externo principal para seu armazenamento externo. Aplicativos em execução como um usuário não terão acesso a arquivos de outro usuário no dispositivo. Os arquivos para todos os usuários ainda são legívels no mundo e graváveis pelo mundo; no entanto, o Android irá sandbox cada perfil de usuário dos outros.

Ler e escrever em arquivos é quase idêntico no Xamarin.Android como é para qualquer outro aplicativo .NET. O aplicativo Xamarin.Android determina o caminho para o arquivo que será manipulado e, em seguida, usa expressões padrão .NET para acesso a arquivos. Como os caminhos reais para o armazenamento interno e externo podem variar de dispositivo para dispositivo ou da versão do Android para a versão Android, não é recomendado codificar o caminho para os arquivos. Em vez disso, o Xamarin.Android expõe as APIs nativas do Android que ajudarão a determinar o caminho para arquivos no armazenamento interno e externo.

Este guia discutirá os conceitos e APIs no Android que são específicos para o armazenamento externo.

## <a name="public-and-private-files-on-external-storage"></a>Arquivos públicos e privados sobre armazenamento externo

Existem dois tipos diferentes de arquivos que um aplicativo pode manter no armazenamento externo:

* **Arquivos privados** &ndash; Arquivos privados são arquivos específicos para o seu aplicativo (mas ainda são legíveis pelo mundo e graváveis pelo mundo). O Android espera que os arquivos privados sejam armazenados em um diretório específico sobre armazenamento externo. Embora os arquivos sejam chamados de "privados", eles ainda são visíveis e acessíveis por outros aplicativos no dispositivo, eles não têm nenhuma proteção especial pelo Android.

* **Arquivos** &ndash; públicos Estes são arquivos que não são considerados específicos para o aplicativo e são destinados a ser compartilhados livremente.

As diferenças entre esses arquivos são principalmente conceituais. Os arquivos privados são privados no sentido de que eles são considerados como parte do aplicativo, enquanto os arquivos públicos são quaisquer outros arquivos que existem no armazenamento externo. O Android fornece duas APIs diferentes para resolver os caminhos para arquivos privados e públicos, mas caso contrário, as mesmas APIs .NET são usadas para ler e gravar nesses arquivos. Estas são as mesmas APIs que são discutidas na seção sobre [leitura e escrita.](~/android/platform/files/index.md#reading-or-writing-to-files-on-internal-storage)

### <a name="private-external-files"></a>Arquivos externos privados

Arquivos externos privados são considerados específicos para um aplicativo (semelhante a arquivos internos), mas estão sendo mantidos em armazenamento externo por várias razões (como ser muito grande para armazenamento interno). Semelhante aos arquivos internos, esses arquivos serão excluídos quando o aplicativo for desinstalado pelo usuário.

O local principal para arquivos externos `Android.Content.Context.GetExternalFilesDir(string type)`privados é encontrado chamando o método . Este método retornará um `Java.IO.File` objeto que representa o diretório de armazenamento externo privado para o aplicativo. Passar `null` para este método retornará o caminho para o diretório de armazenamento do usuário para o aplicativo. Como exemplo, para um aplicativo `com.companyname.app`com o nome do pacote, o diretório "raiz" dos arquivos externos privados seria:

```bash
/storage/emulated/0/Android/data/com.companyname.app/files/
```

Este documento se referirá ao diretório de armazenamento para arquivos privados sobre armazenamento externo como _ARMAZENAMENTO EXTERNO\_\_PRIVADO_.

O parâmetro `GetExternalFilesDir()` para é uma seqüência que especifica um _diretório de aplicativos_. Este é um diretório destinado a fornecer um local padrão para uma organização lógica de arquivos. Os valores de string estão `Android.OS.Environment` disponíveis através de constantes na classe:

| `Android.OS.Environment` | Diretório |
|-|-|
| DirectoryAlarms | **_ARMAZENAMENTO\_\_EXTERNO PRIVADO_/Alarmes** |
| DiretórioDcim | **_ARMAZENAMENTO\_\_EXTERNO PRIVADO_/DCIM** |
| DiretórioDownloads | **_ARMAZENAMENTO\_\_EXTERNO PRIVADO_/Download** |
| Documentos do diretório | **_ARMAZENAMENTO\_\_EXTERNO PRIVADO_/Documentos** |
| Filmes de diretório | **_ARMAZENAMENTO\_\_EXTERNO PRIVADO_/Filmes** |
| DirectoryMusic | **_ARMAZENAMENTO\_\_EXTERNO PRIVADO_/Música** |
| Notificações de diretório | **_ARMAZENAMENTO\_\_EXTERNO PRIVADO_/Notificações** |
| DiretórioPodcasts | **_ARMAZENAMENTO\_\_EXTERNO PRIVADO_/Podcasts** |
| DirectoryRingtones | **_ARMAZENAMENTO\_\_EXTERNO PRIVADO_/Toques** |
| DiretórioSImagen | **_ARMAZENAMENTO\_\_EXTERNO PRIVADO_/Imagens** |

Para dispositivos que possuem várias partições de armazenamento externas, cada partição terá um diretório destinado a arquivos privados. O `Android.Content.Context.GetExternalFilesDirs(string type)` método retornará `Java.IO.Files`uma matriz de . Cada objeto representará um diretório privado específico de aplicativos em todos os dispositivos de armazenamento compartilhados/externos onde o aplicativo pode colocar os arquivos que possui.

> [!IMPORTANT]
> O caminho exato para o diretório de armazenamento externo privado pode variar de dispositivo para dispositivo e entre versões do Android. Por causa disso, os aplicativos não devem codificar o caminho para este diretório e, `Android.Content.Context.GetExternalFilesDir()`em vez disso, usar as APIs Xamarin.Android, tais como .

### <a name="public-external-files"></a>Arquivos externos públicos

Arquivos públicos são arquivos que existem no armazenamento externo que não são armazenados no diretório que o Android aloca para arquivos privados. Os arquivos públicos não serão excluídos quando o aplicativo for desinstalado. Os aplicativos Android devem ser autorizados antes de ler ou escrever quaisquer arquivos públicos. É possível que arquivos públicos existam em qualquer lugar no armazenamento externo, mas por `Android.OS.Environment.ExternalStorageDirectory`convenção o Android espera que os arquivos públicos existam no diretório identificado pela propriedade. Essa propriedade retornará um `Java.IO.File` objeto que representa o diretório de armazenamento externo principal. Como exemplo, `Android.OS.Environment.ExternalStorageDirectory` pode se referir ao seguinte diretório:

```bash
/storage/emulated/0/
```

Este documento se referirá ao diretório de armazenamento para arquivos públicos sobre armazenamento externo como _ARMAZENAMENTO EXTERNO\_\_PÚBLICO_.

O Android também suporta o conceito de diretórios de aplicativos em _ARMAZENAMENTO EXTERNO\_\_PÚBLICO_. Esses diretórios são exatamente os mesmos `PRIVATE_EXTERNAL_STORAGE` dos diretórios de aplicação para e são descritos na tabela na seção anterior. O `Android.OS.Environment.GetExternalStoragePublicDirectory(string directoryType)` método retornará um `Java.IO.File` objeto que corresponde a um diretório de aplicação pública. O `directoryType` parâmetro é um parâmetro obrigatório `null`e não pode ser .

Por exemplo, `Environment.GetExternalStoragePublicDirectory(Environment.DirectoryDocuments).AbsolutePath` a chamada retornará uma seqüência que se assemelhará:

```bash
/storage/emulated/0/Documents
```

> [!IMPORTANT]
> O caminho exato para o diretório de armazenamento externo público pode variar de dispositivo para dispositivo e entre versões do Android. Por causa disso, os aplicativos não devem codificar o caminho para este diretório e, `Android.OS.Environment.ExternalStorageDirectory`em vez disso, usar as APIs Xamarin.Android, tais como .

## <a name="working-with-external-storage"></a>Trabalhando com armazenamento externo

Uma vez que um aplicativo Xamarin.Android tenha obtido o caminho completo para um arquivo, ele deve utilizar qualquer uma das APIs padrão .NET para criar, ler, escrever ou excluir arquivos. Isso maximiza a quantidade de código compatível entre plataformas para um aplicativo. No entanto, antes de tentar acessar um arquivo um aplicativo Xamarin.Android deve garantir que é possível acessar esse arquivo.

1. **Verifique** &ndash; o armazenamento externo Dependendo da natureza do armazenamento externo, é possível que ele não seja montado e utilizável pelo aplicativo. Todos os aplicativos devem verificar o estado do armazenamento externo antes de tentar usá-lo.
2. **Execute uma verificação de permissão em tempo de execução** &ndash; Um aplicativo Android deve solicitar permissão ao usuário para acessar o armazenamento externo. Isso significa que uma solicitação de permissão de tempo de execução deve ser realizada antes de qualquer acesso ao arquivo. O guia [Permissões em Xamarin.Android](~/android/app-fundamentals/permissions.md) contém mais detalhes sobre permissões android.

Cada uma dessas duas tarefas será discutida abaixo.

### <a name="verifying-that-external-storage-is-available"></a>Verificando se o armazenamento externo está disponível

O primeiro passo antes de escrever para o armazenamento externo é verificar se ele é legível ou gravável. A `Android.OS.Environment.ExternalStorageState` propriedade contém uma string que identifica o estado do armazenamento externo. Esta propriedade devolverá uma seqüência que representa o estado. Esta tabela é uma `ExternalStorageState` lista dos valores `Environment.ExternalStorageState`que podem ser devolvidos por :

| Estado de armazenamento externo | Descrição  |
|----------------------|---|
| MediaBadRemoval      | A mídia foi abruptamente removida sem ser adequadamente desmontada. |
| MediaChecking        | A mídia está presente, mas passando por uma verificação de disco.  |
| MediaEjecting        | A mídia está em processo de desmontar e ser ejetada.  |
| MediaMounted         | A mídia é montada e pode ser lida ou escrita para.  |
| MediaMountedReadOnly | A mídia é montada, mas só pode ser lida. |
| MediaNofs            | A mídia está presente, mas não contém um sistema de arquivos adequado para Android. |
| MídiaRemovida         | Não há mídia presente. |
| MídiaShared          | A mídia está presente, mas não está montada. Ele está sendo compartilhado via USB com outro dispositivo.|
| MediaUnknown         | O estado da mídia não é reconhecido pelo Android. |
| MídiaInmontável     | A mídia está presente, mas não pode ser montada pelo Android. |
| MídiaNão montada       | A mídia está presente, mas não está montada. |

A maioria dos aplicativos Android só precisará verificar se o armazenamento externo está montado. O seguinte trecho de código mostra como verificar se o armazenamento externo está montado para acesso somente à leitura ou acesso à gravação de leitura:

```csharp
bool isReadonly = Environment.MediaMountedReadOnly.Equals(Environment.ExternalStorageState);
bool isWriteable = Environment.MediaMounted.Equals(Environment.ExternalStorageState);
```

## <a name="external-storage-permissions"></a>Permissões de armazenamento externo

O Android considera o acesso ao armazenamento externo uma _permissão perigosa,_ o que normalmente exige que o usuário conceda sua permissão para acessar o recurso. O usuário pode revogar essa permissão a qualquer momento.  Isso significa que uma solicitação de permissão de tempo de execução deve ser realizada antes de qualquer acesso ao arquivo. Os aplicativos recebem automaticamente permissões para ler e escrever seus próprios arquivos privados. É possível que os aplicativos leiam e escrevam os arquivos privados que pertencem a outros aplicativos após a [permissão concedida](~/android/app-fundamentals/permissions.md) pelo usuário.

Todos os aplicativos Android devem declarar uma das duas permissões para armazenamento externo no **AndroidManifest.xml** . Para identificar as permissões, um `uses-permission` dos dois elementos a seguir deve ser adicionado ao **AndroidManifest.xml**:

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

> [!NOTE]
> Se o `WRITE_EXTERNAL_STORAGE`usuário `READ_EXTERNAL_STORAGE` conceder, então também é concedido implicitamente. Não é necessário solicitar ambas as permissões no **AndroidManifest.xml**.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

As permissões também podem ser adicionadas usando a guia **Manifesto do Android** das propriedades da **solução:**

![Solution Explorer - Permissões necessárias para o Visual Studio](./images/required-permissions.w157.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

As permissões também podem ser adicionadas usando a guia **Manifesto do Android** do bloco de propriedades da **solução**:

[![Solution Pad - Permissões necessárias para o Visual Studio para Mac](./images/required-permissions.m752-sml.png)](./images/required-permissions.m752.png#lightbox)

-----

De um modo geral, todas as permissões perigosas devem ser aprovadas pelo usuário. As permissões para armazenamento externo são uma anomalia na forma de haver exceções a esta regra, dependendo da versão do Android que o aplicativo está executando:

![Fluxograma de verificações de permissão de armazenamento externo](./images/external-permission-check-flowchart.png)

Para obter mais informações sobre a realização de solicitações de permissão de tempo de execução, consulte o guia [Permissões em Xamarin.Android](~/android/app-fundamentals/permissions.md). O [localFiles](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles) **de amostra de monodróides** também demonstra uma maneira de executar verificações de permissão de tempo de execução.

#### <a name="granting-and-revoking-permissions-with-adb"></a>Concessão e revogação de permissões com ADB

Durante o desenvolvimento de um aplicativo para Android, pode ser necessário conceder e revogar permissões para testar os vários fluxos de trabalho envolvidos com verificações de permissão de tempo de execução. É possível fazer isso no prompt de comando usando ADB. Os seguintes trechos da linha de comando demonstram como conceder ou revogar permissões usando ADB para um aplicativo Android cujo nome do pacote é **com.companyname.app**:

```bash
$ adb shell pm grant com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE

$ adb shell pm revoke com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE
```

## <a name="deleting-files"></a>Excluindo arquivos

Qualquer uma das APIs C# padrão pode ser usada para [`System.IO.File.Delete`](xref:System.IO.File.Delete*)excluir um arquivo do armazenamento externo, como . Também é possível usar as APIs Java em detrimento da portabilidade de código. Por exemplo:

```csharp
System.IO.File.Delete("/storage/emulated/0/Android/data/com.companyname.app/files/count.txt");
```

## <a name="related-links"></a>Links relacionados

* [Amostra de Arquivos Locais xamarin.Android em **amostras de monodróides**](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)
* [Permissões em Xamarin.Android](~/android/app-fundamentals/permissions.md)
