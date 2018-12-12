---
title: 'Xamarin.Essentials: Connectivity'
description: A classe Connectivity no Xamarin.Essentials permite monitorar alterações em condições de rede do dispositivo, verificar o acesso da rede atual e como ele está conectado no momento.
ms.assetid: E1B1F152-B1D5-4227-965E-C0AEBF528F49
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 3c29fc85d20e3a4d91a1ae63feca1cb668af141e
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52899013"
---
# <a name="xamarinessentials-connectivity"></a>Xamarin.Essentials: Connectivity

A classe **Connectivity** permite monitorar alterações em condições de rede do dispositivo, verificar o acesso da rede atual e como ele está conectado no momento.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

Para acessar a funcionalidade **Connectivity**, a seguinte configuração específica da plataforma é necessária.

# <a name="androidtabandroid"></a>[Android](#tab/android)

A permissão `AccessNetworkState` é necessária e deve ser configurada no projeto do Android. Ela pode ser usado das seguintes maneiras:

Abra o arquivo **AssemblyInfo.cs** na pasta **Propriedades** e adicione:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.AccessNetworkState)]
```

OU Atualize o Manifesto do Android:

Abra o arquivo **AndroidManifest.xml** na pasta **Propriedades** e adicione o seguinte dentro do nó do **manifesto**.

```xml
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```

Ou clique com o botão direito do mouse no projeto do Android e abra as propriedades do projeto. Em **Manifesto do Android**, localize a área **Permissões necessárias:** e marque a permissão **Acessar estado da rede**. Isso atualizará automaticamente o arquivo **AndroidManifest.xml**.

# <a name="iostabios"></a>[iOS](#tab/ios)

Não exige mais configurações.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Não exige mais configurações.

-----

## <a name="using-connectivity"></a>Uso de Connectivity

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

Verifique o acesso à rede atual:

```csharp
var current = Connectivity.NetworkAccess;

if (current == NetworkAccess.Internet)
{
    // Connection to internet is available
}
```

O [acesso à rede](xref:Xamarin.Essentials.NetworkAccess) se enquadra às seguintes categorias:

* **Internet** – acesso local e à Internet.
* **ConstrainedInternet** – acesso limitado à Internet. Indica a conectividade do portal cativo, em que o acesso local a um portal da Web é fornecido, mas o acesso à Internet exige que credenciais específicas sejam fornecidas através de um portal.
* **Local** – apenas acesso local à rede.
* **Nenhum** – nenhuma conectividade disponível.
* **Desconhecido** – não é possível determinar a conectividade com a internet.

É possível verificar que tipo de [perfil de conexão](xref:Xamarin.Essentials.ConnectionProfile) o dispositivo está usando ativamente:

```csharp
var profiles = Connectivity.ConnectionProfiles;
if (profiles.Contains(ConnectionProfile.WiFi))
{
    // Active Wi-Fi connection.
}
```

Sempre que o perfil de conexão ou o acesso à rede mudar, você poderá receber um evento quando houver um acionamento:

```csharp
public class ConnectivityTest
{
    public ConnectivityTest()
    {
        // Register for connectivity changes, be sure to unsubscribe when finished
        Connectivity.ConnectivityChanged += Connectivity_ConnectivityChanged;
    }

    void Connectivity_ConnectivityChanged(object sender, ConnectivityChangedEventArgs  e)
    {
        var access = e.NetworkAccess;
        var profiles = e.ConnectionProfiles;
    }
}
```

## <a name="limitations"></a>Limitações

É importante observar que é possível que `Internet` seja reportado pelo `NetworkAccess`. No entanto, o acesso completo à Web não estará disponível. Devido ao funcionamento da conectividade em cada plataforma, só é possível garantir que uma conexão disponível. Por exemplo, o dispositivo pode estar conectado a uma rede Wi-Fi, mas o roteador está desconectado da Internet. Nesta instância, a Internet pode ser reportada, mas uma conexão ativa não estará disponível.

## <a name="api"></a>API

* [Código-fonte de Connectivity](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Connectivity)
* [Documentação da API de Connectivity](xref:Xamarin.Essentials.Connectivity)
