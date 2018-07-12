---
title: 'Xamarin.Essentials: conectividade'
description: A classe de conectividade no Xamarin.Essentials permite monitorar alterações em condições de rede do dispositivo, verifique o acesso de rede atual, e como ele está conectado no momento.
ms.assetid: E1B1F152-B1D5-4227-965E-C0AEBF528F49
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 54c165e15e725caaecb1573b74cfe295170db141
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38848603"
---
# <a name="xamarinessentials-connectivity"></a>Xamarin.Essentials: conectividade

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **conectividade** classe permite que o você monitorar as alterações em condições de rede do dispositivo, verifique o acesso de rede atual, e como ele está conectado no momento.

## <a name="getting-started"></a>Guia de Introdução

Para acessar o **conectividade** funcionalidade a seguinte configuração específica de plataforma é necessária.

# <a name="androidtabandroid"></a>[Android](#tab/android)

O `AccessNetworkState` permissão é necessária e deve ser configurada no projeto do Android. Isso pode ser adicionado das seguintes maneiras:

Abra o **AssemblyInfo.cs** do arquivo sob o **propriedades** pasta e adicione:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessNetworkState)]
```

OU atualize o manifesto do Android:

Abra o **androidmanifest. XML** arquivo sob o **propriedades** pasta e adicione o seguinte dentro do **manifesto** nó.

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

Ou clique com botão direito no projeto do Android e abra as propriedades do projeto. Sob **manifesto do Android** localizar o **permissões necessárias:** área e verifique se o **estado da rede de acesso** permissão. Isso atualizará automaticamente a **androidmanifest. XML** arquivo.

# <a name="iostabios"></a>[iOS](#tab/ios)

Nenhuma configuração adicional necessária.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Nenhuma configuração adicional necessária.

-----

## <a name="using-connectivity"></a>Usando a conectividade

Adicione uma referência ao Xamarin.Essentials em sua classe:

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

[Acesso à rede](xref:Xamarin.Essentials.NetworkAccess) se encaixa nas categorias a seguir:

* **Internet** – acesso Local e internet.
* **ConstrainedInternet** – acesso limitado à internet. Indica a conectividade do portal cativo, onde o acesso local a um portal da web é fornecido, mas o acesso à Internet requer que as credenciais específicas são fornecidas por meio de um portal.
* **Local** – Local apenas acesso à rede.
* **Nenhum** – nenhuma conectividade está disponível.
* **Desconhecido** – não é possível determinar a conectividade com a internet.

Você pode verificar que tipo de [perfil de conexão](xref:Xamarin.Essentials.ConnectionProfile) o dispositivo estiver usando ativamente:

```csharp
var profiles = Connectivity.Profiles;
if (profiles.Contains(ConnectionProfile.WiFi))
{
    // Active Wi-Fi connection.
}
```

Sempre que o perfil de conexão ou a rede acessar as alterações, você poderá receber um evento quando disparado:

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

É importante observar que é possível que `Internet` é relatado pelo `NetworkAccess` mas total acesso à web não está disponível. Devido ao funcionamento de conectividade em cada plataforma só pode garantir que uma conexão está disponível. Por exemplo, o dispositivo pode estar conectado a uma rede Wi-Fi, mas o roteador está desconectado da internet. Nesta instância pode ser relatada à Internet, mas uma conexão do Active Directory não está disponível.

## <a name="api"></a>API

* [Código-fonte de conectividade](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Connectivity)
* [Documentação da API de conectividade](xref:Xamarin.Essentials.Connectivity)
