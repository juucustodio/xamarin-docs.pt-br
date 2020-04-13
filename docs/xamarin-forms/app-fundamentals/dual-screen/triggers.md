---
title: Xamarin.Forms dual-screen triggers
description: Este artigo explica como usar os gatilhos xamarin.forms de tela dupla para responder às alterações da interface do usuário com o XAML.
ms.prod: xamarin
ms.assetid: 2181715D-3995-4E71-9A21-6B892F0B3B59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2020
ms.openlocfilehash: 0cce23973c90c89ce90e40651a2646d5f1bdd2c0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78165530"
---
# <a name="xamarinforms-dual-screen-triggers"></a>Xamarin.Forms dual-screen triggers

![](~/media/shared/preview.png "This API is currently pre-release")

O [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) namespace inclui dois gatilhos de estado:

- [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger)ativa uma [`VisualState`](xref:Xamarin.Forms.VisualState) alteração quando o modo de exibição do layout anexado muda.
- `WindowSpanModeStateTrigger`ativa uma [`VisualState`](xref:Xamarin.Forms.VisualState) alteração quando o modo de exibição da janela muda.

Para obter mais informações sobre os gatilhos do estado, consulte [os gatilhos do Estado](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers).

## <a name="span-mode-state-trigger"></a>Gatilho de estado do modo span

A [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) aciona uma [`VisualState`](xref:Xamarin.Forms.VisualState) alteração quando o modo de extensão do layout anexado muda. Este gatilho tem uma única propriedade vinculável:

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), do [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode)tipo, que indica o [`VisualState`](xref:Xamarin.Forms.VisualState) modo de extensão ao qual deve ser aplicado.

> [!NOTE]
> O [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) deriva da [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) classe e, portanto, pode [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) anexar um manipulador de eventos ao evento.

O exemplo XAML [`Grid`](xref:Xamarin.Forms.Grid) a seguir `SpanModeStateTrigger` mostra um que inclui objetos:

```xaml
<Grid>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState x:Name="GridSingle">
                <VisualState.StateTriggers>
                    <dualScreen:SpanModeStateTrigger SpanMode="SinglePane"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Green" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="GridWide">
                <VisualState.StateTriggers>
                    <dualScreen:SpanModeStateTrigger SpanMode="Wide" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Red" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="GridTall">
                <VisualState.StateTriggers>
                    <dualScreen:SpanModeStateTrigger SpanMode="Tall" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Purple" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
    ...
</Grid>
```

Neste exemplo, os estados visuais [`Grid`](xref:Xamarin.Forms.Grid) são definidos em um objeto. A cor de `Grid` fundo do é verde quando apenas um painel é mostrado, é vermelho quando os painéis são mostrados lado a lado, e é roxo quando os painéis são mostrados na parte superior.

## <a name="window-span-mode-state-trigger"></a>Gatilho de estado do modo de extensão da janela

A `WindowSpanModeStateTrigger` aciona uma [`VisualState`](xref:Xamarin.Forms.VisualState) alteração quando o modo de extensão da janela muda. Este gatilho tem uma única propriedade vinculável:

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), do [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode)tipo, que indica o [`VisualState`](xref:Xamarin.Forms.VisualState) modo de extensão ao qual deve ser aplicado.

> [!NOTE]
> O `WindowSpanModeStateTrigger` deriva da [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) classe e, portanto, pode [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) anexar um manipulador de eventos ao evento.

O exemplo XAML [`Grid`](xref:Xamarin.Forms.Grid) a seguir `WindowSpanModeStateTrigger` mostra um que inclui objetos:

```xaml
<Grid>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState x:Name="NotSpanned">
                <VisualState.StateTriggers>
                    <dualScreen:WindowSpanModeStateTrigger SpanMode="SinglePane"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Red" />
                </VisualState.Setters>
            </VisualState>
            <VisualState x:Name="Spanned">
                <VisualState.StateTriggers>
                    <dualScreen:WindowSpanModeStateTrigger SpanMode="Wide" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Green" />
                </VisualState.Setters>
            </VisualState>
                <VisualState x:Name="Tall">
                    <VisualState.StateTriggers>
                        <dualScreen:WindowSpanModeStateTrigger SpanMode="Tall" />
                    </VisualState.StateTriggers>
                    <VisualState.Setters>
                        <Setter Property="BackgroundColor" Value="Yellow" />
                    </VisualState.Setters>
                </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
    ...
</Grid>    
```

Neste exemplo, os estados visuais [`Grid`](xref:Xamarin.Forms.Grid) são definidos em um objeto. A cor de `Grid` fundo do é vermelho quando apenas um painel é mostrado, é verde quando os painéis são mostrados lado a lado, e é amarelo quando os painéis são mostrados na parte superior.

## <a name="related-links"></a>Links relacionados

- [Gatilhos do Xamarin.Forms](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)
