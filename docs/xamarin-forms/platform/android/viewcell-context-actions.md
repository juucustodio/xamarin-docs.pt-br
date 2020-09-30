---
title: Ações de contexto ViewCell no Android
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Android específica que habilita o modo herdado de ações de contexto ViewCell.
ms.prod: xamarin
ms.assetid: 8BD71B10-5037-443F-9975-B941CE393E5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6cbdea3656daa4df894a79edc70453c78e3844bc
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556744"
---
# <a name="viewcell-context-actions-on-android"></a>Ações de contexto ViewCell no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Por padrão Xamarin.Forms , de 4,3, quando a [`ViewCell`](xref:Xamarin.Forms.ViewCell) em um aplicativo Android define ações de contexto para cada item em um [`ListView`](xref:Xamarin.Forms.ListView) , o menu ações de contexto é atualizado quando o item selecionado é `ListView` alterado. No entanto, nas versões anteriores do Xamarin.Forms menu de ações de contexto não foi atualizada, e esse comportamento é conhecido como o `ViewCell` modo herdado. Esse modo herdado pode resultar em comportamento incorreto se um `ListView` usar um [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) para definir seu `ItemTemplate` de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objetos que definem ações de contexto diferentes.

Essa plataforma do Android específica habilita o [`ViewCell`](xref:Xamarin.Forms.ViewCell) modo herdado do menu ações de contexto, para compatibilidade com versões anteriores, para que o menu ações de contexto não seja atualizado quando o item selecionado for [`ListView`](xref:Xamarin.Forms.ListView) alterado. Ele é consumido em XAML definindo a `ViewCell.IsContextActionsLegacyModeEnabled` propriedade vinculável como `true` :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ItemsSource="{Binding Items}">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell android:ViewCell.IsContextActionsLegacyModeEnabled="true">
                        <ViewCell.ContextActions>
                            <MenuItem Text="{Binding Item1Text}" />
                            <MenuItem Text="{Binding Item2Text}" />
                        </ViewCell.ContextActions>
                        <Label Text="{Binding Text}" />
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

viewCell.On<Android>().SetIsContextActionsLegacyModeEnabled(true);
```

O `ViewCell.On<Android>` método especifica que essa plataforma específica será executada somente no Android. O `ViewCell.SetIsContextActionsLegacyModeEnabled` método, no [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para habilitar o [`ViewCell`](xref:Xamarin.Forms.ViewCell) menu de ações de contexto modo herdado, para que o menu ações de contexto não seja atualizado quando o item selecionado for [`ListView`](xref:Xamarin.Forms.ListView) alterado. Além disso, o `ViewCell.GetIsContextActionsLegacyModeEnabled` método pode ser usado para retornar se o modo herdado de ações de contexto está habilitado.

As capturas de tela a seguir mostram o [`ViewCell`](xref:Xamarin.Forms.ViewCell) modo herdado de ações de contexto habilitado:

![Captura de tela do modo herdado do ViewCell habilitado, no Android](viewcell-context-actions-images/legacy-mode-enabled.png "Modo herdado ViewCell habilitado")

Nesse modo, os itens de menu de ação de contexto exibidos são idênticos para célula 1 e célula 2, apesar de itens de menu de contexto diferentes serem definidos para a célula 2.

As capturas de tela a seguir mostram [`ViewCell`](xref:Xamarin.Forms.ViewCell) o modo herdado de ações de contexto desabilitado, que é o Xamarin.Forms comportamento padrão:

![Captura de tela do modo herdado ViewCell desabilitado, no Android](viewcell-context-actions-images/legacy-mode-disabled.png "Modo herdado ViewCell desabilitado")

Nesse modo, os itens de menu de ação de contexto corretos são exibidos para a célula 1 e a célula 2.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)