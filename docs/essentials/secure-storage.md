---
title: 'Xamarin.Essentials: Armazenamento seguro'
description: Este documento descreve a classe SecureStorage no Xamarin.Essentials, que ajuda a armazenar com segurança os pares de chave/valor simples. Ele aborda como usar a classe, os detalhes da implementação da plataforma e as limitações.
ms.assetid: 78856C0D-76BB-406E-A880-D5A3987B7D64
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.custom: video
ms.openlocfilehash: 1e4cb52772a60489f887116cbcfd4e6a8930fa3a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756792"
---
# <a name="xamarinessentials-secure-storage"></a>Xamarin.Essentials: Armazenamento seguro

A classe **SecureStorage** ajuda a armazenar com segurança os pares de chave/valor simples.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

Para acessar a funcionalidade **SecureStorage**, a seguinte configuração específica da plataforma é necessária:

# <a name="androidtabandroid"></a>[Android](#tab/android)

> [!TIP]
> O [Backup Automático para Aplicativos](https://developer.android.com/guide/topics/data/autobackup) é um recurso do Android 6.0 (nível da API 23) e posterior que faz o backup dos dados do aplicativo do usuário (preferências compartilhadas, arquivos no armazenamento interno do aplicativo e outros arquivos específicos). Os dados são restaurados quando um aplicativo é reinstalado ou instalado em um novo dispositivo. Isso pode afetar a `SecureStorage`, que utiliza as preferências de compartilhamento do backup e que não podem ser descriptografadas quando a restauração ocorrer. O Xamarin.Essentials trata automaticamente desse caso removendo a chave para que ela possa ser redefinida. No entanto, você pode avançar e desabilitar o Backup Automático.

### <a name="enable-or-disable-backup"></a>Habilitar ou desabilitar o backup
Você pode optar por desabilitar o Backup Automático para todo o aplicativo definindo a configuração `android:allowBackup` como falsa no arquivo `AndroidManifest.xml`. Essa abordagem só é recomendada se você planeja restaurar dados de uma outra maneira.

```xml
<manifest ... >
    ...
    <application android:allowBackup="false" ... >
        ...
    </application>
</manifest>
```

### <a name="selective-backup"></a>Backup seletivo
O backup automático pode ser configurado para desabilitar o backup de um conteúdo específico. Você pode criar uma regra personalizada definida para excluir itens do `SecureStore` de passarem por backup.

1. Defina o atributo `android:fullBackupContent` em seu **AndroidManifest.xml**:

    ```xml
    <application ...
        android:fullBackupContent="@xml/auto_backup_rules">
    </application>
    ```

2. Crie um novo arquivo XML chamado **auto_backup_rules.xml** no diretório **Resources/xml** com a ação de compilação de **AndroidResource**. Em seguida, defina o seguinte conteúdo que inclui todas as preferências compartilhadas, exceto para `SecureStorage`:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <full-backup-content>
        <include domain="sharedpref" path="."/>
        <exclude domain="sharedpref" path="${applicationId}.xamarinessentials.xml"/>
    </full-backup-content>
    ```

# <a name="iostabios"></a>[iOS](#tab/ios)

Ao desenvolver no **simulador do iOS**, habilite o direito **Keychain** e inclua um grupo de acesso keychain para o identificador do pacote do aplicativo. 

Abra **Entitlements.plist** no projeto do iOS, localize o direito **Keychain** e habilite-o. Isso adicionará automaticamente o identificador do aplicativo como um grupo.

Nas propriedades do projeto, em **Assinatura de pacote do iOS**, configure os **Direitos Personalizados** para **Entitlements.plist**.

> [!TIP]
> Ao implantar em um dispositivo iOS, esse direito não é necessário e deverá ser removido.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Não exige mais configurações.

-----

## <a name="using-secure-storage"></a>Uso do armazenamento seguro

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

Para salvar um valor para uma determinada _chave_ no armazenamento seguro:

```csharp
try
{
  await SecureStorage.SetAsync("oauth_token", "secret-oauth-token-value");
}
catch (Exception ex)
{
  // Possible that device doesn't support secure storage on device.
}
```

Para recuperar um valor do armazenamento seguro:

```csharp
try
{
  var oauthToken = await SecureStorage.GetAsync("oauth_token");
}
catch (Exception ex)
{
  // Possible that device doesn't support secure storage on device.
}
```

> [!NOTE]
> Se não houver valores associados à chave solicitada, `GetAsync` retornará `null`.

Para remover uma chave específica, chame:

```csharp
SecureStorage.Remove("oauth_token");
```

Para remover todas as chaves, chame:

```csharp
SecureStorage.RemoveAll();
```

## <a name="platform-implementation-specifics"></a>Particularidades de implementação da plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

O [Repositório de chaves do Android](https://developer.android.com/training/articles/keystore.html) é usado para armazenar a chave de criptografia usada para criptografar o valor antes que ele seja salvo em [Preferências Compartilhadas](https://developer.android.com/training/data-storage/shared-preferences.html) com um nome de arquivo **[ID-DO-SEU-PACOTE-DE-APLICATIVO].xamarinessentials**.  A chave (não uma chave de criptografia, a _chave_ para o _valor_) usada no arquivo de preferências compartilhadas é um _Hash MD5_ da chave passada para as APIs do `SecureStorage`.

## <a name="api-level-23-and-higher"></a>API nível 23 e superior

Em níveis da API mais recentes, uma chave **AES** é obtida do Repositório de chaves do Android e usada com uma cifra **AES/GCM/NoPadding** para criptografar o valor antes que ele seja armazenado no arquivo de preferências compartilhadas.

## <a name="api-level-22-and-lower"></a>Nível da API 22 e inferior

Em níveis de API mais antigos, o Repositório de chaves do Android só é compatível com o armazenamento de chaves **RSA**, que é usado com uma cifra **RSA/ECB/PKCS1Padding** para criptografar uma chave **AES** (aleatoriamente gerada no tempo de execução) e armazenado no arquivo de preferências compartilhadas sob a chave _SecureStorageKey_, caso ainda não tenha sido gerado.

**SecureStorage** usa a API [Preferências](preferences.md) e segue a mesma persistência de dados descrita na documentação de [Preferências](preferences.md#persistence). Se um dispositivo fizer o upgrade do nível da API 22 ou inferior para o nível da API 23 e superior, esse tipo de criptografia continuará a ser usado, a menos que o aplicativo seja desinstalado ou **RemoveAll** seja chamado.

# <a name="iostabios"></a>[iOS](#tab/ios)

[KeyChain](xref:Security.SecKeyChain) é usado para armazenar valores em dispositivos iOS com segurança.  O `SecRecord` usado para armazenar o valor tem um valor `Service` definido como **[ID-DO-SEU-PACOTE-DO-APLICATIVO].xamarinessentials**.

Em alguns casos os dados do conjunto de chaves estão sincronizados com o iCloud e a desinstalação do aplicativo poderá não remover os valores seguros do iCloud e outros dispositivos do usuário.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[DataProtectionProvider](https://docs.microsoft.com/uwp/api/windows.security.cryptography.dataprotection.dataprotectionprovider) é usado para criptografar valores em dispositivos UWP com segurança.

Os valores criptografados são armazenados em `ApplicationData.Current.LocalSettings`, dentro de um contêiner com o nome **[ID-DE-SEU-APLICATIVO].xamarinessentials**.

**SecureStorage** usa a API [Preferências](preferences.md) e segue a mesma persistência de dados descrita na documentação de [Preferências](preferences.md#persistence).

-----

## <a name="limitations"></a>Limitações

Essa API destina-se a armazenar pequenas quantidades de texto.  O desempenho pode ser lento se você tentar usá-lo para armazenar grandes quantidades de texto.

## <a name="api"></a>API

- [Código-fonte de SecureStorage](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/SecureStorage)
- [Documentação da API de SecureStorage](xref:Xamarin.Essentials.SecureStorage)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Secure-Storage-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
