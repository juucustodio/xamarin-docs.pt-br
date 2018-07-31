---
title: 'Xamarin.Essentials: Armazenamento seguro'
description: Este documento descreve a classe de SecureStorage Xamarin.Essentials, que ajuda a armazenar com segurança os pares chave/valor simples. Ele discute como usar a classe, particularidades de implementação de plataforma e as limitações.
ms.assetid: 78856C0D-76BB-406E-A880-D5A3987B7D64
author: redth
ms.author: jodick
ms.date: 05/04/2018
ms.openlocfilehash: 2dfdb7051b269e73c68290a557849b9ae606c165
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353289"
---
# <a name="xamarinessentials-secure-storage"></a>Xamarin.Essentials: Armazenamento seguro

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **SecureStorage** classe ajuda a armazenar com segurança os pares chave/valor simples.

## <a name="getting-started"></a>Guia de Introdução

Para acessar o **SecureStorage** funcionalidade, a seguinte configuração específica da plataforma é necessária:

# <a name="androidtabandroid"></a>[Android](#tab/android)

Nenhuma configuração adicional necessária.

# <a name="iostabios"></a>[iOS](#tab/ios)

Ao desenvolver no simulador de iOS, habilite a **Keychain** direitos e adicionar um grupo de acesso ao conjunto de chaves para o identificador de pacote do aplicativo.

Abra o **Entitlements. plist** no projeto do iOS e localizar o **conjunto de chaves** direitos e habilitá-lo. Isso adicionará automaticamente o identificador do aplicativo como um grupo.

Nas propriedades do projeto, sob **assinatura do pacote iOS** definir os **direitos personalizados** para **Entitlements. plist**.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Nenhuma configuração adicional necessária.

-----

## <a name="using-secure-storage"></a>Usando o armazenamento seguro

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Para salvar um valor para um determinado _chave_ no armazenamento seguro:

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

Para recuperar um valor de armazenamento seguro:

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
> Se não houver nenhum valor associado com a chave solicitada, `GetAsync` retornará `null`.

Para remover uma chave específica, chame:

```csharp
SecureStorage.Remove("oauth_token");
```

Para remover todas as chaves, chame:

```csharp
SecureStorage.RemoveAll();
```


## <a name="platform-implementation-specifics"></a>Particularidades de implementação de plataforma

# <a name="androidtabandroid"></a>[Android](#tab/android)

O [Android KeyStore](https://developer.android.com/training/articles/keystore.html) é usado para armazenar a chave de criptografia usada para criptografar o valor antes de ser salvo em um [preferências compartilhadas](https://developer.android.com/training/data-storage/shared-preferences.html) com um nome de arquivo de **.xamarinessentials [YOUR-APP-pacote-ID]** .  A chave usada no arquivo de preferências compartilhado é um _Hash MD5_ da chave passado para o `SecureStorage` APIs.

## <a name="api-level-23-and-higher"></a>Nível de API 23 e superior

Nos níveis de API mais recentes, uma **AES** chave é obtida do repositório de chaves Android e usada com um **GCM/AES/NoPadding** criptografia para criptografar o valor antes de ser armazenada no arquivo de preferências compartilhado.

## <a name="api-level-22-and-lower"></a>API nível 22 e inferior

Nos níveis de API mais antigos, o repositório de chaves Android dá suporte apenas a armazenar **RSA** chaves, que é usado com um **ECB/RSA/PKCS1Padding** criptografia para criptografar um **AES** (aleatoriamente da chave gerado em tempo de execução) e armazenados no arquivo de preferências compartilhado sob a chave _SecureStorageKey_, se um já não tiver sido gerado.

**SecureStorage** usa o [preferências](preferences.md) API e a persistência de dados mesmo descrita a seguir o [preferências](preferences.md#persistence) documentação. Se um dispositivo é atualizado da API nível 22 ou inferior ao nível de API 23 e superior, esse tipo de criptografia continuará a ser usado, a menos que o aplicativo é desinstalado ou **RemoveAll** é chamado.

# <a name="iostabios"></a>[iOS](#tab/ios)

[Conjunto de chaves](https://developer.xamarin.com/api/type/Security.SecKeyChain/) é usado para armazenar valores com segurança em dispositivos iOS.  O `SecRecord` usado para armazenar o valor tem um `Service` valor definido como **.xamarinessentials [YOUR-APP-BUNDLE-ID]**.

Em alguns casos os dados do conjunto de chaves estão sincronizados com o iCloud e desinstalar o aplicativo não pode remover os valores seguros de iCloud e outros dispositivos do usuário.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[DataProtectionProvider](https://docs.microsoft.com/uwp/api/windows.security.cryptography.dataprotection.dataprotectionprovider) é usado para valores criptografados com segurança em dispositivos UWP.

Valores criptografados são armazenados no `ApplicationData.Current.LocalSettings`, dentro de um contêiner com o nome **.xamarinessentials [YOUR-APP-ID]**.

**SecureStorage** usa o [preferências](preferences.md) API e a persistência de dados mesmo descrita a seguir o [preferências](preferences.md#persistence) documentação.

-----

## <a name="limitations"></a>Limitações

Essa API destina-se para armazenar pequenas quantidades de texto.  Desempenho pode ser lento se você tentar usá-lo para armazenar grandes quantidades de texto.

## <a name="api"></a>API

- [Código-fonte SecureStorage](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/SecureStorage)
- [Documentação da API SecureStorage](xref:Xamarin.Essentials.SecureStorage)
