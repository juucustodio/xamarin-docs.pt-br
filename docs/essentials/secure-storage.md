---
title: Armazenamento seguro de Xamarin.Essentials
description: A classe SecureStorage ajuda a armazenar com segurança os pares chave/valor simples.
ms.assetid: 78856C0D-76BB-406E-A880-D5A3987B7D64
ms.technology: xamarin-crossplatform
author: redth
ms.author: jodick
ms.date: 05/04/2018
ms.openlocfilehash: 23b38721d1437edba611dc6575593b83e2d4a548
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-secure-storage"></a>Armazenamento seguro de Xamarin.Essentials

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **SecureStorage** classe ajuda a armazenar com segurança os pares chave/valor simples.

## <a name="using-secure-storage"></a>Usando o armazenamento seguro

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Para salvar um valor para um determinado _chave_ no armazenamento seguro:

```csharp
await SecureStorage.SetAsync("oauth_token", "secret-oauth-token-value");
```

Para recuperar um valor de armazenamento seguro:

```csharp
var oauthToken = await SecureStorage.GetAsync("oauth_token");
```

## <a name="platform-implementation-specifics"></a>Detalhes de implementação de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

O [Android KeyStore](https://developer.android.com/training/articles/keystore.html) é usado para armazenar a chave de criptografia usada para criptografar o valor antes de ser salvo em um [preferências compartilhadas](https://developer.android.com/training/data-storage/shared-preferences.html) com um nome de arquivo de **.xamarinessentials [seu-aplicativo--ID do pacote]** .  A chave usada no arquivo de preferências compartilhadas é um _Hash MD5_ da chave passado para o `SecureStorage` da API.

## <a name="api-level-23-and-higher"></a>API nível 23 e superior

Nos níveis de API mais recentes, uma **AES** chave é obtida do repositório de chaves Android e usada com um **GCM/AES/NoPadding** criptografia para criptografar o valor antes de ser armazenado no arquivo de preferências compartilhadas.

## <a name="api-level-22-and-lower"></a>API nível 22 e inferior

Em níveis de API mais antigos, o armazenamento de chaves Android só dá suporte ao armazenamento **RSA** chaves, que é usado com um **ECB/RSA/PKCS1Padding** criptografia para criptografar um **AES** (aleatoriamente de chave gerados em tempo de execução) e armazenados no arquivo de preferências compartilhadas sob a chave _SecureStorageKey_, se uma já não tiver sido gerada.

Todos os valores criptografados serão removidos quando o aplicativo é desinstalado do dispositivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

[Conjunto de chaves](https://developer.xamarin.com/api/type/Android.Security.KeyChain/) é usado para armazenar valores com segurança em dispositivos iOS.  O `SecRecord` usado para armazenar o valor tem um `Service` valor definido como **.xamarinessentials [seu-aplicativo--ID do pacote]**.

Em alguns casos os dados do conjunto de chaves estão sincronizados com o iCloud e desinstalar o aplicativo não pode remover os valores seguros de iCloud e outros dispositivos do usuário.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[DataProtectionProvider](https://docs.microsoft.com/en-us/uwp/api/windows.security.cryptography.dataprotection.dataprotectionprovider) é usado para valores de encryped com segurança em dispositivos UWP.

Encryped valores são armazenados em `ApplicationData.Current.LocalSettings`, dentro de um contêiner com o nome de **.xamarinessentials [seu-aplicativo-ID]**.

Desinstalar o aplicativo fará com que o _LocalSettings_e criptografados todos os valores a ser removido também.

-----

## <a name="limitations"></a>Limitações

Essa API destina-se para armazenar pequenas quantidades de texto.  Desempenho pode ser lento se você tentar usá-lo para armazenar grandes quantidades de texto.

## <a name="api"></a>API

- [Código-fonte SecureStorage](https://github.com/xamarin/Essentials/tree/master/Essentials/SecureStorage)
- [Documentação da API SecureStorage](xref:Xamarin.Essentials.SecureStorage)
