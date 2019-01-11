---
title: " Verificação ortográfica SearchBar no Windows"
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como utilizar o Windows específicos da plataforma que permite que um SearchBar interagir com o mecanismo de verificação ortográfica.
ms.prod: xamarin
ms.assetid: 7974C91F-7502-4DB3-B0E9-C45E943DDA26
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 6cc85cf9d008d87362bdc890d47248ecce3402f5
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54208671"
---
# <a name="searchbar-spell-check-on-windows"></a>Verificação ortográfica SearchBar no Windows

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Essa plataforma específicas de plataforma Universal do Windows permite que um [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) para interagir com o mecanismo de verificação ortográfica. Ele é consumido em XAML, definindo o [ `SearchBar.IsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty) anexado à propriedade um `boolean` valor:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ... windows:SearchBar.IsSpellCheckEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

searchBar.On<Windows>().SetIsSpellCheckEnabled(true);
```

O `SearchBar.On<Windows>` método Especifica que este específicos da plataforma só serão executado na plataforma Universal do Windows. O [ `SearchBar.SetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.SetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) e no namespace, desativa o verificador ortográfico. Além disso, o `SearchBar.SetIsSpellCheckEnabled` método pode ser usado para ativar/desativar a verificação ortográfica, chamando o [ `SearchBar.GetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.GetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar})) método para retornar se o verificador ortográfico está habilitado:

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

O resultado é inserido no texto a [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) pode ser ortografia verificada, com erros de ortografia incorretos sendo indicados para o usuário:

![Específicos de plataforma de verificação de ortografia de SearchBar](searchbar-spell-check-images/searchbar-spellcheck.png "SearchBar ortográfica seleção específica da plataforma")

> [!NOTE]
> O `SearchBar` classe de [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) também tem um namespace [ `EnableSpellCheck` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*) e [ `DisableSpellCheck` ](xre:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*) métodos que podem ser usados para habilitar e desabilitar o verificador ortográfico no `SearchBar`, respectivamente.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
