---
title: Acesso a arquivos no armazenamento externo com o xamarin. Android
description: Este guia discutirá o acesso a arquivos no armazenamento externo no xamarin. Android
ms.prod: xamarin
ms.assetid: 40da10b2-a207-4f9c-a2dd-165d9b662f33
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/23/2018
ms.openlocfilehash: 78051fce44239eea86948988a4d19ac37c5ea0d5
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58854893"
---
# <a name="external-storage"></a>Armazenamento externo

Armazenamento externo refere-se ao armazenamento de arquivos não está no armazenamento interno e não exclusivamente está acessível para o aplicativo que é responsável por arquivo. O objetivo principal de armazenamento externo é fornecer um local para colocar os arquivos que devem ser compartilhados entre aplicativos ou que são muito grande para caber na memória interna.

Armazenamento externo, falando historicamente chamados uma partição de disco em mídia removível como um cartão SD (também conhecido como _portáteis de armazenamento_). Essa distinção não é mais tão relevante quanto a dispositivos Android evoluíram e muitos dispositivos Android não dá suporte a armazenamento removível. Em vez disso, alguns dispositivos alocará alguns dos seu memória não-volátil interna que Android para executar a mesma mídia removível de função. Isso é conhecido como _emulado_ armazenamento e ainda é considerado como sendo o armazenamento externo. Como alternativa, alguns dispositivos Android podem ter várias partições de armazenamento externo. Por exemplo, um Android tablet (além de seu armazenamento interno) pode ter emulados de armazenamento e um ou mais slots para um cartão SD. Todas essas partições são tratadas pelo Android como o armazenamento externo.

Em dispositivos que têm vários usuários, cada usuário terá um diretório dedicado na partição primária de armazenamento externo para seu armazenamento externo. Aplicativos em execução como um usuário não terá acesso aos arquivos de outro usuário no dispositivo. Os arquivos para todos os usuários ainda estão mundo legível e gravável mundo; No entanto, o Android será área restrita cada perfil de usuário dos outros.

Lendo e gravando em arquivos é quase idêntico no xamarin. Android quanto de qualquer outro aplicativo .NET. O aplicativo xamarin. Android determina o caminho para o arquivo que será manipulado, em seguida, usa padrão .NET expressões para acesso a arquivos. Como os caminhos reais para armazenamento interno e externo podem variar de um dispositivo para o dispositivo ou de versão do Android à versão do Android, não é recomendável para codificar o caminho para os arquivos. Em vez disso, o xamarin. Android expõe as APIs do Android nativo que ajudarão a determinar o caminho para arquivos no armazenamento interno e externo.

Este guia discute os conceitos e as APIs no Android que são específicos para o armazenamento externo.

## <a name="public-and-private-files-on-external-storage"></a>Arquivos públicos e privados no armazenamento externo

Há dois tipos diferentes de arquivos que um aplicativo pode manter no armazenamento externo:

* **Privado** arquivos &ndash; arquivos particulares são arquivos que são específicos para seu aplicativo (mas são ainda mundo legível e gravável com mundo). Android espera que os arquivos particulares são armazenados em um diretório específico no armazenamento externo. Mesmo que os arquivos são chamados de "private", eles ainda são visíveis e acessíveis por outros aplicativos no dispositivo, eles não têm qualquer proteção especial pelo Android.

* **Público** arquivos &ndash; esses são arquivos que não são considerados para ser específico para o aplicativo e devem ser compartilhada livremente.

As diferenças entre esses arquivos for principalmente conceitual. Arquivos privados são particulares no sentido de que eles são considerados parte do aplicativo, enquanto arquivos públicos são todos os arquivos que existem no armazenamento externo. O Android fornece duas APIs diferentes para resolver os caminhos para arquivos públicos e privados, mas caso contrário, as mesmas APIs do .NET são usadas para ler e gravar nesses arquivos. Essas são as mesmas APIs que são discutidas na seção sobre [lendo e gravando](~/android/platform/files/index.md#reading-or-writing-to-files-on-internal-storage).

### <a name="private-external-files"></a>Arquivos externos privados

Arquivos externos privados são considerados para ser específico a um aplicativo (semelhante a arquivos internos), mas estão sendo mantidos no armazenamento externo para qualquer número de razões (tais como sendo muito grande para armazenamento interno). Semelhante a arquivos internos, esses arquivos serão excluídos quando o aplicativo seja desinstalado pelo usuário.

O local primário para arquivos externos particulares for encontrado, chamando o método `Android.Content.Context.GetExternalFilesDir(string type)`. Esse método retornará um `Java.IO.File` objeto que representa o diretório de armazenamento externo privado para o aplicativo. Passando `null` para esse método retornará o caminho para o diretório de armazenamento do usuário para o aplicativo. Por exemplo, para um aplicativo com o nome do pacote `com.companyname.app`, o diretório "raiz" dos arquivos externos privados seria:

```bash
/storage/emulated/0/Android/data/com.companyname.app/files/
```

Este documento fará referência para o diretório de armazenamento para arquivos particulares no armazenamento externo como _PRIVADOS\_externo\_armazenamento_.


O parâmetro `GetExternalFilesDir()` é uma cadeia de caracteres que especifica um _diretório de aplicativo_. Este é um diretório que o objetivo de fornecer um local padrão para uma organização lógica de arquivos. Os valores de cadeia de caracteres estão disponíveis por meio de constantes no `Android.OS.Environment` classe:

| `Android.OS.Environment` | Diretório |
|-|-|
| DirectoryAlarms | **_PRIVATE\_EXTERNAL\_STORAGE_/Alarms** |
| DirectoryDcim | **_PRIVATE\_EXTERNAL\_STORAGE_/DCIM** |
| DirectoryDownloads | **_PRIVATE\_EXTERNAL\_STORAGE_/Download** |
| DirectoryDocuments | **_PRIVATE\_EXTERNAL\_STORAGE_/Documents** |
| DirectoryMovies | **_PRIVATE\_EXTERNAL\_STORAGE_/Movies** |
| DirectoryMusic | **_PRIVATE\_EXTERNAL\_STORAGE_/Music** |
| DirectoryNotifications | **_PRIVATE\_EXTERNAL\_STORAGE_/Notifications** |
| DirectoryPodcasts | **_PRIVATE\_EXTERNAL\_STORAGE_/Podcasts** |
| DirectoryRingtones | **_PRIVATE\_EXTERNAL\_STORAGE_/Ringtones** |
| DirectoryPictures | **_PRIVATE\_EXTERNAL\_STORAGE_/Pictures** |

Para dispositivos que têm várias partições de armazenamento externo, cada partição terá um diretório que é destinado para arquivos particulares. O método `Android.Content.Context.GetExternalFilesDirs(string type)` retornará uma matriz de `Java.IO.Files`. Cada objeto representará um diretório específico do aplicativo privado em todos os dispositivos de armazenamento compartilhados/externa em que o aplicativo pode colocar os arquivos que ele possui.

> [!IMPORTANT]
> O caminho exato para o diretório de armazenamento externa privada pode variar de dispositivo para o dispositivo e entre versões do Android. Por isso, aplicativos devem não arduamente o caminho para esse diretório de código e em vez disso, use as APIs de xamarin. Android, como `Android.Content.Context.GetExternalFilesDir()`.

### <a name="public-external-files"></a>Arquivos externos públicos

Arquivos públicos são arquivos que existem no armazenamento externo que não são armazenados no diretório que o Android aloca para arquivos particulares. Arquivos públicos não serão excluídos quando o aplicativo é desinstalado. Aplicativos Android devem ter a permissão antes que possam ler ou gravar todos os arquivos públicos. É possível que os arquivos públicos existir em qualquer lugar no armazenamento externo, mas, por convenção, o Android espera que os arquivos públicos para existir no diretório identificado pela propriedade `Android.OS.Environment.ExternalStorageDirectory`. Essa propriedade retornará um `Java.IO.File` objeto que representa o diretório de armazenamento externo principal. Por exemplo, `Android.OS.Environment.ExternalStorageDirectory` pode referir-se no seguinte diretório:

```bash
/storage/emulated/0/
```

Este documento fará referência para o diretório de armazenamento de arquivos públicos no armazenamento externo como _pública\_externo\_armazenamento_.


Android também é compatível com o conceito de diretórios de aplicativos no _pública\_externo\_armazenamento_. Esses diretórios são exatamente o mesmo que os diretórios de aplicativo para `_PRIVATE\_EXTERNAL\_STORAGE_` e são descritos na tabela na seção anterior. O método `Android.OS.Environment.GetExternalStoragePublicDirectory(string directoryType)` retornará um `Java.IO.File` objeto corresponder a uma pasta pública do aplicativo. O `directoryType` parâmetro é um parâmetro obrigatório e não pode ser `null`.

Por exemplo, chamar `Environment.GetExternalStoragePublicDirectory(Environment.DirectoryDocuments).AbsolutePath` retornará uma cadeia de caracteres que será parecida com:

```bash
/storage/emulated/0/Documents
```

> [!IMPORTANT]
> O caminho exato para o diretório de armazenamento externo público pode variar de dispositivo para o dispositivo e entre versões do Android. Por isso, aplicativos devem não arduamente o caminho para esse diretório de código e em vez disso, use as APIs de xamarin. Android, como `Android.OS.Environment.ExternalStorageDirectory`.

## <a name="working-with-external-storage"></a>Trabalhar com o armazenamento externo

Depois que um aplicativo xamarin. Android obteve o caminho completo para um arquivo, ele deve utilizar qualquer uma das APIs do .NET standard para criar, ler, gravar ou excluir arquivos. Isso maximiza a quantidade de entre o código de plataforma compatível para um aplicativo. No entanto, antes de tentar acessar um arquivo de um aplicativo xamarin. Android deve garantir que é possível acessar esse arquivo.

1. **Verifique se o armazenamento externo** &ndash; dependendo da natureza de armazenamento externo, é possível que ele não pode ser montado e utilizável pelo aplicativo. Todos os aplicativos devem verificar o estado do armazenamento externo antes de tentar usá-lo.
2. **Executar uma verificação de permissão de tempo de execução** &ndash; Android de um aplicativo deve solicitar permissão do usuário para acessar o armazenamento externo. Isso significa que uma solicitação de permissão deve ser executada antes de qualquer acesso a arquivos de tempo de execução. O guia [permissões no xamarin. Android](~/android/app-fundamentals/permissions.md) contém mais detalhes sobre as permissões do Android.

Cada uma dessas duas tarefas será discutida abaixo.

### <a name="verifying-that-external-storage-is-available"></a>Verificando se o armazenamento externo está disponível

A primeira etapa antes de gravar no armazenamento externo é verificar se ele é legíveis ou graváveis. O `Android.OS.Environment.ExternalStorageState` propriedade contém uma cadeia de caracteres que identifica o estado do armazenamento externo. Essa propriedade retornará uma cadeia de caracteres que representa o estado. Esta tabela é uma lista da `ExternalStorageState` valores que podem ser retornados por `Environment.ExternalStorageState`:

| ExternalStorageState | Descrição  |
|----------------------|---|
| MediaBadRemoval      | A mídia foi removida abruptamente sem corretamente que está sendo desmontado. |
| MediaChecking        | A mídia está presente, mas passando por um disco de verificação.  |
| MediaEjecting        | Mídia está no processo que está sendo desmontado e desconsiderados.  |
| MediaMounted         | Mídia é montada e pode ser lidos ou gravada.  |
| MediaMountedReadOnly | Mídia está montada, mas só pode ser lidos do. |
| MediaNofs            | Mídia está presente, mas não contém um sistema de arquivos adequado para o Android. |
| MediaRemoved         | Não há nenhuma mídia presente. |
| MediaShared          | Mídia está presente, mas não está montada. Ele está sendo compartilhado via USB com outro dispositivo.|
| MediaUnknown         | O estado da mídia não é reconhecido pelo Android. |
| MediaUnmountable     | A mídia está presente, mas não pode ser montada pelo Android. |
| MediaUnmounted       | A mídia está presente, mas não está montada. |


A maioria dos aplicativos Android só precisará verificar se o armazenamento externo está montado. O trecho de código a seguir mostra como verificar se o armazenamento externo está montado para acesso de leitura / gravação ou acesso somente leitura:

```csharp
bool isReadonly = Environment.MediaMountedReadOnly.Equals(Environment.ExternalStorageState);
bool isWriteable = Environment.MediaMounted.Equals(Environment.ExternalStorageState);
```

## <a name="external-storage-permissions"></a>Permissões de armazenamento externo

Android considera ao acessar o armazenamento externo para ser um _permissão perigoso_, que normalmente requer que o usuário conceder permissão para acessar o recurso. O usuário pode revogar essa permissão a qualquer momento.  Isso significa que uma solicitação de permissão deve ser executada antes de qualquer acesso a arquivos de tempo de execução. Aplicativos recebem automaticamente permissões para ler e gravar seus próprios arquivos particulares. É possível que aplicativos ler e gravar os arquivos particulares que pertencem a outros aplicativos depois de ficar [concedeu permissão](~/android/app-fundamentals/permissions.md) pelo usuário.

Todos os aplicativos do Android devem declarar uma das duas permissões para armazenamento externo na **androidmanifest. XML** . Para identificar as permissões, uma das duas seguintes `uses-permission` elementos deve ser adicionada ao **androidmanifest. XML**:

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

> [!NOTE]
> Se o usuário concede `WRITE_EXTERNAL_STORAGE`, em seguida, `READ_EXTERNAL_STORAGE` também é implicitamente concedida. Não é necessário solicitar as permissões no **androidmanifest. XML**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

As permissões também podem ser adicionadas usando o **manifesto do Android** guia da **propriedades da solução**:

![Gerenciador de soluções – permissões necessárias para o Visual Studio](./images/required-permissions.w157.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

As permissões também podem ser adicionadas usando o **manifesto do Android** guia o **painel de propriedades de solução**:

[![Painel de soluções – permissões necessárias para o Visual Studio para Mac](./images/required-permissions.m752-sml.png)](./images/required-permissions.m752.png#lightbox)

-----

Em termos gerais, todas as permissões perigosas devem ser aprovadas pelo usuário. As permissões para armazenamento externo são uma anomalia em que há exceções a essa regra, dependendo da versão do Android que o aplicativo está em execução:

![Fluxograma de verificações de permissão de armazenamento externo](./images/external-permission-check-flowchart.png)

Para obter mais informações sobre como executar solicitações de permissão de tempo de execução, consulte o guia [permissões no xamarin. Android](~/android/app-fundamentals/permissions.md). O **monodroid-sample** [Arquivos_locais](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles) também demonstra uma maneira de executar verificações de permissão de tempo de execução.

#### <a name="granting-and-revoking-permissions-with-adb"></a>Conceder e revogar permissões com o ADB

No decorrer do desenvolvimento de um aplicativo do Android, ele pode ser necessário conceder e revogar permissões para os diversos fluxos de trabalho envolvidos com verificações de permissão de tempo de execução de teste. É possível fazer isso no prompt de comando usando o ADB. Os trechos de linha de comando a seguir demonstram como conceder ou revogar permissões usando o ADB para um aplicativo Android é cujo nome de pacote **com.companyname.app**:

```bash
$ adb shell pm grant com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE

$ adb shell pm revoke com.companyname.app android.permission.WRITE_EXTERNAL_STORAGE
```

## <a name="deleting-files"></a>Excluindo arquivos

Qualquer uma das APIs do c# pode ser usadas para excluir um arquivo de armazenamento externo, como o padrão de [ `System.IO.File.Delete` ](xref:System.IO.File.Delete*). Também é possível usar as APIs de Java às custas de portabilidade do código. Por exemplo:

```csharp
System.IO.File.Delete("/storage/emulated/0/Android/data/com.companyname.app/files/count.txt");
```

## <a name="related-links"></a>Links relacionados

* [Exemplo de arquivos locais do xamarin. Android em **monodroid-samples**](https://github.com/xamarin/monodroid-samples/tree/master/LocalFiles)
* [Permissões no xamarin. Android](~/android/app-fundamentals/permissions.md)
