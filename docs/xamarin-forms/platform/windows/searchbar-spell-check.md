---
title: " SearchBar Verificação Ortográfica no Windows"
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Windows específica que permite que um SearchBar interaja com o mecanismo de verificação ortográfica.
ms.prod: xamarin
ms.assetid: 7974C91F-7502-4DB3-B0E9-C45E943DDA26
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f6c8810ea37c767ef67c7f53d312b63a9de09f26
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136559"
---
# <a name="searchbar-spell-check-on-windows"></a>SearchBar Verificação Ortográfica no Windows

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa Plataforma Universal do Windows específica à plataforma permite que um [`SearchBar`](xref:Xamarin.Forms.SearchBar) interaja com o mecanismo de verificação ortográfica. Ele é consumido em XAML definindo a [`SearchBar.IsSpellCheckEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty) Propriedade anexada como um `boolean` valor:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ... windows:SearchBar.IsSpellCheckEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

searchBar.On<Windows>().SetIsSpellCheckEnabled(true);
```

O `SearchBar.On<Windows>` método especifica que essa plataforma específica será executada somente no plataforma universal do Windows. O [ `SearchBar.SetIsSpellCheckEnabled` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. SearchBar. SetIsSpellCheckEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . SearchBar}, System. Boolean)), no [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, ativa e desativa o verificador de ortografia. Além disso, o `SearchBar.SetIsSpellCheckEnabled` método pode ser usado para alternar o verificador ortográfico chamando o [ `SearchBar.GetIsSpellCheckEnabled` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. SearchBar. GetIsSpellCheckEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . SearchBar})) para retornar se o verificador ortográfico está habilitado:

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

O resultado é que o texto inserido no [`SearchBar`](xref:Xamarin.Forms.SearchBar) pode ser verificado com ortografia, com grafias incorretas sendo indicadas ao usuário:

![Verificação ortográfica SearchBar específica da plataforma](searchbar-spell-check-images/searchbar-spellcheck.png "Verificação ortográfica SearchBar específica da plataforma")

> [!NOTE]
> A `SearchBar` classe no [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace também possui [`EnableSpellCheck`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*) métodos e [`DisableSpellCheck`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*) que podem ser usados para habilitar e desabilitar o verificador ortográfico no `SearchBar` , respectivamente.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
