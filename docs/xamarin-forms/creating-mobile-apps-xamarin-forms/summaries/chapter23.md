---
title: Resumo do Capítulo 23. Gatilhos e comportamentos
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 23. Gatilhos e comportamentos'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 8a1274a8447f49ce39f9c92703bbaec9e875b9e9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760587"
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>Resumo do Capítulo 23. Gatilhos e comportamentos

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)

Gatilhos e comportamentos são semelhantes, pois ambos são destinados a serem usados em arquivos XAML para simplificar interações de elementos além do uso de vinculações de dados, e para estender a funcionalidade de elementos XAML. Tanto os gatilhos quanto os comportamentos são quase sempre usados com objetos visuais de interface de usuário.

Para suportar gatilhos e `VisualElement` comportamentos, ambos e `Style` apoiar duas propriedades de coleta:

- [`VisualElement.Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)e [`Style.Triggers`](xref:Xamarin.Forms.Style.Triggers) de tipo`IList<TriggerBase>`
- [`VisualElement.Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors)e [`Style.Behaviors`](xref:Xamarin.Forms.Style.Behaviors) de tipo`IList<Behavior>`

## <a name="triggers"></a>Gatilhos

Um gatilho é uma condição (uma mudança de propriedade ou a demissão de um evento) que resulta em uma resposta (outra alteração de propriedade ou execução de algum código). A `Triggers` propriedade `VisualElement` `Style` de e `IList<TriggersBase>`é do tipo . [`TriggerBase`](xref:Xamarin.Forms.TriggerBase)é uma classe abstrata da qual quatro classes seladas derivam:

- [`Trigger`](xref:Xamarin.Forms.Trigger)para respostas baseadas em mudanças de propriedade
- [`EventTrigger`](xref:Xamarin.Forms.EventTrigger)para respostas baseadas em demissões de eventos
- [`DataTrigger`](xref:Xamarin.Forms.DataTrigger)para respostas baseadas em vinculações de dados
- [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger)para respostas baseadas em múltiplos gatilhos

O gatilho é sempre definido no elemento cuja propriedade está sendo alterada pelo gatilho.

### <a name="the-simplest-trigger"></a>O gatilho mais simples

A [`Trigger`](xref:Xamarin.Forms.Trigger) classe verifica a alteração de um valor de propriedade e responde definindo outra propriedade do mesmo elemento.

`Trigger`define três propriedades:

- [`Property`](xref:Xamarin.Forms.Trigger.Property)de tipo`BindableProperty`
- [`Value`](xref:Xamarin.Forms.Trigger.Value)de tipo`Object`
- [`Setters`](xref:Xamarin.Forms.Trigger.Setters)de `IList<SetterBase>`tipo , a propriedade de conteúdo de`Trigger`

Além disso, `Trigger` exige que a `TriggerBase` seguinte propriedade herdada seja definida:

- [`TargetType`](xref:Xamarin.Forms.TriggerBase.TargetType)para indicar o tipo do `Trigger` elemento em que o é anexado

O `Property` `Value` e compõem a `Setters` condição, e a coleta é a resposta. Quando o `Property` indicado tem o `Value`valor indicado `Setter` por `Setters` , então os objetos na coleção são aplicados. Quando `Property` o tem um valor diferente, os setters são removidos. `Setter`define duas propriedades que são as mesmas `Trigger`das duas primeiras propriedades de:

- [`Property`](xref:Xamarin.Forms.Setter.Property)de tipo`BindableProperty`
- [`Value`](xref:Xamarin.Forms.Setter.Value)de tipo`Object`

A amostra [**EntryPop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop) `Trigger` demonstra como `Entry` uma aplicação a `Entry` uma `Scale` pode aumentar `IsFocused` o `Entry` tamanho `true`da propriedade quando a propriedade do é .

Embora não seja comum, o `Trigger` pode ser definido em código, como a amostra [**EntryPopCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode) demonstra.

A amostra [**StyledTriggers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers) `Trigger` demonstra como o `Style` pode ser `Entry` definido em um para aplicar a vários elementos.

### <a name="trigger-actions-and-animations"></a>Desencadear ações e animações

Também é possível executar um pequeno código com base em um gatilho. Este código pode ser uma animação que tem como alvo uma propriedade. Uma maneira comum [`EventTrigger`](xref:Xamarin.Forms.EventTrigger)é usar um , que define duas propriedades:

- [`Event`](xref:Xamarin.Forms.EventTrigger.Event)do `string`tipo , o nome de um evento
- [`Actions`](xref:Xamarin.Forms.EventTrigger.Actions)de `IList<TriggerAction>`tipo , uma lista de ações a serem executadas em resposta.

Para usar isso, você precisa escrever uma [`TriggerAction<T>`](xref:Xamarin.Forms.TriggerAction`1)aula `TriggerAction<VisualElement>`que deriva de , geralmente . Você pode definir propriedades nesta classe. Estas são propriedades CLR simples `TriggerAction` em vez de `BindableObject`propriedades vinculáveis porque não deriva de . Você deve substituir [`Invoke`](xref:Xamarin.Forms.TriggerAction`1.Invoke*) o método que é chamado quando a ação é invocada. O argumento é o elemento alvo.

A [`ScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs) classe na biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) é um exemplo. Ele chama `ScaleTo` a propriedade `Scale` para animar a propriedade de um elemento. Como uma de suas `Easing`propriedades [`EasingConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs) é de tipo, `Easing` a classe permite que você use os campos estáticos padrão em XAML.

A amostra [**EntrySwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell) demonstra `ScaleAction` como `EventTrigger` invocar os `Focused` `Unfocused` objetos que monitoram os eventos.

A amostra [**CustomEasingSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell) mostra como definir `ScaleAction` uma função de facilitação personalizada para um arquivo de código atrás.

Você também pode invocar `Trigger` ações usando `EventTrigger`a (como distinguido de ). Isso requer que você `TriggerBase` esteja ciente de que define duas coleções:

- [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions)de tipo`IList<TriggerAction>`
- [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions)de tipo`IList<TriggerAction>`

A amostra [**EnterExitSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell) demonstra como usar essas coleções.

### <a name="more-event-triggers"></a>Mais gatilhos de eventos

A [`ScaleUpAndDownAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs) classe na biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) é chamada `ScaleTo` duas vezes para aumentar e diminuir. A amostra [**ButtonGrowth**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth) usa isso `EventTrigger` em um estilo `Button` para fornecer feedback visual quando um é pressionado. Esta animação dupla também é possível usando duas ações na coleção do tipo[`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

A [`ShiverAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs) classe na biblioteca **Xamarin.FormsBook.Toolkit** define uma ação de arrepio personalizável. A amostra [**ShiverButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo) demonstra isso.

A [`NumericValidationAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs) classe na biblioteca **Xamarin.FormsBook.Toolkit** é `Entry` restrita `TextColor` a elementos `Text` e define `double`a propriedade como vermelha se a propriedade não for uma . A amostra [**TriggerEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation) demonstra isso.

### <a name="data-triggers"></a>Gatilhos de dados

O [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) é semelhante `Trigger` ao exceto que, em vez de monitorar uma propriedade para alterações de valor, ele monitora uma vinculação de dados. Isso permite que uma propriedade em um elemento afete uma propriedade em outro elemento.

`DataTrigger`define três propriedades:

- [`Binding`](xref:Xamarin.Forms.DataTrigger.Binding)de tipo`BindingBase`
- [`Value`](xref:Xamarin.Forms.DataTrigger.Value)de tipo`Object`
- [`Setters`](xref:Xamarin.Forms.DataTrigger.Setters)de tipo`IList<SetterBase>`

A amostra [**GenderColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors) requer a biblioteca [**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) e define as cores dos `Sex` nomes dos alunos como azul ou rosa com base na propriedade:

[![Captura de tela tripla de Cores de Gênero](images/ch23fg04-small.png "Cores de gênero")](images/ch23fg04-large.png#lightbox "Cores de gênero")

A amostra [**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler) `IsEnabled` define `Entry` a `False` propriedade `Length` de `Text` um para `Entry` se a propriedade da propriedade do equal é 0. Observe que `Text` a propriedade é inicializada para uma seqüência de string vazia; por padrão `null`é , `DataTrigger` e o não funcionaria corretamente.

### <a name="combining-conditions-in-the-multitrigger"></a>Combinando condições no MultiTrigger

É [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger) uma coleção de condições. Quando todos `true`são, então os setters são aplicados. A classe define duas propriedades:

- [`Conditions`](xref:Xamarin.Forms.MultiTrigger.Conditions)de tipo`IList<Condition>`
- [`Setters`](xref:Xamarin.Forms.MultiTrigger.Setters)de tipo`IList<Setter>`

[`Condition`](xref:Xamarin.Forms.Condition)é uma classe abstrata e tem duas classes descendentes:

- [`PropertyCondition`](xref:Xamarin.Forms.Condition), que [`Property`](xref:Xamarin.Forms.PropertyCondition.Property) [`Value`](xref:Xamarin.Forms.PropertyCondition.Value) tem e propriedades como`Trigger`
- [`BindingCondition`](xref:Xamarin.Forms.BindingCondition), que [`Binding`](xref:Xamarin.Forms.BindingCondition.Binding) [`Value`](xref:Xamarin.Forms.BindingCondition.Value) tem e propriedades como`DataTrigger`

Na amostra [**AndConditions,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions) a `BoxView` só `Switch` é colorida quando quatro elementos estão todos ligados.

A amostra [**OrConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions) demonstra como `BoxView` você pode fazer `Switch` uma cor a quando qualquer *um* dos quatro elementos são ligados. Isso requer uma aplicação da Lei de De Morgan e reverter toda a lógica.

Combinar lógica e OR não é tão fácil `Switch` e geralmente requer elementos invisíveis para resultados intermediários. A amostra [**XorConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions) `Button` demonstra como um pode `Entry` ser ativado se qualquer um dos dois elementos tiver algum texto digitado, mas não se ambos tiverem algum texto digitado.

## <a name="behaviors"></a>Comportamentos

Qualquer coisa que você possa fazer com um gatilho, você também pode fazer [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) com um comportamento, mas comportamentos sempre exigem uma classe que deriva e substitui os dois métodos a seguir:

- [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo*)
- [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom*)

O argumento é o elemento ao que o comportamento está ligado. Geralmente, o `OnAttachedTo` método anexa alguns manipuladores de eventos e `OnDetachingFrom` os destaca. Como tal classe geralmente salva algum estado, geralmente não `Style`pode ser compartilhada em um .

A amostra [**BehaviorEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation) é semelhante ao **TriggerEntryValidation,** exceto que ele usa um comportamento &mdash; da [`NumericValidationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs) classe na biblioteca [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

### <a name="behaviors-with-properties"></a>Comportamentos com propriedades

`Behavior<T>`deriva de `Behavior`, que `BindableObject`deriva de , de modo que propriedades vinculáveis podem ser definidas em um comportamento. Essas propriedades podem estar ativas em vinculações de dados.

Isso é demonstrado no programa [**EmailValidationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo) [`ValidEmailBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs) que faz uso da classe na biblioteca [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) `ValidEmailBehavior`tem uma propriedade somente leitura e serve como fonte em vinculações de dados.

A amostra [**EmailValidationConv**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv) usa esse mesmo comportamento para exibir outro tipo de indicador para sinalizar que um endereço de e-mail é válido.

A amostra [**EmailValidationTrigger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger) é uma variação da amostra anterior. ButtonGlide usa `DataTrigger` uma combinação com esse comportamento.

### <a name="toggles-and-check-boxes"></a>Alternaes e caixas de verificação

É possível encapsular o comportamento de um botão de alternância em uma classe como [`ToggleBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) na biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) e, em seguida, definir todos os visuais para o alternador inteiramente em XAML.

A amostra [**AlternagemLabel**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel) `ToggleBehavior` usa `DataTrigger` o com `Label` um para usar um com duas seqüências de texto para o alternador.

A amostra [**FormattedTextToggle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle) estende esse conceito `FormattedString` alternando entre dois objetos.

A [`ToggleBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs) classe na biblioteca **Xamarin.FormsBook.Toolkit** deriva, `ContentView` `IsToggled` define uma propriedade `ToggleBehavior` e incorpora uma para a lógica de alternação. Isso facilita a definição do botão de alterneno em XAML, como demonstrado pela amostra [**TraditionalCheckBox.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox)

O [**SwitchCloneDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo) inclui [`SwitchClone`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs) uma classe `ToggleBase` que deriva [`TranslateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs) e usa uma classe para construir um botão `Switch`de alternância que se assemelha ao Xamarin.Forms .

A [`RotateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs) no **Xamarin.FormsBook.Toolkit** fornece uma animação usada para fazer uma alavanca animada na amostra [**LeverToggle.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle)

### <a name="responding-to-taps"></a>Respondendo a torneiras

Uma desvantagem é `EventTrigger` que você não `TapGestureRecognizer` pode anexá-lo a um para responder a toques. Contornar esse problema é [`TapBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs) o propósito do [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

A amostra [**BoxViewTapShiver**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver) usa `TapBehavior` `ShiverAction` para usar `BoxView` os elementos mais cedo aproveitados.

A amostra [**De ShiverViews**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews) mostra como reduzir a `ShiverView` marcação encapsulando uma classe.

### <a name="radio-buttons"></a>Botões de opção

A biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) [`RadioBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs) também tem uma classe para fazer `string` botões de rádio que são agrupados por um nome de grupo.

O programa [**RadioLabels**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels) usa strings de texto para o botão de rádio. A amostra [**RadioStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle) usa um `Style` para a diferença de aparência entre botões verificados e não verificados. A amostra [**RadioImages**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages) usa imagens em caixa para seus botões de rádio:

[![Captura de tela tripla de Imagens de Rádio](images/ch23fg17-small.png "Imagens do botão de rádio")](images/ch23fg17-large.png#lightbox "Imagens do botão de rádio")

A amostra [**TraditionalRadios**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios) desenha botões de rádio tradicionais que aparecem com um pontilhado dentro de um círculo.

### <a name="fades-and-orientation"></a>Desbota e orientação

A amostra final, [**MultiColorSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders) permite alternar entre três diferentes visualizações de seleção de cores usando botões de rádio. As três visualizações desaparecem usando [`FadeEnableAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs) um na biblioteca [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

O programa também responde a mudanças na [`GridOrientationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs) orientação entre retrato e paisagem usando um na biblioteca **Xamarin.FormsBook.Toolkit.**

## <a name="related-links"></a>Links relacionados

- [Capítulo 23 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [Capítulo 23 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [Trabalhando com gatilhos](~/xamarin-forms/app-fundamentals/triggers.md)
- [Comportamentos do Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
