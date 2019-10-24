---
title: Ações de contexto ViewCell no Android
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Android específica que habilita o modo herdado de ações de contexto ViewCell.
ms.prod: xamarin
ms.assetid: 8BD71B10-5037-443F-9975-B941CE393E5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2019
ms.openlocfilehash: b040c7c5b7f132b0832469efba91dd89d6b2ddbd
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697426"
---
# <a name="viewcell-context-actions-on-android"></a>Ações de contexto ViewCell no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Por padrão, no Xamarin. Forms 4,3, quando um [`ViewCell`](xref:Xamarin.Forms.ViewCell) em um aplicativo Android define ações de contexto para cada item em um [`ListView`](xref:Xamarin.Forms.ListView), o menu ações de contexto é atualizado quando o item selecionado no `ListView` é alterado. No entanto, em versões anteriores do Xamarin. Forms, o menu de ações de contexto não foi atualizado e esse comportamento é conhecido como o `ViewCell` modo herdado. Esse modo herdado pode resultar em comportamento incorreto se um `ListView` usa uma [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) para definir sua `ItemTemplate` de objetos [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que definem ações de contexto diferentes.

Essa plataforma Android específica habilita o modo herdado do menu de ações de contexto de [`ViewCell`](xref:Xamarin.Forms.ViewCell) , para compatibilidade com versões anteriores, para que o menu ações de contexto não seja atualizado quando o item selecionado em um [`ListView`](xref:Xamarin.Forms.ListView) for alterado. Ele é consumido em XAML definindo a propriedade vinculável `ViewCell.IsContextActionsLegacyModeEnabled` como `true`:

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

Como alternativa, ele pode ser consumido C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

viewCell.On<Android>().SetIsContextActionsLegacyModeEnabled(true);
```

O método `ViewCell.On<Android>` especifica que essa plataforma específica será executada somente no Android. O método `ViewCell.SetIsContextActionsLegacyModeEnabled`, no namespace [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) , é usado para habilitar o modo herdado do menu de ações de contexto de [`ViewCell`](xref:Xamarin.Forms.ViewCell) , para que o menu ações de contexto não seja atualizado quando o item selecionado em um [`ListView`](xref:Xamarin.Forms.ListView) for alterado. Além disso, o método `ViewCell.GetIsContextActionsLegacyModeEnabled` pode ser usado para retornar se o modo herdado de ações de contexto está habilitado.

As capturas de tela a seguir mostram [`ViewCell`](xref:Xamarin.Forms.ViewCell) modo herdado de ações de contexto habilitadas:

![Captura de tela do modo herdado do ViewCell habilitado, no Android](viewcell-context-actions-images/legacy-mode-enabled.png "Modo herdado ViewCell habilitado")

Nesse modo, os itens de menu de ação de contexto exibidos são idênticos para célula 1 e célula 2, apesar de itens de menu de contexto diferentes serem definidos para a célula 2.

As capturas de tela a seguir mostram [`ViewCell`](xref:Xamarin.Forms.ViewCell) modo herdado de ações de contexto desabilitado, que é o comportamento padrão do Xamarin. Forms:

![Captura de tela do modo herdado ViewCell desabilitado, no Android](viewcell-context-actions-images/legacy-mode-disabled.png "Modo herdado ViewCell desabilitado")

Nesse modo, os itens de menu de ação de contexto corretos são exibidos para a célula 1 e a célula 2.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
