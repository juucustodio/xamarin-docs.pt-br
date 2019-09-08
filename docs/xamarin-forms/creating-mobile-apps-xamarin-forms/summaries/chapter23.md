---
title: Resumo do capítulo 23. Gatilhos e comportamentos
description: 'Criando aplicativos móveis com Xamarin. Forms: Resumo do capítulo 23. Gatilhos e comportamentos'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 8a1274a8447f49ce39f9c92703bbaec9e875b9e9
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760587"
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>Resumo do capítulo 23. Gatilhos e comportamentos

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)

Gatilhos e comportamentos são semelhantes, em que eles ambos destinam-se a ser usado nos arquivos XAML para simplificar as interações do elemento além do uso de associações de dados e estender a funcionalidade de elementos XAML. Gatilhos e comportamentos quase sempre são usados com objetos de interface do usuário visual.

Para dar suporte a gatilhos e comportamentos, ambos `VisualElement` e `Style` dão suporte a duas propriedades de coleção:

- [`VisualElement.Triggers`](xref:Xamarin.Forms.VisualElement.Triggers) e [ `Style.Triggers` ](xref:Xamarin.Forms.Style.Triggers) do tipo `IList<TriggerBase>`
- [`VisualElement.Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) e [ `Style.Behaviors` ](xref:Xamarin.Forms.Style.Behaviors) do tipo `IList<Behavior>`

## <a name="triggers"></a>Gatilhos

Um gatilho é uma condição (uma alteração de propriedade ou o acionamento de um evento) que resulta em uma resposta (outra alteração de propriedade ou executar códigos). O `Triggers` propriedade de `VisualElement` e `Style` é do tipo `IList<TriggersBase>`. [`TriggerBase`](xref:Xamarin.Forms.TriggerBase) é uma classe abstrata da qual derivam de quatro classes lacradas:

- [`Trigger`](xref:Xamarin.Forms.Trigger) para respostas com base nas alterações de propriedade
- [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) para respostas com base em acionamentos de evento
- [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) para respostas com base em associações de dados
- [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger) para respostas com base em vários gatilhos

O gatilho é sempre definido no elemento cuja propriedade está sendo alterada pelo gatilho.

### <a name="the-simplest-trigger"></a>O gatilho mais simples

O [ `Trigger` ](xref:Xamarin.Forms.Trigger) verifica se há uma alteração de um valor de propriedade de classe e responde definindo outra propriedade do mesmo elemento.

`Trigger` define três propriedades:

- [`Property`](xref:Xamarin.Forms.Trigger.Property) do tipo `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Trigger.Value) do tipo `Object`
- [`Setters`](xref:Xamarin.Forms.Trigger.Setters) do tipo `IList<SetterBase>`, a propriedade de conteúdo de `Trigger`

Além disso, `Trigger` requer que a seguinte propriedade herdada da `TriggerBase` ser definido:

- [`TargetType`](xref:Xamarin.Forms.TriggerBase.TargetType) para indicar o tipo do elemento no qual o `Trigger` está anexado

O `Property` e `Value` compõem a condição e o `Setters` coleção é a resposta. Quando indicado `Property` tem o valor indicado por `Value`, em seguida, a `Setter` objetos no `Setters` coleção são aplicadas. Quando o `Property` tem um valor diferente, os setters são removidos. `Setter` define duas propriedades que são os mesmos que as duas primeiras propriedades de `Trigger`:

- [`Property`](xref:Xamarin.Forms.Setter.Property) do tipo `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Setter.Value) do tipo `Object`

O [ **EntryPop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop) exemplo demonstra como um `Trigger` aplicado a um `Entry` pode aumentar o tamanho do `Entry` por meio do `Scale` propriedade quando o `IsFocused`propriedade do `Entry` é `true`.

Embora não seja comum, o `Trigger` pode ser definida no código, como o [ **EntryPopCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode) demonstra.

O [ **StyledTriggers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers) exemplo demonstra como o `Trigger` pode ser definido em um `Style` para aplicar a vários `Entry` elementos.

### <a name="trigger-actions-and-animations"></a>Ações de gatilho e animações

Também é possível executar um pouco de código com base em um gatilho. Esse código pode ser uma animação que tem como destino uma propriedade. Uma maneira comum é usar um [ `EventTrigger` ](xref:Xamarin.Forms.EventTrigger), que define duas propriedades:

- [`Event`](xref:Xamarin.Forms.EventTrigger.Event) do tipo `string`, o nome de um evento
- [`Actions`](xref:Xamarin.Forms.EventTrigger.Actions) do tipo `IList<TriggerAction>`, uma lista de ações a serem executadas em resposta.

Para usar isso, você precisa escrever uma classe que deriva de [ `TriggerAction<T>` ](xref:Xamarin.Forms.TriggerAction`1), em geral, `TriggerAction<VisualElement>`. Você pode definir as propriedades nessa classe. Elas são propriedades CLR simples em vez de propriedades vinculáveis porque `TriggerAction` não deriva de `BindableObject`. Você deve substituir a [ `Invoke` ](xref:Xamarin.Forms.TriggerAction`1.Invoke*) método é chamado quando a ação é invocada. O argumento é o elemento de destino.

O [ `ScaleAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs) classe os [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca é um exemplo. Ele chama o `ScaleTo` propriedade para animar o `Scale` propriedade de um elemento. Porque uma de suas propriedades é do tipo `Easing`, o [ `EasingConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs) classe permite que você use o padrão `Easing` campos estáticos em XAML.

O [ **EntrySwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell) exemplo demonstra como invocar o `ScaleAction` do `EventTrigger` objetos que monitora o `Focused` e `Unfocused` eventos.

O [ **CustomEasingSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell) exemplo mostra como definir uma função de easing personalizada `ScaleAction` em um arquivo code-behind.

Você também pode invocar ações usando um `Trigger` (distinguished de `EventTrigger`). Isso exige que você esteja ciente de que `TriggerBase` define duas coleções:

- [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) do tipo `IList<TriggerAction>`
- [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) do tipo `IList<TriggerAction>`

O [ **EnterExitSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell) exemplo demonstra como usar essas coleções.

### <a name="more-event-triggers"></a>Mais gatilhos de evento

O [ `ScaleUpAndDownAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs) classe os [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) chamadas para a biblioteca `ScaleTo` duas vezes para escalar verticalmente. O [ **ButtonGrowth** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth) exemplo usa isso em um controle com estilo `EventTrigger` para fornecer comentários visuais quando um `Button` é pressionado. Essa animação dobrar também é possível usar duas ações na coleção do tipo [`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

O [ `ShiverAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs) classe os **Xamarin.FormsBook.Toolkit** biblioteca define uma ação de shiver personalizável. O [ **ShiverButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo) exemplo demonstra isso.

O [ `NumericValidationAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs) classe o **Xamarin.FormsBook.Toolkit** biblioteca é restrita a `Entry` elementos e conjuntos do `TextColor` propriedade para vermelho se o `Text` propriedade não é um `double`. O [ **TriggerEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation) exemplo demonstra isso.

### <a name="data-triggers"></a>Gatilhos de dados

O [ `DataTrigger` ](xref:Xamarin.Forms.DataTrigger) é semelhante ao `Trigger` , exceto que, em vez de monitoramento de uma propriedade para alterações de valor, ele monitora uma associação de dados. Isso permite que uma propriedade em um elemento para afetar uma propriedade em outro elemento.

`DataTrigger` define três propriedades:

- [`Binding`](xref:Xamarin.Forms.DataTrigger.Binding) do tipo `BindingBase`
- [`Value`](xref:Xamarin.Forms.DataTrigger.Value) do tipo `Object`
- [`Setters`](xref:Xamarin.Forms.DataTrigger.Setters) do tipo `IList<SetterBase>`

O [ **GenderColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors) exemplo requer o [ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) biblioteca e define as cores dos nomes de alunos para azul ou rosa com base no `Sex` propriedade:

[![Tripla captura de tela de cores de sexo](images/ch23fg04-small.png "cores gênero")](images/ch23fg04-large.png#lightbox "cores de gênero")

O [ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler) conjuntos de exemplo o `IsEnabled` propriedade de um `Entry` para `False` se o `Length` propriedade do `Text` propriedade do `Entry`é igual a 0. Observe que o `Text` propriedade é inicializada para uma cadeia de caracteres vazia; por padrão é `null`e o `DataTrigger` não funcionará corretamente.

### <a name="combining-conditions-in-the-multitrigger"></a>Combinando as condições no MultiTrigger

O [ `MultiTrigger` ](xref:Xamarin.Forms.MultiTrigger) é uma coleção de condições. Quando eles estão todos os `true`, em seguida, os setters são aplicadas. A classe define duas propriedades:

- [`Conditions`](xref:Xamarin.Forms.MultiTrigger.Conditions) do tipo `IList<Condition>`
- [`Setters`](xref:Xamarin.Forms.MultiTrigger.Setters) do tipo `IList<Setter>`

[`Condition`](xref:Xamarin.Forms.Condition) é uma classe abstrata e tem duas classes descendentes:

- [`PropertyCondition`](xref:Xamarin.Forms.Condition), que tem [ `Property` ](xref:Xamarin.Forms.PropertyCondition.Property) e [ `Value` ](xref:Xamarin.Forms.PropertyCondition.Value) propriedades como `Trigger`
- [`BindingCondition`](xref:Xamarin.Forms.BindingCondition), que tem [ `Binding` ](xref:Xamarin.Forms.BindingCondition.Binding) e [ `Value` ](xref:Xamarin.Forms.BindingCondition.Value) propriedades como `DataTrigger`

No [ **ECONDIÇÕES** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions) exemplo, um `BoxView` só é colorido quando quatro `Switch` elementos são todos ativados.

O [ **OrConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions) exemplo demonstra como você pode fazer uma `BoxView` uma cor quando *qualquer* de quatro `Switch` elementos estão ativados. Isso requer um aplicativo da lei e reverter toda a lógica De Morgan.

Combinando e e ou lógica não é tão fácil e geralmente requer invisível `Switch` elementos para os resultados intermediários. O [ **XorConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions) exemplo demonstra como um `Button` pode ser habilitada se qualquer um de dois `Entry` elementos têm algum texto digitado, mas não se ambos tiverem algum texto digitado.

## <a name="behaviors"></a>Comportamentos

Tudo que você pode fazer com um gatilho, você também pode fazer com um comportamento, mas sempre, comportamentos exigem uma classe que deriva de [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) e substitui os dois métodos a seguir:

- [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo*)
- [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom*)

O argumento é o elemento que o comportamento é anexado ao. Em geral, o `OnAttachedTo` método anexa alguns manipuladores de eventos e `OnDetachingFrom` desanexa-los. Como essa classe geralmente salva algum estado, ele geralmente não pode ser compartilhado em um `Style`.

[**BehaviorEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation) exemplo é semelhante ao **TriggerEntryValidation** exceto que ele usa um comportamento &mdash; o [ `NumericValidationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs) classe a [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca.

### <a name="behaviors-with-properties"></a>Comportamentos com propriedades

`Behavior<T>` deriva `Behavior`, que é derivada de `BindableObject`, portanto, as propriedades associáveis podem ser definidas em um comportamento. Essas propriedades podem estar ativas em associações de dados.

Isso é demonstrado na [ **EmailValidationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo) programa que faz uso da [ `ValidEmailBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs) classe o [  **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca. `ValidEmailBehavior` tem uma propriedade associável somente leitura e serve como uma fonte em associações de dados.

O [ **EmailValidationConv** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv) exemplo usa esse mesmo comportamento para exibir outro tipo de indicador para sinalizar que um endereço de email é válido.

O [ **EmailValidationTrigger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger) exemplo é uma variação no exemplo anterior. ButtonGlide usa um `DataTrigger` em combinação com esse comportamento.

### <a name="toggles-and-check-boxes"></a>Ativa/desativa e caixas de seleção

É possível encapsular o comportamento de um botão de alternância em uma classe, como [ `ToggleBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca e, em seguida, definir todas as os elementos visuais para alternância inteiramente em XAML.

O [ **ToggleLabel** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel) de exemplo usa o `ToggleBehavior` com um `DataTrigger` usar um `Label` com duas cadeias de caracteres de texto para alternância.

O [ **FormattedTextToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle) exemplo amplia esse conceito, alternando entre dois `FormattedString` objetos.

O [ `ToggleBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs) classe o **Xamarin.FormsBook.Toolkit** deriva de biblioteca `ContentView`, define uma `IsToggled` propriedade e incorpora um `ToggleBehavior` para alternância lógica. Isso torna mais fácil definir o botão de alternância em XAML, como demonstrado pelo [ **TraditionalCheckBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox) exemplo.

O [ **SwitchCloneDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo) inclui um [ `SwitchClone` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs) classe que deriva de `ToggleBase` e usa um [ `TranslateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs)classe para construir um botão de alternância que se parece com o xamarin. Forms `Switch`.

Um [ `RotateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs) no **Xamarin.FormsBook.Toolkit** fornece uma animação usada para fazer uma alavanca animada no [ **LeverToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle)exemplo.

### <a name="responding-to-taps"></a>Responder aos toques

Uma desvantagem `EventTrigger` é que você não é possível anexá-lo para um `TapGestureRecognizer` para responder aos toques. Contornar esse problema é a finalidade das [ `TapBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs) no [ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

O [ **BoxViewTapShiver** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver) de exemplo usa `TapBehavior` usar anterior `ShiverAction` para tocado `BoxView` elementos.

O [ **ShiverViews** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews) exemplo mostra como reduzir a marcação, encapsulando um `ShiverView` classe.

### <a name="radio-buttons"></a>Botões de opção

O [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) library também tem um [ `RadioBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs) classe para fazer os botões de opção são agrupados por um `string` nome do grupo.

O [ **RadioLabels** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels) programa usa cadeias de caracteres de texto para seu botão de opção. O [ **RadioStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle) de exemplo usa um `Style` à diferença na aparência entre botões marcadas e desmarcadas. O [ **RadioImages** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages) amostra usa imagens demarcadas seus botões de opção:

[![Captura de tela tripla de imagens de rádio](images/ch23fg17-small.png "imagens de botão de rádio")](images/ch23fg17-large.png#lightbox "imagens de botão de rádio")

O [ **TraditionalRadios** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios) exemplo desenha tradicional botões de opção que aparece com um ponto dentro de um círculo.

### <a name="fades-and-orientation"></a>Esmaece e orientação

O exemplo final, [ **MultiColorSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders) permite que você alterne entre os três modos de seleção de cor diferente usando botões de opção. As três exibições de fade in e check-out usando um [ `FadeEnableAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs) no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca.

O programa também responde às alterações na orientação entre retrato e paisagem usando um [ `GridOrientationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs) no **Xamarin.FormsBook.Toolkit** biblioteca.

## <a name="related-links"></a>Links relacionados

- [Capítulo 23 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [Capítulo 23 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [Trabalhar com gatilhos](~/xamarin-forms/app-fundamentals/triggers.md)
- [Comportamentos do Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
