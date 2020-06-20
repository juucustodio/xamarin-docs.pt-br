---
title: Xamarin.Formsgatilhos de tela dupla
description: Este artigo explica como usar Xamarin.Forms gatilhos de tela dupla para responder a alterações de interface do usuário com XAML.
ms.prod: xamarin
ms.assetid: 2181715D-3995-4E71-9A21-6B892F0B3B59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: df10327b2ac12d2b119f1ab558d7f27e8c319507
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84131190"
---
# <a name="xamarinforms-dual-screen-triggers"></a>Xamarin.Formsgatilhos de tela dupla

![](~/media/shared/preview.png "This API is currently pre-release")

O [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) namespace inclui dois gatilhos de Estado:

- [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger)dispara uma [`VisualState`](xref:Xamarin.Forms.VisualState) alteração quando o modo de exibição do layout anexado é alterado.
- `WindowSpanModeStateTrigger`dispara uma [`VisualState`](xref:Xamarin.Forms.VisualState) alteração quando o modo de exibição da janela é alterado.

Para obter mais informações sobre gatilhos de estado, consulte [gatilhos de estado](~/xamarin-forms/app-fundamentals/triggers.md#state-triggers).

## <a name="span-mode-state-trigger"></a>Gatilho de estado do modo Span

Um [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) dispara uma [`VisualState`](xref:Xamarin.Forms.VisualState) alteração quando o modo de span do layout anexado é alterado. Esse gatilho tem uma única propriedade vinculável:

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), do tipo [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode) , que indica o modo Span ao qual o [`VisualState`](xref:Xamarin.Forms.VisualState) deve ser aplicado.

> [!NOTE]
> O [`SpanModeStateTrigger`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger) deriva da [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) classe e, portanto, pode anexar um manipulador de eventos ao [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) evento.

O exemplo de XAML a seguir mostra um [`Grid`](xref:Xamarin.Forms.Grid) que inclui `SpanModeStateTrigger` objetos:

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

Neste exemplo, os Estados visuais são definidos em um [`Grid`](xref:Xamarin.Forms.Grid) objeto. A cor da tela de fundo do `Grid` é verde quando apenas um painel é mostrado, é vermelho quando os painéis são mostrados lado a lado e é roxo quando os painéis são exibidos de cima para baixo.

## <a name="window-span-mode-state-trigger"></a>Gatilho de estado do modo de extensão da janela

Um `WindowSpanModeStateTrigger` dispara uma [`VisualState`](xref:Xamarin.Forms.VisualState) alteração quando o modo de span da janela é alterado. Esse gatilho tem uma única propriedade vinculável:

- [`SpanMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode), do tipo [`TwoPaneViewMode`](xref:Xamarin.Forms.DualScreen.SpanModeStateTrigger.SpanMode) , que indica o modo Span ao qual o [`VisualState`](xref:Xamarin.Forms.VisualState) deve ser aplicado.

> [!NOTE]
> O `WindowSpanModeStateTrigger` deriva da [`StateTriggerBase`](xref:Xamarin.Forms.StateTriggerBase) classe e, portanto, pode anexar um manipulador de eventos ao [`IsActiveChanged`](xref:Xamarin.Forms.StateTriggerBase.IsActiveChanged) evento.

O exemplo de XAML a seguir mostra um [`Grid`](xref:Xamarin.Forms.Grid) que inclui `WindowSpanModeStateTrigger` objetos:

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

Neste exemplo, os Estados visuais são definidos em um [`Grid`](xref:Xamarin.Forms.Grid) objeto. A cor da tela de fundo do `Grid` é vermelha quando apenas um painel é mostrado, fica verde quando os painéis são mostrados lado a lado e é amarelo quando os painéis são exibidos de cima para baixo.

## <a name="related-links"></a>Links relacionados

- [Xamarin.FormsGatilhos](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.FormsGerenciador de estado visual](~/xamarin-forms/user-interface/visual-state-manager.md)
