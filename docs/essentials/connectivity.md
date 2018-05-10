---
title: Conectividade de Xamarin.Essentials
description: A classe de conectividade permite monitorar as alterações nas condições de rede do dispositivo, verifique o acesso de rede atual, e como ele está conectado no momento.
ms.assetid: E1B1F152-B1D5-4227-965E-C0AEBF528F49
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: fd757bec32d2854d2c9693812dece05ef11e2f80
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-connectivity"></a>Conectividade de Xamarin.Essentials

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **conectividade** permite classe monitorar as alterações nas condições da rede do dispositivo, verifique o acesso de rede atual, e como ele está conectado no momento.

## <a name="getting-started"></a>Guia de Introdução

Para acessar o **conectividade** funcionalidade a seguinte configuração específica de plataforma é necessária.

# <a name="androidtabandroid"></a>[Android](#tab/android)

O `AccessNetworkState` permissão é necessária e deve ser configurada no projeto Android. Isso pode ser adicionado das seguintes maneiras:

Abra o **AssemblyInfo.cs** arquivo sob o **propriedades** pasta e adicionar:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessNetworkState)]
```

OU atualize o manifesto do Android:

Abra o **AndroidManifest.xml** arquivo sob o **propriedades** pasta e adicione o seguinte dentro do **manifesto** nó.

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

Ou clique com o botão direito no projeto Android e abra as propriedades do projeto. Em **manifesto do Android** encontrar o **as permissões necessárias:** área e verifique se o **estado da rede acesso** permissão. Isso atualizará automaticamente o **AndroidManifest.xml** arquivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

Nenhuma configuração adicional necessária.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Nenhuma configuração adicional necessária.

-----

## <a name="using-connectivity"></a>Usando a conectividade

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Verificar o acesso de rede atual:

```csharp
var current = Connectivity.NetworkAccess;

if (current == NetworkAccess.Internet)
{
    // Connection to internet is available
}
```

[Acesso à rede](xref:Xamarin.Essentials.NetworkAccess) se enquadra em categorias a seguir:

* **Internet** – acesso Local e a internet.
* **ConstrainedInternet** – limitado acesso à internet. Indica a conectividade do portal cativo, onde o acesso local a um portal da web é fornecido, mas o acesso à Internet requer que as credenciais específicas são fornecidas por meio de um portal.
* **Local** – Local somente para acesso à rede.
* **Nenhum** – nenhuma conectividade está disponível.
* **Desconhecido** – não é possível determinar a conectividade com a internet.

Você pode verificar o tipo de [perfil de conexão](xref:Xamarin.Essentials.ConnectionProfile) o dispositivo estiver usando ativamente:

```csharp
var profiles = Connectivity.Profiles;
if (profiles.Contains(ConnectionProfile.WiFi))
{
    // Active Wi-Fi connection.
}
```

Sempre que o perfil de conexão ou rede acessar as alterações, você poderá receber um evento de disparo:

```csharp
public class ConnectivityTest
{
    public ConnectivityTest()
    {
        // Register for connectivity changes, be sure to unsubscribe when finished
        Connectivity.ConnectivityChanged += Connectivity_ConnectivityChanged;
    }

    void Connectivity_ConnectivityChanged(ConnectivityChangedEventArgs  e)
    {
        var access = e.NetworkAccess;
        var profiles = e.Profiles;
    }
}
```

## <a name="limitations"></a>Limitações

É importante observar que é possível que `Internet` é reportado pelo `NetworkAccess` mas acesso completo à web não está disponível. Devido ao funcionamento de conectividade em cada plataforma pode apenas garante que uma conexão está disponível. Por exemplo o dispositivo pode estar conectado a uma rede Wi-Fi, mas o roteador está desconectado da internet. Nesta instância Internet pode ser relatada, mas uma conexão ativa não está disponível.

## <a name="api"></a>API

* [Código-fonte conectividade](https://github.com/xamarin/Essentials/tree/master/Essentials/Connectivity)
* [Documentação da API de conectividade](xref:Xamarin.Essentials.Connectivity)
